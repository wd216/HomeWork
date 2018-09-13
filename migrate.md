把lagou_position_bk表分为3个表分别是 lagou_city 、 lagou_company 、lagou_position

lagou_company 公司表 我们可以从lagou_position_bk 表中把属于公司信息的字段查询出来，再利用as关键字,来把查询到的数据创建为一个新的表

	create table lagou_company as
	select distinct t.company_id         as cid,
                  t.company_short_name as short_name,
                  t.company_full_name  as full_name,
                  t.company_size       as size,
                  t.financestage
	from lagou_position_bk t;
这样就已经把属于公司的信息创建到lagou_company表中
再对lagou_position_bk表进行清理，把属于公司表的一些字段删除掉

	alter table lagou_position_bk drop company_size;
	alter table lagou_position_bk drop company_full_name;
	alter table lagou_position_bk drop company_short_name;
	alter table lagou_position_bk drop financestage;

lagou_city 表需要用lagou_position_bk表和chian_city表进行关联，因为chian_city表利用递归的方式把省，市，县区分好了

	create table lagou_city as
	select d.id, p.cityName as province, c.cityName as city, d.cityName as district from
  	(select * from china_city where depth=3) d
    join china_city c on d.parentId = c.id and c.depth=2
    join china_city p on c.parentId = p.id and p.depth=1

利用多表查询把china_city表中所有的县查询出来，上面中的d.id是代表县的id,因为laou_position_bk里面有些公司在的位置是在市里面可能不在县里所以还需要再次利用多表查询把lagou_china里所有属于市的信息查出来下面c.id是代表市的id

	select c.id, p.cityName as province, c.cityName as city, null as district from (select * from china_city where depth=2) c
  	join china_city p on c.parentId = p.id and p.depth = 1;

再利用union把2个查询的结果进行连接并利用as关键字创建一个新表

	create table lagou_city as
	select d.id, p.cityName as province, c.cityName as city, d.cityName as district from
  	(select * from china_city where depth=3) d
    join china_city c on d.parentId = c.id and c.depth=2
    join china_city p on c.parentId = p.id and p.depth=1
	union
	select c.id, p.cityName as province, c.cityName as city, null as district from (select * from china_city where depth=2) c
  	join china_city p on c.parentId = p.id and p.depth = 1;

把lagou_position_bk中一些关于所在位置信息的列进行清理
	
	alter table lagou_position_bk drop city;
	alter table lagou_position_bk drop district;

lagou_position 需要把lagou_city表的信息和lagou_position_bk属于职业信息关联起来并合成一张新的表
	
	利用关联的关系把lagou_position_bk表中公司位置在县的公司查询出来
	
	select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at from
	(
  	select p.*, c.cid from (select * from lagou_position_bk where district is null) p
     join lagou_city c on c.city like concat(p.city, '%') and c.district is null;


	再把位置属于市的公司信息查询出来

	select p.*, c.cid from (select * from lagou_position_bk where district is not null) p
    join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%')；

	再利用union把2个查询的信息连接起来并创建成一个新表

不是很理解这个把信息和城市关联起来，不清楚为什么这样写就能够把城市和职业信息查出来，查询的条件理解的不好。