---
title: distributed systems
date: 2024-01-09 00:38:41
---

# 分布式

# 分布式数据一致性算法

## 一致性Hash算法

### 引入

在分布式集群中，对机器的增减是集群管理的基本功能，如果采用基本的`hash(obj) % N`，会导致原有的数据无法找到，严重违反了**单调性**原则

### 简介

一致性hash算法是MIT提出的一种分布式哈希实现算法，设计目标是为了解决网络中的热点问题，初衷与CARP类似，修正了CARP使用简单哈希算法带来的问题。

一致性Hash算法提出了在动态的cache环境中，判断hash算法好坏的四个定义：
- 平衡性(Balance)：平衡性是指哈希的结果能够尽可能分布到所有的缓冲中去，这样可以使得所有的缓冲空间都得到利用。很多哈希算法都能够满足这一条件。
- 单调性(Monotonicity)：单调性是指如果已经有一些内容通过哈希分派到了相应的缓冲中，又有新的缓冲加入到系统中。哈希的结果应能够保证原有已分配的内容可以被映射到原有的或者新的缓冲中去，而不会被映射到旧的缓冲集合中的其他缓冲区。
- 分散性(Spread)：分布式环境中，终端可能看不到所有的缓冲，而只能看到其中一部分。当中断通过hash将内容映射到缓冲时，由于不同终端所见的缓冲范围有可能不同，从而导致哈希的结果不一致，最终的结果是相同的内容被不同的终端映射到不同的缓冲区中。分散性的定义就是上述情况发生的严重程度。好的哈希算法应能够尽量避免不一致的情况发生，也就是尽量降低分散性。
- 负载(Load)：对于一个特定的缓冲区而言，也可能被不同的用户映射为不同的内容。好的哈希算法应能够尽量降低缓冲的负荷。

### 算法

首先将key值哈希到2^32的范围内，假设是一个首尾相接的环形空间；假设有三台cache服务器，把服务器通过hash算法，加入到环中。一般根据机器的IP或唯一的计算机别名进行计算

key值哈希后的结果沿着环顺时针寻找离自己最近的cache服务器，然后将数据存到上面

删除节点：假设cache3服务器宕机，需要将其从集群中摘除。之前存储在c3上的k1会顺着环找下一个距离最近的服务器，即c1。最后之影响到了k1，解决了`hash(key) % N`带来的雪崩问题。

不平衡问题
- 上述方案仍存在问题：一个节点宕机之后，数据需要落到距离他最近的节点上，会导致下个节点的压力突然增大，可能导致雪崩，整个服务挂掉。
  - 当节点c3摘除后，之前请求c3的流量全部来到了c1，若原来c3存在热点数据，则可能导致c1扛不住压力崩溃
  - 之前存储在c3的key全部到了c1，导致c1内存不足
- 虚拟节点：“虚拟节点”( virtual node )是实际节点(机器)在 hash 空间的复制品( replica )，一个实际节点(机器)对应了若干个“虚拟节点”，这个对应个数也称为“复制个数”，“虚拟节点”在 hash 空间中以hash值排列。
  - 某个节点宕机之后，存储及流量压力并没有全部转移到某台机器上，而是分散到了多台节点上。解决了节点宕机可能存在的雪崩问题。
  - 当物理节点越多的时候，虚拟节点也越多，雪崩的可能性越小

## Paxos算法

由Lamport提出的一种基于消息传递的分布式一致性算法

Paxos算法解决的是分布式一致性问题，即分布式系统中各个进程如何就某一个值达成一致。


### Paxos算法的实现

Paxos算法运行在允许宕机故障的分布式系统中，不要求可靠的消息传递，允许消息出现延迟、丢失、乱序或重复。他利用Majority机制，保证了2F+1的容错能力，即2F+1个节点的系统中，最多允许F个节点同时出现故障。

- 角色：Paxos将系统中的角色分为Proposer、Acceptor、Learner
  - Proposer：提出提案（Proposal）。提案包括提案ID和提议的值
  - Acceptor：Acceptor参与决策，回应Proposers的提案。收到Proposal后可以接受它，如果一个Proposal被大多数Acceptor接受，则称该Proposal被批准
  - Learner：不参与决策，从Proposers/Acceptors学习最新达成一致的提案value
- 3个阶段
  - 第一阶段：Prepare阶段，Proposer向Acceptors发出Prepare请求，Acceptors针对收到的Prepare请求进行Promise承诺。
    - `Prepare`：Proposer生成全局唯一且递增的ID（可使用时间戳+server ID），向所有Acceptors发送Prepare请求，这里无需携带提案内容，只携带Proposal ID即可
    - `Promise`：Acceptors收到Prepare请求后，做出两个承诺Promise和一个应答
      - 承诺1：不再接受Proposal ID**小于等于**当前请求的Prepare请求
      - 承诺2：不再接受Proposal ID**小于**当前请求的Prepare请求
      - 应答：不违背以前做出的承诺的前提下，从之前accept的提案中选取Proposal ID最大的那个，回复它的value和Proposal ID，没有则返回空值
  - 第二阶段：Accept阶段，Proposer收到多数Acceptors承诺的Promise后，向Acceptors发出Propose请求，Acceptors针对收到的Propose请求进行Accept处理。
    - `Propose`：Proposer 收到多数Acceptors的Promise应答后，从应答中选择Proposal ID最大的提案的Value，作为本次要发起的提案。如果所有应答的提案Value均为空值，则可以自己随意决定提案Value。然后携带当前Proposal ID，向所有Acceptors发送Propose请求。
    - `Accept`：Acceptor收到Propose请求后，在不违背自己之前作出的承诺下，接受并持久化当前Proposal ID和提案Value。
  - 第三阶段：Learn阶段
    - Proposer在收到大多数Acceptors的Accept后，标志着本次Accept成功，决议形成，将决议发给所有的Learners
- 伪代码
  - 获取一个Proposal ID n，为了保证Proposal ID唯一，可采用时间戳+Server ID生成；
  - Proposer向所有Acceptors广播Prepare(n)请求；
  - Acceptor比较n和minProposal，如果n>minProposal，minProposal=n，并且将 acceptedProposal 和 acceptedValue 返回；
  - Proposer接收到过半数回复后，如果发现有acceptedValue返回，将所有回复中acceptedProposal最大的acceptedValue作为本次提案的value，否则可以任意决定本次提案的value；
  - 到这里可以进入第二阶段，广播Accept (n,value) 到所有节点；
  - Acceptor比较n和minProposal，如果n>=minProposal，则acceptedProposal=minProposal=n，acceptedValue=value，本地持久化后，返回；否则，返回minProposal。
  - 提议者接收到过半数请求后，如果发现有返回值result >n，表示有更新的提议，跳转到1；否则value达成一致。

Basic Paxos算法可能会形成活锁：两个Proposer交替Prepare成功，而Accept失败，形成活锁

原始的Paxos算法一次只能对一个值形成决议，决议的形成至少需要两次网络来回。如果想一次确定多个值，且提高效率，可以使用Multi-Paxos算法，Multi-Paxos做出的两点改进：
- 针对每一个要确定的值，运行一次Paxos算法实例(Instance)，形成决议。每一个Paxos实例使用唯一的Instance ID标识。
- 在所有Proposers中选举一个Leader，由Leader唯一地提交Proposal给Acceptors进行表决。这样没有Proposer竞争，解决了活锁问题。在系统中仅有一个Leader进行Value提交的情况下，Prepare阶段就可以跳过，从而将两阶段变为一阶段，提高效率。

Multi-Paxos首先需要选举Leader，Leader的确定也是一次决议的形成，所以可执行一次Basic Paxos实例来选举出一个Leader。选出Leader之后只能由Leader提交Proposal，在Leader宕机之后服务临时不可用，需要重新选举Leader继续服务。在系统中仅有一个Leader进行Proposal提交的情况下，Prepare阶段可以跳过。

Multi-Paxos通过改变Prepare阶段的作用范围至后面Leader提交的所有实例，从而使得Leader的连续提交只需要执行一次Prepare阶段，后续只需要执行Accept阶段，将两阶段变为一阶段，提高了效率。为了区分连续提交的多个实例，每个实例使用一个Instance ID标识，Instance ID由Leader本地递增生成即可。

Multi-Paxos允许有多个自认为是Leader的节点并发提交Proposal而不影响其安全性，这样的场景即退化为Basic Paxos。

Chubby和Boxwood均使用Multi-Paxos。ZooKeeper使用的Zab也是Multi-Paxos的变形。

## Raft算法

Paxos算法是从分布式一致性问题出发，Raft算法从多副本状态机的角度，它实现了与Paxos相同的功能，它将一致性分为了多个子问题：Leader选举 (Leader Election)、日志同步 (Log Replication)、安全性 (Safety)、日志压缩 (Log Compaction)、成员变更 (Membership Change)等。同时，Raft使用了更强的假设，使得考虑的子问题减少。

### Raft算法中的角色
- Leader：接受客户端的请求，并向Follower同步请求日志，当日志同步到大多数节点之后，告诉Follower提交日志
- Follower：接受并持久化Leader同步的日志，当Leader告知可以提交之后，提交日志
- Candidate：Leader选举过程中的临时角色

Raft要求系统在任意时刻最多只有一个Leader，正常工作期间只有Leader和Followers。

### 角色状态转换

Follower只响应其他服务器的请求。如果超时没有收到Leader的消息，它会成为一个candidate并开始一次Leader选举，收到大多数投票的Candidate会成为新的Leader。Leader在宕机之前会一直保持Leader的状态。

Raft算法将时间分为一个个的任期 (term)，每一个term的开始都是leader选举。成功选举leader后，整个term内都由leader管理集群。若leader选举失败，该term就会因为没有leader而结束。

### Raft算法子问题

#### leader选举

Raft 使用心跳(heartbeat)触发Leader选举。当服务器启动时，初始化为Follower。Leader向所有Followers周期性发送heartbeat。如果Follower在选举超时时间内没有收到Leader的heartbeat，就会等待一段随机的时间后发起一次Leader选举。

Follower将其当前term加一然后转换为Candidate。它首先给自己投票并且给集群中的其他服务器发送 RequestVote RPC (RPC细节参见八、Raft算法总结)。

结果有以下三种情况:
- 赢得了多数的选票，成功选举为Leader；
- 收到了Leader的消息，表示有其它服务器已经抢先当选了Leader；
- 没有服务器赢得多数的选票，Leader选举失败，等待选举时间超时后发起下一次选举。

Raft保证选举出的Leader上一定具有最新的已提交的日志，见安全性。

#### 日志同步

Leader选出后，就开始接收客户端的请求。Leader把请求作为日志条目(Log entries)加入到它的日志中，然后并行的向其他服务器发起 AppendEntries RPC 复制日志条目。当这条日志被复制到大多数服务器上，Leader将这条日志应用到它的状态机并向客户端返回执行结果。

若有Follower没有成功复制日志，Leader会无限重试 AppendEntries RPC，直到所有的Followers最终存储了所有的日志

Raft日志同步保证如下两点:
- 如果不同日志中的两个条目有着相同的索引和任期号，则它们所存储的命令是相同的。
- 如果不同日志中的两个条目有着相同的索引和任期号，则它们之前的所有条目都是完全一样的。

Leader通过强制Followers复制它的日志来处理日志的不一致，Followers上的不一致的日志会被Leader的日志覆盖。Leader为了使Followers的日志同自己的一致，Leader需要找到Followers同它的日志一致的地方，然后覆盖Followers在该位置之后的条目。Leader会从后往前试，每次AppendEntries失败后尝试前一个日志条目，直到成功找到每个Follower的日志一致位点，然后向后逐条覆盖Followers在该位置之后的条目。

#### 安全性

Raft增加了如下两条限制以保证安全性：
- 拥有最新的已提交的log entry的Follower才有资格成为Leader
  - 这个保证是通过RequestVote RPC进行的。每个Candidate发送该RPC时，要带上自己的最后一条日志的term和log index，其他节点收到消息后，若发现自己的日志比请求中携带的更新，则拒绝投票。
  - 日志比较原则：本地最后一条log entry的term更大，则term大的更新；若term相同，log index更大的更新
- Leader只能推进commit index来提交当前term的已经复制到大多数服务器上的日志，旧term日志的提交要等到提交当前term的日志来间接提交(log index 小于 commit index的日志被间接提交)
  - 原因是：防止已提交的日志又被覆盖

#### 日志压缩

实际系统中，不能让日志无限增长，否则系统重启时会花很长时间回放，影响可用性。Raft采用对整个系统进行snapshot来解决，snapshot以前的日志都可以丢弃。

每个副本独立的对自己的状态进行snapshot，并且只能对已提交的日志进行snapshot。

snapshot包含：
- 日志元数据。最后一条提交的日志的log index和term。这两个值在snapshot之后的第一条log entry的AppendEntries RPC的完整性检查的时候会被用上。
- 系统当前状态

当leader发给某个日志落后太多的Follower的log entry被丢弃，Leader会将snapshot发给follower。或者当新加入一台机器时，会发送snapshot。发送snapshot使用的是 InstalledSnapshot RPC。

snapshot不能做的太频繁，否则消耗磁盘带宽；也不能间隔太久，否则一旦节点重启需要回放太多，影响可用性。

可以使用copy-on-write技术，避免snapshot耗时太长影响正常日志同步

#### 成员变更

Raft提出两阶段成员变更方法。集群先从旧成员配置Cold切换到过渡成员配置，称为共同一致 (joint consensus)，它是旧成员配置 + 新成员配置，一旦共同一致被提交，系统就会切换到Cnew。过程如下
- Leader收到成员变更请求从Cold切成Cnew；
- Leader在本地生成一个新的log entry，其内容是Cold∪Cnew，代表当前时刻新旧成员配置共存，写入本地日志，同时将该log entry复制至Cold∪Cnew中的所有副本。在此之后新的日志同步需要保证得到Cold和Cnew两个多数派的确认；
- Follower收到Cold∪Cnew的log entry后更新本地日志，并且此时就以该配置作为自己的成员配置；
- 如果Cold和Cnew中的两个多数派确认了Cold U Cnew这条日志，Leader就提交这条log entry；
- 接下来Leader生成一条新的log entry，其内容是新成员配置Cnew，同样将该log entry写入本地日志，同时复制到Follower上；
- Follower收到新成员配置Cnew后，将其写入日志，并且从此刻起，就以该配置作为自己的成员配置，并且如果发现自己不在Cnew这个成员配置中会自动退出；
- Leader收到Cnew的多数派确认后，表示成员变更成功，后续的日志只要得到Cnew多数派确认即可。Leader给客户端回复成员变更执行成功。



## ZAB算法

## Snowflake算法

snowflake算法（雪花算法）
- 将64bit划分为不同部分，Java中的long类型就是64bit，因此snowflake算法生成的id使用long存储
  - 符号位：1bit，永远为0
  - 时间戳：31bit，以毫秒为单位，可以使用69年
  - 工作机器id：10bit，可表示1024台机器，
  - 自增序列号：12bit，可表示4096个数
  - 因此在一毫秒一个数据中心的一台机器上可产生4096个有序的不重复的ID
- 缺陷：雪花算法强依赖机器时钟，如果机器上时钟回拨，会导致发号重复或者服务会处于不可用状态。若回退前生成了一些id，回退后再生成id则可能造成重复
  - 解决方案：百度 UidGenerator，美团Leaf，Mist薄雾算法