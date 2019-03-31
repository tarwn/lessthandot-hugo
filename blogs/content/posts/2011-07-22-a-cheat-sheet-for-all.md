---
title: 'A Cheat Sheet for All the *{_(%#$] PowerShell Punctuation'
author: Jes Borland
type: post
date: 2011-07-22T13:13:00+00:00
excerpt: "A PowerShell Punctuation Cheat Sheet - for those of us that can't remember why {} instead of []."
url: /index.php/datamgmt/dbprogramming/a-cheat-sheet-for-all/
views:
  - 71713
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming

---
I’ve gone to The Dark Side: I took a PowerShell class. I&#8217;ve started writing new scripts and replacing old VBscripts. Was it scary? Terrifying. Learning a new language – programming or speaking – is never easy. Do I know everything? Not even close. I know how to get help (get-help – get it? harharhar&#8230;), and who to ask for help. 

One of the biggest obstacles for me was figuring out the punctuation and syntax in PowerShell. How do I comment out a line? Why (), [] and {}? _What does it all mean?_ 

I made myself this PowerShell Punctuation Cheat Sheet. Hopefully it helps you out too!

<div class="tables">
  <table border="1">
    <tr>
      <th>
        Symbol
      </th>
      
      <th>
        Name
      </th>
      
      <th>
        Function
      </th>
      
      <th>
        Example
      </th>
    </tr>
    
    <tr>
      <td>
        #
      </td>
      
      <td>
        Pound or Hash
      </td>
      
      <td>
        Declare comment line.
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        $
      </td>
      
      <td>
        Dollar sign
      </td>
      
      <td>
        Declare a variable.
      </td>
      
      <td>
        $Name
      </td>
    </tr>
    
    <tr>
      <td>
        =
      </td>
      
      <td>
        Equal
      </td>
      
      <td>
        Assigns value to variable.
      </td>
      
      <td>
        $Name=&#8221;Jes&#8221;
      </td>
    </tr>
    
    <tr>
      <td>
        |
      </td>
      
      <td>
        Pipe
      </td>
      
      <td>
        Take info from first cmdlet; pass to second.
      </td>
      
      <td>
        Get-Childitem | Get-Member
      </td>
    </tr>
    
    <tr>
      <td>
        &#8211;
      </td>
      
      <td>
        Hyphen
      </td>
      
      <td>
        Joins verbs-nouns.<br /> Used for parameters, modifiers, filters.
      </td>
      
      <td>
        Get-Member<br /> Get-Process -name s*
      </td>
    </tr>
    
    <tr>
      <td>
        &#8221;
      </td>
      
      <td>
        Double-quote
      </td>
      
      <td>
        Use around text. Variables will show the value.
      </td>
      
      <td>
        $a=100<br /> &#8220;The value of a is $a&#8221; will output as:<br /> The value of a is 100
      </td>
    </tr>
    
    <tr>
      <td>
        &#8216;
      </td>
      
      <td>
        Single-quote
      </td>
      
      <td>
        Treats text as literal.
      </td>
      
      <td>
        $a=100<br /> &#8216;The value of a is $a&#8217; will output as:<br /> The value of a is $a
      </td>
    </tr>
    
    <tr>
      <td>
        `
      </td>
      
      <td>
        Escape/grave accent
      </td>
      
      <td>
        The escape character. Use to take the next character literally.
      </td>
      
      <td>
        &#8220;The value is `$10&#8221; will output as:<br /> The value is $10<br /> It won&#8217;t treat it as a variable.
      </td>
    </tr>
    
    <tr>
      <td>
        ()
      </td>
      
      <td>
        Parentheses
      </td>
      
      <td>
        Provide arguments.<br /> Grouping.
      </td>
      
      <td>
        &#8220;text&#8221;.ToUpper()<br /> (2 +1)*4
      </td>
    </tr>
    
    <tr>
      <td>
        []
      </td>
      
      <td>
        Brackets
      </td>
      
      <td>
        Access elements of array.<br /> In -like comparisons.<br /> Set variable type.
      </td>
      
      <td>
        $Names[0]<br /> -like [ab]*<br /> [int]$count
      </td>
    </tr>
    
    <tr>
      <td>
        {}
      </td>
      
      <td>
        Curly brackets
      </td>
      
      <td>
        Enclose block of code.
      </td>
      
      <td>
        Get-Wmiobject -list | where {$_.name -match &#8220;win32*&#8221;}
      </td>
    </tr>
    
    <tr>
      <td>
        ,
      </td>
      
      <td>
        Comma
      </td>
      
      <td>
        Separate items in a list.
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        ;
      </td>
      
      <td>
        Semi-colon
      </td>
      
      <td>
        Run multiple commands on same line.
      </td>
      
      <td>
        $Name=&#8221;Jes&#8221;; $Name
      </td>
    </tr>
    
    <tr>
      <td>
        +
      </td>
      
      <td>
        Plus
      </td>
      
      <td>
      </td>
      
      <td>
        Concatenate.
      </td>
    </tr>
  </table>
</div>

There are four main commands to remember for PowerShell. Using these four commands, you can figure out nearly anything. 

<div class="tables">
  <table border="1">
    <tr>
      <th>
        Command
      </th>
      
      <th>
        Function
      </th>
      
      <th>
        Example
      </th>
    </tr>
    
    <tr>
      <td>
        Get-Help
      </td>
      
      <td>
        Get help with a cmdlet. Provides name, syntax, links and more.
      </td>
      
      <td>
        Get-Help Get-Date
      </td>
    </tr>
    
    <tr>
      <td>
        Get-Command
      </td>
      
      <td>
        Provides information about all available cmdlets.
      </td>
      
      <td>
        Get-Command Format-List
      </td>
    </tr>
    
    <tr>
      <td>
        Get-Member
      </td>
      
      <td>
        Get the properties and methods of an objects.
      </td>
      
      <td>
        Get-Process | Get-Member
      </td>
    </tr>
    
    <tr>
      <td>
        Get-PSDrive
      </td>
      
      <td>
        Lists all the PowerShell drives in the current session &#8211; FileSystem, Functions, Alias, etc.
      </td>
      
      <td>
        Get-PSDrive
      </td>
    </tr>
  </table>
</div>

Note: I have not included the percent symbol (%) or the question mark (?) on purpose. They are most commonly used as aliases for other commands, and that is beyond the scope of this post.