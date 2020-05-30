---
title: Android App 架构模式
tags: [Andorid, Architecture]
description: 记录 Android App 常用的架构模式，如MVC，MVP，MVVM，MVI 和 Viper
date: 2020-01-01 13:44:11
---

## MVC

`Model-View-Controller` (`MVC`) 定义了三种组件：

- **Model** 模型层，包含了数据对象，数据库实体类和数据操作相关的业务逻辑；
- **View** 视图层，使用模型层渲染数据成用户可看到的界面
- **Controller** 控制层，作为`MVC`模式的核型组件，它封装了系统的主要逻辑并且控制视图层与模型层

原本作为桌面应用程序和 Web 应用使用模式，`MVC`：

或：

Android 理论上的 `MVC`

`View`显示是由`Controller`更新的，而由于`Android`里的`View`仅仅是静态`XML`布局，Android 的`View`不能实现更新逻辑，因此导致部分**视图逻辑**（比如视图的显示与隐藏）由`Activity`承担，因此实际上 Android 下的 MVC 如下：

Activity 同时承担视图层和控制层的工作，不仅使 Activity 类变得庞大，还使组件耦合后的 Activity 单元测试困难。

## MVP

`Model View Presenter`（`MVP`）定义三种组件：

- **Model** 模型层，包含了数据对象，数据库实体类和数据操作相关的业务逻辑；
- **View** 视图层，显示 UI 并通知展示层用户操作
- **Presenter** 展示层，与模型层和视图层通信并处理展示逻辑

由于 MVC 模式下 Activity 包含了视图层和控制层，并非正确的 MVC 实现，因此应用 MVP 模式，将 Activity 内控制层的代码迁移出来，

## MVVM

`Model-View-ViewModel`(`MVVM`) 定义三种组件：

- **View** 显示 UI，通知其他层用户的操作
- **ViewModel** 向（多个） View 提供信息
- **Model** 从数据库中获取信息并向 ViewModel 提供信息

`ViewModel`不会包含`View`的引用，仅向`View`提供信息；但提供`View`可以监听的事件。
