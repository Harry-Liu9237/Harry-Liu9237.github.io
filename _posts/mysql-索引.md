<h1>1.什么是索引?</h1>
<p>索引其实是一种数据结构，能够帮助我们快速的检索数据库中的数据</p>
<h1>2.索引具体采用的哪种数据结构呢？</h1>
<p>常见的MySQL主要有两种结构：Hash索引和B+ Tree索引, InnoDB引擎，默认的是B+树</p>
<h1>3.B+ Tree索引和Hash索引区别？</h1>
<p>哈希索引适合等值查询，但是无法进行范围查询 <br/>
哈希索引没办法利用索引完成排序 <br/>
哈希索引不支持多列联合索引的最左匹配规则 <br/>
如果有大量重复键值的情况下，哈希索引的效率会很低，因为存在哈希碰撞问题</p>
<h1>4.InnoDB使用的B+ 树的索引模型，为什么采用B+ 树吗？这和Hash索引比较起来有什么优缺点吗？</h1>
<p>因为Hash索引底层是哈希表，哈希表是一种以key-value存储数据的结构，所以多个数据在存储关系上是完全没有任何顺序关系的，
所以，对于区间查询是无法直接通过索引查询的，就需要全表扫描。所以，哈希索引只适用于等值查询的场景。
而B+ 树是一种多路平衡查询树，所以他的节点是天然有序的（左子节点小于父节点、父节点小于右子节点），
所以对于范围查询的时候不需要做全表扫描</p>
<h1>5.B+ Tree的叶子节点都可以存哪些东西吗？</h1>
<p>InnoDB的B+ Tree可能存储的是整行数据，也有可能是主键的值</p>
<h1>6.那这两者有什么区别吗？</h1>
<p>在 InnoDB 里，索引B+ Tree的叶子节点存储了整行数据的是主键索引，也被称之为聚簇索引。
而索引B+ Tree的叶子节点存储了主键的值的是非主键索引，也被称之为非聚簇索引</p>
<h1>7.聚簇索引和非聚簇索引，在查询数据的时候有区别吗？</h1>
<p>聚簇索引：将数据存储与索引放到了一块，索引结构的叶子节点保存了行数据<br/>
    非聚簇索引: 将数据与索引分开存储，索引结构的叶子节点指向了数据对应的位置</p>
<h1>8.什么情况下设置了索引但无法使用 </h1>
<p>以“%”开头的LIKE语句，模糊匹配<br/>
  OR语句前后没有同时使用索引<br/>
  数据类型出现隐式转化（如varchar不加单引号的话可能会自动转换为int型）</p>
<h1>9.简单描述mysql中，索引，主键，唯一索引，联合索引的区别，对数据库的性能有什么影响（从读写两方面）</h1>
<p>索引是一种特殊的文件(InnoDB数据表上的索引是表空间的一个组成部分)，它们包含着对数据表里所有记录的引用指针。<br/>

  普通索引(由关键字KEY或INDEX定义的索引)的唯一任务是加快对数据的访问速度。<br/>

  普通索引允许被索引的数据列包含重复的值。如果能确定某个数据列将只包含彼此各不相同的值，<br/>
  在为这个数据列创建索引的时候就应该用关键字UNIQUE把它定义为一个唯一索引。也就是说， 唯一索引可以保证数据记录的唯一性。<br/>

  主键，是一种特殊的唯一索引，在一张表中只能定义一个主键索引，主键用于唯一标识一条记录，使用关键字PRIMARY KEY 来创建。<br/>

  索引可以覆盖多个数据列，如像INDEX(columnA, columnB)索引，这就是联合索引。<br/>

  索引可以极大的提高数据的查询速度，但是会降低插入、删除、更新表的速度，因为在执行这些写操作时，还要操作索引文件。<br/>
<h1>10.索引的目的是什么？</h1>
  快速访问数据表中的特定信息，提高检索速度

  创建唯一性索引，保证数据库表中每一行数据的唯一性。

  加速表和表之间的连接

  使用分组和排序子句进行数据检索时，可以显著减少查询中分组和排序的时间
<h1>11.索引对数据库系统的负面影响是什么？</h1>
  创建索引和维护索引需要耗费时间，这个时间随着数据量的增加而增加；索引需要占用物理空间，不光是表需要占用数据空间，
  每个索引也需要占用物理空间；当对表进行增、删、改、的时候索引也要动态维护，这样就降低了数据的维护速度。
<h1>12.为数据表建立索引的原则有哪些？</h1>
  在最频繁使用的、用以缩小查询范围的字段上建立索引。在频繁使用的、需要排序的字段上建立索引
<h1>13.什么情况下不宜建立索引？</h1>
  对于查询中很少涉及的列或者重复值比较多的列，不宜建立索引。对于一些特殊的数据类型，不宜建立索引，比如文本字段（text）等
<h1>14.实现索引的方式? 索引的原理? 索引的代价? 索引的类型?</h1>
    实现索引的方式有两种：针对一张表的某些字段创建具体的索引,如对oracle: create index 索引名称 on 表名(字段名)；
    在创建表时为字段建立主键约束或者唯一约束，系统将自动为其建立索引。

    索引的原理：根据建立索引的字段建立索引表，存放字段值以及对应记录的物理地址，从而在搜索的时候根据字段值搜索索引表的到物理地址直接访问记录。

    引入索引虽然提高了查询速度,但本身占用一定的系统存储容量和系统处理时间,需要根据实际情况进行具体的分析.

    索引的类型有：B树索引，位图索引，函数索引等。

<h1>15.怎样创建一个一个索引,索引使用的原则,有什么优点和缺点?</h1>
    创建标准索引： CREATE  INDEX 索引名 ON 表名 (列名)  TABLESPACE 表空间名; 

创建唯一索引: CREATE unique INDEX 索引名 ON 表名 (列名)  TABLESPACE 表空间名; 

创建组合索引: CREATE INDEX 索引名 ON 表名 (列名1,列名2)  TABLESPACE 表空间名; 

创建反向键索引: CREATE INDEX 索引名 ON 表名 (列名) reverse TABLESPACE 表空间名; 

索引使用原则： 

索引字段建议建立NOT NULL约束 

经常与其他表进行连接的表，在连接字段上应该建立索引； 

经常出现在Where子句中的字段且过滤性很强的，特别是大表的字段，应该建立索引； 

可选择性高的关键字 ，应该建立索引； 

可选择性低的关键字，但数据的值分布差异很大时，选择性数据比较少时仍然可以利用索引提高效率 

复合索引的建立需要进行仔细分析；尽量考虑用单字段索引代替： 

A、正确选择复合索引中的第一个字段，一般是选择性较好的且在where子句中常用的字段上； 

B、复合索引的几个字段经常同时以AND方式出现在Where子句中可以建立复合索引；否则单字段索引； 

C、如果复合索引中包含的字段经常单独出现在Where子句中，则分解为多个单字段索引； 

D、如果复合索引所包含的字段超过3个，那么仔细考虑其必要性，考虑减少复合的字段； 

E、如果既有单字段索引，又有这几个字段上的复合索引，一般可以删除复合索引； 

频繁DML的表，不要建立太多的索引； 

不要将那些频繁修改的列作为索引列； 


<h1>16.索引的优缺点?</h1>
  优点： 

  1. 创建唯一性索引，保证数据库表中每一行数据的唯一性 

  2. 大大加快数据的检索速度，这也是创建索引的最主要的原因 

  3. 加速表和表之间的连接，特别是在实现数据的参考完整性方面特别有意义。 

  4. 在使用分组和排序子句进行数据检索时，同样可以显著减少查询中分组和排序的时间。 
  
  缺点： 

  1. 索引创建在表上，不能创建在视图上 

  2. 创建索引和维护索引要耗费时间，这种时间随着数据量的增加而增加 

  3. 索引需要占物理空间，除了数据表占数据空间之外，每一个索引还要占一定的物理空间，如果要建立聚簇索引，那么需要的空间就会更大 

  4. 当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，降低了数据的维护速度 

<h1>17.索引的实现方式</h1>
    都是 B+树索引， Innodb 是索引组织表， myisam 是堆表， 索引组织表和堆表的区别要熟悉
