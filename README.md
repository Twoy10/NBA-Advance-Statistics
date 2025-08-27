# NBA-Advance-Statistics of Curry, LeBron and Durant

This SQL project analyzes the performance of LeBron James, Stephen Curry, and Kevin Durant by calculating both player-centric and team-centric KPIs. For the players, the metrics cover scoring efficiency such as points per game, field goal percentage, three-point percentage, free throw percentage, true shooting percentage, effective field goal percentage, and points per shot attempt. Playmaking and ball handling are also included through assists, turnovers, assist-to-turnover ratio, and turnover percentage, while rebounding, steals, blocks, and personal fouls represent contributions on defense and on the boards. Player availability and impact are reflected through games played, plus/minus, and usage rate.

On the team side, the project measures overall team scoring, assists, rebounds, steals, blocks, turnovers, and fouls. It further evaluates efficiency through team true shooting and effective field goal percentages, as well as team-level turnover percentage and assist-to-turnover ratio.

The process relies on multiple common table expressions (CTEs) to progressively build the metrics. It begins with identifying the relevant games, then aggregates player statistics, before moving into calculations of advanced measures such as shooting efficiency, usage rate, and possessions. In parallel, it aggregates team-level statistics for the seasons in which each player was active. The final CTE consolidates all of these results into a single dataset that is ready for visualization.

The outcome is a comprehensive dataset that captures advanced player and team performance metrics, designed to be connected with Power BI to create an interactive dashboard.


Dataset: https://www.kaggle.com/datasets/nathanlauga/nba-games
