---
title: Calculating Fibonaci numbers with .Net 4.0’s BigInteger
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-26T16:13:23+00:00
ID: 769
url: /index.php/desktopdev/mstech/calculating-fibonaci-numbers-with-net-4/
views:
  - 3254
categories:
  - Microsoft Technologies
tags:
  - biginteger
  - vb.net

---
In .Net 4.0 we now have a BigInteger to play with.

This is how I used to do it as can be seen [LTD Puzzle 5: Calculating the Fibonacci Sequence][1].

```vbnet
Module Module1
 
    Sub Main()
        Dim x As Double
        Console.WriteLine("Give the number to create")
        Dim input As String
        input = Console.ReadLine()
        If Double.TryParse(input, x) AndAlso x &gt; 2 Then
            Dim y As Double
            Dim oldz As Double
            Dim z As Double = 0
            Dim newz As Double = 1
            Console.WriteLine(z & ",")
            Console.WriteLine(newz & ",")
            For y = 1 To x
                Try
                    oldz = newz
                    newz += z
                    Console.WriteLine(y & ". " & newz & ",")
                    z = oldz
                Catch ex As Exception
                    Console.WriteLine("end reached")
                    Exit For
                End Try
            Next
        End If
        Console.ReadLine()
    End Sub
 
End Module```
Which runs out of bytes at around 1475 when using the double. 

There is a way to do this with string if you don&#8217;t have .Net 4.0.

```vbnet
Imports System.Numerics

Module Module1

    Sub Main()
        Dim x As Double
        Console.WriteLine("Give the number to create")
        Dim input As String
        input = Console.ReadLine()
        If Double.TryParse(input, x) AndAlso x &gt; 2 Then
            Dim y As BigInteger
            Dim oldz As BigInteger
            Dim z As BigInteger = 0
            Dim newz As BigInteger = 1
            Console.WriteLine(z.ToString & ",")
            Console.WriteLine(newz.ToString & ",")
            For y = 1 To CType(x, BigInteger)
                Try
                    oldz = newz
                    newz += z
                    Console.WriteLine(y.ToString & ". " & newz.ToString & ",")
                    z = oldz
                Catch ex As Exception
                    Console.WriteLine("end reached")
                    Exit For
                End Try
            Next
        End If
        Console.ReadLine()
    End Sub

End Module

```
To make the above work I had to reference System.Numerics and do an Imports. And I had to add a few ToStrings to.

I tried it with 10000 which gives this as a result.

> 5443837311356528133873426099375038013538918455469596702624771584120858286
  
> 56223490170830515479389605411738226759780263173843595847511162414391747026429591
  
> 69925586334117906063048089793531476108466259072759367899150677960088306597966641
  
> 96582493772180038144115884104248099798469648737533718002816376331778192794110136
  
> 92627509795098007135967180238147106699126442147752544785876745689638080029622651
  
> 33111359929762726679441400101575800043510777465935805362502461707918059226414679
  
> 00569075232189586814236784959388075642348375438634263963597073375626009896246266
  
> 87461120417398194048750624437098686543156268471861956201461266422327118150403670
  
> 18825205314845875817193533529827837800351902529239517836689467661917953884712441
  
> 02846393544948461445077876252952096188759727288922076853739647586954315917243453
  
> 71936112637439263373130058961672480517379863063681150030883967495871026195246313
  
> 52447499505204198305187168321623283859794627245919771454628218399695789223798912
  
> 19943177546970521613108109655995063829726125384824200789710905475402843814961193
  
> 04650618661701229832889643527337507927860694447618535251444210779280459799045612
  
> 98129423809156055033032338919609162236698759922782923191896688017718575555520994
  
> 65332012844650237115371514174929091310489720345557750719664542523286202201950609
  
> 14835852238827110167084330511699421157751512555102516559318881640483441295570388
  
> 25477521111577395780115868397072602565614824956460538700280331311861485399805397
  
> 03155572752969339958607985038158144627643385882852953580342485084542644647168153
  
> 10015331804795674363968156533261525095711274804119281960221488491482843891241785
  
> 20174507305538928717857923509417743383331506898239354421988805429332440371194867
  
> 21554357654856549913451927109891980266518456492782782721295764924023550759555820
  
> 56475693653948733176590002063731265706435097094826497100387335174777134033190281
  
> 05575667931789470024118803094604034362953471997461392274791549730356412633074230
  
> 82405199999610154978466734045832685296038830112076562924599813625165234709396304
  
> 97340464451063653041636308236692422577614682884617918432247934344060799178833606
  
> 76846711185597501

I won&#8217;t even try to read that. And it&#8217;s pretty darn quick too. I guess you could easily go higher. I guess the sky is the limit.

Here is the version using strings as made by [George Mastros][2]. 

```vbnet
Sub Main()
        Dim Number As Long
        Console.WriteLine("Give the number to create")
        Dim input As String
        input = Console.ReadLine()

        If Not IsNumeric(input & ".0e0") Then
            Console.WriteLine("The number to calculate must be a valid integer greater than 2.")
            Exit Sub
        End If

        Number = CLng(input)

        FibonacciSequence(Number)
        Console.ReadLine()
        End Sub

    Private Sub FibonacciSequence(ByVal NumberToCalculate As Long)

        Dim arTemp() As String

        Dim i As Long

        ReDim arTemp(CInt(NumberToCalculate - 1))

        arTemp(0) = "0"
        arTemp(1) = "1"

        For i = 2 To NumberToCalculate - 1
            arTemp(CInt(i)) = AddString(arTemp(CInt(i - 2)), arTemp(CInt(i - 1)))
        Next i

        Console.WriteLine(Join(arTemp, ","))

    End Sub

    Private Function AddString(ByVal String1 As String, ByVal String2 As String) As String

        Dim i As Long
        Dim Output() As String
        Dim CarryTheOne As Long
        Dim Digit1 As Long
        Dim Digit2 As Long

        If Len(String1) &lt; Len(String2) Then
            String1 = Replace(Right(Space(Len(String2)) & String1, Len(String2)), " ", "0")
        Else
            String2 = Replace(Right(Space(Len(String1)) & String2, Len(String1)), " ", "0")
        End If

        ReDim Output(Len(String1))
        CarryTheOne = 0
        For i = Len(String1) To 1 Step -1
            Digit1 = CLng(Mid(String1, CInt(i), 1))
            Digit2 = CLng(Mid(String2, CInt(i), 1))

            Output(CInt(i)) = CStr((Digit1 + Digit2 + CarryTheOne) Mod 10)
            If Digit1 + Digit2 + CarryTheOne &gt; 9 Then
                CarryTheOne = 1
            Else
                CarryTheOne = 0
            End If
        Next

        If CarryTheOne = 1 Then
            Output(0) = "1"
        End If
        AddString = Join(Output, "")

    End Function```

 [1]: http://forum.ltd.local/viewtopic.php?f=102&t=2055
 [2]: http://forum.ltd.local/viewtopic.php?f=102&t=2055#p11845