# SQL Processing Approach in Snowflake

## group by senders and receivers to analyze the data 

	select namedest, sum(amount) sum_amount, count(*) txn_count 
		from payments group by namedest 
	    order by txn_count;
	    
	select nameorig, sum(amount) sum_amount, count(*) txn_count 
		from payments group by nameorig 
	    order by txn_count;
	

##  largest degree between participants and 
	select namedest, sum(amount) sum_amount, count(*) txn_count 
		from payments group by namedest 
	    order by txn_count desc limit 5;

### results
	NAMEDEST	SUM_AMOUNT		TXN_COUNT
	C1286084959	77,428,943.31		113
	C985934102	42,422,887.98		109
	C665576141	88,749,384.38		105
	C2083562754	53,073,938.76		102
	C1590550415	43,206,101.59		101



## largest amounts of transactions performed between connected participants
	select namedest, sum(amount) sum_amount, count(*) txn_count 
		from payments group by namedest 
	    order by sum_amount desc limit 5;
	
### results
	NAMEDEST	SUM_AMOUNT		TXN_COUNT
	C439737079	357,440,831.44		18
	C707403537	299,374,418.42		17
	C167875008	274,736,432.8		28
	C20253152	270,116,188.69		20
	C172409641	255,310,174.25		57


## top 10 who serve as cash out hubs
	select namedest, sum(amount) amount, count(*) from payments where type = 'CASH_OUT' 
		group by namedest
	    order by amount desc
		limit 10
### results
	NAMEDEST	AMOUNT	COUNT(*)
	C766681183	16,131,184.18	31
	C2059078386	15,219,699.7	27
	C150091086	14,654,741.55	22
	C1049795740	14,194,980.54	27
	C141316671	13,994,935.37	21
	C242619213	13,783,198.83	20
	C779798347	13,332,713.57	17
	C308220962	13,281,158.65	14
	C1990623385	13,238,327.84	16
	C1477641326	12,964,861.56	28


## Find rings of transactions
### create table dest_groups and orig_groups
	create table dest_groups as (
		select namedest, sum(amount) sum_amount, count(*) txn_count 
			from payments group by namedest
	    );
	create table orig_groups as (
		select nameorig, sum(amount) sum_amount, count(*) txn_count 
			from payments group by nameorig
	    );
	
	select g.namedest participant, o.sum_amount amt_sent, o.txn_count txn_sent, g.sum_amount amt_received, g.txn_count txn_received, ((amt_received / 	amt_sent)*100) percentage
	from dest_groups g, orig_groups o where g.namedest = o.nameorig and percentage < 100 and percentage > 80
	order by amt_sent desc
	limit 10

### results
	PARTICIPANT	AMT_SENT	TXN_SENT	AMT_RECEIVED		TXN_RECEIVED		PERCENTAGE
	C1701114059	1,475,754.13	1		1,355,174.37		9			91.829278499
	C2125416624	757,149.36	1		715,369.55		5			94.481959279
	C1299730751	735,554.48	1		633,012.85		5			86.05927463
	C1369386225	549,129.32	1		444,028.61		3			80.860481098
	C378366946	459,475.05	1		452,393.05		5			98.45867583
	C946076563	437,040.17	1		409,174.5		1			93.624002572
	C122642644	436,893.89	1		418,684.9		1			95.832171056
	C1447902328	394,363.86	1		356,640.66		3			90.434417596
	C1303691866	385,342.43	1		352,774.25		2			91.548249696
	C2126942087	361,414.19	1		321,584.05		2			88.979364645

