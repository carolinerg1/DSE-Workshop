#DSE Search Workshop

####Getting Started with DSE Search

-------------
##Overview
  
--------------

This document will guide you through how to get started using DSE Search. This includes:

1.	Creating a keyspace and table

2.	Inserting sample data

3.	Using “dsetool” to auto generate the config files

4.	Sample queries using CQL Solr query and its equivalent in Solr Admin UI

5.	How to apply changes to your Solr schema
   
   
  
Sample queries include:

•	Using AND/OR

•	How to escape special characters

•	Partial text search

•	Sorting

•	Date ranges

•	Facets

•	Facets on date range

•	Joining Solr cores

    
   
      



   
   The last steps will walk you through a scenario where you already have data in your table and only want to index certain fields:

1.	Use dsetool to auto generate the Solr config files without indexing the data

2.	Modify the schema.xml to only index certain fields

3.	Reload the schema.xml and index the data
   
     
   
  
---------------------
##Instructions
  
----------------------

####1.	Create keyspace: Datacenter name: Solr / RF=3

```
create keyspace demo with replication = {'class': 'NetworkTopologyStrategy', 'Solr': 3};
```


####2.	Create table “onlinetransactions”

```
Create table demo.onlinetransactions (
	unique_tran_id text,
	user_id text,
	account_type text,
	account_id text,
	create_ts timestamp,
	data text,
	primary key (unique_tran_id)
);
```


####3.	Insert 20 rows

Note: last row has 1) upper case user_id 2) space in “account_type”

```
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans1', 'user1', 'creditcard', '1234567890123451', '2015-01-01 09:00:00', '{amount:100.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans2', 'user1', 'creditcard', '1234567890123451', '2015-01-01 09:01:00', '{amount:199.99,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans3', 'user2', 'creditcard', '1234567890123452', '2015-01-01 09:01:00', '{amount:10.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans4', 'user3', 'creditcard', '1234567890123453', '2015-01-01 09:01:00', '{amount:110.50,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans5', 'user3', 'creditcard', '1234567890123453', '2015-01-01 10:00:00', '{amount:100.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans6', 'user4', 'creditcard', '1234567890123454', '2015-01-01 10:00:00', '{amount:100.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans7', 'user4', 'creditcard', '1234567890123454', '2015-01-01 10:01:00', '{amount:10.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans8', 'user4', 'creditcard', '1234567890123454', '2015-01-02 09:00:00', '{amount:40.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans9', 'user2', 'creditcard', '1234567890123422', '2015-01-02 09:00:00', '{amount:50.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans10', 'user3', 'creditcard', '1234567890123453', '2015-01-03 09:00:00', '{amount:100.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans11', 'user2', 'creditcard', '1234567890123451', '2015-01-03 09:00:00', '{amount:199.99,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans12', 'user5', 'creditcard', '1234567890123452', '2015-01-03 09:00:00', '{amount:100.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans13', 'user1', 'creditcard', '1234567890123453', '2015-01-03 10:00:00', '{amount:10.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans14', 'user2', 'creditcard', '1234567890123454', '2015-01-03 10:30:00', '{amount:40.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans15', 'user3', 'creditcard', '1234567890123455', '2015-01-04 09:00:00', '{amount:100.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans16', 'user3', 'creditcard', '1234567890123453', '2015-01-04 09:01:00', '{amount:50.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans17', 'user2', 'creditcard', '1234567890123422', '2015-02-01 09:00:00', '{amount:10.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans18', 'user1', 'creditcard', '1234567890123451', '2015-02-01 09:00:00', '{amount:1.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans19', 'user4', 'creditcard', '1234567890123454', '2015-02-01 09:00:00', '{amount:100.00,vendor:Amazon}');
insert into demo.onlinetransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans20', 'User1', 'credit card', '1234567890123451', '2015-02-01 10:00:00', '{amount:100.00,vendor:Amazon}');
```


####4.	Verify data was successfully inserted 

```
select * from demo.onlinetransactions;

```



####5.	Create schema and solrconfig

```
~# dsetool create_core demo.onlinetransactions generateResources=true reindex=true

```





###6.	View in SolrAdmin UI: (http://[IP Address]:8983/solr/#/demo.onlinetransactions)

![Solr Admin UI](https://github.com/carolinerg1/DSE-Workshop/blob/master/DSE%20Search/Solr%20Admin%20UI/solradmin%20ui%201.png)


###7.	Update schema: change TextField to String fields for columns searched on entire value

```
~# dsetool get_core_schema demo.onlinetransactions
```


####7.1.	Copy/Paste in vi and change type from “[TextField](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchSolrType.html)” to “[StrField](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchSolrType.html)” for “user_id” and “account_id”


```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<schema name="autoSolrSchema" version="1.5">
<types>
<fieldType class="org.apache.solr.schema.StrField" name="StrField"/>
<fieldType class="org.apache.solr.schema.TextField" name="TextField">
<analyzer>
<tokenizer class="solr.StandardTokenizerFactory"/>
<filter class="solr.LowerCaseFilterFactory"/>
</analyzer>
</fieldType>
<fieldType class="org.apache.solr.schema.TrieDateField" name="TrieDateField"/>
</types>
<fields>
<field indexed="true" multiValued="false" name="unique_tran_id" stored="true" type="StrField"/>
<field indexed="true" multiValued="false" name="user_id" stored="true" type="StrField"/>
<field indexed="true" multiValued="false" name="account_id" stored="true" type="StrField"/>
<field indexed="true" multiValued="false" name="create_ts" stored="true" type="TrieDateField"/>
<field indexed="true" multiValued="false" name="account_type" stored="true" type="TextField"/>
<field indexed="true" multiValued="false" name="data" stored="true" type="TextField"/>
</fields>
<uniqueKey>unique_tran_id</uniqueKey>
</schema>

```


####7.2.	Save it as schema_onlinetrans.xml

####7.3.	[Reload new schema](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchReldCore.html)


```
~# dsetool reload_core demo.onlinetransactions schema=schema_onlinetrans.xml reindex=true
```


####8.	Run queries via cqlsh

####8.1.	Get all trans for a user

```
select * from demo.onlinetransactions where solr_query = '{"q": "*:*", "fq": "user_id:user1"}';
```

Note: For exact value search, use “fq” (filter query), which leverages Solr filter cache, which is never invalidated so results can be cached, vs “q” (query filter), which uses query cache and is flushed with every soft commit


** This is the same as entering “user_id:user1” in “q” via Solr Admin UI **

![](https://github.com/carolinerg1/DSE-Workshop/blob/master/DSE%20Search/Solr%20Admin%20UI/solradmin%20ui%202.png)




####8.2.	Filter on 2 words 

Note: use “\” to [escape special characters](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchJSON.html) including:  +, -, &&, ||, !, (, ), ", ~, *, ?, and :
 


```
select * from demo.onlinetransactions where solr_query = '{"q": "*:*", "fq": "account_type:\"credit card\""}' limit 20;
```



####8.3.	All trans for multiple users

Note: OR / AND needs to be in capital letter

```
select * from demo.onlinetransactions where solr_query = '{"q": "*:*", "fq": "user_id:user1 OR user_id:user2"}';
```



####8.4.	Search for text within text


```
select * from demo.onlinetransactions where solr_query = '{"q": "data:*Amazon*"}' limit 20;
```



####8.5.	Sorting by user ID

```
select * from demo.onlinetransactions where solr_query = '{"q": "*:*", "sort":"user_id asc"}' limit 20;
```





####8.6.	Get all trans for a date/date range
  
```
select * from demo.onlinetransactions where solr_query = '{"q":"*:*", "fq":  "create_ts:[2015-01-01T00:00:00Z TO 2015-01-01T23:59:59Z] "}';
```





####8.7.	Get all trans for a user by date range

```
select * from demo.onlinetransactions where solr_query = '{"q":"*:*", "fq":"user_id:user1 AND create_ts:[2015-01-01T00:00:00Z TO 2015-01-01T23:59:59Z] "}';
```




####8.8.	Get only 1 column, data, for an account type

```
select data from demo.onlinetransactions where solr_query = '{"q":"*:*", "fq":"account_type:creditcard"}';
```









####8.9.	Get all trans based on account type & account id

```
select * from demo.onlinetransactions where solr_query = '{"q":"*:*", "fq":"account_type:creditcard AND account_id:1234567890123451"}';
```





####8.10.	Get all trans based on account type & account id for date

```
select * from demo.onlinetransactions where solr_query = '{"q":"*:*", "fq":"account_type:creditcard AND account_id:1234567890123451 AND create_ts:[2015-01-01T00:00:00Z TO 2015-01-01T23:59:59Z]"}';
```





####8.11.	Facet per month

Note: use fcs facet method for better facet performance if index is changing a lot (cache per segment)


```
select * from demo.onlinetransactions where  solr_query='{"q":"*:*","facet":{"range":"create_ts", "f.create_ts.range.start":"2015-01-01T00:00:00Z", "f.create_ts.range.end":"2015-03-01T00:00:00Z", "f.create_ts.range.gap":"+1MONTH", "method":"fcs"}}';
```









####9.	Create another table “mobiletransactions”



```
Create table demo.mobiletransactions (
	unique_tran_id text,
	user_id text,
	account_type text,
	account_id text,
	create_ts timestamp,
	data text,
	primary key (unique_tran_id)
);
```










####10.	Insert data in “mobiletransactions”


```
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans1', 'user1', 'apple', '1234567890123451', '2015-01-02 09:00:00', '{amount:100.00,vendor:AmazonMobile}');
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans2', 'user1', 'apple', '1234567890123451', '2015-01-02 09:01:00', '{amount:199.99,vendor:AmazonMobile}');
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans3', 'user2', 'apple', '1234567890123452', '2015-01-02 09:01:00', '{amount:10.00,vendor:AmazonMobile}');
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans4', 'user3', 'mobile', '1234567890123453', '2015-01-02 09:01:00', '{amount:110.50,vendor:AAmazonMobilemazon}');
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans5', 'user3', 'mobile', '1234567890123453', '2015-01-02 10:00:00', '{amount:100.00,vendor:AmazonMobile}');
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans6', 'user4', 'android', '1234567890123454', '2015-01-02 10:00:00', '{amount:100.00,vendor:AAmazonMobilemazon}');
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans7', 'user4', 'android', '1234567890123454', '2015-01-02 10:01:00', '{amount:10.00,vendor:AmazonMobile}');
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans8', 'user4', 'android', '1234567890123454', '2015-01-02 09:00:00', '{amount:40.00,vendor:Amazon}');
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans9', 'user2', 'mobile', '1234567890123422', '2015-01-02 09:00:00', '{amount:50.00,vendor:Amazon}');
insert into demo.mobiletransactions (unique_tran_id, user_id, account_type, account_id, create_ts, data) values ('trans10', 'user3', 'mobile', '1234567890123453', '2015-01-03 09:00:00', '{amount:100.00,vendor:Amazon}');
```



####11.	Make sure data is inserted


```
select * from demo.mobiletransactions;
```





####12.	Auto generate config files for mobiletransactions table 


```
~# dsetool create_core demo.mobiletransactions generateResources=true reindex=true
```



####13.	Join tables using Solr


####13.1.	Get all online trans for users who have "mobile" mobile trans



```
select * from demo.onlinetransactions where solr_query='{"q":"*:*", "fq":"{!join from=unique_tran_id to=unique_tran_id force=true fromIndex=demo.mobiletransactions}account_type:mobile"}';
```




13.2.	Get all online trans for users who have "mobile" mobile trans within a time frame 



```
select * from demo.onlinetransactions where solr_query='{"q":"*:*", "fq":"{!join from=unique_tran_id to=unique_tran_id force=true fromIndex=demo.mobiletransactions}account_type:mobile  AND create_ts:[2015-01-02T00:00:00Z TO 2015-01-02T23:59:59Z]"}';
```




####13.3.	Facet per account type




```
select * from demo.mobiletransactions where  solr_query='{"q":"*:*","facet":{"field":"account_type", "method":"fcs"}}';
```




---------------------
  
#Indexing Specific Fields
   
----------------------
  
####1.	Create a new table, which we won’t automatically index when creating the core so we can select which columns to index





```
create table demo.account (
	first_name text, 
	last_name text, 
	acct_num text,
	email text, 
	phone set<text>, 
	notes text, 
	primary key (email, acct_num)
);
```












####2.	Insert data
```
insert into demo.account (first_name, last_name, acct_num, email, phone) values ('Caroline', 'George', '1234567890123451', 'caroline.george@datastax.com', {'212-555-1212', '917-555-1212'});
insert into demo.account (first_name, last_name, acct_num, email, phone) values ('Travis', 'Price', '1234567890123452', 'travis.pricee@datastax.com', {'339-555-1212'});
insert into demo.account (first_name, last_name, acct_num, email, phone) values ('Eric', 'Bergman', '1234567890123453', 'eric.bergman@datastax.com', {'609-55501212', '609-666-1212', '609-777-1212'});
insert into demo.account (first_name, last_name, acct_num, email, phone) values ('Alex', 'Rivils', '1234567890123454', 'alex.rivilis@datastax.com', {'617-555-1212'});
insert into demo.account (first_name, last_name, acct_num, email, phone) values ('Alex', 'Rivils', '1234567890123455', 'alex.rivilis@datastax.com', {'617-555-1212'});
```


####3.	Create core but don’t index data

```
~# dsetool create_core demo.account generateResources=true reindex=false
```









Verify that the data hasn’t been indexed via Solr Admin

![](https://github.com/carolinerg1/DSE-Workshop/blob/master/DSE%20Search/Solr%20Admin%20UI/solradmin%20ui%203.png)


Data should not be returned when running a Solr query

![](https://github.com/carolinerg1/DSE-Workshop/blob/master/DSE%20Search/Solr%20Admin%20UI/solradmin%20ui%204.png)


####4.	Change the schema to not index First Name or Notes, reload it and index the data

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<schema name="autoSolrSchema" version="1.5">
<types>
<fieldType class="org.apache.solr.schema.TextField" name="TextField">
<analyzer>
<tokenizer class="solr.StandardTokenizerFactory"/>
<filter class="solr.LowerCaseFilterFactory"/>
</analyzer>
</fieldType>
<fieldType class="org.apache.solr.schema.StrField" name="StrField"/>
</types>
<fields>
<field indexed="false" multiValued="false" name="first_name" stored="true" type="TextField"/>
<field indexed="true" multiValued="false" name="acct_num" stored="true" type="StrField"/>
<field indexed="false" multiValued="false" name="notes" stored="true" type="TextField"/>
<field indexed="true" multiValued="true" name="phone" stored="true" type="TextField"/>
<field indexed="true" multiValued="false" name="email" stored="true" type="StrField"/>
<field indexed="true" multiValued="false" name="last_name" stored="true" type="TextField"/>
</fields>
<uniqueKey>(email,acct_num)</uniqueKey>
</schema>
```











 
Note: 
<[uniqueKey](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchConfSkema.html)> includes both the partition key and clustering column
	“phone” is multi valued since it’s a set














