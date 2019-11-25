![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 获取每个实例的SQL Server版本平均事务
#### Get SQL Server Version Average Transactions Per Instance
**发布-日期: 2015年07月08日 (评论)**

![#](images/##############?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
以下是一些可以获取有关数据库服务器的以下信息的快速逻辑。

1. 服务器名称
2. SQL版（完整版）
3. 总数据库大小（所有数据库）
4. 服务在线的天数（用于确定每天的平均交易量。在线天数为你提供样本）
5. 整个样本的总交易量（在线天数）
6. 每日平均交易



## English
Here’s some quick logic to get you the following information about your database server.

1. Server Name
2. SQL Version (complete)
3. Total Combined Database Size (all databases)
4. Number of Days the Service has been online (this is used to determine the average transactions per day. Number of days online gives you the sample)
5. Total transactions across entire sample (days online)
6. Average transactions per day

---
## Logic
```SQL
use master;
 set nocount on
 declare @days smallint
 declare @trans bigint
 set @days = ( select datediff(d, create_date, getdate()) from sys.databases where database_id = 2 )
 set @trans = ( select cntr_value from sys.dm_os_performance_counters where counter_name = 'transactions/sec' and instance_name = '_total' )
 
select
 'server' = @@servername
 , 'sql version' =
 cast(substring(left(@@version, 25), 10, 16) as varchar(50)) + ' ' +
 cast(serverproperty('edition') as varchar(50)) + ' ' +
 cast(serverproperty('productlevel') as varchar(50)) + ' Build: ' +
 cast(serverproperty('productversion') as varchar(50))
 , 'total database size' = cast (convert(decimal(10,2),(sum(size*8.00)/1024.00/1024.00)) as varchar(10)) + ' GB'
 , 'sample num days' = @days
 , 'total trans' = @trans
 , 'average daily trans' = @trans / case when @days = 0 then 1 else @days end
 
from
 sys.master_files;


```

注意： 
如有必要，可以更精细地计算每日平均交易。这个逻辑是所有插入/更新/删除等的总事务，而不是通过任何方式缩小，但它足以计算数据库实例整体经历的用法类型。有关每个数据库的平均事务（我将在稍后编写）的更明确的想法应该按照数据库完成。此逻辑引用了“_total”实例名称，该名称是实例下的所有内容。感谢Waterox的Chris Bell发表的关于收集平均交易的文章。更多信息请访问：https://wateroxconsulting.com/archives/average-transactions-per-day/

Note:
The average transactions per day can be calculated with a bit more granularity if necessary. This logic is the total transactions all inserts/updates/deletes etc. and not narrowed down by any means, but it’s sufficient to calculate the kind of usage your database instance is experiencing as a whole. For more definitive idea of average transactions per database (which I’ll be writing later) should be done per database. This logic is referencing ‘_total’ instance name which is everything under the instance. Thanks goes out to Chris Bell at Waterox for posting his write-up on collecting average transactions. More can be found at: https://wateroxconsulting.com/archives/average-transactions-per-day/



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

