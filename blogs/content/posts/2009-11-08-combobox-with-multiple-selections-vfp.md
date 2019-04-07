---
title: “Combobox” with multiple selections (Visual FoxPro)
author: Naomi Nosonovsky
type: post
date: 2009-11-08T15:52:02+00:00
excerpt: |
  The class listed below was born as a result of this discussion in UniversalThread Non-standard combobox Thread #1424555 where the idea of using ListBox with MultiSelect property instead of the standard combobox was suggested by Gregory Adam.
  
  
  
  Whil&hellip;
url: /index.php/datamgmt/datadesign/combobox-with-multiple-selections-vfp/
views:
  - 22442
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
The class listed below was born as a result of this discussion in UniversalThread **Non-standard combobox** Thread #1424555 where the idea of using ListBox with MultiSelect property instead of the standard combobox was suggested by Gregory Adam.

<!--more-->

While there are other solutions available to resolve this issue (such as the class by Dorin Vasilescu, [File #31183][1] available at www.universalthread.com in the Downloads section) this class best suits my purpose. 

<pre>**************************************************
*-- Class:        listbox_collapsable (c:nadyacollapsable_listboxcollapsable_listbox.vcx)
*-- ParentClass:  listbox
*-- BaseClass:    listbox
*-- Time Stamp:   10/18/09 09:14:09 PM
*-- Collapsable listbox class
*
DEFINE CLASS listbox_collapsable AS listbox


	RowSourceType = 5
	Height = 22
	MultiSelect = .T.
	SpecialEffect = 0
	Width = 181
	*-- Height of the listbox in its closed state
	noriginalheight = 22
	*-- XML Metadata for customizable properties
	_memberdata = [<vfpdata><memberdata name="noriginalheight" display="nOriginalHeight"><memberdata name="cselecteditems" display="cSelectedItems"><memberdata name="cselecteditemslist" display="cSelectedItemsList"><memberdata name="ccurrentlyselected" display="cCurrentlySelected"><memberdata name="refreshdependents" display="RefreshDependents"><memberdata name="openlist" display="OpenList"><memberdata name="closelist" display="CloseList"><memberdata name="nselecteditemforecolor" display="nSelectedItemForeColor"><memberdata name="nselecteditembackcolor" display="nSelectedItemBackColor"><memberdata name="restoreselections" display="RestoreSelections"><memberdata name="arrrowsource" display="arrRowSource"><memberdata name="clearselections" display="ClearSelections"></memberdata></memberdata></memberdata></memberdata></memberdata></memberdata></memberdata></memberdata></memberdata></memberdata></memberdata></memberdata></vfpdata>]
	*-- Comma Delimited list of selected items
	cselecteditems = ""
	*-- Comma-delimited list of selected items. The difference with the cSelectedItems is that items are put in quotes for character values
	cselecteditemslist = ""
	*-- Items currently selected
	ccurrentlyselected = ""
	*-- Selected item ForeColor
	nselecteditemforecolor = 0
	*-- Selected Item Back Color
	nselecteditembackcolor = 0
	llistclosed = .T.
	Name = "listbox_collapsable"

	*-- RowSource array
	DIMENSION arrrowsource[1]


	*-- This method refreshes dependent controls
	PROCEDURE refreshdependents
		LPARAMETERS tcSelectedItems
	ENDPROC


	*-- Expands listbox
	PROCEDURE openlist
		this.Height = this.nOriginalHeight * MIN(10, this.ListCount)  && 10 items
		this.TopIndex = 1
		this.ZOrder(0) && Move to front
		this.SelectedItemBackColor = this.nSelectedItemBackColor 
		this.SelectedItemForeColor = this.nSelectedItemForeColor 

		this.lListClosed=.F.
	ENDPROC


	*-- Occurs when the list is "closed"
	PROCEDURE closelist
		LOCAL lnTopIndex, lnI
		lnTopIndex = 0

		STORE "" TO this.cSelectedItems, this.cSelectedItemsList 

		this.TopIndex = 1 && The blank item is always on top

		this.Height = this.nOriginalHeight
		this.ZOrder(1) && Send to back
		this.SelectedItemBackColor = this.ItemBackColor 
		this.SelectedItemForeColor = this.ItemForeColor 

		** Save selected items   
		FOR lnI = 1 TO this.ListCount 
		  IF this.Selected(m.lnI) = .t.     
		     
			IF NOT EMPTY(this.ListItem[m.lnI,1])
				IF m.lnTopIndex = 0
			        lnTopIndex = m.lnI
		    	    this.TopIndex = m.lnTopIndex
			     ENDIF
			     
		    	 this.cSelectedItems = this.cSelectedItems + ;
		     					IIF(not EMPTY(this.cSelectedItems),",","") + this.ListItem[m.lnI,1]
		     
		     	this.cSelectedItemsList = this.cSelectedItemsList + ;
		     					IIF(not EMPTY(this.cSelectedItemsList),",'","'") + this.ListItem[m.lnI,1] + "'"
		     ENDIF
		     
		  ENDIF
		NEXT

		thisform.Cls()

		IF NOT this.cCurrentlySelected == this.cSelectedItems 
		   this.RefreshDependents(this.cSelectedItemsList) 
		ENDIF

		this.lListClosed = .T.
	ENDPROC


	*-- This method restores selections based on the passed array
	PROCEDURE restoreselections
		LPARAMETERS taSelectedItems
		EXTERNAL ARRAY taSelectedItems

		LOCAL lnI, lnIndex
		STORE 0 TO this.ListItemId && Reset all selections

		STORE "" TO this.value, this.cSelectedItems, this.cSelectedItemsList  

		IF NOT EMPTY(taSelectedItems[1])
		  FOR lnI = 1 TO ALEN(taSelectedItems,1)
		     lnIndex = ASCAN(this.arrRowSource, taSelectedItems[m.lnI],1,this.ListCount,1,8) 
		     IF m.lnIndex > 0
		        this.Selected(m.lnIndex,1) = .T.
		     ENDIF
		  NEXT
		ENDIF       
		        
	ENDPROC


	*-- This method removes all selections made
	PROCEDURE clearselections
		STORE 0 TO this.ListItemId && Reset all selections
		STORE "" TO this.value, this.cSelectedItems, this.cSelectedItemsList  
	ENDPROC


	PROCEDURE KeyPress
		LPARAMETERS nKeyCode, nShiftAltCtrl

		IF m.nKeyCode = 27 && ESC
		   NODEFAULT
		   this.CloseList()
		ENDIF

		IF m.nKeyCode = -3 && F4
		   nodefault
		   this.OpenList() 
		endif   
		   
	ENDPROC


	PROCEDURE LostFocus
		DODEFAULT()
		this.CloseList()
		   
		           
	ENDPROC


	PROCEDURE GotFocus
		DODEFAULT()
		this.cCurrentlySelected = this.cSelectedItems 
	ENDPROC


	PROCEDURE Init
		DODEFAULT()
		this.arrRowSource[1] = ""
		this.RowSource = "this.arrRowSource"

		this.nSelectedItemBackColor = this.SelectedItemBackColor 
		this.nSelectedItemForeColor = this.SelectedItemForeColor 

		this.SelectedItemBackColor = this.ItemBackColor 
		this.SelectedItemForeColor = this.ItemForeColor 

		LOCAL slcControlsClassLibrary, sllReleaseControlsClassLibrary, slcCmdName, loButton
		slcControlsClassLibrary = JUSTSTEM(THIS.CLASSLIBRARY)
		IF LOWER(slcControlsClassLibrary) $ LOWER(SET('CLASSLIB'))
			sllReleaseControlsClassLibrary = .F.
		ELSE
			SET CLASSLIB TO (slcControlsClassLibrary) ADDITIVE
			sllReleaseControlsClassLibrary = .T.
		ENDIF

		* Determine the name of the new lookup CommandButton.
		slcCmdName = ALLTRIM(THIS.NAME)
		IF UPPER(LEFT(slcCmdName,3)) = 'LST'
			slcCmdName = 'cmdLookup' + SUBSTR(slcCmdName,4)
		ELSE
			slcCmdName = 'cmdLookup' + slcCmdName
		ENDIF
		* Create the new lookup CommandButton.
		THIS.PARENT.ADDOBJECT(slcCmdName,slcControlsClassLibrary +'.commandbutton')
		* Change the properties of the new lookup CommandButton.
		loButton = EVALUATE('this.Parent.' + slcCmdName)
		WITH loButton
			.TABSTOP = .F.
			.TABINDEX = THIS.TABINDEX
			.HEIGHT = 20
			.WIDTH = 17

			.LEFT = THIS.LEFT+THIS.WIDTH-17

			.TOP = THIS.TOP+INT((THIS.HEIGHT-20)/2)

			IF THIS.ANCHOR <> 0
				.ANCHOR = 8 && move to the right
			ENDIF

			IF FILE('LOOKUP.BMP')
				.CAPTION = ''
				.PICTURE = 'LOOKUP.BMP'
			ELSE
				.CAPTION = '...'
			ENDIF

			.VISIBLE = THIS.VISIBLE
			BINDEVENT(m.loButton, 'Click', THIS, 'OpenList')
		ENDWITH

		* Release the class library that was loaded.
		IF sllReleaseControlsClassLibrary
			RELEASE CLASSLIB (slcControlsClassLibrary)
		ENDIF
	ENDPROC


	PROCEDURE MouseLeave
		LPARAMETERS nButton, nShift, nXCoord, nYCoord
		this.CloseList()
	ENDPROC


	PROCEDURE MouseUp
		LPARAMETERS nButton, nShift, nXCoord, nYCoord
		* Added the following code to make it function more like a combo box unless a modifier key is held down
		DO CASE
			CASE this.lListClosed
				this.OpenList()

			CASE nShift=0 AND !BETWEEN(nXCoord, this.Left+this.Width-SYSMETRIC(5), this.Left+this.Width)
				this.CloseList()
				this.Value=this.Value
				this.Refresh()
		ENDCASE
	ENDPROC


ENDDEFINE
*
*-- EndDefine: listbox_collapsable
**************************************************</pre>

Attached is the <a href="/wp-content/uploads/blogs/DataMgmt/collapsable\_listbox.zip" Title = "Collapsable Listbox">collapsable\_listbox.zip</a>

 [1]: http://www.universalthread.com/Report.aspx?Session=4E347844434F494E675A633D20664C635A4A4F345A4F3762735542576858773356636E377936573763386E4541