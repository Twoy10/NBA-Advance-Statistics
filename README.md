# NBA-Advance-Statistics of Curry, LeBron and Durant

Key Performance Indicators (KPIs) and Questions Answered by this Query:

This query aims to answer a wide array of questions about the performance of LeBron James, Stephen Curry, and Kevin Durant, both individually and within the context of their teams.

**Player-Centric KPIs/Questions:**

1. Scoring & Offensive Production:
  a. Points Per Game (PPG): How many points does each player average per game each season?
  b. Field Goal % (Career FG%): What is their career shooting efficiency from the field?
  c. 3-Point % (Career 3P%): What is their career shooting efficiency from beyond the arc?
  d. Free Throw % (Career FT%): What is their career accuracy from the free-throw line?
  e. True Shooting % (TS%): How efficiently do they score when considering field goals, three-pointers, and free throws?
  f. Effective Field Goal % (eFG%): How efficient are they at shooting, giving extra weight to three-pointers?
  g. Points Per Shot Attempt: How many points do they generate per shot attempt, factoring in free throws?

2. Playmaking & Ball Handling:
  a. Assists Per Game (APG): How many assists do they average per game each season?
  b. Turnovers Per Game (TOPG): How many turnovers do they commit per game each season?
  c. Assist-Turnover Ratio: How effectively do they balance assists with turnovers?
  d. Turnover % (TO%): What percentage of their possessions end in a turnover?

3. Rebounding:
  a. Rebounds Per Game (RPG): How many rebounds do they average per game each season?

5. Defense & Hustle:
  a. Steals Per Game (SPG): How many steals do they average per game each season?
  b. Blocks Per Game (BPG): How many blocks do they average per game each season?
  c. Personal Fouls Per Game (PFPG): How many personal fouls do they commit per game each season?

6. Availability & Impact:
  a. Games Played (per season): How many games did each player participate in each season?
  b. Average Plus/Minus (AVG +/-): What is their average on-court impact on the score difference when they are on the floor?

6.Usage & Offensive Involvement:
  a. Usage Rate: What percentage of team possessions are used by the player while they are on the floor?

**Team-Centric KPIs/Questions (for teams played by the selected players):**

1. Team Offensive Production:
  a. Team PPG, APG, RPG, SPG, BPG, TOPG, PFPG (per team per season): How did the teams these players played for perform in basic statistical categories during those seasons?
  b. Team True Shooting % (Team TS%): What was the overall scoring efficiency of the teams?
  c. Team Effective Field Goal % (Team eFG%): What was the team's shooting efficiency, weighted for three-pointers?

2. Team Ball Handling:
  a. Team Turnover % (Team TO%): What percentage of team possessions ended in a turnover?
  b. Team Assist-Turnover Ratio: How well did the team balance assists with turnovers?

**Process (Query Breakdown):**

The query follows a structured approach using Common Table Expressions (CTEs) to progressively build and refine the data, moving from raw game details to calculated player and team metrics.

1. game_ids CTE:
  a. Purpose: Identifies all game_ids where Stephen Curry, LeBron James, or Kevin Durant participated, within the specified season date ranges (2003-2021).
  b. Process: Joins games_details.csv (for player names) and games.csv (for game dates) and filters by player name and a comprehensive list of game date ranges for each season. This ensures only relevant games are considered for subsequent calculations.

2. inputs CTE:
  a. Purpose: Aggregates raw player statistics per season and team for the three selected players. It calculates per-game averages and total sums needed for advanced metric calculations.
  b. Process: Joins games_details.csv and games.csv, filtering by game_ids from the game_ids CTE and the player names. It then groups by season, PLAYER_NAME, and team_abbreviation to calculate:
    i   Per-game averages (PPG, APG, RPG, SPG, BPG, TOPG, PFPG)
    ii  Total counts (games played, total points, FGM, FGA, FTM, FTA, 3PM, 3PA, turnovers, assists)
    iii Average PLUS_MINUS

3. career_pct CTE:
  a. Purpose: Calculates the career shooting percentages (FG%, FT%, 3FG%) for each player.
  b. Process: Aggregates the total FGM, FGA, FTM, FTA, 3PM, and 3PA from the inputs CTE across all seasons for each player, then computes the respective shooting percentages.

4. possessions_inputs CTE:
  a. Purpose: Gathers raw data required to calculate team possessions for each game where the target players participated.
  b. Process: Joins games_details.csv and games.csv, filtering by game_ids. It then groups by game_id, date, and season to sum up FGA, FTA, TO, and OREB for both the home and away teams in each game.

5.possessions_initial CTE:
  a. Purpose: Calculates the estimated team possessions for both the home and away teams in each game using the formula: 0.5 * [ (Team FGA + 0.44 * Team FTA + Team TO – Team OREB) + (Opponent FGA + 0.44 * Opponent FTA + Opponent TO – Opponent OREB) ].
  b. Process: Applies the possession formula to the aggregated data from possessions_inputs for both home and away teams.

6. possessions_final CTE:
  a. Purpose: Pivots the possessions_initial data to have one row per team per game for possessions, standardizing the format.
  b. Process: Uses a UNION operation to combine the home team possessions and away team possessions into a single column, along with the respective team abbreviation.

7.curry_possessions, lebron_possessions, kd_possessions CTEs:
  a. Purpose: Calculates the total team possessions for each season when each specific player (Curry, LeBron, Durant) was playing for a team. This is crucial for calculating "Usage Rate."
  b. Process: Joins possessions_final with games_details.csv, filtering by player name and ensuring the team abbreviation matches the team in possessions_final. It then sums the possessions per season for each player.

8. usage_rate CTE:
  a. Purpose: Calculates the "Usage Rate" for each player per season using the formula: [(FGA + 0.44 × FTA + TO) / (Team Possessions)].
  b. Process: Uses UNION ALL to combine the usage rate calculations for each player. It joins the inputs CTE (for player-specific FGA, FTA, TO) with their respective total possessions CTE (curry_possessions, lebron_possessions, kd_possessions) to compute the seasonal        usage rate.

9. teams_played CTE:
  a. Purpose: Identifies all unique teams and seasons in which Stephen Curry, LeBron James, or Kevin Durant played. This is used to filter team-level data.
  b. Process: Selects distinct season and team_abbreviation from games_details.csv and games.csv, filtering by the three player names.

10. teams_played_total_initial CTE:
  a. Purpose: Aggregates the total raw statistics for all games played by the teams identified in teams_played for the relevant seasons.
  b. Process: Joins games_details.csv, games.csv, and teams_played. It sums up various team-level statistics (points, assists, rebounds, etc.) per game, ensuring only games from the relevant seasons for the specified teams are included.

11. teams_played_total CTE:
  a. Purpose: Aggregates the total raw statistics per team per season from teams_played_total_initial.
  b. Process: Sums the game-level totals from teams_played_total_initial to get total season statistics for each team.

12. teams_played_stats CTE:
  a. Purpose: Calculates the per-game averages for core team statistics (PPG, APG, RPG, etc.) for each team per season.
  b. Process: Averages the game-level totals from teams_played_total_initial to derive per-game team statistics.

13. final_metrics CTE:
  a. Purpose: Combines all calculated player and team metrics into a single comprehensive dataset. This is the main output for the dashboard.
  b. Process: Performs multiple JOIN operations:
    i   inputs (for basic player stats)
    ii  usage_rate (for player usage)
    iii career_pct (for career shooting percentages)
    iv  teams_played_total (for total team stats, needed for team advanced metrics)
    v   teams_played_stats (for per-game team stats)
  c. Finally, it calculates the remaining advanced player and team metrics (True Shooting %, eFG%, Turnover %, Assist-Turnover Ratio, Points Per Shot Attempt for players; and Team True Shooting %, eFG%, Turnover %, Assist-Turnover Ratio for teams) using the aggregated       sums and averages from the preceding CTEs.

14. Final SELECT Statement:
  a. Purpose: Retrieves all the calculated columns from the final_metrics CTE, presenting the complete dataset ready for visualization in Power BI.
