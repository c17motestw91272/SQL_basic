
### 在章節 **Nested simple subqueries** 中

以下查詢為匯集比賽紀錄之 _賽季_ 、_最大比分_
以及綜觀全資料中最大比分以及 _每一季中七月的最大比_

```sql
SELECT
	-- Select the season and max goals scored in a match
	season,
    MAX(home_goal + away_goal) AS max_goals,
    -- Select the overall max goals scored in a match
   (SELECT MAX(home_goal + away_goal) FROM match) AS overall_max_goals,
   -- Select the max number of goals scored in any match in July
   (SELECT MAX(home_goal + away_goal) 
    FROM match
    WHERE id IN (
          SELECT id FROM match WHERE EXTRACT(MONTH FROM date) = 07)) AS 
          july_max_goals
FROM match
GROUP BY season; 
```
  
結果如下 
```
season	max_goals	overall_max_goals	july_max_goals  
2013/2014	10	11	7  
2012/2013	11	11	7  
2014/2015	10	11	7  
2011/2012	10	11	7
```

第一時間沒有看懂，是因為所有的查詢組成都很類似，因為巢狀結構讓條件分散在查詢的不同位置  

但後來由查詢中的第一行 **MAX(home_goal + away_goal) AS max_goals,** 可知  
單一賽季中的最高比分＝MAX加上home_goal+away_goal  
而且僅該句受到主查詢中的GROUP BY 影響   

因為依照查詢結構來說，overall_max_goals的組成也是 **SELECT MAX(home_goal + away_goal) FROM match**  
但查詢已完成，僅回傳了一組數值＝11，就算回到主查詢加上了GROUP BY 也不受影響

第三個查詢則使用了子查詢中

#### 問題紀錄時間 2023/2/5 17:00
