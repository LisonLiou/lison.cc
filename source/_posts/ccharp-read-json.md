---
title: .NET序列化反序列化对象并写入JSON文件（读取JSON并反射其属性与值）
tags:
  - json
categories:
  - c#
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2022-04-27 15:03:22
desc:
keywords: .net framework 4.6读取写入json文件, C#序列化/反序列化json对象
---


<!--more-->

不使用NEWTONSOFT的JSON库，使用.net framework 4.6，序列化对象并存入JSON文件；然后读取JSON文件内容并反序列化对象；使用反射方法读取其所有属性与属性值，直接上代码：

```
using System;
using System.IO;
using System.Text;
using System.Runtime.Serialization;
using System.Runtime.Serialization.Json;
using System.Reflection;

namespace ConsoleApp1
{
    [DataContract]
    class MyConfig
    {
        [DataMember]
        public string IP
        {
            get; set;
        }
        [DataMember]
        public int Port
        {
            get; set;
        }
    }

    class Program
    {
        const string CONFIG_JSON = ".config";

        static void Main(string[] args)
        {

            MyConfig config = new MyConfig();

            Console.WriteLine("IP:");
            config.IP = Console.ReadLine();
            Console.WriteLine("Port:");
            config.Port = int.Parse(Console.ReadLine());

            string json = JSONSerializer<MyConfig>.Serialize(config);
            File.WriteAllText(CONFIG_JSON, json);
            Console.WriteLine("写入JSON文件.config\r\n开始读取文件内容");

            json = File.ReadAllText(CONFIG_JSON);
            Console.WriteLine("读取JSON文件.config\r\n" + json);
            config = JSONSerializer<MyConfig>.DeSerialize(json);

            Type t = config.GetType();
            PropertyInfo[] pArray = t.GetProperties();
            for (int i = 0; i < pArray.Length; i++)
            {
                Console.WriteLine("属性" + i + ": " + pArray[i].Name + " " + pArray[i].GetValue(config));
            }

            Console.Read();

        }
    }

    public static class JSONSerializer<TType> where TType : class
    {
        /// <summary>
        /// Serializes an object to JSON
        /// </summary>
        public static string Serialize(TType instance)
        {
            var serializer = new DataContractJsonSerializer(typeof(TType));
            using (var stream = new MemoryStream())
            {
                serializer.WriteObject(stream, instance);
                return Encoding.Default.GetString(stream.ToArray());
            }
        }

        /// <summary>
        /// DeSerializes an object from JSON
        /// </summary>
        public static TType DeSerialize(string json)
        {
            using (var stream = new MemoryStream(Encoding.Default.GetBytes(json)))
            {
                var serializer = new DataContractJsonSerializer(typeof(TType));
                return serializer.ReadObject(stream) as TType;
            }
        }
    }
}

```

执行后会在当前路径生成.config文件。
