---
title: Displaying and Saving Unicode data in Visual FoxPro desktop application
author: Naomi Nosonovsky
type: post
date: 2012-01-01T17:09:00+00:00
ID: 1472
excerpt: 'Recently I had to deal with the problem of displaying and saving Unicode data in a Visual FoxPro form. As we all know, Visual FoxPro does not support Unicode, so this was  quite a challenge for me. I would never have solved this problem by myself if Gre&hellip;'
url: /index.php/datamgmt/datadesign/displaying-and-saving-unicode-data/
views:
  - 25015
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Recently I had to deal with the problem of displaying and saving Unicode data in a Visual FoxPro form. As we all know, Visual FoxPro does not support Unicode, so this was quite a challenge for me. I would never have solved this problem by myself if Gregory Adam from the [UniversalThread][1] would not lend me his generous support and practically solved the problem of saving for me. I want to show the full solution we came to together as this may be a very important topic for the remaining Visual FoxPro developers (and because Koen asked me to write this blog post).

There used to be the classical and very comprehensive article on this topic written by Rick Strahl Using Unicode in Visual FoxPro Web and Desktop Applications. The link to that article now appears to be broken, so I found another shorter article: [Unicode to ANSI Translation in VFP 9.0 &#8211; Sys(987)][2] 

I originally started from the first mentioned article. Our application uses SQL Server to hold data and uses sql pass thru technique to send information from and to SQL Server. 

Now, since I am describing the solution I used for the particular application, I will start with the SQL Server table I was using. That table was named Prefs_sl and it was a flat table with 1 record containing all preferences for a sales point. Because of its flat nature it was badly normalized. Say, it had 11 language fields language00 &#8211; language10 (nvarchar(100)) to hold name of the language the Sales Point can display and 11 image lngImage00-lngImage10 (varbinary(max)) fields to hold the image of the country flag. So, this was the table I was working with on the SQL Server side.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Prefs_sl SQL Server table.JPG?mtime=1325442752"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Prefs_sl SQL Server table.JPG?mtime=1325442752" width="1764" height="162" /></a>
</div>

Now, I wanted to display these 11 languages with their corresponding flags on the form, so the logical choice was to create a class with the textbox for the unicode text and image for the image flag and then instantiate this class on the form.

The first question was what to chose for displaying unicode data? Rick mentioned MS Edit Box control ActiveX, but I didn't find it in the list of my installed ActiveX. But I found MS Forms2 TextBox ActiveX control and since I also remembered [this thread][3] on foxite forum, I decided to try this ActiveX control.
  
The first problem I encountered with this ActiveX as that it didn't show up until I clicked on it when I was running the form. Luckily I found the following solution &#8211; invoke SetFocus of this control (I called it txtLanguage in my class) and then it was displayed correctly. I am not sure if my solution is the best to handle this ActiveX behavior quirks, but hey, it works.

Ok, so far so good. Now, following Rick's article I used the following in my form to get the data in the binary format:

```
LOCAL lcSQL
TEXT TO lcSQL noshow
SELECT pri_key      
      ,defltlang
      ,cast(language00 as varbinary(100)) as language00
      ,lngimage00      
      ,cast(language01 as varbinary(100)) as language01
      ,lngimage01
      ,cast(language02 as varbinary(100)) as language02
      ,lngimage02
      ,cast(language03 as varbinary(100)) as language03
      ,lngimage03
      ,cast(language04 as varbinary(100)) as language04
      ,lngimage04
      ,cast(language05 as varbinary(100)) as language05
      ,lngimage05
      ,cast(language06 as varbinary(100)) as language06
      ,lngimage06
      ,cast(language07 as varbinary(100)) as language07
      ,lngimage07
      ,cast(language08 as varbinary(100)) as language08
      ,lngimage08
      ,cast(language09 as varbinary(100)) as language09
      ,lngimage09
      ,cast(language10 as varbinary(100)) as language10
      ,lngimage10      
  FROM dbo.prefs_sl
endtext  
mysqlexec(m.lcSQL, 'Prefs_sl', program())
```
mysqlexec here is a wrapper for the sqlexec function.

Now I found the first discrepancy with Rick's article &#8211; I found that I don't need any conversion after that, I can assign the field's content to the text property of this ActiveX (or ControlSource property) and the control correctly displays the unicode data!

I was happy &#8211; the first part of displaying data turned out to be quite easy!

Here is the code I used to display the data:

```
FOR EACH loObject IN thisform.Objects foxobject
   IF loObject.Baseclass = 'Container'
      loObject.LanguageField = SUBSTR(loObject.name,4,LEN(loObject.name))
      loObject.ImageField = 'lngImage' + RIGHT(loObject.Name,2)
   ENDIF 
next   

thisform.cntLanguage01.SetFocus()
```

And in the container itself the following code in the LanguageField assign method:

```
this.lblLanguage.caption = this.lblLanguage.caption + ' ' + transform(val(right(tLanguageField,2)))
  this.txtLanguage.Enabled = .t.
  this.txtLanguage.TabStop = .t.   
  this.lProgrammaticChange = .t.
  this.txtLanguage.text = evaluate('prefs_sl.' + tLanguageField)
  this.lProgrammaticChange = .f.
```

Everything was good so far. However, I haven't expected how hard (for me) it will be the &#8216;saving' data part. I was spending days trying various ideas from Rick's article and was still unable to achieve the desired result. I was about to throw all I had away and try to switch to ADO for data retrieval, but luckily Gregory Adam helped me here and published a working sample.

First of all, we need to make sure there is no UT8 translation to the current code page. For this we need to do the following

```
COMPROP(this,'UTF8',1)
```
In the txtLanguage Init method.

We also need the following functions:

```
&& StringConversion
&& Gregory Adam 2011
*===============================================================================
#define true	.T.
#define false	.F.

*_______________________________________________________________________________
function StringToUTF8(utf8Out, stringIn, codepageIn)
	
	local success
	success = true
	
	do case
	case !m.success
	
	case !StringToUTF16(@m.utf8Out, m.stringIn, m.codepageIn)
		assert false
		success = false
	
	case !StringUTF16ToUTF8(@m.utf8Out, m.utf8Out)
		assert false
		success = false
	
	endcase
	
	return m.success
	
endfunc
*_______________________________________________________________________________


*_______________________________________________________________________________
*_______________________________________________________________________________
#define CP_ACP					0
#define CP_MACCP				2
#define CP_OEMCP				1
#define CP_SYMBOL				42
#define CP_THREAD_ACP			3
#define CP_UTF7					65000
#define CP_UTF8					65001
#define MB_PRECOMPOSED			0x1
#define MB_COMPOSITE			0x2
#define MB_USEGLYPHCHARS		0x4
#define MB_ERR_INVALID_CHARS	0x8

#define WC_DEFAULTCHAR			0x00000040 
#define WC_ERR_INVALID_CHARS	0x00000080 
#define WC_NO_BEST_FIT_CHARS 	0x00000400 
*_______________________________________________________________________________
function StringToUTF16(utf16Out, stringIn, codepageIn)

	local success
	success = true
	
	do case
	case !m.success
	
	case empty(len(m.stringIn))
		utf16Out = ''
		
	otherwise
		local lpWideCharStr, result
		lpWideCharStr = space(len(m.stringIn)*2)
	
		result = MultiByteToWideChar( ;
					evl(m.codepageIn, cpcurrent()), ;
					MB_ERR_INVALID_CHARS, ;
					@m.stringIn, ;
					len(m.stringIn), ;
					@m.lpWideCharStr, ;
					len(m.lpWideCharStr) ;
				)
			
		do case
		case !m.success
		
		case empty(m.result)
			assert false
			success = false
		
		otherwise
			utf16Out = left(m.lpWideCharStr, m.result * 2) 
		
		endcase
		
	endcase
	
	return m.success
	
	
endfunc
*_______________________________________________________________________________
function StringUTF16ToUTF8(utf8Out, utf16In)

	local success
	success = true
	
	do case
	case !m.success
	
	case empty(len(m.utf16In))
		utf8Out = ''
		
	otherwise
	
		local lpMultiByteStr, lpUsedDefaultChar, result
		lpMultiByteStr = space(len(m.utf16In) * 2)
		lpUsedDefaultChar = 0
		
		result = WideCharToMultiByte( ;
					CP_UTF8, ;
					WC_ERR_INVALID_CHARS, ;
					@m.utf16In, ;
					len(m.utf16In)/2, ;
					@m.lpMultiByteStr, ;
					len(m.lpMultiByteStr), ;
					null, ;
					@m.lpUsedDefaultChar ;
				)
		
		do case
		case !m.success
		
		case empty(m.result)			
			assert false
			success = false
			
			
		otherwise
			utf8Out = left(m.lpMultiByteStr, m.result)
		
		endcase
	endcase
	
	return m.success
	
	
endfunc
*_______________________________________________________________________________
function StringUTF8ToUTF16(utf16Out, uft8In)

	return StringToUTF16(@m.utf16Out, uft8In, CP_UTF8)
	
endfunc
*_______________________________________________________________________________

function MultiByteToWideChar
	lparameters codepage, ;
				dwFlags, ;
				lpMultiByteStr, ;
				cbMultiByte, ;
				lpWideCharStr, ;
				cchWideChar

	local success
	success = true

	local result
	
	do case
	case !m.success
	
	otherwise
		try
			declare integer MultiByteToWideChar in Kernel32.dll ;
				long	codepage, ;
				long	dwFlags, ;
				string@	lpMultiByteStr, ;
				integer	cbMultiByte, ;
				string@	lpWideCharStr, ;
				integer	cchWideChar
		
			result = MultiByteToWideChar( ;
					m.codepage, ;
					m.dwFlags, ;
					@m.lpMultiByteStr, ;
					m.cbMultiByte, ;
					@m.lpWideCharStr, ;
					m.cchWideChar ;
				)
		catch
			assert false
			success = false
			
		endtry
	endcase
	
	return iif(m.success, m.result, 0)
	
endfunc
*_______________________________________________________________________________
function WideCharToMultiByte
	lparameters codepage, ;
				dwFlags, ;
				lpWideCharStr, ;
				cchWideChar, ;
				lpMultiByteStr, ;
				cbMultiByte, ;
				lpDefaultChar, ;
				lpUsedDefaultChar

	local success
	success = true

	local result
	
	do case
	case !m.success
	
	otherwise
		try
			declare integer WideCharToMultiByte in Kernel32.dll ;
				long	codepage, ;
				long	dwFlags, ;
				string@	lpWideCharStr, ;
				integer	cchWideChar, ;
				string@	lpMultiByteStr, ;
				integer	cbMultiByte, ;
				string	lpDefaultChar, ;
				integer	@lpUsedDefaultChar
		
			result = WideCharToMultiByte ( ;
					m.codepage, ;
					m.dwFlags, ;
					@m.lpWideCharStr, ;
					m.cchWideChar, ;
					@m.lpMultiByteStr, ;
					m.cbMultiByte, ;
					m.lpDefaultChar, ;
					@m.lpUsedDefaultChar;
				)
		catch
			assert false
			success = false
			
		endtry
	endcase
	
	return iif(m.success, m.result, 0)
	
endfunc
```
and then in the Change event of the ActiveX textbox I added the following code

```
IF this.parent.lProgrammaticChange 
    RETURN
ENDIF     
if thisform.IsChanged OR (not this.CurrentValue == this.Text)
   thisform.IsChanged = .t.
   local utf8, utf16Out
   utf8 = this.text
   if not empty(utf8)
      = StringUtf8ToUTF16(@m.utf16Out, m.utf8)
      replace (this.parent.LanguageField) with utf16Out
   else
      replace (this.parent.LanguageField) with ''
   endif
endif
```
and finally in the Save method of the form

```
IF THISFORM.IsChanged 
  LOCAL lcSQL

  TEXT TO lcSQL noshow
     UPDATE dbo.prefs_sl 
     SET defltlang = ?prefs_sl.DefltLang,
     language00 = cast(?prefs_sl.language00 as nvarchar(100)),
     lngimage00 = ?prefs_sl.lngimage00,
     language01 = cast(?prefs_sl.language01 as nvarchar(100)),
     lngimage01 = ?prefs_sl.lngimage01,
     language02 = cast(?prefs_sl.language02 as nvarchar(100)),
     lngimage02 = ?prefs_sl.lngimage02,
     language03 = cast(?prefs_sl.language03 as nvarchar(100)),
     lngimage03 = ?prefs_sl.lngimage03,
     language04 = cast(?prefs_sl.language04 as nvarchar(100)),
     lngimage04 = ?prefs_sl.lngimage04,
     language05 = cast(?prefs_sl.language05 as nvarchar(100)),
     lngimage05 = ?prefs_sl.lngimage05,
     language06 = cast(?prefs_sl.language06 as nvarchar(100)),
     lngimage06 = ?prefs_sl.lngimage06,
     language07 = cast(?prefs_sl.language07 as nvarchar(100)),
     lngimage07 = ?prefs_sl.lngimage07,
     language08 = cast(?prefs_sl.language08 as nvarchar(100)),
     lngimage08 = ?prefs_sl.lngimage08,
     language09 = cast(?prefs_sl.language09 as nvarchar(100)),
     lngimage09 = ?prefs_sl.lngimage09,
     language10 = cast(?prefs_sl.language10 as nvarchar(100)),
     lngimage10 = ?prefs_sl.lngimage10
     where pri_key = ?prefs_sl.pri_key
  ENDTEXT
 RETURN mySQLExec(m.lcSQL, '',PROGRAM())
ENDIF
```
As we can see, we need to convert data back from binary to nvarchar.
  
With all this code in place, we display the unicode data and save them back to SQL Server.

A big thanks to Gregory for helping me with the code &#8211; I don't know where I would be without his help.

Olaf Doschke showed another way which is even simpler than implemented solution and in accordance to the original Rick's article.

In the form's Load we need the following:

```
Sys(987,.F.)
Sys(3101,65001)
```
Then, after getting binary data from SQL Server the same way as I show in this blog, we still need to use createbinary, e.g.

this.txtLanguage.text = createbinary(prefs_sl.Language00)

We don't want to use COMPROP now for the ActiveX.

and then, saving data, we need to follow Rick's steps:

```
pcSavedText1 = Strconv(This.Text,12)

*** Must explicitly force to binary – can also use CAST in 9.0
pcSavedText1 = CREATEBINARY(pcSavedText1)
```
and then convert this value back to nvarchar(max) when saving with sqlexec.

This is how the form with many languages looked like

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/ManyLanguages.JPG?mtime=1325442752"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/ManyLanguages.JPG?mtime=1325442752" width="927" height="654" /></a>
</div>

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
I need to also tell, that after I finished the form and was happy to show it to my colleagues, I was up to a big disappointment. It turned out we are not going to support unicode (our main application is not up to support it yet) and so I will be re-doing this form after we will agree on the tables' structures (they also are going to change). So, what I showed in this blog post we're not going to use at present in production.

<span class="MT_red">UPDATE.</span> My colleague also re-designed prefs_sl table to one EAV Settings table. We know that EAV design has its own problems, but for saving various application level settings it can be used. I already re-designed the forms we're going to use. 

But in any case, it was a very important exercise and I hope it will help other people who need to display and save Unicode data in their Visual FoxPro applications.

 [1]: http://universalthread.com
 [2]: https://www.west-wind.com/wconnect/WebLog/ShowEntry.blog?id=608
 [3]: http://www.foxite.com/archives/how-to-implement-unicode-in-vfp-0000209431.htm