---
title: Android Architecture Components
tags: [Android, Architecture]
description: 记录 Android Architecture Components 中各的作用和用法
date: 2020-01-01 14:46:22
---

## 介绍

Android Architecture Components 是 Google 在 2017 年 11 月推出的解决 Andorid 开发过程中处理据库映射、生命周期管理问题和异步操作等重复性和复杂性高的痛点的组件库集合，主要有： Room，LifeCycle，ViewModel，LiveData，DataBinding，Pageing，Navigation 和 WorkManager。

## Room

处理数据库持久化

Entity

```kotlin
@Entity(tableName = "user")
data class User {
    @PrimaryKey
    var id: Int? = null
}
```

Dao

```kotlin
@Dao
interface UserDao {
  @get:Query("SELECT * FROM user")
  val all: Observable<List<User>>

  @Insert(onConflict = REPLACE)
  fun insert(user: User)

  @Query("DELETE FROM user WHERE id = :id")
  fun delete(id: Int?)

  @Query("DELETE FROM user")
  fun deleteAll()

  @Update
  fun update(user: User)
}
```

DataBase

```kotlin
@Database(entities = [User::class], version = 1)
@TypeConverters(IntegerListTypeConverter::class)
abstract class LocalDatabase : RoomDatabase() {...}
```

## LifeCycle

创建可感知当前 ndroid 生命周期状态的组件

## ViewModel

承载数据与配置变化

## LiveData

向视图层提供**可订阅**数据

```kotlin
@Query("select * from user")
fun getAll(): LiveData<List<User>>
```

## DataBinding

允许 Views 订阅 XML 中的数据变化

单向绑定

```xml
<TextView
  android:id="@+id/userTitleTextView"
  android:text="@{user.title}"
  ... />
```

双向绑定

```xml
<TextView
  android:id="@+id/userTitleTextView"
  android:text="@={user.title}"
  ... />
```

## Paging

Paging 用于处理大量数据显示，相比`RecycrViewle`更简单

## Navigation

处理导航

## WorkManager

处理后台任务，替换`JobScheduler`确保后台任务执行。由于 Google 在 Android 系统电池优化上工作，`Doze mode`与`background service limits`的存在导致后台任务的实现更复杂，`WorkManager`则屏蔽这些技术细节，提供简单的接口来实现可延时需确保执行的后台任务。
