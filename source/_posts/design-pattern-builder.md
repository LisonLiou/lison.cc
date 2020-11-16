---
title: 设计模式之我所使用的Builder模式 – 构建器模式
tags:
  - 设计模式
categories:
  - java
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2020-4-15 19:45:22
desc:
keywords: 设计模式构造器模式,设计模式建造者模式,安卓构建器模式
---

Builder模式，翻译过来中文是构建器、建造器都可以。定义为：将一个复杂对象的构建与其表示分离，使得同样的构建过程可以创建不同的表示。同样的构建过程是指同样的构建方式，比如汽车组装的过程是 组装大架子(); 组装轮胎(); 喷漆(); 这样一个过程，那最后组装完成下线的是一辆本田还是一辆自行车，取决于你调用构建过程所传入的Builder类型。

<!--more-->

经典的Builder模式主要有四个参与者：

- Product：被构造的复杂对象，ConcreteBuilder用来创建该对象的内部表示，并定义它的装配过程。
- Builder：抽象接口，用来定义创建Product对象的各个组成部件的操作。
- ConcreteBuilder：Builder接口的具体实现，可以定义多个，是实际构建Product对象的地方，同时会提供一个返回Product的接口。
- Director：Builder接口的构造器和使用者。

直接代码说话

```
public class User {
    private final String firstName;
    private final String lastName;
    private final String gender;
    private final int age;
    private final String phoneNo;

    private User(Builder builder) {
        firstName = builder.firstName;
        lastName = builder.lastName;
        gender = builder.gender;
        age = builder.age;
        phoneNo = builder.phoneNo;
    }

    public static final class Builder {
        private int age;
        private String firstName;
        private String lastName;
        private String gender;
        private String phoneNo;

        public Builder() {

        }

        public Builder age(int val) {
            age = val;
            return this;
        }

        public Builder firstName(String val) {
            firstName = val;
            return this;
        }

        public Builder lastName(String val) {
            lastName = val;
            return this;
        }

        public Builder gender(String val) {
            gender = val;
            return this;
        }

        public Builder phoneNo(String val) {
            phoneNo = val;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }

    @Override
    public String toString() {
        return "User{" +
                "firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", gender='" + gender + '\'' +
                ", age=" + age +
                ", phoneNo='" + phoneNo + '\'' +
                '}';
    }
}

使用方法：
User user = new User.Builder().firstName("Lison").lastName("Liou").age(32).phoneNo("15066796811").build();
构造器与链式调用，得劲儿。
如果用Android Studio等Intellij全家桶的话，推荐插件：InnerBuilder，自动对Bean生成构造器模式模式的代码，
好东西。
```