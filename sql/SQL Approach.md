# SQL Processing Approach in Snowflake

## group by senders and receivers to analyze the data 
''''
select namedest, sum(amount) sum_amount, count(*) txn_count 
	from payments group by namedest 
    order by txn_count;
    
select nameorig, sum(amount) sum_amount, count(*) txn_count 
	from payments group by nameorig 
    order by txn_count;
''''

##  largest degree between participants and 
select namedest, sum(amount) sum_amount, count(*) txn_count 
	from payments group by namedest 
    order by txn_count desc limit 5;


-- largest amounts of transactions performed between connected participants
select namedest, sum(amount) sum_amount, count(*) txn_count 
	from payments group by namedest 
    order by sum_amount desc limit 5;

-- top 10 who serve as cash out hubs
select namedest, sum(amount) amount, count(*) from payments where type = 'CASH_OUT' 
	group by namedest
    order by amount desc
	limit 10


-- Find rings of transactions
--- create table dest_groups and orig_groups
create table dest_groups as (
	select namedest, sum(amount) sum_amount, count(*) txn_count 
		from payments group by namedest
    );
create table orig_groups as (
	select nameorig, sum(amount) sum_amount, count(*) txn_count 
		from payments group by nameorig
    );

select g.namedest participant, o.sum_amount amt_sent, o.txn_count txn_sent, g.sum_amount amt_received, g.txn_count txn_received, ((amt_received / amt_sent)*100) percentage
from dest_groups g, orig_groups o where g.namedest = o.nameorig and percentage < 100 and percentage > 80
order by amt_sent desc
limit 10


