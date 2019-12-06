---
title: Implement Materialized views PostgreSQL + Rails
date: 2015-06-01 16:42:01 Z
layout: post
categories:
- programming
tags:
- rails
- ruby
- postgres
- tutorial
---

Today I would like to talk about Materialized views (henceforth referred as matviews) and how to add it to an Rails app.

### What is Matviews?

From wikipedia,

> A materialized view is a database object that contains the results of a query.

Basically, matviews are similar to database views except they are stored in disk and updated periodically. Matviews stores the result of an query into a table like structure of its own, from which the data can be queried on. We can't add or delete rows from it but rest it behaves as an actual table.

<!-- more -->

Also, when the data the matviews is created upon changes the matviews need to be refreshed before being available.

At the time of this writing, matviews is natively available in [Oracle DB](http://docs.oracle.com/cd/B10501_01/server.920/a96567/repmview.htm), [PostgreSQL](http://www.postgresql.org/docs/9.3/static/rules-materializedviews.html), [Sybase](http://infocenter.sybase.com/help/index.jsp?topic=/com.sybase.infocenter.dc00269.1571/doc/html/bde1279401694270.html), [IBM DB2](http://www.ibm.com/developerworks/data/library/techarticle/dm-0509melnyk/), [Microsoft SQL Server](https://msdn.microsoft.com/library/ms191432.aspx). MySQL doesn't provide native support for matviews unfortunately, but there are open source alternatives to it.

#### Matviews in PostgreSQL

Matviews was [introduced](http://wiki.postgresql.org/wiki/Materialized_Views) in postgres version 9.3, and in version 9.4 they introduced an option to refresh the data concurrently.

The syntax for creating a matview in postgres in uncannily similar to how you'll create a table.

`CREATE MATERIALIZED VIEW ex_matview AS SELECT col1, col2 FROM mytable WHERE col3 = condition;`

Enough intro, let's get started on how to add an matview to an existing rails app.

### Matviews in Rails
For our simpler example, let's take we have a table called `sales` that has the inventory of all the items sold on our website with 2M+ rows on it and in the following schema,

<table>
  <tr>
    <th>id<br></th>
    <th>item</th>
    <th>price</th>
    <th>sold_date</th>
    <th>order_code</th>
  </tr>
  <tr>
    <td>1</td>
    <td>Book<br></td>
    <td>23</td>
    <td>2015-06-01 17:06:29 UTC</td>
    <td>AVK21YO</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Guitar</td>
    <td>136<br></td>
    <td>2015-03-23 13:12:03 UTC</td>
    <td>BJ24GTS</td>
  </tr>
</table>


And, we are regularly performing a query for our admin site to show how much books accounted for sale every day. We can use the below query for it

`Sale.where(item: 'Book').group_by('DATE TRUNC('day', sold_date)').sum(:price)`

Now, this is an expensive query though you add indexes to it. It is a good candidate for a matview. Start by adding a migration.

`rails g migration AddMatViewsToSale`

and add below lines to your migration file,

<script src="https://gist.github.com/avinoth/a0b77f2970e230b5f7d4.js"></script>

now, `bin/rake db:migrate` and voila! the matviews are created. Now, it won't be available on schema file since it's not a table.

#### Creating a Model
One awesome thing by using matviews with Rails is we can create Models for it. Now, that the matviews are created we can create a model, `SalesMatview` in our case.

Set the table name as `sales_matview` using `self.table_name` and set the model to readonly by adding the below method to it,

`def readonly?
  true
end`

and a `self.refresh` method with below line in it.

`ActiveRecord::Base.connection.execute(‘REFRESH MATERIALIZED VIEW sales_matview’)`

We're all set to use the matview we've just created. You can query from it like how you'll do on a normal ActiveRecord query. Now, our previous query would become like

`SalesMatview.where(item: 'Book').select(:date, :amount)`

The queries are similar but you'll notice an big difference on how these two performs. Give it a try and see it for yourself.

And to refresh the matview, we can simply call the `refresh` method we created.

`SalesMatview.refresh`

We can add it to a rake task and schedule it to perform periodically. For the refreshes that are taking too much time there is an option in Postgres to perform them concurrently, just change the refresh code to

`REFRESH MATERIALIZED VIEW CONCURRENTLY sales_matview`

but keep in mind, a unique key will be required to perform concurrent refreshes.

Give it a try and let me know in comments what do you think of it. See you in next post.

Credits to:
http://www.postgresql.org/docs/9.4/static/rules-materializedviews.html
http://en.wikipedia.org/wiki/Materialized_view


