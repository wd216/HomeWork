首先我们先把广东省下面所有的县查出来，这里的s.id 代表的是县的id
select s.id,r.cityName,p.cityName,s.cityName 
from s_provinces s,s_provinces p,s_provinces r 
where s.parentId = p.id and p.parentId = r.id and r.cityName = '广东省' 

把广东省下面所有的市查出来，p.id代表的是市的id 

select p.id,r.cityName,p.cityName,null
from s_provinces s,s_provinces p,s_provinces r 
where p.parentId = r.id and r.cityName = '广东省'；

最后把2个查询的结果用 union 连接起来，这样新的表里面就有县的id又有市的id

select s.id,r.cityName,p.cityName,s.cityName 
from s_provinces s,s_provinces p,s_provinces r 
where s.parentId = p.id and p.parentId = r.id and r.cityName = '广东省'

union

select p.id,r.cityName,p.cityName,null
from s_provinces s,s_provinces p,s_provinces r 
where p.parentId = r.id and r.cityName = '广东省'；
