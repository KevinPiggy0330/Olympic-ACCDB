# Olympic-ACCDB 基于Access和MySQL的奥林匹克数据库

本项目旨在开发并实现一个全面的奥运历史数据管理系统，收集并存储关于奥运会的各类详细数据。我们整合了运动员的基本信息（如性别、身高、体重、年龄、奖牌数）以及涵盖各类体育项目、赛事、队伍、举办城市和国家的历史数据，所有数据均高效存储在SQL数据库中，确保系统的高效管理与查询。

## 一、数据分析与多维度查询功能

数据库支持复杂的SQL查询，用户可以通过条件组合（如国家、赛事、年份、运动员等）进行数据筛选与排序。例如，用户可以查询某个特定国家在历届奥运会中的奖牌总数，或是对不同运动员在多个赛事中的表现进行对比分析，帮助用户全面了解奥运各方面的历史数据。通过利用SQL的聚合函数、连接查询及子查询功能，用户能够深入分析奥运历史的长周期趋势。该功能帮助用户识别不同国家或运动员在特定项目和年份中的表现变化，并揭示奥运历史中的显著模式和发展趋势。

## 二、SQL查询举例（下列例子已在accdb数据表中实现，查询结果以子表显示）

### 1. 按年份统计奖牌数量（Medals Per Year Query）

本查询用于统计各个参赛队伍（Team）在历届奥运会（Games）中获得的奖牌总数，并按奖牌类型（铜牌、银牌、金牌）分别统计。这一数据可以帮助各参赛队对比不同年份的成绩变化，评估自身的发展趋势，同时也能用于分析各国代表队体育实力的演变情况。

```sql
SELECT Teams.team, Games.games, 
       Count(IIF(MainSheet.Medal<>'NA', 1, Null)) AS TotalMedalCount, 
       Count(IIF(MainSheet.Medal='Bronze', 1, Null)) AS BronzeMedalCount, 
       Count(IIF(MainSheet.Medal='Silver', 1, Null)) AS SilverMedalCount, 
       Count(IIF(MainSheet.Medal='Gold', 1, Null)) AS GoldMedalCount
FROM Games 
INNER JOIN (Sports 
INNER JOIN (Teams 
INNER JOIN (Athletes 
INNER JOIN MainSheet ON Athletes.ID = MainSheet.Athletes_ID) 
ON Teams.ID = Athletes.Teams_ID) 
ON Sports.ID = MainSheet.Sports_ID) 
ON Games.ID = MainSheet.Games_ID
GROUP BY Teams.team, Games.games;
```

#### 应用场景：

分析某个国家或地区在历届奥运会中的奖牌总数及其变化趋势。

评估某个队伍的历史表现，比较不同时期的成绩差异。

研究全球体育竞争格局，了解不同国家的体育实力发展。

### 2. 获奖者查询（Winners Query）

本查询用于统计某个体育项目（Event）的获胜者及其奖牌表现。这项统计能够帮助我们了解该项目在历史上的发展情况、各年获胜者的表现，并为各国教练制定相应的训练计划和竞赛规则提供参考。此外，它还可以促进该项目的整体发展，提升竞技水平。

```sql
SELECT Sports.Event, MainSheet.Name, MainSheet.Medal
FROM Sports 
INNER JOIN (Athletes 
INNER JOIN MainSheet ON Athletes.ID = MainSheet.Athletes_ID) 
ON Sports.ID = MainSheet.Sports_ID
WHERE Medal <> 'NA'
GROUP BY Sports.Event, MainSheet.Name, MainSheet.Medal;
```

#### 应用场景：

分析不同年份各大体育项目的获胜者以及他们的奖牌表现。

研究特定体育项目的发展历程，揭示该项目中顶尖选手的持续表现。

帮助各国教练和运动员了解历史获胜者的特点，从而改进训练方法和竞赛策略。

### 3. 金牌排名查询（Ranking of Gold Medal Query）

本查询用于统计单届奥运会中获得金牌的队伍，并按照金牌数量进行排名。这项统计可以直接反映各队在该届奥运会中的表现，帮助分析哪些队伍在该届奥运会中表现最强。此外，这种排名不仅能够增强前几名队伍的信心和凝聚力，还能激励排名靠后的队伍及时调整策略，制定更加合理的备战计划。

```sql
SELECT Games.Games, Teams.Team, Count(MainSheet.Medal) AS MedalOfCount
FROM Games 
INNER JOIN (Teams 
INNER JOIN (Athletes 
INNER JOIN MainSheet ON Athletes.ID = MainSheet.Athletes_ID) 
ON Teams.ID = Athletes.Teams_ID) 
ON Games.ID = MainSheet.Games_ID
WHERE (((MainSheet.Medal)='Gold'))
GROUP BY Games.Games, Teams.Team
ORDER BY Count(MainSheet.Medal) DESC;
```

#### 应用场景：

比较不同奥运会中各队伍获得的金牌数，揭示各国在体育项目上的强项和发展趋势。

增强金牌获得队伍的自信和凝聚力，同时激励其他队伍针对性地调整战术和备战策略。

为研究奥运会历史中的竞争格局变化提供数据支持。

## 用户自定义分析

我们提供灵活的数据查询接口，用户可以根据需求通过自定义SQL查询提取特定时间段、特定国家或特定运动员的数据，进行深入分析，满足个性化的研究需求。这一功能确保了数据库的高度适应性和扩展性。

## 数据可视化支持

为了增强数据洞察力，项目还提供了数据的可视化支持。用户通过图表等形式直观展示查询结果，帮助更好地理解奥运历史中的关键数据和趋势。
