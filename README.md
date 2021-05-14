# Chess-Analysis
In this project, I try to analyse chess games from lichess.org - there is free data available on the website, which I clean, convert to a usable CSV file and perform analysis on and build visualizations. The project is divided into two parts:

1. Preprocessing the raw data and preparation of the CSV file.
2. Analysing the data and constructing vizualisations based on the collected data.

The file I used for this analysis was of all the games played January 2013 since it was the smallest available file. However the code can be applied to any month's file from the lichess database. The file had the names of the type of games, ELO ratings of both players, rating difference, the opening style, termination reason and the PGN of the game (few had analysis attached). PGN is a shorthand representation of the entire game.

In this readme, I mainly illustrate the results of step 2, the code can be found for both steps here.

Exploratory Analysis
----
1. **The different types of games played over the period:** Chess has 3 formats based on length: bullet games (1 or 2 minutes long), blitz games (3 to 14 minutes) and standard (more than 15 minutes).

![mode distribution](https://user-images.githubusercontent.com/39181870/118170798-29640d80-b448-11eb-9604-f71923963b9f.png)

Blitz games are slightly more popular, due to the short attention span of internet chess players. This analysis shows a greater popularity in faster games in more recent game files.

2. **The outcomes of all the games in this period:** Comparing the  sides which came out victorious.

![result distribution](https://user-images.githubusercontent.com/39181870/118170803-29fca400-b448-11eb-91b7-263c78ac4b96.png)

Since white plays first, white always has a small advantage over black. This is clear from the above image. The few draws implies that online chess players play out drawn positions until one of the sides resigns or blunders.

3. **Game termination types:** The game ends in 4 ways - normally (Win/Loss or Draw), time forfeit, cheating or abandonment.

![termination distribution](https://user-images.githubusercontent.com/39181870/118170805-2a953a80-b448-11eb-9b5b-6d51f9755b34.png)

In this visual, there is no data for the last two types of termination, but with the more recent data files, they start to appear. It is worth to note to prevent future errors.

4. **Rating distribution:**

![rating distribution](https://user-images.githubusercontent.com/39181870/118173954-dd1acc80-b44b-11eb-8278-31ad602ca04d.png)

![rating stats](https://user-images.githubusercontent.com/39181870/118173912-cffddd80-b44b-11eb-83fa-480bbdc94bdb.png)

![rating type](https://user-images.githubusercontent.com/39181870/118174311-4995cb80-b44c-11eb-80f0-023c86a67c97.png)

The distribution above is right skewed, as seen by the positive skew given by the analysis. There are on average more beginners learning how to play chess in comparision to experts in this dataset. This skew has most likely expanded since with the growing popularity of chess. With progress in time, the percentage of master and advanced players would expect to rise.

![rating difference distribution](https://user-images.githubusercontent.com/39181870/118174521-8e216700-b44c-11eb-8e07-98b9bfa366c5.png)

![rating difference stats](https://user-images.githubusercontent.com/39181870/118174737-d2146c00-b44c-11eb-8c28-ce31204c5e64.png)

The distribution is platykurtic, since the excess kurotsis is negative; this means that we don't expect many extreme events near the tails of the distribution (no games with rating difference around -1000 and 1000). This follows Lichess' default rating difference search which is 500, and most people will either keep it that way or decrease it to somewhere around 50.

5. **Chess game length distribution:**

![game length distribution](https://user-images.githubusercontent.com/39181870/118175082-42bb8880-b44d-11eb-904d-0c4cc443a102.png)

The mean and median of chess game lengths seem to be the mid 30s range, somewhat lower than the expected average of ~40 moves per game. This is maybe because this dataset includes speed chess (games with time control less than 5 minutes), so players may flag more often than a game that is played over the board. Something interesting to note is the number of games that are 2 moves long. This is probably because some people will leave their games seeing a high rating difference, thus resulting in a time forfeit.

![forfeit stats](https://user-images.githubusercontent.com/39181870/118177335-308f1980-b450-11eb-8c8d-83f28213f2c7.png)

We can see that the previous hypothesis that most of the early forfeits are due to time forfeits. Another interesting point is that the average number of moves for a game that ends in time forfeit is larger than a normal game. This might be due to people not resigning in bullet games to flag their opponents (eventually someone loses on time) or selection bias, where people who resign will most likely resign somewhere in the middlegame (move 25 to 35) as opposed to later in the game when all hope is lost.

![game length distribution with different modes](https://user-images.githubusercontent.com/39181870/118208202-f50e4280-b483-11eb-904f-0543270f5fce.png)

It is important to note that there is an outlier for how many games end in the first 2 moves is because of correspondence games. This could be so if a person decides not to play in correspondence, they lose on time. Apart from that, the distribution looks pretty normal, with no noticeable outliers.

![game length distribution with different ratings](https://user-images.githubusercontent.com/39181870/118210282-f04b8d80-b487-11eb-8b33-805682011870.png)

Master players tend to have shorter games, and intermediate/advanced players have the longest games. This can be because master players know when a position is lost sooner than other players, while beginner players may resign earlier when they blunder or due to 4 move checkmates or similar gambits. Intermediate/advanced players may continue playing the game waiting for their opponent to blunder at some point.

This concludes the preliminary analysis of the raw data.

Deeper Evaluations
------------------

Most data analyses are done with the attempt to answer a question. At this point, I try to answer the following questions:

1. How many times does a piece move in a game?

If one were to guess, the pawns would probably be the best guess, because there are 16 of them on the board. The queen is also a good candidate as it can move across the board.

![chess piece move number](https://user-images.githubusercontent.com/39181870/118212488-60f3a980-b48a-11eb-99ab-f7b42583707f.png)

![chess piece move percentage](https://user-images.githubusercontent.com/39181870/118212459-5fc27c80-b48a-11eb-9ff9-8c6d45349bde.png)

![chess piece move total number](https://user-images.githubusercontent.com/39181870/118212478-60f3a980-b48a-11eb-8a6b-4d804dffd397.png)

As expected, pawn moves dominate chess games, of course, since there are at most 16 pawns on the board, compared to the at most 4 for any other piece. The singular queen also moves around quite alot, but that isn't surprising, since there are instances of pawn promotion beside the fact that it can move across the board and is the 2nd most valuable piece on the board.

2. What is the probability a specific chess piece moves on a given move?

The obvious next step after finding how many times a chess piece moves, is to analyse at what point in the game it moves. In the openings and end games can be assumed to be dominant, while the middle game stages have more moves of the minor pieces battling it out probably. The dominance of kings in the end game can also be expected.

![move probability all](https://user-images.githubusercontent.com/39181870/118213582-de6be980-b48b-11eb-8657-74035f18464c.png)

![move probability by level](https://user-images.githubusercontent.com/39181870/118234453-f6a22f80-b4b0-11eb-83f4-c5170888a8a1.png)

![pawn movement](https://user-images.githubusercontent.com/39181870/118236080-43870580-b4b3-11eb-9c2b-ec8812fe459d.png)

![knight movement](https://user-images.githubusercontent.com/39181870/118236074-41bd4200-b4b3-11eb-9814-4f9fd1b27776.png)

![bishop movement](https://user-images.githubusercontent.com/39181870/118236078-42ee6f00-b4b3-11eb-965a-b837e4195eb6.png)

![rook movement](https://user-images.githubusercontent.com/39181870/118236083-441f9c00-b4b3-11eb-9448-939987ab3816.png)

![queen movement](https://user-images.githubusercontent.com/39181870/118236311-8943ce00-b4b3-11eb-9161-df2b94e3d024.png)

![king movement](https://user-images.githubusercontent.com/39181870/118236473-bf814d80-b4b3-11eb-9e1f-6f3183b653d7.png)

![castling movement](https://user-images.githubusercontent.com/39181870/118236904-49c9b180-b4b4-11eb-8200-78795631939f.png)

The interesting things to notice are that beginner players move their queen way too often in the opening and don't castle as much. Another interesting thing to see is more rook movements in the endgame in comparision to beginners. This could be because master players do not trade rooks as often as beginners.

3. What is the probability a piece lands on a certain square?

The final question to answer, is to analyse the probabilities of pieces landing on squares. 

![raw count](https://user-images.githubusercontent.com/39181870/118241340-a7143180-b4b9-11eb-8dcf-8dc1783addcb.png)

![percentage probability](https://user-images.githubusercontent.com/39181870/118241347-a8455e80-b4b9-11eb-95b5-9c322941570b.png)

![pawn movement to square](https://user-images.githubusercontent.com/39181870/118241398-b98e6b00-b4b9-11eb-8f24-a9b49cf8b405.png)

![king and rook](https://user-images.githubusercontent.com/39181870/118241847-3f121b00-b4ba-11eb-969d-1a4014223952.png)

For reference, I've included an example of using the castling method here. Including castling heavily skews the data to kingside castling squares, so I don't include them in my later analysis. It's worth noting that many games will just end with the king ending on g1/g8 (after kingside castling) and the rook not moving from its square on f1/f8.

The main lesson from these images is that pieces are always trying control the center. This reinforces the age old advice of trying to control the central squares. For the rest of this combined data (where the movement of white vs black is not separated), the data is a lot to digest and very unclear, and so in the future sections, I'll be splitting the movement up for into two graphs, one for white and one for black, and performing analysis on the much simpler data. Splitting the colours gives a better insight rather than averaging the data for black and white.

![white piece probability](https://user-images.githubusercontent.com/39181870/118241946-60730700-b4ba-11eb-9306-539ba6447fdb.png)

![black piece probability](https://user-images.githubusercontent.com/39181870/118241937-5ea94380-b4ba-11eb-92a6-294fe65f06bc.png)

![black and white](https://user-images.githubusercontent.com/39181870/118241950-610b9d80-b4ba-11eb-9317-f41ad3890eb6.png)

Splitting the colors up makes it alot easier to see the differences between the play of different colors, due to the difference in positions in black and white.

- **Pawns:** Most players with the white pieces will move e4 at some point, whereas black plays its counterpart (e5) far less. This maybe due to the popularity of alternate openings such as the Sicilian and Queen's Gambit (Declined), the black e pawn will perch itself on e6. White also pushes the pawn to the 5th rank far more than black does, implying that white has an immediate space advantage due to the start.
- **Bishop:** Bishop to c4 is extremely common for white, whereas its counterpart (Bishop to c5) by black is quite uncommon, with Bishop to e7 being the preferred choice for black. This may due to differences in development; white wants to put their light-squared bishop in an attacking position, whereas black would like to first play solid and equalize before launching a counter-attack. Black also tends to put their bishop on b7/g7 to control the long diagonal—more often than white, supporting the counter-attacking nature of Black. Black also plays b6/g6 more often than white to fianchetto their bishop).
- **Knight:** Black plays its knight to the 7th rank more often than white, which can be explained by the fact white will oftentimes decide to push its d/e pawn to the 5th rank, gaining space and attacking the knight on the 6th rank, forcing it to retreat. For both sides though, knight moves to the c3/f3/c6/f6 are by far the most common (>50%).
- **Rook:** Surprisingly, rooks played on the queenside are more common than towards the center, with Rb8/Rc8 being played more often than Rd8/Re8 for black, and the counterpart squares for white are also close.
- **Queen:** Very similar distribution for the white and black player except Qc7 is played a bit more often than Qc2 (Qc7 in the Sicilian is very common). Qd5 and Qa5 are also very common moves for black, perhaps suggesting some type of Scandanavian setup being heavily played in this data.
- **King:** Pretty much the same. Most king moves are towards the center.

Lastly, it maybe useful to visualize which squares pieces move to for differently rated players.

![movement by rating](https://user-images.githubusercontent.com/39181870/118242339-db3c2200-b4ba-11eb-914b-c841cc180186.png)

It seems that master players seem to play on the long diagonals a lot more than beginner players; f3 is a popular square for beginners to put their queen (probably after black plays Bg4, Bxf3) whereas e2 is a far more popular square for master players to put their queen. Beginner players seem to move Ke2 very often, implying that many don't castle. 

I hope you learned something and will start/continue playing chess. I have recently revisited it, and I have obtained great pleasure to learn and improve. I hope intermediate/advanced players enjoyed these visualizations.
