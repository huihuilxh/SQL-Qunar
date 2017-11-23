# SQL-Qunar
SQL

SQL查询语句

1. 查询酒店订单

字段	值	含义

pay_type	0	预付

（付款方式）	1	现付

ppb_order_status	4	确认

（订单状态）	5	取消

15	退款

使用时修改uid和时间

SQL语句  

select create_time,b.uid,city_name,hotel_id,hotel_seq,hotel_name,from_date,to_date,room_name,bed_type,pay_type,ppb_order_status from mppb_order_channel as a inner join (select touch_uid,uid from ods_travel_touch_uid where uid='3812B41A-4611-4923-A157-82C19EF2868C' ) as b on a. uid=b.touch_uid WHERE a.dt>='2017-07-01' and a.dt<='2017-08-10' AND a.STATUS IN('0','2') AND a.vid LIKE '91%' AND a.chan_value in('travel_touch','travel_gonglue','travel_client','travelnote_client','travel note_gonglue','gonglue_zhuanti','gonglue_zhuanti2','jd_mt_huaweiqjzn') AND SUBSTRING(a.create_time,1,10)>='2017-07-01' AND SUBSTRING(a.create_time,1,10)<='2017-08-10'

2. 查询浏览的游记的页面位置

位置字段为itemOrder，数值加1为所处位置，使用时修改uid和时间

select * from travel_client_access where dt='2017-08-03' and uid='45523841-53E3-428E-A6F0-C9861FA11F8E' and requesturi='/api/book/getSimplified' and requestparammap like '%type=2%' order by currenttime asc

3. 查询浏览的酒店的页面位置

位置字段为itemOrder，数值加1为所处位置，使用时修改uid和时间

select * from travel_client_access where dt='2017-08-03' and uid='45523841-53E3-428E-A6F0-C9861FA11F8E' and requesturi='/api/book/element' and requestparammap like '%poiType=2%' order by currenttime asc

4. 单用户全部访问行为记录

使用时修改uid和时间

select * from travel_client_access where dt>='2017-05-12' and dt<='2017-07-26' and uid='47B360D0-0424-49C0-AE12-77CC94902C8F' order by currenttime asc

5. 单用户常居地查询

使用时修改uid

select * from mobile_user_new2 where uid='47B360D0-0424-49C0-AE12-77CC94902C8F'

6. 单用户定位接口查询

使用时修改uid和时间
select * from travel_client_access where dt='2017-09-12' and uid='47B360D0-0424-49C0-AE12-77CC94902C8F' and requesturi='/api/city/locate' order by currenttime asc

7.查询搜索词为当前城市名的搜索词和用户UID（例如用户在北京酒店列表页搜搜“北京”的情况）

select uid,query,cityid from 
(select uid,regexp_extract(requestparammap,'query=([^, }]+)',1) as query,regexp_extract(requestparammap,'cityId=([0-9]+)',1) as cityid from travel_client_access where dt='2017-11-01' and requesturi='/api/hotel/search') as a,
(select distinct dist_id,dist_name from poi_detail) as b where a.cityid=b.dist_id and a.query=b.dist_name

8.根据userid查询用户实名认证的信息

select uid,userid,c.* from
(select a.uid,a.userid,b.* from user_profile_common a lateral view json_tuple(pmap,'account_realname_info') b where userid='131980484' and dt='2017-11-19') d lateral view json_tuple(c0,'birthday','gender','province','city','age','is_real') c 


9.单用户 火车票订单查询

select b.uid,create_time,train_from,train_to,train_start_time,train_end_time,train_seat,train_seat_count,ticket_price,pre_status from ods_train_order_channel inner join (select touch_uid,uid from ods_travel_touch_uid ) as b on ods_train_order_channel.gid=b.touch_uid and dt='2017-07-11' and chan_value='gonglue_client'




10.单用户机票订单查询

select b.uid,depcity,arrcity,deptime from orderinfo_channel inner join (select touch_uid,uid from ods_travel_touch_uid ) as b on orderinfo_channel.deviceuid=b.touch_uid where dt>='2017-11-01' and dt<='2017-11-01' AND orderstatus NOT IN (0, 12, 20, 51, 91) ;


11.统计游记的读完的情况（是否滑动到游记详情页最底部）

返回的字段列表： 
time：事件发生时间，t：事件类型（4代表游记）,id：游记ID和子类型,ac：动作（13代表滑动到底部）
如果想查具体用户的，就可以带 uid='' 的条件
如果想查具体游记的，就可以带 id='111222-0' 的条件
 	


select * from travel_gonglue_action where t='4' and ac='13' and dt='2017-11-07' limit 10


12.某一天所有的 搜索词为当前城市名的搜索词和用户UID(也就是用户在 北京酒店列表页 搜 北京 的情况)


select uid,query,cityid from 
(select uid,regexp_extract(requestparammap,'query=([^, }]+)',1) as query,regexp_extract(requestparammap,'cityId=([0-9]+)',1) as cityid from travel_client_access where dt='2017-11-01' and requesturi='/api/hotel/search') as a,
(select distinct dist_id,dist_name from poi_detail) as b where a.cityid=b.dist_id and a.query=b.dist_name



