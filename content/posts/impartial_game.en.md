---
title: Impartial Game
date: 2022-08-05T15:51:00+08:00
tags: ["SG Theory", "Nim Game"]
categories: ["Game Theory"]
math: true
description: 'Though named "impartial", its result has been determined before the beginning'
---

An impartial game is a 2-players game where players take turns to move that satisfies the following constraints:

- Impartial: A player's optional move is restricted by the current game status only and not by his/her identity.
- Finite: The game will end after finite rounds.
- Non-random: For any given move under a certain game status, there will be a predictable and unique consequence.
- Perfect-information: Both players know all information about the current game status.

"Impartial" doesn't mean that the game is fair, but that players' optional move isn't decided by their identity. For example, go isn't an impartial game, since whether a player can place white stones or black stones is determined by their identity. Games like nim game are impartial game, however, because the rules for both players to remove stones is identical.

Impartial games are definitely unfair, that is, when one of the players moves in a certain way, he/she will definitely win and his/her opponent will definitely lose. The consequence is completely determined by who goes first if both players are smart enough to move optimally. Zermelo's theorem points out that all 2-players turn-based games that are finite, non-random and with perfect information are unfair. For impartial games, we mostly discuss which one of the players can win.

## Win-status and lose-status

Let's call a game status win-status if under such status the player who moves first can win, and call it lose-situtation elsewhere. For any game status, its next-statuses are all statuses that can be obtained by a single move of a single player from the current status.

It can be shown that a status is win-status if and only if at least one of its next-statuses is lose-status, and it is lose-status if and only if all of its next-statuses are win-status. This recursive defination means any game status that has no next-statuses should be defined to be win- or lose-status in advance. Generally, a status with no next-statuses is a lose-status.

To find who will win in an impartial game is to find the initial game status is win-status or lose-status.

## Nim game

Nim game is a classic impartial game. A nim game contains $n$ piles of items, and players take turns selecting an arbitrary pile and removing at least one item from it. The player in whose turn no item remain loses the game. A pile in a nim game is called a nim pile.

There is a conclusion that a nim game containing $n$ nim piles $\left\\{a_1, a_2, \ldots, a_n\right\\}$ and another nim game with a single nim pile of $\bigoplus\limits_{1\le i \le n}a_i$ items will have the same winner. Since the first to move wins in a one-pile nim game if and only if that pile contains at least one item, for any nim game $\left\\{a_1, a_2, \ldots, a_n\right\\}$ we have that if $\bigoplus\limits_{1\le i \le n}a_i > 0$ the first mover wins and if $\bigoplus\limits_{1\le i \le n}a_i =  0$ the second mover wins.

So how to prove the mentioned conclusion? The provement consists of four points:

1. A lose-status $\\{0, 0, \ldots, 0\\}$ has $\bigoplus\limits_{1\le i \le n}a_i = 0$.
2. For any status that has $\bigoplus\limits_{1\le i \le n}a_i = 0$, none of its next-statuses keeps $\bigoplus\limits_{1\le i \le n}a_i = 0$.
3. For any status that has $\bigoplus\limits_{1\le i \le n}a_i \neq 0$, at least one of its next-statuses has $\bigoplus\limits_{1\le i \le n}a_i = 0$. Let some pile has $x$ items, we only need to make this pile $x \oplus \bigoplus\limits_{1\le i \le n}a_i$. It can be shown that there always exist at one pile such that $x \oplus \bigoplus\limits_{1\le i \le n}a_i < x$.
4. After infinite moves, the game ends up with a non-draw result.

## Sprague–Grundy theorem

Sprague–Grundy theorem, a.k.a. SG theorem, is about how to transfer a impartial game to a nim game:

Define a function $\operatorname{SG}(X)$ on a game status $X$:

- If $X$ has no next-status, then $\operatorname{SG}(X)=0$. It is notable that all $X$ that has no next-status must be a lose-status.
- Else, let the set of $X$'s next-statuses be $S$, then $\operatorname{SG}(X)=\operatorname{mex}\\{\mathrm{SG}(Y) | Y \in S\\}$.

$X$ is a win-status if $\operatorname{SG}(X)\neq 0$, and a lose-status if $\operatorname{SG}(X)=0$.

According to the $\operatorname{mex}$ function's defination, for all statuses $X$ that $\operatorname{SG}(X)\neq 0$ at least one $Y$ of its next-statuses has $\operatorname{SG}(Y)=0$, and for all statuses $X$ that $\operatorname{SG}(X)= 0$ all $Y$ of its next-statuses has $\operatorname{SG}(Y)\neq 0$, so the above statement holds.

There are many other functions like $\operatorname{SG}(X)=1-\min\\{\mathrm{SG}(Y) | Y \in S\\}$ that work as well as $\operatorname{mex}$ does. Why do we choose $\operatorname{mex}$ rather than them? It is because only the $\operatorname{mex}$ function has the following effect:

If the game is consisted by $n$ subgames, whose statuses are $P_1, P_2, \ldots, P_n$, then for the status $X$ of the total game, $\operatorname{SG}(X) = \bigoplus\limits_{1\le i \le n}\operatorname{SG}(P_i)$ holds. If a game consists of some subgames, players can arbitrarily choose exactly one of the subgames on each turn and move on that subgame.

Provement: It can be shown that $X$ has at least one next-status $Y$ such that $\operatorname{SG}(Y)=i$ for every integer $i$ in $[0,\operatorname{SG}(X))$, and has no next-status whose SG value is $\operatorname{SG}(X)$. For the predetermined winner, if the opponent moved in such a way that decreases the SG value of a subgame, then he/she can move in such a way that offsets the oppponent's effect on the xor-sum of the SG value of the subgames, just like in a nim game; if the opponent increased the SG value of a subgame rather, then he/she can change the SG value of that subgame back, which also keeps the xor-sum of the SG value the subgames.
