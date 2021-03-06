# Chapter_3 客户端API：基础知识

## 3.1 概述

&emsp;&emsp;Hbase的主要客户端接口是由org.apache.hadoop.hbase.client包中的HTable类提供的。通过这个类，用户可以实现向Hbase中存储数据和从Hbase检索数据，以及删除无效数据之类的操作。  

&emsp;&emsp;所有修改数据的操作都保证 **行级别** 的**原子性**，这会影响到在一行数据上的所有的并发读写操作。换句话说，不同客户端或线程对同一行数据的并发读写操作，并不会影响彼此操作过程的原子性：要么读到最新的修改，要么等待系统允许写入该行修改。

&emsp;&emsp;通常，在正常负载和常规操作下，客户端读操作不会受到其他修改数据的客户端的影响，因为他们之间的冲突可以忽略不计，但是当存在许多客户端需要同时修改同一行数据时就会产生问题，所以，应当尽量使用**批处理**（batch）更新来减少单独操作同一行数据的次数。

&emsp;&emsp;写操作过程中涉及的列的数目不会影响该行数据的原子性，行原子性会同时保护到所有列。

&emsp;&emsp;最后，创建HTable实例是有代价的，每个实例都需要扫描.META.表，以检查该表是否存在，是否可用，此外还要执行一些其他操作，这些检查和操作导致实例调用非常耗时。因此，推荐用户只创建一次HTable实例，而且是每个线程创建一个，然后在客户端应用的生存周期内复用这个对象。

**总结**：

* 只创建一次HTable实例，一般在应用程序开始时创建；
* 为执行的每一个线程创建独立的HTable实例；
* 所有的修改操作只保证行级别的原子性。

## 3.2 CRUD操作

&emsp;&emsp;HBase中的CRUD操作都由HTable类提供。

### 3.2.1 put方法

&emsp;&emsp;以下介绍的这组操作可以分为两类：一类作用于单行，另一类作用于多行。

1. 单行put

    void put(Put put) throws IOException
    这个方法以单个Put或者存储在列表中的一组Put对象作为输入参数，其中Put 对象是由以下几个构造函数创建：

2. 
