# MySQL优化---EXPAIN（mysq版本8.0）

​	Explain命令是MySQL提供的内置命令，它的作用是向我们展示MySQL是如何执行sql语句的。SELECT, DELETE, INSERT, REPLACE,  UPDATE 语句都可以使用Explain命令。

​	EXPLAIN为SELECT语句中使用的每个table返回一行信息。它以MySQL在处理语句时的读取顺序列出所有的table。MySQL使用嵌套循环的方式解决所有的表连接（join）。这意味着MySQL从第一个table读取一行，然后在第二个table、第三个table中查找匹配的行，以此类推。当所有的table都被处理过时，MySQL会输出所选的列并在所有的table中进行反向跟踪，直到找到一个有更多匹配行的table。从该table中读取下一行，并继续处理下一个table。

​	理解Explain输出的信息含义对SQL语句的优化有着至关重要的作用。 下面让我们详细解析下Explain命令输出的各字段的含义。

####Explain 输出的列：

| 列名          | JSON 名(FORMAT=JSON) | 含义                 |
| ------------- | -------------------- | -------------------- |
| [id](#explain_id)  | select_id            | SELECT 语句的id      |
| [select_type](#explain_select_type) | 无                   | SELETE 语句的类型    |
| [table](#explain_table)         | table_name           | 表名                 |
| [partitions](#explain_partitions)    | partitions           | 分区                 |
| [type](#explain_type)          | access_type          | join 类型            |
| [possible_keys](#explain_possible_keys) | possible_keys        | 可供选择使用的索引   |
| [key](#explain_key)           | key                  | 实际使用的索引       |
| [key_len](#explain_key_len)       | key_length           | 实际使用索引的长度   |
| [ref](#explain_ref)           | ref                  | 与索引比较的列       |
| [rows](#explain_rows)          | rows                 | 预估检测行数         |
| [filtered](#explain_filtered)          | filtered             | 依据表条件过滤行占比 |
| [Extra](#explain_extra)          | 无                   | 其他信息             |

1. <span id ='explain_id'>id</span>

   `SELECT`的标识符。这是查询中的`SELECT`的序号。如果当前行引用了其他行的联合结果，则该值为空。举个例子，当table列显示<unionM,N>时，表示该行引用了id值为M和N的行的联合结果，这个时候id列的值就会为空。

   如下sql：

   ```sql
    EXPLAIN SELECT *FROM(SELECT 2 UNION ALL SELECT 3 )t;
   ```

   执行结果：

   ![53243224326](C:\Users\wanghaohao.QingQing\Desktop\开发资料\自我\java-learn-note\pic\1532432243263.png)

2. <span id="explain_select_type">select_type</span>

   SELECT的类型，下表中展示了所有可能的值及其含义。

   | 名称                 | JSON 名                    | 含义                                                         |
   | -------------------- | -------------------------- | ------------------------------------------------------------ |
   | SIMPLE               | 无                         | 简单的SELECT(没有使用UNION或者子查询)                        |
   | PRIMARY              | 无                         | 最外层的查询                                                 |
   | UNION                | 无                         | 在一个UNION中第二或后面的SELECT语句                          |
   | DEPENDENT UNION      | dependent (true)           | 在一个UNION中第二或后面的SELECT语句，并且依赖于外层查询      |
   | UNION RESULT         | union_result               | UNION的结果                                                  |
   | SUBQUERY             | 无                         | 子查询中的第一个SELECT                                       |
   | DEPENDENT SUBQUERY   | dependent (true)           | 子查询中的第一个SELECT，并且依赖于外层查询                   |
   | DERIVED              | 无                         | 派生表（Derived table）                                      |
   | MATERIALIZED         | materialized_from_subquery | 实例化子查询（Materialized subquery）                        |
   | UNCACHEABLE SUBQUERY | cacheable (false)          | 不能缓存结果的子查询，并且必须为外部查询的每一行重新计算结果 |
   | UNCACHEABLE UNION    | cacheable (false)          | 在一个UNCACHEABLE SUBQUERY的UNION语句中第二或后面的SELECT语句 |

   `DEPENDENT`通常意味着使用了关联子查询。

   `DEPENDENT SUBQUERY ` 不同于 `UNCACHEABLE SUBQUERY` 。对于`DEPENDENT SUBQUERY`，对来自其外部上下文的每一组变量的不同值只重新计算一次子查询。对于`UNCACHEABLE SUBQUERY`，将会为外层上下文的每一行重新计算子查询。

   对于非查询语句，`select_type`的值为语句类型。比如，对于一个`DELETE`语句，值为`DELETE`

3. <span id="explain_table">table</span>

   当前行引用的表名，除此之外也可能是以下几种值：

   * `<union*M*,*N*>`：该行引用了id值为M和N的行的联合结果
   * `<derived*N*>`：该行引用了id值为N的行的派生表结果。例如，衍生表可以从from语句的子查询中生成。
   * `<subquery*N*>`：该行引用了id值为N的行的实例化子查询结果。

4. <span id="explain_partitions">partitions</span>

   查询将从其中匹配记录的分区。此列仅在使用partition关键字时显示。对于非分区表，值为null。

5. <span id="explain_type">type</span>

   join类型，本列展示了表是如何join的。下面是可能的值类型，排序规则为最好到最坏。

   * system

     该表只有一行。这是下面`const`类型的一种特例。

   * const

     该表最多只有一行匹配，在查询开始时就会被读取。因为只有一行，所以这一行中列的值可以被优化器的其他部分视为常量。`const`表非常快，因为它们只需要读一次。

     当把一个表的主键或唯一索引和一个常量值作比较时，`const`就会被使用。下面的语句中，t1就可以被作为一个`const`表。

     ```sql
     SELECT * FROM  t1 WHERE id=1;
     SELECT * FROM  t2 WHERE unique_index_part1=1 and unique_index_part1=2;
     ```

   * eq_ref

     在与前表的每个行组合中，从该表中读取一行。除了`system`和`const`类型，这个是最好的join类型。当一个join使用了主键或一个非空唯一索引的全部部分时，join类型就为`eq_ref`。

     `eq_ref`可用于使用`=`运算符进行比较的索引列。比较值可以是一个常量，也可以是一个表达式，它使用在这个表之前读取的表中的列。

     ```sql
     SELECT * FROM ref_table,other_table
       WHERE ref_table.key_column=other_table.column;

     SELECT * FROM ref_table,other_table
       WHERE ref_table.key_column_part1=other_table.column
       AND ref_table.key_column_part2=1;
     ```

   * ref

     在与前表的每个行组合中，从该表中读取具有匹配索引值的所有行。当join仅仅使用一个索引的左前缀，或者使用的索引不是一个主键，又或者不是唯一索引，`ref`就会被使用。如果使用的索引匹配的行数很少，这也不失为一个很好的join类型。

     `eq_ref`可用于使用`=`或`<=>`运算符进行比较的索引列。

     ```sql
     SELECT * FROM ref_table WHERE key_column=expr;

     SELECT * FROM ref_table,other_table
       WHERE ref_table.key_column=other_table.column;

     SELECT * FROM ref_table,other_table
       WHERE ref_table.key_column_part1=other_table.column
       AND ref_table.key_column_part2=1;
     ```

   * fulltext

     使用全文索引执行join

   * ref_or_null

     这个join类型和`ref`有点像，但是对于包含null值行，MySQL会做一个额外的查询。这种join类型优化最常用于解析子查询。

     ```sql
     SELECT * FROM ref_table
       WHERE key_column=expr OR key_column IS NULL;
     ```

   * index_merge

     这个join类型表示使用了索引合并优化。这个时候，`key`列显示了一系列使用到的索引，`key_len`显示了使用到的索引的最长键长。

   * unique_subquery

     在以下形式的某些`in`子查询中，这个join类型会替代`eq_ref`。

     ```sql
     value IN (SELECT primary_key FROM single_table WHERE some_expr)
     ```

     `unique_subquery`只是一个索引查找函数，它完全替代了子查询以提高效率。

   * index_subquery

     这个join类型和`unique_subquery`有些类似。它在子查询中替换，但在以下形式的子查询中适用于非唯一索引:

     ```sql
     value IN (SELECT key_column FROM single_table WHERE some_expr)
     ```

   * range

     当使用一个索引去选择行时只检索了给定范围的行。`key`列显示了使用了哪个索引，`key_len`显示了使用到的索引的最长键长。当join类型为range时，`ref`列值为null。

     `range`可用于使用 `=, <>, >, >=, <, <=, IS NULL, <=>,BETWEEN, LIKE, IN()` 运算符和一个常量进行比较的索引列。

     ```sql
     SELECT * FROM tbl_name
       WHERE key_column = 10;

     SELECT * FROM tbl_name
       WHERE key_column BETWEEN 10 and 20;

     SELECT * FROM tbl_name
       WHERE key_column IN (10,20,30);

     SELECT * FROM tbl_name
       WHERE key_part1 = 10 AND key_part2 IN (10,20,30)
     ```

   * index

     这个join类型和ALL相似，区别是扫描了索引数。以下两种情况：

     * 当索引是一个覆盖索引，即包含(或覆盖)所有需要查询的字段的值，那么就只需要扫描索引树而无需回表，这个时候`Extra`列的值就会为`Using index`。`index`类型通常要比`ALL`类型要快，因为索引的长度通常要比表数据小得多。
     * 按索引顺序查找数据行来执行全表扫描，这个时候`Extra`列的值不会为`Using index`。

   * ALL

     为了和前表的每一行做组合而全表扫描。这种类型通常不太好，应当尽量避免。我们可以通过给表添加索引来避免这种情况。

6. <span id="explain_possible_keys">possible_keys</span>

   这一列显示了可供MySQL选择用来查询使用的所有索引。需要注意的是其中的一些索引在实际运行时可能不会被使用。

   如果这一列为null，那就表示没有想关的索引。这个时候，可以检查where子句引用的列是否适合建立索引，通过建立合适的索引增强执行性能。索引建立完成后，可以再次执行`EXPLAIN`查看效果。

   想要知道表已经建立了哪些索引，可以通过`SHOW INDEX FROM table_name`查看。

7. <span id="explain_key">key</span>

   本列显示的MySQL实际选择使用的索引。如果本列为空，则表示MySQL没有找到索引来提升执行效率。

   这个索引可能不在`possible_keys`里。如果所有`possible_keys`索引都不适合查找行，但是其他有个索引包含了查询语句列举出的所有的列,就会发生这种情况，换句话说，这个索引覆盖了查询列。尽管它不能用来确定要检索哪些行，但扫描索引的效率比扫描数据行要高。

   在`InnoDB`引擎中，即使查询列包含主键，辅助索引也可能会覆盖所选列。因为`InnoDB`将主键值存储在每个辅助索引中。

   我们可以通过在查询语句中使用`FORCE INDEX`, `USE INDEX`,  `IGNORE INDEX` 来强制MySQL使用或忽略`possible_keys`列中列出的索引。

8. <span id="explain_key_len">key_len</span>

   本列显示了MySQL决定使用的索引的长度。本列的值能够使你确定MySQL实际使用了复合索引的多少部分。如果`key`列的值为null，那么`key_len`列的值也为null。

   由于索引存储格式，一个允许空值的列，索引长度要比非空列要长。

9. <span id="explain_rows">rows</span>

   本列表示MySQL认为执行查询语句时必须查找的行数。在InnoDB引擎中，这个数值是个预估值，可能会不精确。

10. <span id="explain_ref">ref</span> 

11. <span id="explain_filtered">filtered</span> 

12. <span id="explain_extra">Extra</span>  

   本列展示了MySQL解析查询时一些额外的信息。下面介绍下本列可能出现的值。

   * const row not found 
   * Deleting all rows
   * Distinct
   * FirstMatch
   * Full scan on NULL key
   * Impossible HAVING
   * Impossible WHERE
   * Impossible WHERE noticed after reading const tables
   * LooseScan
   * No matching min/max row
   * no matching row in const table
   * No matching rows after partition pruning 
   * No tables used
   * Not exists 
   * Plan isn't ready yet 
   * Range checked for each record (index map: N)
   * Recursive
   * Select tables optimized away
   * Start temporary, End temporary
   * Using filesort
   * Using index
   * Using index condition
   * Using index for group-by 
   * Using index for skip scan
   * Using join buffer
   * Using MRR
   * Using sort_union(...), Using union(...), Using intersect(...)
   * Using where
   * Using where with pushed condition
   * Zero limit 

