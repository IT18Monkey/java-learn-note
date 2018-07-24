# MySQL优化---EXPAIN

​	Explain命令的作用是展示MySQL是如何执行sql语句的，SELECT, DELETE, INSERT, REPLACE,  UPDATE 语句都可以使用Explain命令。

​	EXPLAIN为SELECT语句中使用的每个table返回一行信息。它以MySQL在处理语句时的读取顺序列出所有的table。MySQL使用嵌套循环的方式解决所有的表连接（join）。这意味着MySQL从第一个table读取一行，然后在第二个table、第三个table中查找匹配的行，以此类推。当所有的table都被处理过时，MySQL会输出所选的列并在所有的table中进行反向跟踪，直到找到一个有更多匹配行的table。从该table中读取下一行，并继续处理下一个table。

​	理解Explain输出的信息含义对SQL语句的优化有着至关重要的作用。 下面让我们详细解析下Explain命令输出的各字段的含义。

####Explain 输出的列：

| 列名          | JSON 名(FORMAT=JSON) | 含义                   |
| ------------- | -------------------- | ---------------------- |
| id            | select_id            | SELECT 语句的id        |
| select_type   | 无                   | SELETE 语句的类型      |
| table         | table_name           | 表名                   |
| partitions    | partitions           | 分区                   |
| type          | access_type          | join 类型              |
| possible_keys | possible_keys        | 可供选择使用的索引     |
| key           | key                  | 实际使用的索引         |
| key_len       | key_length           | 实际使用索引的长度     |
| ref           | ref                  | 列与索引的比较         |
| rows          | rows                 | 检查的行预估值。       |
| filtered      | filtered             | 按表条件过滤的行百分比 |
| Extra         | 无                   | 其他信息               |

1. id

   SELECT的标识符。这是查询中的SELECT的序号。如果行引用其他行的联合结果，则该值可以为空。举个例子，当table列显示<unionM,N>时，表示该行引用了id值为M和N的行的联合结果，这个时候id列的值就会为空。

   如下sql：

   ```sql
    EXPLAIN SELECT *FROM(SELECT 2 FROM dual UNION ALL SELECT 3 FROM dual)t
   ```

   执行结果：

   ![53243224326](C:\Users\wanghaohao.QingQing\Desktop\开发资料\自我\java-learn-note\pic\1532432243263.png)

2. select_type

   SELECT的类型，下表中展示了所有可能的值及其含义。

   | 名称                 | JSON 名                    | 含义                                                         |
   | -------------------- | -------------------------- | ------------------------------------------------------------ |
   | SIMPLE               | 无                         | 简单的SELECT(没有使用UNION或者子查询)                        |
   | PRIMARY              | 无                         | 最外层的查询                                                 |
   | UNION                | 无                         | 在一个UNION中第二或后面的SELECT语句                          |
   | DEPENDENT UNION      | dependent (true)           | 在一个UNION中第二或后面的SELECT语句，依赖于外层查询          |
   | UNION RESULT         | union_result               | UNION的结果                                                  |
   | SUBQUERY             | 无                         | 子查询中的第一个SELECT                                       |
   | DEPENDENT SUBQUERY   | dependent (true)           | 子查询中的第一个SELECT，依赖于外层查询                       |
   | DERIVED              | 无                         | 衍生表（Derived table）                                      |
   | MATERIALIZED         | materialized_from_subquery | 实例化子查询（Materialized subquery）                        |
   | UNCACHEABLE SUBQUERY | cacheable (false)          | 不能缓存结果的子查询，并且必须为外部查询的每一行重新计算结果 |
   | UNCACHEABLE UNION    | cacheable (false)          | 在一个UNCACHEABLE SUBQUERY的UNION语句中第二或后面的SELECT语句 |

3. table

   输出行引用的表名，也可能是以下几种值：

   * `<union*M*,*N*>`：该行引用了id值为M和N的行的联合结果
   * `<derived*N*>`：该行引用了id值为N的行的衍生表结果。例如，衍生表可以从from语句的子查询中生成。
   * `<subquery*N*>`：该行引用了id值为N的行的实例化子查询结果。

4. partitions

5. type

   join类型，本列展示了表是如何join的。下面是可能的值类型，按照顺序由最好到最坏。

   * system
   * ​