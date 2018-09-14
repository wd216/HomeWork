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


union 和 union all 的区别
union 会对要连接的2个表进行2个查询的结果进行对比,然后最后的结果不会出现重复的结果
union all 不会把重复的数据清理掉

如果确保2个需要连接的表没有重复的数据 使用 union all 连接比 union 要好，效率高
