---
title: Creating a Matrix Infographic in SSRS
author: Koen Verbeeck
type: post
date: 2014-07-11T11:16:09+00:00
url: /index.php/datamgmt/ssrs/creating-a-matrix-infographic-in-ssrs/
views:
  - 13211
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS
tags:
  - infographic
  - matrix
  - ssrs
  - syndicated
  - world cup

---
With “Matrix Infographic” I simply mean those infographics FiveThirtyEight ([site][1] | [Twitter][2]), the website of statistic revelation Nate Silver, uses to display forecasts of World cup matches. An example:

[<img class="alignnone size-medium wp-image-2830" src="/wp-content/uploads/2014/07/worldcup_matrixgraph2-300x296.png" alt="worldcup_matrixgraph2" width="300" height="296" srcset="/wp-content/uploads/2014/07/worldcup_matrixgraph2-300x296.png 300w, /wp-content/uploads/2014/07/worldcup_matrixgraph2.png 576w" sizes="(max-width: 300px) 100vw, 300px" />][3]

I was wondering if something similar could be built using SSRS. And yes, it is possible and it isn’t that hard. My starting point is a 10 by 10 matrix I downloaded from a [blog post][4] by Jason Thomas ([blog][5] | [twitter][6]). He uses this matrix in his 24 hours of PASS session “DataViz You Thought You Could NOT Do with SSRS”, which is an [absolute must-watch][7].

Each cell has a unique number and we start with 1 in the left bottom corner and we end with 100 in the top right corner. The font is set extra small, so that the column that contains 100 is not resized because of the 3 digits.

[<img class="alignnone size-full wp-image-2831" src="/wp-content/uploads/2014/07/emptyMatrix.png" alt="emptyMatrix" width="296" height="271" />][8]

The source query (complicated right?):

<pre>SELECT
	 BottomStart	= 0 -- England wins
	,BottomEnd		= 37
	,MiddleStart	= 38 -- Tie
	,MiddleEnd		= 66
	,TopStart		= 67 -- Uruguay wins
	,TopEnds		= 100;</pre>

So we have 3 ranges: bottom, middle and top.

The colors of the matrix are assigned according to the values in the source query. Now, we’re not going to create 100 expressions, so we select all of the cells of the matrix by clicking on one cell in the corner, holding shift and then clicking on the opposite corner.

[<img class="alignnone size-full wp-image-2826" src="/wp-content/uploads/2014/07/emptyMatrix_selected.png" alt="emptyMatrix_selected" width="285" height="276" />][9]

Now we can create one single expression for all of the cells using the **Me** placeholder.

<pre>=iif(Me.Value &lt;= Fields!BottomEnd.Value,"#CD2027",iif(Me.Value &gt;= Fields!MiddleStart.Value And Me.Value &lt;= Fields!MiddleEnd.Value,"#DEDEDF","#9FC9EB"))</pre>

The hexadecimal values are the colors I extracted from the FiveThirtyEight tweet using a [color picker][10]. When we run the report we get our result:

[<img class="alignnone size-full wp-image-2827" src="/wp-content/uploads/2014/07/matrixfinished.png" alt="matrixfinished" width="248" height="243" />][11]

Pretty easy right? If you want the borders smaller or thicker, you can play with the border width.

[<img class="alignnone size-full wp-image-2828" src="/wp-content/uploads/2014/07/matrixfinished_borders.png" alt="matrixfinished_borders" width="252" height="257" />][12]

The only thing that is not possible is all those fancy labels. Sure you can use textboxes in SSRS, but SSRS kind of hates it when objects overlap. And drawing arrows is pretty impossible, unless you dive deeply into custom code.

If you want to learn more about matrix infographics, check out the 24HOP session by Jason Thomas I mentioned earlier.

 [1]: http://fivethirtyeight.com/
 [2]: https://twitter.com/FiveThirtyEight
 [3]: /wp-content/uploads/2014/07/worldcup_matrixgraph2.png
 [4]: http://www.sqljason.com/2014/02/download-link-for-my-24-hop-session.html
 [5]: http://www.sqljason.com/
 [6]: https://twitter.com/SQLJason
 [7]: http://www.sqlpass.org/bac/2014/Sessions/SneakPeeks/Details.aspx?sid=5907
 [8]: /wp-content/uploads/2014/07/emptyMatrix.png
 [9]: /wp-content/uploads/2014/07/emptyMatrix_selected.png
 [10]: http://stephanieevergreen.com/choosing-a-color-picking-tool/
 [11]: /wp-content/uploads/2014/07/matrixfinished.png
 [12]: /wp-content/uploads/2014/07/matrixfinished_borders.png