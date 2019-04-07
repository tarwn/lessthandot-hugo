---
title: Always Double Check Your Work
author: chaospandion
type: post
date: 2010-06-29T19:32:13+00:00
url: /index.php/desktopdev/mstech/always-double-check-your-work/
views:
  - 9316
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c# .net bug'

---
I just finished a 2 session bug hunt and I feel quite foolish. See if you can spot the bug.

**Algorithm**

The abstract operation IsWordChar takes an integer parameter e and performs the following: 

  1. If e == –1 or e == InputLength, return false.
  2. Let c be the character Input[e].
  3. <span>If c is one of the sixty-three characters below, return true.</span>
              
    _
              
    a b c d e f g h i j k l m n o p q r s t u v w x y z
              
    A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
              
    0 1 2 3 4 5 6 7 8 9 _
              
_ 
  4. Return false.

**Code**

<pre>private Expression<IsWordChar> CreateIsWordCharExpression()
{
    var e = Expression.Parameter(typeof(int), "e");
    var c = Expression.Variable(typeof(char), "c");
    var returnLabel = Expression.Label(Expression.Label(typeof(bool)), _falseConstant);
    var lambda = Expression.Lambda<IsWordChar>(
        Expression.Block(
            new[] { c },
            Expression.IfThen(
                Expression.OrElse(
                    Expression.Equal(e, Expression.Constant(-1)),
                    Expression.Equal(e, _inputLengthVar)
                ),
                Expression.Return(returnLabel.Target, _falseConstant)
            ),
            Expression.Assign(c, Expression.MakeIndex(_str, _stringCharsPropertyInfo, new[] { e })),
            Expression.IfThenElse(
                Expression.OrElse(
                    Expression.OrElse(
                        Expression.OrElse(
                            Expression.AndAlso(
                                Expression.GreaterThanOrEqual(c, Expression.Constant('a')),
                                Expression.LessThanOrEqual(c, Expression.Constant('z'))
                            ),
                            Expression.AndAlso(
                                Expression.GreaterThanOrEqual(c, Expression.Constant('A')),
                                Expression.LessThanOrEqual(c, Expression.Constant('Z'))
                            )
                        ),
                        Expression.AndAlso(
                            Expression.GreaterThanOrEqual(c, Expression.Constant('0')),
                            Expression.LessThanOrEqual(c, Expression.Constant('1'))
                        )
                    ),
                    Expression.Equal(c, Expression.Constant('_'))
                ),
                Expression.Return(returnLabel.Target, _trueConstant),
                Expression.Return(returnLabel.Target, _falseConstant)
            ),
            returnLabel
        ),
        "IsWordChar",
        new[] { e }
    );
    return lambda;
}</pre>