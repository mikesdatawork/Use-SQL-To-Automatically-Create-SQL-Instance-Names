![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL To Automatically Create SQL Instance Names
**Post Date: October 15, 2016**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's some SQL Logic to get a list of all instance names, and automatically create the next instance name.
In this environment I have several installations of SQL Server. I want to get the next instance name automatically so I can throw that into a build script and run that from a Job which installs and configures the next instance.
Here's a list of the existing SQL Instances. I'm using the SQLSHARE* as for all the instances in the multi-instance database server.

![See Instance Name]( https://mikesdatawork.files.wordpress.com/2016/10/image0012.png "SQL Instances")</p> 
 
I have a default instance already for SQL Server: MSSQLSERVER, then I have several more instances called:
SQLSHARE01
SQLSHARE02
SQLSHARE03
SQLSHARE04
SQLSHARE05…
What I want to do is get the next Instance Name automatically so I use this little piece of logic to produce the next name in the list. 

![SQL Logic]( https://mikesdatawork.files.wordpress.com/2016/10/mikesdatawork_multi_instance_sql_server1.png?w=829 "Instance Names")
 
    
## SQL-Logic
```SQL
use master;
set nocount on
 
-- create table to hold instance names
declare @sql_instances table
(
    [id]        int identity(0,1)
,   [rootkey]   nvarchar(255)
,   [sql_instances] nvarchar(255)
,   [value]     nvarchar(255)
)
 
-- get instances names using xp_regread
insert into @sql_instances ([rootkey], [sql_instances], [value])
execute xp_regread
    @rootkey    = 'hkey_local_machine'
,   @key        = 'software\microsoft\microsoft sql server'
,   @value_name = 'installedinstances'
 
select [sql_instances] from @sql_instances
 
-- set prefix name for multi-instance environment aka: Shared SQL Environment "SQLSHARE"
-- produce the next instance name.
declare @prefix     varchar(255) = 'SQLSHARE'
declare @next_instance  varchar(255) = 
(
select
    case   
        when cast(right(max([sql_instances]), 2) as int) < 10 then @prefix + '0' + cast(cast(right(max([sql_instances]), 2) as int) + 1 as varchar)
        else @prefix + cast(cast(right(max([sql_instances]), 2) as int) + 1 as varchar)
    end as [sql_instances]
from
    @sql_instances si
where
    si.[id] > 0
)
 
select (@next_instance)
```
Here's the result:
SQLSHARE06
SQLSHARE06 naturally represents the next in-line Instance Name. I'll work this into a build script so this can be carried out automatically by running a Job.




[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)


## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

