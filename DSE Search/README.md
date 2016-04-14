# DSE Search Workshop

![icon](http://i.imgur.com/FoIOBlt.png)

In this session, you'll learn all about DataStax Enterprise Solr integration. Feel free to bookmark this page for future reference! 

----------

Overview
-------------


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

-------------


Instructions

-------------

1.	Create keyspace: Datacenter name: Solr / RF=3



2.	Create table “onlinetransactions”















3.	Insert 20 rows

Note: last row has 1) upper case user_id 2) space in “account_type”

















4.	Verify data was successfully inserted 





5.	Create schema and solrconfig






6.	View in SolrAdmin UI: (http://[IP Address]:8983/solr/#/demo.onlinetransactions)




7.	Update schema: change TextField to String fields for columns searched on entire value

7.1.	Copy/Paste in vi and change type from “[TextField](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchSolrType.html)” to “[StrField](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchSolrType.html)” for “user_id” and “account_id”





7.2.	Save it as schema_onlinetrans.xml

7.3.	[Reload new schema](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchReldCore.html)


8.	Run queries via cqlsh

8.1.	Get all trans for a user





Note: For exact value search, use “fq” (filter query), which leverages Solr filter cache, which is never invalidated so results can be cached, vs “q” (query filter), which uses query cache and is flushed with every soft commit


** This is the same as entering “user_id:user1” in “q” via Solr Admin UI **






8.2.	Filter on 2 words 

Note: use “\” to [escape special characters](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchJSON.html)  including:  +, -, &&, ||, !, (, ), ", ~, *, ?, and :







8.3.	All trans for multiple users

Note: OR / AND needs to be in capital letter





8.4.	Search for text within text






8.5.	Sorting by user ID







8.6.	Get all trans for a date/date range






8.7.	Get all trans for a user by date range




8.8.	Get only 1 column, data, for an account type










8.9.	Get all trans based on account type & account id






8.10.	Get all trans based on account type & account id for date





8.11.	Facet per month

Note: use fcs facet method for better facet performance if index is changing a lot (cache per segment)











9.	Create another table “mobiletransactions”













10.	Insert data in “mobiletransactions”


11.	Make sure data is inserted




12.	Auto generate config files for mobiletransactions table 


13.	Join tables using Solr


13.1.	Get all online trans for users who have "mobile" mobile trans





13.2.	Get all online trans for users who have "mobile" mobile trans within a time frame 





13.3.	Facet per account type






Indexing Specific Fields

1.	Create a new table, which we won’t automatically index when creating the core so we can select which columns to index














2.	Insert data


3.	Create core but don’t index data










Verify that the data hasn’t been indexed via Solr Admin




Data should not be returned when running a Solr query




4.	Change the schema to not index First Name or Notes, reload it and index the data














Note: 
<[uniqueKey](http://docs.datastax.com/en/datastax_enterprise/4.8/datastax_enterprise/srch/srchConfSkema.html)> includes both the partition key and clustering column
“phone” is multi valued since it’s a set

















