---
title: Lazy Coding?
author: chaospandion
type: post
date: 2010-03-11T14:03:43+00:00
url: /index.php/desktopdev/mstech/lazy-coding/
views:
  - 3831
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - .net
  - 'c#'
  - lazy

---
Are you waiting for .NET 4.0 to take advantage of lazy initialization? Now you don&#8217;t have to. The question you need to ask yourself is why haven&#8217;t I already made this myself?

_Aren&#8217;t we all a little lazy?_

<pre>/// &lt;summary&gt;
/// Provides support for lazy initialization.
/// &lt;/summary&gt;
/// &lt;typeparam name="T"&gt;Specifies the type of object that is being lazily initialized.&lt;/typeparam&gt;
public sealed class Lazy&lt;T&gt;
{
	private readonly Func&lt;T&gt; createValue;
	private volatile bool isValueCreated;
	private T value;


	/// &lt;summary&gt;
	/// Gets the lazily initialized value of the current Lazy{T} instance.
	/// &lt;/summary&gt;
	public T Value
	{
		get
		{
			if (!isValueCreated)
			{
				lock (this)
				{
					if (!isValueCreated)
					{
						value = createValue();
						isValueCreated = true;
					}
				}
			}
			return value;
		}
	}		

	/// &lt;summary&gt;
	/// Gets a value that indicates whether a value has been created for this Lazy{T} instance.
	/// &lt;/summary&gt;
	public bool IsValueCreated
	{
		get 
		{
			lock (this)
			{
				return isValueCreated;
			} 
		}
	}


	/// &lt;summary&gt;
	/// Initializes a new instance of the Lazy{T} class.
	/// &lt;/summary&gt;
	/// &lt;param name="createValue"&gt;The delegate that produces the value when it is needed.&lt;/param&gt;
	public Lazy(Func&lt;T&gt; createValue)
	{
		if (createValue == null) throw new ArgumentNullException("createValue");

		this.createValue = createValue;
	}


	/// &lt;summary&gt;
	/// Creates and returns a string representation of the Lazy{T}.Value.
	/// &lt;/summary&gt;
	/// &lt;returns&gt;The string representation of the Lazy{T}.Value property.&lt;/returns&gt;
	public override string ToString()
	{
		return Value.ToString();
	}
}</pre>

**Example Usage**

<pre>public int MyProperty
{
    get { return myProperty.Value; }
}
private readonly Lazy&lt;int&gt; myProperty = new Lazy&lt;int&gt;(() =&gt; 2);</pre>