---
title: projects
date: 2023-11-29 13:46:41
---

# Selected Projects

## Home Monitoring System

A home monitoring system based on Python, Django, OpenCV, PyTorch, ECS, Cloud MySQL.

Features:
- real-time **video streaming** of network cameras through RTMP, nginx, ECS.
- **emotion recognition** through two models.
- **pedestrian and pet recognition** through YOLOv4.
- facial recognition and blink detection for **dynamic face login**. Refuse pictures for better security.
- **webpage navigation** through gestures for the convenience of people who have difficulty operating a mouse.
- **fall detection** through YOLO + Single Person Pose Estimator + Multi-Objects Tracker + Spatial Temporal GCN

gesture navigation demo:
![a](https://i.imgur.com/6AbZ7UX.gif)

dynamic facial recognition for login - using a picture will cause failure
![b](https://i.imgur.com/xL4pfoW.gif)

dynamic facial recognition for login - user must blink to login
![c](https://i.imgur.com/f4fX6fZ.gif)

## eMovie (Movie Recommendation System)

A movie recommendation system based on Spring Boot, MyBatis, Redis, Cloud MySQL, Lucene, Python.

Features:
- user authorization and authentication
- fuzzy query of movie title
- movie metadata caching
- Cache-Aside pattern for data consistency
- distributed locks and Lua scripts for high-concurrency scenario
- recommendation
  - personalized recommendation, based on hybrid algorithm and collaborative filtering
  - general recommendation, based on TF-IDF

## DBMS

A Database Management System based on C++11 and Qt.

Features:
- **SQL parse and execution**
- **Table**: create, alter, drop
- **Data**: insert, delete, select (WHERE, ORDER BY, LIMIT), update
- **User**: create, drop, grant, revoke
- **Join**: two table join
- **Integrity**: primary key, foreign key
