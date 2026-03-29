# Mục lục:
A. Data analystic
1. [Level](#1-level)
2. [Advertisement](#2-advertisement)
3. [InApp](#3-inapp)
4. [Funnel](#4-funnel)

B. Event source connector config
1. [Create a game](#1-create-a-game)
2. [Create a connector](#2-create-a-connector)
3. [Link a game](#3-link-a-game)
<hr/>

# A. Data analystic
# 1. Level

## I. Quartiles

Quarters are a concept in statistics used to divide a data set into four equal parts based on the number of observations. Each part represents 25% of the data.

Quarters include:

- Q1 (first quartile: 25%): the value at which 25% of the data is less than or equal to Q1.

- Q2 (median: 50%): dividing the data into two equal parts, 50% of which is less than or equal to Q2.

- Q3 (third quartile: 75%): the value at which 75% of the data is less than or equal to Q3.

Using quartiles in level charts provides a more objective view of the analytical indicators compared to considering only the mean.

## II. Charts

### 1. Difficulty for Passers (Difficulty for Passers)

**Definition:** The number of failed attempts a player makes to pass a level (How many failed attempts a player must make to pass a level).

**Meaning:** Indicates which levels are too difficult or too easy, allowing for optimization to improve user experience.

**Formula:**

- From the LevelLog records, based on the status field, we can calculate how many times each player failed a level, only including players who passed the level (number of pass records for that player > 0).

$$
LevelLog -> PlayData := {Player, Level, FailCount| PassCount>0}
$$

- From the data above, we have:

$$
\begin{split}
Difficulty\_{LvX} &= \frac
{sum(PlayData.FailCount\_{Level = x})}
{count(PlayData.Player\_{Level=x})} \\\\&= \frac{Total~~Fails~~LvX\_{For~~players~~passed~~LvX} }{Total~~players~~passed~~LvX}
\end{split}
$$

- Where:
  - Level: the player's current level
  - Fail count: the number of records with status = fail corresponding to that player and level
  - Pass count: the number of records with status = pass corresponding to that player and level

**Example**: Figure 1.1 shows that at level 69, each player needs an average of 0.43 fail attempts to pass level 69. 25% and 50% of players pass the level on their first attempt, and 75% of players need an average of 0.43 attempts to pass the level.

<div align="center"><figure><img width="1428" height="960" alt="Image" src="https://github.com/user-attachments/assets/bfe2a450-c0c9-4ab2-8b84-2c9322d67236" /><figcaption><i>Figure 1.1 Difficulty Chart for Passers-Level Players</i></figcaption></figure></div>

### 2. Difficulty for Players Who Fail (Difficulty for Players Who Haven't Passed)

**Definition:** The number of times a player fails a level and hasn't passed it. (The number of times a player fails before quitting/stopping the game).

**Meaning:** Indicates which levels are too difficult and discouraging for users.

**Formula:**

- From the LevelLog records, based on the status field, we can calculate how many times each player failed a level, only including players who didn't pass the level (the number of records showing that player passed the level = 0).

  $$
  LevelLog -> PlayData := {Player, Level, FailCount| PassCount=0}
  $$

- From the data above, we have:

$$
\begin{split}
Difficulty\_{LvX} &= \frac
{sum(PlayData.FailCount\_{Level = x})}
{count(PlayData.Player\_{Level=x})} \\\\&= \frac{Total~~Fails~~LvX\_{For~~players~~not~~passed~~LvX} }{Total~~players~~not~~passed~~LvX}
\end{split}
$$

- Where:
  - Level: the player's current level
  - Fail count: the number of records with status = fail corresponding to that player and level
  - Pass count: the number of records with status = pass corresponding to that player and level

**Example:** Figure 1.2 shows that at level 69, each player averages 3.23 fail attempts and still cannot pass the level. 25% of players only average 0.31 attempts and quit the game, 50% of players average 1.31 attempts and still cannot pass the level, and 75% of players average 1.62 attempts and quit/stop the game at level 69.

<div align="center"><figure><img width="1440" height="970" alt="Image" src="https://github.com/user-attachments/assets/a2ac0e3e-3532-4b3c-b807-e095a11f1ee1" /><figcaption><i>Figure 1.2 Difficulty Chart for Fail Players</i></figcaption></figure></div>

### 3. Percentage of Players Who Haven't Passed a Level

**Definition:** This graph shows the percentage of players who haven't passed a level.

**Meaning:** It indicates which levels have a high drop rate (due to difficulty/bugs/boringness/...).

**Formula:**

- From the LevelLog records, based on the status field, we can calculate how many Pass logs there are for each level. Since each player only logs their pass once, PassCount = number of players who passed the level.

  $$
  LevelLog -> PlayData := {Level, PassCount}
  $$

- From the data above, we have:

$$
\begin{split}
Ratio\_{LevelX} &= \frac
{PlayData.PassCount\_{Level = x})}
{PlayData.PassCount\_{Level=x-1})}
\\\\&= \frac
{Total~~players~~passed~~Lv~~x }
{Total~~players~~passed~~Lv~~x-1}
\end{split}
$$

- Where:
  - Level: The highest level the player has passed.
  - Pass count: The number of records with a status equal to the Pass corresponding to that level.

**Note:** If you only log levels from level 1, there will be no data for level 0 (players who have only opened the game/feature but haven't actually played), so there will be no drop rate for level 1. Therefore, in the example graph, the level axis starts from 2. If you want the drop rate for level 1, please log level 0.

**Example:** Figure 1.3 shows that 6.21% of players have not yet passed level 69.

<div align="center"><figure><img width="1434" height="966" alt="Image" src="https://github.com/user-attachments/assets/e90ee59b-054c-4797-9c9a-73c5d232a8ab" /><figcaption><i>Figure 1.3. Chart showing the percentage of players who have not yet passed a level.</i></figcaption></figure></div>

### 4. Total Players by Level

**Definition:** This graph shows the distribution of players by level.

**Formula:**

- From the LevelLog records, based on the status field, we can determine the maximum level each player can reach:

  $$
  LevelLog -> PlayData := {Player, Level}
  $$

- From the data above, we have:

$$
\begin{split}
Total\_{LevelX}&= count(PlayData.Player\_ {Level=X})
\\&= Total\~Player\_{MaxPassedLv=X}\
\end{split}
$$

- Where:
  - Level: The highest level the player has passed.

**Note:**

- Player A has passed level 100, but wants to return to level 5 to earn resources, player A will still be counted as being at level 100.
- Players who fail the first level and then quit the game before passing will be counted as level 0 by the system.

**Example:**

<div align="center"><figure><img width="1430" height="960" alt="Image" src="https://github.com/user-attachments/assets/558454f1-b051-449b-ac00-e40f09ac3f17" /><figcaption><i>Figure 1.4. Chart of Total Players by Level</i></figcaption></figure></div>

### 5. Average Playtime

**Definition:** This graph shows the average time a player spends on each type of play status.

**Significance:** The graph provides information for developers to adjust the length of the levels.

**Formula:**

- From the LevelLog records, we have:

$$
\begin{split}
Time\_{LevelX,StatusY} &= \frac
{\sum LevelLog.Duration\_{Level = X, Status = Y})}
{count(LevelLog\_{Level = X, Status = Y})}
\\\\&= \frac
{Total~~duration~~players~~spent~~for~~LvX~~StatusY }
{Times~~players~~play~~LvX~~StatusY}
\end{split}
$$

- Where:
  - Level: The player's current level
  - Status: Pass, Fail,...
  - Unit: Seconds

**Example**: Figure 1.5, at current level 69, the average time to pass the level is 339.02 (s) and the average time to fail is 240.88 (s).

<div align="center"><figure><img width="1434" height="960" alt="Image" src="https://github.com/user-attachments/assets/13eb60af-a360-4a2a-92ad-03daf4c31fb2" /><figcaption><i>Figure 1.5 Average time graph</i></figcaption></figure></div>

### 6. Số lượt chơi ở level hiện tại

### 6. Number of plays at the current level

**Definition:** A graph showing the number of plays a player has made at the current level.

**Formula:**

- From the LevelLog record, we have:

$$
\begin{split}
PlayTimes\_{LevelX}&= count(LevelLog\_{Level=X})
\end{split}
$$

- Where:
  - Level is the player's current level
  - Status: Pass, Fail

**Example**: In Figure 1.6, at level 69, there were 332 plays with the status Pass and 185 plays with the status Fail.

<div align="center"><figure><img width="1425" height="957" alt="Image" src="https://github.com/user-attachments/assets/3bee8723-a4df-4a66-9c06-7b3658e163a1" /><figcaption><i>Figure 1.6 Graph of the number of plays by a player at the current level.</i></figcaption></figure></div>

# 2. Advertisement

## I. Filters

### 1. Ad Type

Type of ad

### 2. Ad Where

Ad placement

## II. Charts

### 1. Total ad views by date (Imp by Date)

**Definition:** This chart shows the total number of ad views for a game over several days.

**Meaning:** Checks the trend of ad performance in the game.

**Formula:**

- From AdLog records, we have:

$$
\begin{split}
Imp\_{DayX} &= count(AdLog\_{Day=X})
\\\\&= Number~~AdLog~~Record\_{Day = X}
\end{split}
$$

- Where:
  - Day: Log recording time/time the player first opened the game, depending on the filter selection.
  - **Impression**: Total number of ads in the day.

**Example**: Figure 1.1 shows the growth in ad views over recent days.

<div align="center"><figure><img width="1431" height="1031" alt="Image" src="https://github.com/user-attachments/assets/4cb497c1-d3b0-4a48-b87c-9707a46f1109" /><figcaption><i>Figure 1.1. Chart of total ad views by date</i></figcaption></figure></div>

### 2. Total Ad Views by Level (Imp by Level)

**Definition:** This graph shows the total number of ad views for a game by level.

**Meaning:** Level:

- If it's maxPassedLevel: each ad column (high or low) reflects the nature of the next level; here, if the columns:
  - High: The next level is too difficult, players can't pass it, so they need to watch more ads; balance with the drop rate to avoid over-exploitation that causes players to quit the game.
  - Low: The next level is too easy, players pass it too quickly; try increasing the difficulty of the next level.
- If it's currentLevel (only used if the game allows replaying levels): shows the effectiveness of levels in influencing players to watch ads.
  - Levels with high ad views may be due to:
    - That level has problems (unbalanced resources, bugs, etc.), so players exploit that level to obtain resources.
    - Levels that appeal to players, encouraging them to play multiple times.
    - Levels that are efficient, requiring players to watch many ads to pass.
- Levels with low ad content may be due to:
  - Being too easy (compare with [Difficulty on Pass](https://github.com/nguyenkimluang98/pixiu-insight/edit/master/README.md#1-%C4%91%E1%BB%99-kh%C3%B3-tr%C3%AAn-ng%C6%B0%E1%BB%9Di-pass-%C4%91%E1%BB%99-kh%C3%B3-v%E1%BB%9Bi-ng%C6%B0%E1%BB%9Di-pass) in the Level section), possibly increasing the difficulty/length of the level.
  - Poorly designed levels that players don't replay.

**Formula**:

- From the AdLog records, we have:

$$
\begin{split}
Imp\_{LvX} &= count(AdLog\_{Level=X})
\\\\&= Number~~AdLog~~Record\_{Level= X}
\end{split}
$$

- Where:
  - **Impression**: Total number of ads.

**Example**: Figure 2.1 shows that level 8 has the highest number of viewed ads.

<div align="center"><figure><img width="1426" height="1120" alt="Image" src="https://github.com/user-attachments/assets/c040e67a-a6d2-420c-955d-24377d19cf13" /><figcaption><p><i>Figure 2.1. Chart of Total Ad Views by Level</i></p></figcaption></figure></div>

The slider shows the percentage of ads on a given level compared to the total number of ads across all levels (Ad allocation ratio by level).

<div align="center"><figure><img width="605" height="88" alt="Image" src="https://github.com/user-attachments/assets/c4f85d17-85ac-4812-8856-6af0288168fc" /><figcaption><p><i>Figure 2.2. The slider shows the ad allocation ratio by level.</i></p></figcaption></figure></div>

### 3. Imp/DAU

**Definition:** Represents the average number of ads a player sees per day.

**Formula:** &#x20;

- From AdLog records, we have:

$$
\begin{split}
Imp\_{DayX} &= count(AdLog\_{Day=X})
\\\\&= Number~~AdLog~~Record\_{Day = X}
\end{split}
$$

- From the records automatically collected by the RetentionLog SDK (only one record is generated for each player who logs in per day):

$$
\begin{split}
DAU\_{DayX} &= count(RetentionLog\_{Day=X})
\\\\&= Number~~RetentionLog~~Record\_{Day = X}
\end{split}
$$

- Based on the two data points above

$$
\begin{split}
ImpDAU\_{DayX} &= Imp\_{Day=X}/DAU\_{Day=X}
\\\\&= NumberAdsDay~~X/NumberPlayerDay~~X
\end{split}
$$

- Where:
  - Day: Log recording time/time the player first opened the game, depending on the filter selection.
  - **Impression**: Total number of ads in the day.
  - **DAU (Daily Engaged User)**: Number of players who entered the game in the day.

**Example**: Figure 3.1 shows that on March 9, 2026, 2,209 players opened the game; on average, each player viewed 43.91 ads.

<div align="center"><figure><img width="1429" height="997" alt="Image" src="https://github.com/user-attachments/assets/54848575-0b49-4010-974a-1d163d09645e" /><figcaption><i>Figure 3.1. Impression/DAU chart</i></figcaption></figure></div>

### 4. Impression/DEU

**Definition:** Represents the average number of ads a player **views** per day.

**Formula:** &#x20;

- From AdLog records, we have:

$$
\begin{split}
Imp\_{DayX} &= count(AdLog\_{Day=X})
\\\\&= Number~~AdLog~~Record\_{Day = X}
\end{split}
$$

$$
\begin{split}
DEU\_{DayX} &= count(AdLog\_{Day=X}.Player)
\\\\&= NumberPlayerHasAd\~Log\_{Day = X}
\end{split}
$$

- From the two data points above:

$$
\begin{split}
ImpDEU\_{DayX} &= Imp\_{Day=X}/DEU\_{Day=X}
\\\\&= Number~~AdLog~~Record~~DayX/NumberPlayerHasAdDay\~X
\end{split}
$$

- Where:
  - **Day**: Log recording time/time the player first opened the game, depending on the filter selection.
  - **Impression**: Total number of ads in the day.
  - **DEU (Daily Engaged User)**: Number of players who viewed ads in the day.

Example: Figure 4.1 shows that on March 9, 2026, 1,834 players opened the game and viewed at least one ad, meaning each player viewed an average of 52.89 ads.

<div align="center"><figure><img width="1428" height="1004" alt="Image" src="https://github.com/user-attachments/assets/b6837496-52fe-4ef1-ad9f-006aca663726" /><figcaption><i>Figure 4.1. Impression/DEU chart</i></figcaption></figure></div>

### 5. Impression/LEU (Level)

**Definition:** Represents the average number of ads seen by a player at one level.

**Formula:**

- From AdLog records, we have:

$$
\begin{split}
Imp\_{LvX} &= count(AdLog\_{Level=X})
\\\\&= Number~~AdLog~~Record\_{Lv = X}
\end{split}
$$

$$
\begin{split}
LEU\_{LvX} &= count(AdLog\_{Lv=X}.Player)
\\\\&= NumberPlayerHasAd\~Log\_{Lv = X}
\end{split}
$$

- From the two data points above:

$$
\begin{split}
ImpLEU\_{LvX} &= Imp\_{Lv=X}/LEU\_{Level=X}
\\\\&= Number~~AdLog~~Record~~Lv~~X/NumberPlayerHasAd~~Log~~At~~Lv~~X
\end{split}
$$

- Where:
  - **Day**: Log recording time/time the player first opened the game, depending on the filter selection.
  - **Impression**: Total number of ads in the day.
  - **LEU (Level Engaged User)**: Number of players who viewed ads in the day.

**Example**: Figure 5.1 shows that at level 69, 353 players opened the game; on average, each player viewed 9.34 ads upon opening the game.

<div align="center"><figure><img width="1432" height="1077" alt="Image" src="https://github.com/user-attachments/assets/57d3c15a-2c6f-42a7-83be-30d1cd734f39" /><figcaption><i>Figure 5.1. Impression/LAU chart</i></figcaption></figure></div>

### 6. Revenue/DAU (ARPDAU)

**Definition:** Average Revenue Per Daily Active User - is the average advertising revenue per daily active user.

**Formula:**

- From AdLog records, we have:

$$
\begin{split}
Revenue\_{DayX} &= sum(AdLog\_{Day=X}.Revenue)
\\\\&= RevenueOfAdsAtDay\~X
\end{split}
$$

- From the records automatically collected by the RetentionLog SDK (only one record is generated for each player who logs in per day):

$$
\begin{split}
DAU\_{DayX} &= count(RetentionLog\_{Day=X})
\\\\&= Number~~Retention~~Log~~Record\_{Day = X}
\end{split}
$$

- From the two data points above:

$$
\begin{split}
ARPDAU\_{DayX} &= Revenue\_{Day=X}/DAU\_{Day=X}
\\\\&= RevenueOfAdsAtDay~~X/NumberPlayer\~X
\end{split}
$$

- Where:
  - Day: Log recording time/time the player first opened the game, depending on the filter selection.
  - **Impression**: Total number of ads in the day.
  - **DAU (Daily Engaged User)**: Number of players who entered the game in the day.

**Example**: Figure 6.1 shows that on March 9, 2026, with revenue from ad views of 253.19 ($), there were 2,209 active users -> ARPDAU is 0.11 (¢).

<div align="center"><figure><img width="1428" height="998" alt="Image" src="https://github.com/user-attachments/assets/b5621f23-b4df-4632-9d6a-9d6a4eb4b72d" /><figcaption><i>Figure 6.1. ARPDAU diagram</i></figcaption></figure></div>

### 7. eCPM ( Effective cost per thousand impressions)

**Definition:** This is the revenue earned for 1000 ad impressions on an app.

**Significance:** Used to measure campaign performance and maximize value from ad space.

**Formula:**

- From AdLog records, we have:

$$
\begin{split}
Revenue\_{DayX} &= sum(AdLog\_{Day=X}.Revenue)
\\\\&= RevenueOfAdsAtDay\~X
\end{split}
$$

$$
\begin{split}
Imp\_{DayX} &= count(AdLog\_{Day=X})
\\\\&= NumberAdRecordAtDay~~X
\end{split}
$$

- From the two data points above:

$$
\begin{split}
eCPM\_{DayX} &= Revenue\_{Day=X}/Imp\_{Day=X}
\\\\&= RevenueOfAdsAtDay~~X/NumberOfAdAtDay\~X
\end{split}
$$

- Where:
  - Day: log recording time/time the player first opens the game, depending on the filter selection

**Example**: Figure 7.1 shows that on March 9, 2026, with revenue of 253.19 ($), and 96,999 ad impressions, we can calculate the eCPM as 2.61 ($).

<div align="center"><figure><img width="1426" height="997" alt="Image" src="https://github.com/user-attachments/assets/f848b764-6d37-437e-bc8b-e7650a046b3d" /><figcaption><i>Figure 7.1. eCPM diagram</i></figcaption></figure></div>

# 3. InApp

## I. Filters

### 1. Where

- Where the product was purchased in the game

### 2. Product

- The product the player purchased (purchased at different locations (Where)).

## I. Charts

### 1. Total Inapp by Day

The chart shows the amount of Inapp purchases made by players distributed by day, allowing you to track which periods saw the most significant player spending to develop the game.

- Unit: USD

Example: Figure 1.1 shows that on March 9, 2026, players made the most Inapp purchases in the month, with the strongest spending occurring when purchasing `battle.pass`

<div align="center"><figure><img width="1429" height="1030" alt="Image" src="https://github.com/user-attachments/assets/37612024-0d87-451c-829d-5ca6f0af9105" /><figcaption><i>Figure 1.1. Total In-App Purchases by Day Chart</i></figcaption></figure></div>

### 2. ARPDAU by Day

- ARPDAU: Average Revenue Per Daily Active User.
- This graph shows the average in-app revenue per daily active player.
- Formula: ARPDAU = Total Daily Revenue / Total Daily Active Players
- Example: Figure 2.1 shows that on March 8, 2026, the in-app revenue was $19.99, there were 1,182 active users, and the ARPDAU was 0.02¢

<div align="center"><figure><img width="1427" height="998" alt="Image" src="https://github.com/user-attachments/assets/d6a75c2f-b186-4f15-ae0e-2a2395e6f15d" /><figcaption><i>Figure 2.1. ARPDAU chart by day</i></figcaption></figure></div>

### 3. Total In-App Purchases by Level

This chart shows the amount of In-App purchases made by players, distributed by level. It allows you to track which levels see players spending the most to progress in the game.

- Unit: USD

Example: Figure 3.1 shows that players spend the most In-App purchases within the level range of 3-111.

<div align="center"><figure><img width="1425" height="1117" alt="Image" src="https://github.com/user-attachments/assets/d0f52e9a-bcc0-4f10-919e-6a2deac9c1b4" /><figcaption><i>Figure 3.1. Total In-App Purchases Chart by Level</i></figcaption></figure></div>

The slider shows the percentage of in-app purchases made by a particular level compared to the total in-app purchases across all levels (In-app purchase allocation ratio by level).

<div align="center"><figure><img width="620" height="94" alt="Image" src="https://github.com/user-attachments/assets/2158c149-54c6-4734-94f6-f55b9e42cd52" /><figcaption><p><i>Figure 3.2. The slider shows the distribution ratio of Inapp amount by level.</i></p></figcaption></figure></div>

### 4. ARPDAU by Level

- This chart shows the average in-app revenue per active player for each level.
- Formula: ARPDAU by level = Total revenue per level / Total active players per level
- Example: Figure 4.1 shows that at level 51, the in-app revenue is $7.99, there are 223 active users, and the ARPDAU is 0.04¢

<div align="center"><figure><img width="1432" height="1080" alt="Image" src="https://github.com/user-attachments/assets/556906ce-056f-4981-9e20-1539805954fd" /><figcaption><i>Figure 4.1 ARPDAU chart by level</i></figcaption></figure></div>

### 5. Daily ARPPU

- ARPPU: Average revenue per paying user - is a measure used to calculate the average revenue generated by paying users over a given period.
- The graph shows the average revenue per in-app purchaser.
- Calculation formula: ARPPU = Total revenue generated during period X / Total number of in-app purchasers during period X.
- Example: According to Figure 5.1, on March 9, 2026, the revenue was 24.96 ($), and the number of in-app purchasers was 2 -> ARPPU = 12.48 ($).

<div align="center"><figure><img width="1428" height="1000" alt="Image" src="https://github.com/user-attachments/assets/8bb09e2e-5b9b-48c9-989a-78795ecabbde" /><figcaption><i>Figure 5.1. ARPPU chart by day</i></figcaption></figure></div>

### 6. Daily Paying Rate

- This chart shows the ratio of in-app purchases to the total number of active players daily. This provides detailed information about the game's monetization efficiency and engagement level.
- Formula: Paying Rate = Total number of in-app purchases in a given period X \* 100 / Total number of active users in the same period X.
- Example: In Figure 6.1 on March 9, 2026, there were 2 in-app purchases, and the number of active users was 1,235 -> The Paying Rate would be 0.16%.

<div align="center"><figure><img width="1429" height="999" alt="Image" src="https://github.com/user-attachments/assets/518d5bc0-023a-4bff-9053-7c8fbe2d5265" /><figcaption><i>Figure 6.1 Daily Paying Rate Chart</i></figcaption></figure></div>

# 4. Funnel

**Nothing yet**

# B. Event source connector config

# 1. Create a game
- From Sidebar pannel, choose `Overview > Games` to go to game setting page.
<div align="center"><figure><img width="1716" height="562" alt="image" src="https://github.com/user-attachments/assets/126d060b-b57a-4f23-bde2-edde08c75a7c" />
</figure></div>

- Click to `Add New Game` to open add game dialog. At this dialog, you need provide the Google Store URL of the game, which get from Google Store page.

**Example**: Getting the URL of Screw Puzzel game, you just need get the URL with only ID of it, other query variable is no need.

The Google Store URL of this game is: `https://play.google.com/store/apps/details?id=com.apollo.screw.it.out`

<div align="center"><img width="1702" height="609" alt="image" src="https://github.com/user-attachments/assets/27cb9aaf-91cf-4636-b3fc-91b8b56e0909" /></figure></div>

- After getting the correct Google Store URL of the game, input it into the dialog then click `Search` button to find the game information. If found a game and make sure the information is corrected, click `Add game` button to register the new game.

<div align="center"><img width="1711" height="880" alt="image" src="https://github.com/user-attachments/assets/843ef43f-f1ee-4ff4-9d46-de39a2b6ceaa" />
</figure></div>

- The new game is now registered. You can continue to create a connector to link the game with a datasource.

# 2. Create a connector
- From Sidebar pannel, choose `Overview > Connectors` to go to connector setting page.

At this page, you will see some elements related to config the connectors:
* Connector/Dataset: Name of the connector (1) and datasets related to the this connector (2). Each connector will have no or many datasets, depend on the number BQ datasets that you have linked to this connector.
* Linked App: Show the games that linked or not with the corresponding dataset (3). You can link/unlink when using the button at section (4).

**Example**: Dataset `krOQeLHqTrW8cv8PxgNNFA` (first dataset) is not linked with any game. Dataset `analytics_491484878` is linked with `Block Out: Jam Puzzle` game.

<div align="center"><img width="1715" height="806" alt="image" src="https://github.com/user-attachments/assets/ccb66826-243d-4e42-9d59-b0e4ec57dd18" />
</figure></div>

- To create new connector, click to the button `Add New Connector` to open the create dialog. Input your Connector name then click `Save` button.
<div align="center"><img width="512" height="447" alt="image" src="https://github.com/user-attachments/assets/34dcb35d-19d8-4af6-9b5f-b2ef569feab4" /></figure></div>

- After the new connector is created, you can see a new information about Google Service Account (GSA). You need to register this GSA with BQ Dataset that containing your data (Following Step in the `Requirements`)
- You can click copy button to copy this GSA (If copy button is not working, please you Inspect tool of browser to open HTML and copy it in manual)
- You can also click button information of existing connector to open this dialog to see information of the connector and copy its GSA
<div align="center"><img width="1447" height="853" alt="image" src="https://github.com/user-attachments/assets/2f9dbc48-56ee-4a74-8e15-e1e7ac8ec877" /></figure></div>

> [!WARNING]  
> Because we haven't yet developed a feature to delete created connectors, please only create new connectors if you truly want to separate games and not share connectors. From a management perspective, separating connectors makes them easier to identify and avoid errors; however, from a technical standpoint, creating a new connector requires creating new resources, leading to wasted resources.

> [!TIP]
> If possible, reuse existing connectors to reduce unnecessary operations and resources.

# 3. Link a game
> [!NOTE]  
> In this section, I will guide you through setting up and registering a connector with BQ, and then linking your game to the dataset available on BQ. I will utilize an existing connector to optimize resources. If you want to use a new connector, you can create one and follow the same steps.
> I will reuse `Main connector` in this guide.

- From Step [2. Create a connector](#2-create-a-connector), you have retrieved the GSA of the connector (`sa-26ea0ae294881f12@pixiu-insight.iam.gserviceaccount.com`). You need to register this GSA with BQ to get BQ datasets.
- To register GSA with your BQ, open Google Cloud Console then go to `IAM & Admin > IAM` service.
- Click `+ Grant access` to grand your GSA with 3 role:
  -  BigQuery Data Viewer
  -  BigQuery Job User
  -  BigQuery Read Session User

- After register successfully, your GSA may show as the image below.

<div align="center"><img width="1686" height="1236" alt="image" src="https://github.com/user-attachments/assets/2c3c76a6-3c48-4a05-973c-b51c24234c5d" /></figure></div>

> [!NOTE]  
> You may need to wait some minute to GSA is effected.

- Next, go back to your Connectors screen, click button reload to get the new BQ datasets. You may wait a minus between each refresh click.
<div align="center"><img width="1432" height="701" alt="image" src="https://github.com/user-attachments/assets/ca5405f7-54d8-4552-8a07-1dcf52effeda" /></figure></div>

- When the new dataset is shown under the corressponding connector, check the dataset name must be the same at the BQ console.
**Example**: Dataset `analytics_490531765` has the name equal with the name at the BQ service.

<img width="1432" height="701" alt="image" src="https://github.com/user-attachments/assets/d8fac0a9-89be-4f30-8aee-3b7ef071a93d" />
<img width="652" height="565" alt="image" src="https://github.com/user-attachments/assets/8fdde93f-f97e-45f6-bead-56f097debe62" />

- Now, when the new dataset is corrected, you can link your game with your dataset by click button `Link to app`
<img width="539" height="240" alt="image" src="https://github.com/user-attachments/assets/908c93db-4763-4b7d-905d-a904b8248fe6" />

- After linked, you now successfully config the datasource for your game.
