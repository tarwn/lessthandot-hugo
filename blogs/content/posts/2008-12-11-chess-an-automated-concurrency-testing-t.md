---
title: 'CHESS: An Automated Concurrency Testing Tool'
author: SQLDenis
type: post
date: 2008-12-11T18:29:08+00:00
url: /index.php/desktopdev/mstech/chess-an-automated-concurrency-testing-t/
views:
  - 10681
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - chess
  - concurrency
  - ms research
  - parallel computing
  - programming
  - rise
  - software engineering research
  - testing
  - tools

---
CHESS is an automated tool from Microsoft Research for finding errors in multithreaded software by systematic exploration of thread schedules. It finds errors, such as data-races, deadlocks, hangs, and data-corruption induced access violations, that are extremely hard to find with current testing tools. Once CHESS locates an error, it provides a fully repeatable execution of the program leading to the error, thus greatly aiding the debugging process. In addition, CHESS provides a valuable and novel notion of test coverage suitable for multithreaded programs. CHESS can use existing concurrent test cases and is therefore easy to deploy. Both developers and testers should find CHESS useful.

More on Channel 9 (including video) http://channel9.msdn.com/shows/Going+Deep/CHESS-An-Automated-Concurrency-Testing-Tool/