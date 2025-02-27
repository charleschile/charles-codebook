we, you know the whole idea right? You will be having some documents in S. 3. And then you have to create embedding. And then, yeah, you have to chunk the data, and then you have to create embedding on those channels and then store it in index table. And then like that table would be useful. Very.



chunk 


---



create hidden sql table in the back end


So that's that's what I am saying. So right now we don't have the so, for we can allow users to upload the document. But in order for us to specify the document. URL, right?


sql包->sql engine-> logical plan

compile -> physical plan



database -> query plan > logical plan -> physical plan -> execution plan


build.go:
 package which contains all the keywords like insert select Update delete and all those things. So if you look at this, these are actually parser keywords.

`buildCreateIndex`
build index will create index comprises of unique Index Regular Secondary Index vector Index and Master Index logics.

build unique indexes
```go
if uIdx != nil {
		if err := buildUniqueIndexTable(indexInfo, []*tree.UniqueIndex{uIdx}, colMap, oriPriKeyName, ctx); err != nil {
			return nil, err
		}
		createIndex.TableExist = true
	}
```


build secondary index:
```go
if sIdx != nil {
		if err := buildSecondaryIndexDef(indexInfo, []*tree.Index{sIdx}, colMap, oriPriKeyName, ctx); err != nil {
			return nil, err
		}
		createIndex.TableExist = true
	}
```

secondary index comprises of regular secondary index, vector index, master index

---





llm也是secondary index
So all the new new indexes also will be part of secondary index, which is the Llm index that you're working on.

 I'm also working on a separate index called the Full Text Index, which I'll be adding to this thing as well.


`buildSecondaryIndexDef`:you have the regular secondary index which you will be building index dev.
And then this is the part where you will be building. Ivf flat index which is the vector, index.


---

`buildIvfFlatSecondaryIndexDef`

so in the case of Ivf or vector index, we have to create 3 tables 3 hidden tables which will be part of the index. So I am creating details for meta table centroid and entries table. 

```
在向量索引（Vector Index）中，为了实现高效的向量相似性搜索，通常需要创建三个隐藏表，这些表在索引的内部结构中起着关键作用。具体来说，这些表包括：

Meta Table (元数据表)：

功能：存储索引的元数据，如索引的配置信息、参数和状态等。
内容：可能包括索引创建时间、参数设置（如向量维度、分区数量）、索引的版本信息和其他与索引相关的元数据。
Centroid Table (质心表)：

功能：存储向量分区的质心（centroids）信息。质心是向量聚类算法（如K-means算法）生成的代表各个簇中心的向量。
内容：每个质心对应一个簇，表中记录了这些质心向量及其相关信息，如质心的ID、向量值和可能的其他统计数据。
Entries Table (条目表)：

功能：存储实际的数据向量及其与质心的关系。每个数据向量会被分配到离它最近的质心，从而形成簇。
内容：包括每个向量的ID、向量值以及它所属的质心ID。这使得在查询时可以快速找到与查询向量最相似的簇，从而加速相似性搜索。
通过这三个表的配合，向量索引能够实现以下功能：

快速聚类：质心表（Centroid Table）允许快速确定查询向量应归属的簇，从而减少需要比较的向量数量。
高效搜索：条目表（Entries Table）保存了实际的数据向量，查询时只需要比较特定簇中的向量，提升了搜索效率。
元数据管理：元数据表（Meta Table）提供了索引的配置信息和状态管理，方便对索引进行维护和优化。
通过这种结构，向量索引能够在处理大规模数据集时保持高效的相似性搜索性能。
```





The overall idea is that you'll be defining the table schema. And this thing. So for our case the table, the index hidden table would look something like this.

```go
		// 1.d PK def
		tableDefs[0].Pkey = &PrimaryKeyDef{
			Names:       []string{catalog.SystemSI_IVFFLAT_TblCol_Metadata_key},
			PkeyColName: catalog.SystemSI_IVFFLAT_TblCol_Metadata_key,
		}
```

original_table_primary key
chunk
embedding



So the corresponding embedding for the chat. So you will be doing schema definition something like this. 

This will be probably just one table for the Llm index rather than 3 tables for vector, index. So yeah, you'll be just needing to create one table.




---
重要
meta主要是对于vector来说的，我们不需要创建meta数据这个表格
对于vector来说，metadata table is basically storing information related to what is the current version of centroid that we need to use


对于`buildIvfFlatSecondaryIndexDef`

this is the part where we define the stable schema this is the index definition for the Iv flat secondary index. You will need to create a similar function which creates the index definition.


complie中compile.go中有createindex
```go
	case CreateIndex:
		return s.CreateIndex(c)
```

点进去
```go
if indexDef.Unique {
			// 1. Unique Index related logic
			err = s.handleUniqueIndexTable(c, indexDef, qry.Database, originalTableDef, indexInfo)
		} else if !indexDef.Unique && catalog.IsRegularIndexAlgo(indexAlgo) {
			// 2. Regular Secondary index
			err = s.handleRegularSecondaryIndexTable(c, indexDef, qry.Database, originalTableDef, indexInfo)
		} else if !indexDef.Unique && catalog.IsMasterIndexAlgo(indexAlgo) {
			// 3. Master index
			err = s.handleMasterIndexTable(c, indexDef, qry.Database, originalTableDef, indexInfo)
		} else if !indexDef.Unique && catalog.IsIvfIndexAlgo(indexAlgo) {
			// 4. IVF indexDefs are aggregated and handled later
			if _, ok := multiTableIndexes[indexDef.IndexName]; !ok {
				multiTableIndexes[indexDef.IndexName] = &MultiTableIndex{
					IndexAlgo: catalog.ToLower(indexDef.IndexAlgo),
					IndexDefs: make(map[string]*plan.IndexDef),
				}
			}
			multiTableIndexes[indexDef.IndexName].IndexDefs[catalog.ToLower(indexDef.IndexAlgoTableType)] = indexDef
		}
```

以regular为例，点进去她有一个table是b tree
而我们是llm


we had to create a map and then handle it separately in that particular function.



`handleVectorIvfFlatIndex`


`genCreateIndexTableSqlForIvfIndex`



So the population logic is different for your scenario in the in your scenario. You'll be reading  the column, then, doing the chunking, then doing the embedding, and then finally writing it as insert statements.
`handleIvfIndexMetaTable`



But master index seems good start for them as well.



使用之前需要先make啦


其中一个启动mo:
```shell
./mo-service -debug-http :9876 -launch ./etc/launch/launch.toml >out.log 2>err.log
```



![[截屏2024-07-10 16.59.07.png]]


在开一个terminal启动
```shell
mysql -h 127.0.0.1 -P 6001 -udump -p111
mysql -h 127.0.0.1 -P 6002 -u root -p123

```


```sql
create database if not exists a;

use a;

CREATE TABLE `t5` (
    `a` INT NOT NULL,
    `b` VECF32(960) DEFAULT NULL,
    PRIMARY KEY (`a`),
    KEY `idx5` USING ivfflat (`b`) lists = 500 op_type 'vector_l2_ops'
);

CREATE TABLE `t7` (
    `a` INT NOT NULL,
    `b` VECF32(3) DEFAULT NULL,
    PRIMARY KEY (`a`),
    KEY `idx7` USING ivfflat (`b`) lists = 2 op_type 'vector_l2_ops'
);

insert into `t7` (`a`,`b`) values(1, '[1.1, 1.2, 3.1]');


show create table t5;

select * from mo_catalog.mo_indexes where name="idx5";

 select * from  `__mo_index_secondary_01909be9-f06b-758d-b7a4-7ed6e82eefeb`;
 
mysql> select * from `__mo_index_secondary_01909be9-f06b-75cc-b02f-9247a2ce34a0` limit 1;


```


```
we have created the table with 2 columns, A and B
and then we have primary key on a and then secondary key on b
the secondary key name is idx5


you will see that there are 3 records in the
mo indexes of mo catalog database. So mo indexes is the table name, and then mo catalog is the database name 


mo catalog is an internal database as in it's specific to metrics, origin and such. Okay, if you look at MYSQL, there is information, schema database which stores all the necessary metadata for the particular MYSQL tables right?
  
```


```
创建表 t5：这条语句创建一个名为 t5 的表。

定义列 a：

a 是一个整数类型 (INT)。
该列不能为空 (NOT NULL)。
该列是主键 (PRIMARY KEY)，意味着每行在这列上的值必须唯一且不能为空。
定义列 b：

b 是一个自定义类型 VECF32，长度为960。这通常用于表示一个包含960个浮点数的向量。
该列可以为空 (DEFAULT NULL)，即插入记录时可以不为此列赋值。
定义索引 idx5：

为列 b 创建了一个名为 idx5 的索引。
使用 ivfflat 索引类型，这是一个常用于向量数据的索引类型。
参数 lists = 500 表示创建索引时使用500个列表。
参数 op_type = 'vector_l2_ops' 指定了向量操作类型为L2距离计算，即欧氏距离。
这个表 t5 主要用于存储带有整数主键 a 和浮点数向量 b 的数据，并且为向量列 b 创建了一个高效的向量检索索引。这在机器学习和数据科学中非常常见，尤其是用于快速查找和检索相似向量的应用场景。
```



---
 attach debugger to process

使用ide可以在`run`里面选择`attach to process`



When I started off with vector index, I had, I had to go through similar process, where, like, I have to add to kind of see other Pr request which was for secondary index or unique index, and then,
make similar changes and then see my change. My code was entering that path, and then


incorporate all the other functions. Like, basically, we have to support insert function update function, delete function.


Yeah, we have to add a new function, and then support querying from that particular thing.



---
creating the particular table. Right? Once you create the table, make sure to create the vector, index on that table.
So here, when you create a particular hidden table which is original table and embedding right?
you have to create an vector. Index on this embedding column so that it can be easily queried, or something of that sort. So


---



And the output can be Json.


So once you pass the model name as an argument to the create index. We will be using the model name, and then basically mapping corresponding dimension length. And that will be used for creating the underlying tables. 



now coming to the index hidden table part. So there is original table primary key, which is the primary key of the original table.

So it is kind of used as a mapping.
We might need to modify this table structure. Think of any better structure like, think of primary keys to use for this table structure. Think of secondary key would be definitely embedding. So there is definitely no doubt in that.



lvf flat index is basically a centroid based index It creates 100 centroids. And then maps. All the entries. Based on L, 2 distance to the centroid. So that is how current vector, index is supported in metrics. Origin.


So this would be the syntax. Okay, create Llm index. Idx on table text

But the role idea is that you create an Llm. Index and then pass 3 parameters, list 100 opt type L. 2. And then embedding model as an argument, and then that should create this particular table and then create an index on that.




create index would have a dependency on this chunk and embedding. But you can start working on it and then create the background table and all those things right that you that I just mentioned make sure that need not be the embedding and all those things. But yeah, at least you have something working. And once the once they have the functions ready, you can start working on integrating them.




---


### 1. 测试index
### 2. 关于extract local path
### 3. chunk指的是character
### 4. all the functions we created right now should support datalink as the input



测试index：
![[Pasted image 20240806003648.png]]







一开始讲的东西，但是后来否认了：
```sql


create table t3(a int, b datalink);

drop t3;


create table t3(id int, file_url datalink, text_url datalink);

---If this thing doesn't work, you'll need to create an extra table in the secondary index. Just to show this particular
insert into t3 values(1, "file:///user/a.pdf", extract_text("file:///user/a.txt"));

---  the basic idea is that in in the 1st column we'll be storing a dot. Pdf, in the second column will be storing its corresponding text






```







```sql


create table t3(id int, file_url datalink);


create table __mo_index_secondary_index(id int, text_url datalink);

create table __mo_index_secondary_index2(id int, chunk datalink, embedding vcf32(128));

---the basic idea is that when you insert a value into this T. 3 table.
--- you should be able to insert an entry into the mo index table, one with the details such as

 
```






![[Pasted image 20240806014415.png]]


```sql
insert into t3 values(1，"file:///a/b/c.pdf");

insert into __mo_index_secondary_index values(1, "file:///a/b/c.filter.txt");

insert into __mo_index_secondary_index2(1, "file:///a/b/c.filter.txt?offset=0&size=100", NULL);

insert into __mo_index_secondary_index2(1, "file:///a/b/c.filter.txt?offset=100&size=200", NULL);


```


NULL里面insert得失corresponding vectors for that particular chunk




### ivfflat index

```sql
show databases;

create database if not exists indextest;

use indextest;

CREATE TABLE `t5` (
    `a` INT,
    `b` VARCHAR(255)
);

INSERT INTO `t5` (`a`, `b`) VALUES (1, '数据1'), (2, '数据2'), (3, '数据3');

desc t5;

create index idx2 on t5(a);

select * from mo_catalog.mo_indexes where name="idx2";

select * from `__mo_index_secondary_0191236d-4ae9-7d00-9bf9-e4e86f602a36`;

select * from `__mo_index_secondary_0191236d-4ae9-7d00-9bf9-e4e86f602a36`;


```




```sql
SHOW VARIABLES LIKE 'experimental_ivf_index';

SET experimental_ivf_index = 1;


drop table if exists tbl;

create table tbl(id int primary key, embedding vecf32(3));

insert into tbl values(1, "[1,2,3]");
insert into tbl values(2, "[1,2,4]");
insert into tbl values(3, "[1,2.4,4]");
insert into tbl values(4, "[1,2,5]");
insert into tbl values(5, "[1,3,5]");
insert into tbl values(6, "[100,44,50]");
insert into tbl values(7, "[120,50,70]");
insert into tbl values(8, "[130,40,90]");


create index idx1 using IVFFLAT on tbl(embedding) lists = 2 op_type 'vector_l2_ops';


show index from tbl;


show create table tbl;

select * from mo_catalog.mo_indexes where name="idx1";

select name, type, column_name, algo, algo_table_type,algo_params from mo_catalog.mo_indexes where name="idx1";

select * from `__mo_index_secondary_01914554-47ef-792b-949e-bd1f777048dd`;
```


```sql
mysql> select name, type, column_name, algo, algo_table_type,algo_params from mo_catalog.mo_indexes where name="idx1";
+------+----------+-------------+---------+-----------------+-----------------------------------------+
| name | type     | column_name | algo    | algo_table_type | algo_params                             |
+------+----------+-------------+---------+-----------------+-----------------------------------------+
| idx1 | MULTIPLE | embedding   | ivfflat | metadata        | {"lists":"2","op_type":"vector_l2_ops"} |
| idx1 | MULTIPLE | embedding   | ivfflat | centroids       | {"lists":"2","op_type":"vector_l2_ops"} |
| idx1 | MULTIPLE | embedding   | ivfflat | entries         | {"lists":"2","op_type":"vector_l2_ops"} |
+------+----------+-------------+---------+-----------------+-----------------------------------------+
3 rows in set (0.00 sec)

```


![[Pasted image 20240812145729.png]]

```sql
mysql> select * from `__mo_index_secondary_01914554-47ef-792b-949e-bd1f777048dd`;
+------------------+----------------------------+
| __mo_index_key   | __mo_index_val             |
+------------------+----------------------------+
| clustering_end   | 2024-08-12 14:43:57.069835 |
| clustering_start | 2024-08-12 14:43:57.064098 |
| mapping_end      | 2024-08-12 14:43:57.073997 |
| mapping_start    | 2024-08-12 14:43:57.070378 |
| pruning_end      | 2024-08-12 14:43:57.075660 |
| pruning_start    | 2024-08-12 14:43:57.074459 |
| version          | 0                          |
+------------------+----------------------------+
7 rows in set (0.00 sec)

mysql> select * from `__mo_index_secondary_01914554-47ef-7947-ac48-68e1f06e471f`;
+-----------------------------+------------------------+---------------------+
| __mo_index_centroid_version | __mo_index_centroid_id | __mo_index_centroid |
+-----------------------------+------------------------+---------------------+
|                           0 |                      1 | [1, 2, 3]           |
|                           0 |                      2 | [1, 2, 4]           |
+-----------------------------+------------------------+---------------------+
2 rows in set (0.01 sec)

mysql> select * from `__mo_index_secondary_01914554-47ef-7952-9aa9-c8587092d06d`;
+--------------------------------+---------------------------+--------------------+------------------------------+
| __mo_index_centroid_fk_version | __mo_index_centroid_fk_id | __mo_index_pri_col | __mo_index_centroid_fk_entry |
+--------------------------------+---------------------------+--------------------+------------------------------+
|                              0 |                         1 |                  1 | [1, 2, 3]                    |
|                              0 |                         2 |                  2 | [1, 2, 4]                    |
+--------------------------------+---------------------------+--------------------+------------------------------+
2 rows in set (0.00 sec)

```

![[Pasted image 20240812150848.png]]

### llm index测试

```sql
SHOW VARIABLES LIKE 'experimental_llm_index';

SET experimental_llm_index = 1;

SHOW VARIABLES LIKE 'ollama_server_proxy';


```










he basic idea is that when we insert a Pdf, we should be able to extract the text and then use that as a
use that as the following data link for all the computations. So yeah, extracting a text from the Pdf should be a function, and then that should be able to write to a particular location.


I think probably you can look at the Ap's which are right now that, I believe read Apa should be like there is a file service Ap. I think you should be able to write with that as well.








So it's going to be yeah, you can. Let's keep it that way. So yeah, we'll have source and destination as an argument, and then source will be the original file and destination should be. Let's keep the destination as the source file with this particular prefix, or suffix called c dot like. Let's say, dot filter dot text or something.




![[Pasted image 20240806015520.png]]




PDF 200MB in size, 如果用string存储的话，会使用大量的空间




ask的问题：
We want to expose that as an Ap. Okay, we'd want to expose that as a rest endpoint or something.

And in that case, you will be creating a micro service which particularly runs the Olama by binary. And then it will accept Get put request.

service URL is going to be the URL for that particular micro service



open api为什么不用：pay ｜ local deployment


现在的llm index暂时先返回两个空的（blank tables）



![[Pasted image 20240806021034.png]]






### 使用 datalink



这段描述的翻译和解释如下：

翻译：

ParseDatalink 从一个 Datalink 字符串中提取数据，并返回 MO FS URL、[]int{offset, size}、文件类型和错误信息。

MO FS URL：用于 MO FS 访问文件的 URL。
offsetSize：要读取的文件的偏移量和大小。
file type：文件类型，可以是 ".txt" 或 ".csv"。
解释：

ParseDatalink 是一个函数或方法，它的功能是从一个 Datalink 字符串中提取相关信息。Datalink 字符串可能包含关于如何访问某个文件的详细信息，例如文件的路径（URL）、需要读取的文件部分的偏移量和大小，以及文件的类型（例如文本文件 .txt 或逗号分隔值文件 .csv）。该函数返回四个值：

MO FS URL：这是 MO FS（可能是一个文件系统或存储服务）用来访问文件的 URL。
offsetSize：这是一个包含两个整数的数组，分别表示文件读取的起始位置（偏移量）和要读取的数据量（大小）。
fileType：文件的类型，通常为 ".txt" 或 ".csv"。
error：如果在解析过程中出现错误，该函数会返回一个错误信息，否则这个值为 nil。
这个函数可以用于解析和处理与文件存取相关的 Datalink 字符串，帮助系统在指定的文件位置读取数据。









```sql
select extract_text("/Users/charles/Desktop/codes/testData/example.pdf", "/Users/charles/Desktop/codes/testData/example.txt");
```


```sql
select load_file(cast('file://$resources/file_test/normal.txt?offset=0&size=3' as datalink));
```




embedding + datalink测试

```sql

select load_file(cast('/Users/charles/Desktop/codes/testData/embeddingTest.txt' as datalink));

select load_file(cast('file://Users/charles/Desktop/codes/testData/embeddingTest.txt' as datalink));

SELECT embedding(cast('file:///Users/charles/Desktop/codes/testData/embeddingTest.txt' as datalink));


```


```go
indexParts := make([]string, 2)

	// 0. validate indexInfo and colMap
	{
		// only support 1 column index
		if len(indexInfo.KeyParts) != 1 {
			return nil, nil, moerr.NewNotSupported(ctx.GetContext(), "don't support multi column LLM index")
		}

		colName := indexInfo.KeyParts[0].ColName.ColName()
		if _, ok := colMap[colName]; !ok {
			return nil, nil, moerr.NewInvalidInput(ctx.GetContext(), "column '%s' is not exist", indexInfo.KeyParts[0].ColName.ColNameOrigin())
		}
	}

	// init index and table definition slices
	indexDefs := make([]*plan.IndexDef, 1)
	tableDefs := make([]*TableDef, 1)

	// 1. create LLM hidden table
	{
		// 1.a table consists of 3 columns: original primary key, chunk, embedding
		indexTableName, err := util.BuildIndexTableName(ctx.GetContext(), false)
		if err != nil {
			return nil, nil, err
		}

		tableDefs[0] = &TableDef{
			Name:      indexTableName,
			TableType: catalog.SystemSI_LLM_Table_Type,
			Cols:      make([]*ColDef, 3),
		}

		// 1.b indexDef0 init
		// Use primary key and chunk as two keys for index
		// primary key ensures each chunked string can be associated back to the original table's data
		// chunks facilitates querying of text fragments
		// embedding are high-dimensional floating-point arrays, not suitable for direct key-value query

		indexParts[0] = catalog.LLM_Index_Table_Primary_ColName
		indexParts[1] = catalog.LLM_Index_Table_Chunk_ColName

		indexDefs[0], err = CreateIndexDef(indexInfo, indexTableName, catalog.SystemSI_LLM_Table_Type, indexParts, false)
		if err != nil {
			return nil, nil, err
		}

		// 1.c columns: primary key, chunk, embedding
		tableDefs[0].Cols[0] = &ColDef{
			Name: catalog.LLM_Index_Table_Primary_ColName,
			Alg:  plan.CompressType_Lz4,
			Typ: Type{
				// TODO the types of primary key column?
				Id:    int32(types.T_varchar),
				Width: types.MaxVarcharLen,
			},
			Primary: true,
			Default: &plan.Default{
				NullAbility:  false,
				Expr:         nil,
				OriginString: "",
			},
		}

		tableDefs[0].Cols[1] = &ColDef{
			Name: catalog.LLM_Index_Table_Chunk_ColName,
			Alg:  plan.CompressType_Lz4,
			Typ: Type{
				Id:    int32(types.T_varchar),
				Width: types.MaxVarcharLen,
			},
			Default: &plan.Default{
				NullAbility:  false,
				Expr:         nil,
				OriginString: "",
			},
		}

		tableDefs[0].Cols[2] = &ColDef{
			Name: catalog.LLM_Index_Table_Embedding_ColName,
			Alg:  plan.CompressType_Lz4,
			Typ: Type{
				Id: int32(types.T_array_float32),
				// Width: types.MaxVarcharLen,
			},
			Default: &plan.Default{
				NullAbility:  false,
				Expr:         nil,
				OriginString: "",
			},
		}

		// 1.d set primary key definition
		tableDefs[0].Pkey = &PrimaryKeyDef{
			Names:       []string{catalog.LLM_Index_Table_Primary_ColName},
			PkeyColName: catalog.LLM_Index_Table_Primary_ColName,
		}
	}

	return indexDefs, tableDefs, nil
```



### datalink  LLM index测试数据


```sql
show databases;

use indextest;

show tables;


create table t1(a int primary key, b datalink);


insert into t1 values(1, 'file://$resources/file_test/normal.txt?offset=0&size=3');

insert into t1 values(3, 'file://$resources/file_test/normal.txt');

-- 4. insert with size alone
insert into t1 values(6, 'file://$resources/file_test/normal.txt?size=3');

-- 5. insert with offset alone
insert into t1 values(4, 'file://$resources/file_test/normal.txt?offset=0');

select * from t1;

create index idx1 using LLM on t1(b);

create index idx1 using LLM on t1(b) lists = 2 op_type 'vector_l2_ops';


create index idx1 using IVFFLAT on tbl(embedding) lists = 2 op_type 'vector_l2_ops';


select * from mo_catalog.mo_indexes where name="idx1";


show create table t1;

```


### datalink  LLM index测试数据2

```sql
show databases;

use indextest1;

show tables;


create table t1(a int primary key, b datalink);




insert into t1 values(1, 'file://$resources/file_test/normal.txt?offset=0&size=3');

insert into t1 values(3, 'file://$resources/file_test/normal.txt');

-- 4. insert with size alone
insert into t1 values(6, 'file://$resources/file_test/normal.txt?size=3');

-- 5. insert with offset alone
insert into t1 values(4, 'file://$resources/file_test/normal.txt?offset=0');

select * from t1;

create index idx1 using LLM on t1(b);

create index idx1 using LLM on t1(b) lists = 2 op_type 'vector_l2_ops';


create index idx1 using IVFFLAT on tbl(embedding) lists = 2 op_type 'vector_l2_ops';


select * from mo_catalog.mo_indexes where name="idx1";


show create table t1;

```




![[Pasted image 20240812161721.png]]


![[Pasted image 20240812161735.png]]






```sql
SHOW VARIABLES LIKE 'ollama_server_proxy';

SHOW VARIABLES LIKE 'ollama_model';

```


progress:

embedding : 1. support datalink and string as input

```sql
SELECT embedding("This is text... bla ");

SELECT embedding(cast('file:///Users/charles/Desktop/codes/testData/embeddingTest.txt' as datalink));
```



extract_text function: datalink does not support pdf 
ParseDatalink in datalink.go only supports txt or csv file


llm index hidden tables

```sql
SHOW VARIABLES LIKE 'experimental_llm_index';

SET experimental_llm_index = 1;

show databases;

create database if not exists indextest3;

use indextest3;

show tables;


create table t6(a int primary key, b datalink);


insert into t6 values(1, 'file://$resources/file_test/normal.txt');


insert into t6 values(3, 'file://$resources/file_test/normal.txt');

-- 4. insert with size alone
insert into t6 values(6, 'file://$resources/file_test/normal.txt?size=3');

-- 5. insert with offset alone
insert into t6 values(4, 'file://$resources/file_test/normal.txt?offset=0');

select * from t6;

create index idx10 using LLM on t6(b);


select * from mo_catalog.mo_indexes where name="idx9";


show create table t6;



---------------------------------------



drop table if exists tbl;

create table tbl(id int primary key, embedding vecf32(3));

insert into tbl values(1, "[1,2,3]");
insert into tbl values(2, "[1,2,4]");
insert into tbl values(3, "[1,2.4,4]");
insert into tbl values(4, "[1,2,5]");
insert into tbl values(5, "[1,3,5]");
insert into tbl values(6, "[100,44,50]");
insert into tbl values(7, "[120,50,70]");
insert into tbl values(8, "[130,40,90]");


create index idx1 using IVFFLAT on tbl(embedding) lists = 2 op_type 'vector_l2_ops';


show index from tbl;


show create table tbl;

select * from mo_catalog.mo_indexes where name="idx1";

select name, type, column_name, algo, algo_table_type,algo_params from mo_catalog.mo_indexes where name="idx1";

select * from `__mo_index_secondary_01914554-47ef-792b-949e-bd1f777048dd`;
```



1. embedding: offset and size message
2. mo_catalog compile 




handleVectorIvfFlatIndex的前面是ddl.go的1541行

```sql

UPDATE t6
SET b = '新的b值'
WHERE a = 1;

UPDATE t6 SET b = 'file:///Users/charles/Desktop/codes/testData/embeddingTest.txt' WHERE a = 3;



SELECT embedding(cast('file:///Users/charles/Desktop/codes/testData/embeddingTest.txt' as datalink));


SELECT embedding(cast('file:///Users/charles/Desktop/codes/testData/embeddingTest.txt' as datalink));

--------
| [-0.021955958, -0.024112409, 0.010994452, -0.011615187, 0.03107838, -0.024938945, -0.029811664, -0.018000955, -0.01751671, -0.0007840055, -0.021467123, -0.014996716, -0.0045815744, -0.026652282, -0.016832603, 0.009745739, -0.016642446, 0.00514828, -0.015859831, -0.010222016, -0.010362088, 0.0056358385, -0.015048031, -0.0031982209, -0.010094931, 0.016682236, 0.008610655, -0.010177104, 0.0035889482, -0.011772896, 0.002079




-------


SELECT embedding(cast('file:///Users/charles/Desktop/codes/testData/embeddingTest.txt?offset=0&size=3' as datalink));


   ----

    [-0.016928744, -0.017229967, -0.009889638, -0.012103492, -0.011038898, -0.0005

	 ----

select extract_text("/Users/charles/Desktop/codes/testData/example1.pdf", "/Users/charles/Desktop/codes/testData/example.txt");
select extract_text("file:///Users/charles/Desktop/codes/testData/example.pdf", "/Users/charles/Desktop/codes/testData/example.txt");

select extract_text("file:///Users/charles/Desktop/codes/testData/example.pdf?offset=0&size=3", "/Users/charles/Desktop/codes/testData/example.txt");

select extract_text("file:///Users/charles/Desktop/codes/testData/example.pdf?offset=0&size=4", "file:///Users/charles/Desktop/codes/testData/example.txt");


```




s3

embedding model ?

do not chunk 
vector index
end to end 



thrilled

ask function and stage url

1. ask function
2. end to end
3. stage url
4. end to end demo 




### 9.1开发


SELECT llm_embedding("This is text... bla ");


SELECT llm_embedding(cast('file:///Users/charles/Desktop/codes/testData/embeddingTest.txt' as datalink));


SELECT LOAD_FILE(cast('file:///Users/charles/Desktop/codes/testData/embeddingTest.txt' as datalink));