---
layout: post
title: World Cup 2026 Prediction Model
description: One model that predicts any international match, run on all 104 games of the 2026 World Cup and checked against what actually happened.
skills:
- Python
- pandas
- scikit-learn
- Logistic Regression
- Monte Carlo Simulation
main-image: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/2026_FIFA_World_Cup_Match_98%2C_Spain_v_Belgium.jpg/1280px-2026_FIFA_World_Cup_Match_98%2C_Spain_v_Belgium.jpg"
---

## Overview

Before the 2026 World Cup, I built a notebook to predict one game: USA vs Paraguay on June 12 at SoFi Stadium. For this project I turned it into one model that can predict any matchup. I ran it on all 104 games of the tournament and checked every pick against what really happened. Each game was predicted using only matches played before it.

## Approach

- Used a public dataset of every men's international match since 1872. The model trained on the 18,452 games between FIFA teams from 2006 to the day before the World Cup.
- Kept the four form features from my notebook: win rate, goals scored, goals conceded and goal difference over each team's last 8 games.
- Added each team's Elo rating, which gives more credit for beating strong teams (World Football Elo Ratings method). On eight seasons of league football, Hvattum and Arntzen (2010) found Elo-based predictions beat the other statistical methods they tested and lost only to bookmaker odds.
- Used logistic regression with three outcomes (win, draw or loss), plus home advantage for the three hosts.
- In knockout games, the chance to go through is the chance to win plus a share of the draw chance for extra time and penalties. I then simulated the knockout bracket 100,000 times from the round of 32 to get each team's chance to win the trophy.

## Challenge and insight

My notebook only worked for one game. When I tried to reuse it for other matchups, I found it had never trained on a real matchup. It lined up USA's 1st game with Paraguay's 1st game, its 2nd with Paraguay's 2nd, and so on, then labeled each pair with USA's result against a different team. Rebuilt on this data, that was only about 30 made-up matchups. It also dropped every draw, and its home advantage feature was always 1, so the scaling step turned it into zero.

Training on every real match fixed that, but the form features alone only picked the right result 54% of the time on games from 2022 to the World Cup. The insight came from noticing that a "last 8 games" win rate counts a win over San Marino the same as a win over Brazil. Elo ratings fix that by rewarding wins over strong teams more. Adding Elo raised the hit rate to 60%.

## Result

| Check | Result |
|---|---|
| Games from 2022 to the World Cup (4,361) | Form only: 54% right. Form + Elo: 60% right |
| Group stage (72 games) | Picked the right result in 42 (58%) |
| Knockouts (32 games) | Picked who went through in 24 (75%). 4 of the 8 misses were penalty shootouts |
| Biggest upsets | Spain 0-0 Cape Verde (10% chance of a draw), Norway 2-1 Brazil (31% for Norway), Paraguay beat Germany on penalties (37%) |
| Who wins it all | From the round of 32: Argentina 30%, Spain 25%, France 17%. Spain beat Argentina 1-0 after extra time in the final, where the model gave Spain 58% |
| USA vs Paraguay | USA 34%, draw 30%, Paraguay 36%. USA won 4-1 |

![What the model expected in the 72 group games compared to what happened](/_projects/World%20Cup%20ML%20prediction%20model/group_stage_expected_vs_actual.svg)

The model expected the favorite to win 42.2 of the 72 group games, and they won 42. It expected 17.5 draws and there were 20.

![Knockout bracket from the round of 16, with the chance the model gave each team that went through](/_projects/World%20Cup%20ML%20prediction%20model/knockout_bracket.svg)

Every quarter-final, semi-final and the final went to the model's favorite. Half of its knockout misses came in the round of 16, where Norway knocked out Brazil.

## Things to know

- **The model never picks a draw.** A draw was never the single most likely result (the highest draw chance it gave any group game was 30%), so it always picks a team. 20 of the 72 group games were draws, so those were always counted as misses.
- **Penalties are close to a coin flip.** The model splits the draw chance between the two teams by strength, but 4 knockout games went to penalties and the favorite lost all 4.
- **One tournament is a small test.** At the World Cup, the form-only version also picked 42 group results right. The 4,361 games before it are where adding Elo clearly helped. The model also only knows results, not injuries, squads or coaches.

## Sources

1. martj42, [*International football results from 1872*](https://github.com/martj42/international_results) (dataset, CC0), GitHub.
2. [World Football Elo Ratings](https://www.eloratings.net/about), rating method.
3. L. M. Hvattum and H. Arntzen, ["Using ELO ratings for match result prediction in association football,"](https://doi.org/10.1016/j.ijforecast.2009.10.002) *International Journal of Forecasting*, vol. 26, no. 3, pp. 460-470, 2010.
4. F. Pedregosa et al., "Scikit-learn: Machine Learning in Python," *Journal of Machine Learning Research*, vol. 12, pp. 2825-2830, 2011.
5. Wikipedia, [2026 FIFA World Cup](https://en.wikipedia.org/wiki/2026_FIFA_World_Cup), used to check the results.

*Cover photo: ["2026 FIFA World Cup Match 98, Spain v Belgium"](https://commons.wikimedia.org/wiki/File:2026_FIFA_World_Cup_Match_98,_Spain_v_Belgium.jpg) by Alexis Doine, [CC0](https://creativecommons.org/publicdomain/zero/1.0/), via Wikimedia Commons.*
