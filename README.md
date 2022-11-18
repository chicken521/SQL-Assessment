# SQL Challenge

The database contains two tables, store_revenue and marketing_data.  Refer to the two CSV
files, store_revenue and marketing_data to understand how these tables have been created.

store_revenue contains revenue by date, brand ID, and location:

 >  create table store_revenue (
 >     id int not null primary key auto_increment,
 >    date datetime,
 >    brand_id int,
 >    store_location varchar(250),
 >    revenue float  
 >  );

marketing_data contains ad impression and click data by date and location:

> create table marketing_data (
>  id int not null primary key auto_increment,
>  date datetime,
>  geo varchar(2),
>  impressions float,
>  clicks float
> );

### Please provide a SQL statement under each question.

* Question #0 (Already done for you as an example)
 Select the first 2 rows from the marketing data
​
>  select *
>  from marketing_data
> limit 2;
​
*  Question #1
 Generate a query to get the sum of the clicks of the marketing data
​
> SELECT sum(clicks)
> from marketing_data;

*  Question #2
 Generate a query to gather the sum of revenue by store_location from the store_revenue table
​
> Select sum(revenue), store_location
> from store_revenue
> group by store_location;

*  Question #3
 Merge these two datasets so we can see impressions, clicks, and revenue together by date
and geo.
 Please ensure all records from each table are accounted for.
​
> insert into marketing_data (date, geo)
> select date, replace(store_location, 'United States-TX','TX')
> from store_revenue
> where date='2016-01-06';
> insert into store_revenue (date, store_location)
> select date, geo
> from marketing_data
> where geo='MN';
> select marketing_data.impressions, marketing_data.clicks, sum(store_revenue.revenue) as revenue, marketing_data.date,  marketing_data.geo
> from store_revenue
> full join marketing_data on store_revenue.date=marketing_data.date and replace(store_revenue.store_location, 'United States-','')=marketing_data.geo
> group by marketing_data.date, store_revenue.date, marketing_data.geo, marketing_data.impressions, marketing_data.clicks
> order by marketing_data.geo, store_revenue.date;

* Question #4
 In your opinion, what is the most efficient store and why?

> The most efficient store is in California, because they earn approximately 4.5 times the amount of revenue as the next highest store (New York), and that's only with marginally higher ad impressions and clicks. Additionally, California as a whole makes up almost 80% of the total revenue across all store locations, and their revenue to clicks and impressions ratio (10.28) is about 4 times as high as the next highest store (New York, 2.56).

* Question #5 (Challenge)
 Generate a query to rank in order the top 10 revenue producing states

> select revenue, store_location,
> rank() over(order by revenue desc) rank
> from store_revenue
> where store_location!='MN'
> order by rank
> limit 10;
