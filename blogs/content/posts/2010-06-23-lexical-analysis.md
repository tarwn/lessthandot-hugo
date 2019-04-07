---
title: Lexical Analysis
author: chaospandion
type: post
date: 2010-06-23T14:41:16+00:00
ID: 829
url: /index.php/desktopdev/mstech/lexical-analysis/
views:
  - 6635
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies

---
So how exactly does your favorite compiler turn text into executable code?

  1. Lexical Analysis
  2. Syntactical Analysis
  3. Semantic Analysis

In this post I want to go over the lexical analysis process. The process might seem a little daunting at first but with the right tools and a little patience you would be surprised with what you can accomplish. The only goal of the lexical analysis process is to take an input string and return a sequence of tokens that represent input string. A token is a sequence of characters that follow certain rules.

Given the following string:

`10 + 2 - 3`

We will need the following tokens:

  * Number
  * Plus
  * Dash

We need to define the following struct and enum to represent the tokens.

```CSharp
public struct Token
{
    public string Value
    {
        get { return _value; }
    }
    private readonly string _value;

    public TokenType Type
    {
        get { return _type; }
    }
    private readonly TokenType _type;


    public Token(string value, TokenType type)
    {
        _value = value;
        _type = type;
    }
}

public enum TokenType
{
    Number,
    Dash,
    Plus
}
```

Now that we have our Token struct we can write our method to tokenize the input string.

```CSharp
public static IEnumerable<Token> Tokenize(string input)
{
    var st = new StringTraverser(input);
    var c = default(char?);
    while ((c = st.Next())!= null)
    {
        if (c == ' ')
        {
            continue; // We can ignore white space.
        }
        else if (c == '+')
        {
            yield return new Token("+", TokenType.Plus);
        }
        else if (c == '-')
        {
            yield return new Token("-", TokenType.Dash);
        }
        else if (c >= '0' && c <= '9')
        {
            var s = c.ToString();
            while ((c = st.Next()) != null && c >= '0' && c <= '9')
            {
                s += c;
            }
            if (c != null) 
            {
                st.Back(); // We don't want to skip the next token do we?
            }
            yield return new Token(s, TokenType.Number);
        }
        else
        {
            yield return new Token(c.ToString(), TokenType.Unknown);
        }
    }
}
```
If we were to pass our input string into the Tokenize method we should get back the following tokens:

  * Number = **10**
  * Plus = **+**
  * Number = **2**
  * Dash = **&#8211;**
  * Number = **3**

In this form we can easily move forward in the process and begin the syntactical analysis process but that is for another day. Take notice of how in the final else condition we return an unknown token. This is because it is not the job of the lexical analyzer to decide how to handle an invalid token. Given this simple example it is easy to imagine what other tokens we could add. With enough though you can come up with your own programming language in no time. 

The following code is what makes up the StringTraverser object. 

```CSharp
public sealed class StringTraverser
{
    private readonly string _value;
    private readonly int _length;
    private int _index;


    public StringTraverser(string value)
    {
        if (value == null)
        {
            throw new ArgumentNullException("value");
        }

        _value = value;
        _length = value.Length;
    }


    public char? Next()
    {
        if (_index == _length)
        {
            return null;
        }

        return _value[_index++];
    }

    public string Next(int count)
    {
        if (count <= 0)
        {
            throw new ArgumentOutOfRangeException("count", count, "The count must be greater than zero.");
        }

        if (_index + count > _length)
        {
            return null;
        }

        var result = _value.Substring(_index, count);
        _index += count;
        return result;
    }

    public string Next(IEnumerable<char> chars)
    {
        if (chars == null)
        {
            throw new ArgumentNullException("chars");
        }

        var closestIndex = int.MaxValue;
        foreach (var c in chars)
        {
            var index = _value.IndexOf(c, _index);
            if (index != -1)
            {
                closestIndex = Math.Min(closestIndex, index);
            }
        }

        if (closestIndex == int.MaxValue)
        {
            return null;
        }

        var result = _value.Substring(_index, closestIndex - _index);
        _index = closestIndex;
        return result;
    }

    public string NextWhile(Func<char, bool> predicate)
    {
        if (predicate == null)
        {
            throw new ArgumentNullException("predicate");
        }

        var startIndex = _index;
        ForwardWhile(predicate);
        return _value.Substring(startIndex, _index - startIndex);
    }

    public char? PeekNext()
    {
        var result = Next();
        if (result != null)
        {
            _index--;
        }
        return result;
    }

    public string PeekNext(int count)
    {
        var result = Next(count);
        if (result != null)
        {
            _index -= count;
        }
        return result;
    }

    public string PeekNext(IEnumerable<char> chars)
    {
        var startIndex = _index;
        var result = Next(chars);
        _index = startIndex;
        return result;
    }
        
    public char? Previous()
    {
        if (_index == 0)
        {
            return null;
        }

        return _value[--_index];
    }

    public string Previous(int count)
    {
        if (count <= 0)
        {
            throw new ArgumentOutOfRangeException("count", count, "The count must be greater than zero.");
        }

        if (_index - count < 0)
        {
            return null;
        }

        _index -= count;
        return _value.Substring(_index, count);
    }

    public string Previous(IEnumerable<char> chars)
    {
        if (chars == null)
        {
            throw new ArgumentNullException("chars");
        }

        var closestIndex = -1;
        foreach (var c in chars)
        {
            var index = _value.IndexOf(c);
            if (index != -1)
            {
                closestIndex = Math.Max(closestIndex, index);
            }
        }

        if (closestIndex == -1)
        {
            return null;
        }

        var result = _value.Substring(closestIndex + 1, _index - closestIndex - 1);
        _index = closestIndex;
        return result;
    }
        
    public char? PeekPrevious()
    {
        var result = Previous();
        if (result != null)
        {
            _index++;
        }
        return result;
    }

    public string PeekPrevious(int count)
    {
        var result = Previous(count);
        if (result != null)
        {
            _index += count;
        }
        return result;
    }

    public string PeekPrevious(IEnumerable<char> chars)
    {
        var startIndex = _index;
        var result = Previous(chars);
        _index = startIndex;
        return result;
    }


    public void Forward()
    {
        _index = Math.Min(_length, _index + 1);
    }

    public void Forward(int count)
    {
        if (count <= 0)
        {
            throw new ArgumentOutOfRangeException("count", count, "The count must be greater than zero.");
        }

        _index = Math.Min(_length, _index + count);
    }

    public void ForwardWhile(Func<char, bool> predicate)
    {
        if (predicate == null)
        {
            throw new ArgumentNullException("predicate");
        }

        while (predicate(PeekNext() ?? char.MinValue))
        {
            _index++;
        }
    }

    public void Back()
    {
        _index = Math.Max(0, _index - 1);
    }

    public void Back(int count)
    {
        if (count <= 0)
        {
            throw new ArgumentOutOfRangeException("count", count, "The count must be greater than zero.");
        }

        if (_index == _length)
        {
            _index--;
        }

        _index = Math.Max(0, _index - count);
    }

    public void BackWhile(Func<char, bool> predicate)
    {
        if (predicate == null)
        {
            throw new ArgumentNullException("predicate");
        }

        while (predicate(PeekPrevious() ?? char.MinValue))
        {
            _index--;
        }
    }

    public void Reset()
    {
        _index = 0;
    }
}
```
