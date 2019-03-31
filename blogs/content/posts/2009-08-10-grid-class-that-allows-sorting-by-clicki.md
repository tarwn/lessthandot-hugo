---
title: Grid class that allows sorting by clicking on headers
author: Naomi Nosonovsky
type: post
date: 2009-08-10T21:43:19+00:00
excerpt: 'Unlike the ASP.NET GridView Class, the Visual Foxpro native grid class does not provide a means for automatic sorting by clicking on the column header. Although there are several workarounds for this deficiency available (such as http://www.rcs-solution&hellip;'
url: /index.php/datamgmt/datadesign/grid-class-that-allows-sorting-by-clicki/
views:
  - 28423
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Unlike the ASP.NET GridView Class, the Visual Foxpro native grid class does not provide a means for automatic sorting by clicking on the column header. Although there are several workarounds for this deficiency available (such as http://www.rcs-solutions.com/downloads.aspx), here is my solution using my grdSorting class which allows automatic sorting by clicking on the column headers. Attached also is the library containing this class and two bmp files for displaying Up and Down arrows.<!--more-->

<pre>**************************************************
*-- Class:        grdSorting 
*-- ParentClass:  grid
*-- BaseClass:    grid
*-- Time Stamp:   08/10/09 06:57:11 PM
*
DEFINE CLASS grdSorting AS grid


	Optimize = .T.
	*-- This property tells, if we want to add visual representation of the sorting
	lshowsortingarrows = .T.
	*-- This property tells, if the grid's recordsource should be at the top after sorting
	lgotopaftersorting = .T.
	*-- This property tells wheither we need to update toolbar buttons or not
	lupdatetoolbar = .T.
	*-- This property saves the header of the last sorted column
	olastsorted = "null"
	*-- If this property is set to true (default), then the indexes are created for every column in the grid
	lcreateindexes = .T.
	*-- Holds grid's column controlsources and sizes
	cgridscript = ""
	*-- Original width of the grid
	noriginalwidth = 0
	*-- Current record number
	nrecno = 1
	*-- Defines the sort order for the grid
	lascending = .F.
	*-- Active Tag Name
	ctagname = ""
	*-- If this property is set to false, then the Initialize code is not called
	lsortheaders = .T.
	*-- XML Metadata for customizable properties
	_memberdata = [&lt;VFPData&gt;&lt;memberdata name="lsortheaders" display="lSortHeaders"/&gt;&lt;memberdata name="cgridscript" display="cGridScript"/&gt;&lt;memberdata name="headerclick" display="HeaderClick"/&gt;&lt;memberdata name="gettagname" display="GetTagName"/&gt;&lt;memberdata name="createtag" display="CreateTag"/&gt;&lt;memberdata name="clearheaderpictures" display="ClearHeaderPictures"/&gt;&lt;memberdata name="setorder" display="SetOrder"/&gt;&lt;/VFPData&gt;]
	Name = "grid_"

	*-- If this property is set, use DynamicBackColor to set highlight
	lmanualhighlight = .F.

	*-- If this property is set to true, then grid's properties are saved
	lsavegridproperties = .F.


	*-- This method is invoked when any grid's column header is clicked
	PROCEDURE headerclick
		local loCalledBy && as Object

		*ShowCursor(0)
		aevents[aCurEvent,0]
		loCalledBy = aCurEvent[1] && should be a Header object

		if vartype(m.loCalledBy)= "O" and not empty(m.loCalledBy.parent.cOriginalControlSource)
			local lcOrder, lcRecSource, llDirection
			lcRecSource = this.recordsource
			llDirection = this.lAscending

			lcOrder = order(m.lcRecSource) && Saves current tag
			this.SetOrder(m.loCalledBy.parent.cOriginalControlSource, m.loCalledBy.CurrentTag, m.loCalledBy)
			if this.lShowSortingArrows
		* Check, if we changed the order
				if not order(m.lcRecSource)== m.lcOrder or m.llDirection &lt;&gt; this.lAscending
		** Clear the picture of the previously sorted column
					loCalledBy.CurrentTag = this.cTagName
					this.ClearHeaderPictures()
					this.SetHeaderPicture(m.loCalledBy)
				endif
			endif
		ENDIF

		*=SetCursorPos(MROW(),MCOL())
		*Mouse at Mrow(), Mcol()
		*ShowCursor(1)
		*his.mousepointer = 0 && Attempt to restore Mouse Pointer
	ENDPROC


	*-- This method removes pictures from all the headers
	PROCEDURE clearheaderpictures
		*   Method: 	ClearHeaderPictures()
		*--------------------- Maintenance Section ----------------------
		*   Change Log:
		*       CREATED 	01/24/2005 - NN
		*		MODIFIED
		*----------------------------------------------------------------
		if vartype(m.this.oLastSorted) = "O"
			this.oLastSorted.ResetToDefault('picture')
		endif
	ENDPROC


	*-- This method sets picture for the Header object which is passed by reference
	PROCEDURE setheaderpicture
		*   Method: 	Setheaderpicture()
		*--------------------- Maintenance Section ----------------------
		*   Change Log:
		*       CREATED 	01/25/2005 - NN
		*		MODIFIED
		*----------------------------------------------------------------
		lparameters toHeader, tcPicture

		if vartype(m.toHeader) &lt;&gt; "O"
			return .f.
		endif
		if toHeader.alignment = 0 && Default
			toHeader.caption = padr(alltrim(m.toHeader.caption),200) && we want to show picture at the right
		ENDIF

		toHeader.picture = iif(not empty(m.tcPicture), m.tcPicture, ;
			iif(this.lAscending, "UpArrow.bmp","DownArrow.bmp"))
		this.oLastSorted = m.toHeader

		this.ClearWaitCursor()
	ENDPROC


	*-- Indexes a column on the fly
	*---------------------- Location Section ------------------------
*   Method: 	Createtag()
*----------------------- Usage Section --------------------------
*)  Description: Creates index for the passed header's column's controlsource on the fly
*)               The code is taken from Vlad Grynchyshyn (with NN's minor modifications)

*   Scope:      Public
*   Parameters:
*$  Usage:
*$
*   Returns:
*--------------------- Maintenance Section ----------------------
*   Change Log:
*       CREATED 	02/07/2005 - NN
*		MODIFIED
*----------------------------------------------------------------
* assuming alias for sorting is in the current workarea
LPARAMETERS toHeader
LOCAL lc__SortExpr, lnLimitation, llError, llNonStructural, lnSelect, lcOrder, loException AS EXCEPTION

lnSelect = SELECT()
lcOrder = ORDER()
llError = .F.

WITH toHeader

* calculate the expression for sorting
*-- CHANGE - WGB - November 11, 2008 - 10:13:17 - the original length was 240,
*   but it generates "Invalid Key Length" error, so we decrease it to 200
* ------------------------------------------------------------------------
	lnLimitation = IIF(SET("Collate")=="MACHINE",200,120)
	lc__SortExpr = .PARENT.cOriginalControlSource
* make basic checking for control source type
	LOCAL lcType, lnValLen, llIsField, lcTag, lcField, lcCursor
	lcType = TYPE(m.lc__SortExpr)
	llIsField = '.' $ m.lc__SortExpr AND ;
		USED(LEFT(m.lc__SortExpr,AT('.',m.lc__SortExpr)-1)) AND ;
		FSIZE(SUBSTR(m.lc__SortExpr,AT('.',m.lc__SortExpr)+1),LEFT(m.lc__SortExpr,AT('.',m.lc__SortExpr)-1)) &gt; 0

	lcTag = ""

	IF m.llIsField
		lcField = JUSTEXT("." + m.lc__SortExpr)
		lcCursor = JUSTSTEM(m.lc__SortExpr)
		lcTag = THIS.GetTagName (m.lcCursor, m.lcField)
	ENDIF
	
	TRY 
	   SELECT (lcCursor)
	CATCH TO loException
	   llError = .T.
	ENDTRY
	
	IF EMPTY(m.lcTag) AND NOT m.llError
		DO CASE
		CASE m.lcType $ "GOPUS"
			lc__SortExpr = ""

		CASE m.lcType == "C"
			IF m.llIsField
				lnValLen = MIN(FSIZE(m.lcField),m.lnLimitation)
			ELSE
				lnValLen = m.lnLimitation
			ENDIF
			lc__SortExpr = "PADR(NVL(" + m.lc__SortExpr + ",'')," + ALLTRIM(STR(m.lnValLen))+")"

		CASE m.lcType == "M"
			lc__SortExpr = "PADR(" + m.lc__SortExpr + "," + ALLTRIM(STR(m.lnLimitation))+")"

		OTHERWISE
			DO CASE
			CASE m.lcType $ "DT"
				lc__SortExpr = "NVL(" + m.lc__SortExpr + ",{})"
			CASE m.lcType == "L"
				lc__SortExpr = "NVL(" + m.lc__SortExpr + ",.F.)"
			CASE m.lcType == "Y"
				lc__SortExpr = "NVL(" + m.lc__SortExpr + ",$0)"
			OTHERWISE
				lc__SortExpr = "NVL(" + m.lc__SortExpr + ",0)"
			ENDCASE
		ENDCASE

&& create index tag
		IF !EMPTY(m.lc__SortExpr)
			.CurrentTag = "T" + SUBSTR(ALLTRIM(SYS(2015)), 4, 10) && generate unique tag name

			LOCAL lnRestoreBuffering
			lnRestoreBuffering = CURSORGETPROP('Buffering')

			TRY
				IF m.lnRestoreBuffering &gt;= 4
&& OOPS - cannot index cursors in 5 buffering mode.
&& check that it does not contain modified records
					IF GETNEXTMODIFIED(0) = 0
						CURSORSETPROP('Buffering',IIF(m.lnRestoreBuffering=5,3,2))
					ELSE
&& OOPS - we will not be able to index it...
						.CurrentTag = ""
					ENDIF
				ENDIF

&& check how we should create index - structural or non-structural
				llNonStructural = CURSORGETPROP('SourceType') = 3 AND (! ISEXCLUSIVE() OR ;
					!(CURSORGETPROP('Database')=="") OR !(SYS(2023) == JUSTPATH(DBF())) )
* it is a table for which either database is specified or file path is not in temporary folder
* or just alias is not opened in exclusive mode (file in temporary folder, but it is shared)

				IF !EMPTY(.CurrentTag)
&& disable errors
					LOCAL llError, lnOldRecNo
					lnOldRecNo = IIF(EOF(),0,RECNO())

					IF m.llNonStructural
						INDEX ON ;
							&lc__SortExpr ;
							TAG (.CurrentTag) OF (SYS(2023) + "" + .CurrentTag) ADDITIVE
					ELSE
						INDEX ON ;
							&lc__SortExpr ;
							TAG (.CurrentTag) ADDITIVE
					ENDIF

* restore record number
					IF m.lnOldRecNo &lt;&gt; RECNO()
						IF m.lnOldRecNo = 0
							GO BOTTOM
							IF !EOF()
								SKIP
							ENDIF
						ELSE
							GO (m.lnOldRecNo)
						ENDIF
					ENDIF

					IF m.lnRestoreBuffering &gt;= 4
						CURSORSETPROP('Buffering', m.lnRestoreBuffering)
					ENDIF
				ENDIF
			CATCH TO loException
* we may want to log the exceptions
				llError = .T.
			ENDTRY

			IF m.llError && error occurred during indexing
				.CurrentTag = "" && indicate no tag is created
			ENDIF

		ELSE
			.CurrentTag = ""
		ENDIF

	ELSE
		.CurrentTag = m.lcTag
	ENDIF
ENDWITH

SELECT (m.lnSelect)
IF NOT EMPTY(m.lcOrder)
	SET ORDER TO (m.lcOrder)
ELSE
	SET ORDER TO
ENDIF


	*-- Returns the tag name (if exists) for the passed field name
	PROCEDURE gettagname
		*   Method: 	Gettagname()
		*--------------------- Maintenance Section ----------------------
		*   Change Log:
		*       CREATED 	02/10/2005 - NN
		*		MODIFIED
		*----------------------------------------------------------------
		lparameters tcCursor, tcField

		local lnCount, lcIndexExpr, lnIndexes, lcTagName
		local array laIndex[1]

		*lnIndexes = AIndexes(@laIndex, m.tcCursor)
		lnIndexes = tagcount("", m.tcCursor)
		lcTagName = ""
		*----------------------------------------------------------------
		*-- See if the field name is in the leftmost part of an index
		*-- expression OR simply within an UPPER() statement.
		*-- If so, SET ORDER TO the index.
		*----------------------------------------------------------------
		for lnCount = 1 to m.lnIndexes
			lcIndexExpr = upper(sys(14, m.lnCount, m.tcCursor))

		*--------------------------------------------------
		*--- Allow for character indexes that are simply
		*--- surrounded by "UPPER()". Remove "UPPER()".
		*--------------------------------------------------
			if left(m.lcIndexExpr,6) = 'UPPER('
				lcIndexExpr = substr(m.lcIndexExpr,7)
				lcIndexExpr = substr(m.lcIndexExpr,1,len(m.lcIndexExpr)-1)
			endif

			if m.lcIndexExpr = upper(m.tcField)
				lcTagName = tag(m.lnCount, m.tcCursor)
		*--- Index found...exit ---*
				lnCount = m.lnIndexes + 1
			endif
		endfor

		return m.lcTagName
	ENDPROC


	*-- This method saves column's properties as a script
	PROCEDURE savegridproperties
		*   Method: 	Savegridproperties()
		*--------------------- Maintenance Section ----------------------
		*   Change Log:
		*       CREATED 	03/30/2005 - WGB
		*		CHANGED - NN - March 31, 2005 - 09:53:29
		*----------------------------------------------------------------
		if empty(this.cGridScript)
			local lcGridScript, lcReference, loColumn

			lcReference = sys(1272, this)
			lcReference = "_screen.ActiveForm." + substr(m.lcReference,at(".",m.lcReference)+1)

			set textmerge on
			set textmerge to memvar lcGridScript noshow && TEXTMERGE begins on the next line.

		With &lt;&lt;m.lcReference&gt;&gt;
			for each loColumn in this.columns

		        .&lt;&lt;loColumn.name&gt;&gt;.ControlSource = "&lt;&lt;m.loColumn.ControlSource&gt;&gt;"
		        .&lt;&lt;loColumn.name&gt;&gt;.width = &lt;&lt;m.loColumn.width&gt;&gt;
			next
		endwith
			set textmerge to
			set textmerge off

			this.cGridScript = m.lcGridScript
		endif
	ENDPROC


	*-- This method sets columns' properties
	PROCEDURE resetgridproperties
		*   Method: 	ResetGridProperties()
		*--------------------- Maintenance Section ----------------------
		*   Change Log:
		*       CREATED 	03/30/2005 - WGB
		*		MODIFIED    05/03/2005 - NN
		*----------------------------------------------------------------
		local loColumn
		for each loColumn in this.columns
			loColumn.controlsource = m.loColumn.cOriginalControlSource
		next
	ENDPROC


	*-- This method re-creates indexes used for sorting. It should be called explicitly after re-creating grid's recorsource
	PROCEDURE recreateindexes
		*   Method: 	Recreateindexes()
		*--------------------- Maintenance Section ----------------------
		*   Change Log:
		*       CREATED 	04/01/2005 - NN
		*		MODIFIED
		*----------------------------------------------------------------
		local loColumn, loControl
		for each loColumn in this.columns
			for each loControl in loColumn.controls
				if upper(m.loControl.baseclass) = "HEADER"
					this.CreateTag (m.loControl)
				endif
			endfor
		endfor
	ENDPROC


	*-- This method refreshes navigation buttons on the toolbar
	PROCEDURE refreshtoolbars
		*   Method: 	Refreshtoolbars()
		*--------------------- Maintenance Section ----------------------
		*   Change Log:
		*       CREATED 	07/06/2005 - NN
		*		MODIFIED
		*----------------------------------------------------------------
		local lcRecordSource, lcCondition
		with this
			lcRecordSource = .recordsource
			if vartype(thisform.oToolbar) = "O" and ;
					lower(thisform.cToolbar)='anavtoolbar' and ;
					type('thisform.oToolBar.cntNavButtons')='O' and ;
					thisform.oToolbar.cntNavButtons.visible = .t.
				thisform.lBOF = bof(m.lcRecordSource) or reccount(m.lcRecordSource) = 0
				thisform.lEOF = eof(m.lcRecordSource)

				lcCondition = ""
				go bottom in (m.lcRecordSource)
				if .nRecno = recno(m.lcRecordSource)
					lcCondition = "EOF"
				else
					go top in (m.lcRecordSource)
					if .nRecno = recno(m.lcRecordSource)
						lcCondition = "BOF"
					endif
				endif
				if between(.nRecno,1,reccount(m.lcRecordSource))
					goto .nRecno in (m.lcRecordSource)
				endif
				thisform.oToolbar.cntNavButtons.refresh(m.lcCondition, m.lcRecordSource)
			endif
		endwith
	ENDPROC


	*-- This method binds controls to grid's methods
	PROCEDURE initialize
		local loColumn, loControl
		*Declare ShowCursor in Win32API Long
		* Delegate header's click to grid's HeaderClick custom method
		* and column's textbox DblClick to grid's DblClick
		this.nOriginalWidth = this.Width 

		for each loColumn in this.columns
			loColumn.addproperty("cOriginalControlSource", m.loColumn.controlsource)
			loColumn.addproperty("nOriginalWidth", m.loColumn.width)

			for each loControl in m.loColumn.controls
				if upper(m.loControl.baseclass) = "HEADER"
					loControl.FontSize = 8
					bindevent(m.loControl,"Click",this,"HeaderClick")
					loControl.addproperty("CurrentTag","") && Adds CurrentTag property
					if this.lCreateIndexes && Adds indexes on the fly
						this.CreateTag (m.loControl)
					endif
				else
					if upper(m.loControl.baseclass) = "TEXTBOX"
						bindevent(m.loControl,"DblClick",this,"DblClick")
						bindevent(m.loControl,"KeyPress",this,"KeyPress")
					endif
				endif
			endfor
		endfor

		if this.lManualHighlight
			local lcDynamicBackColor, lcDynamicForeColor

			lcDynamicBackColor = "iif(recno(this.RecordSource)=this.nRecno,this.HighlightBackColor,this.BackColor)"
			lcDynamicForeColor = "iif(recno(this.RecordSource)=this.nRecno,this.HighlightForeColor,this.ForeColor)"

			this.setall("DynamicBackColor", m.lcDynamicBackColor, "Column")
			this.setall("DynamicForeColor", m.lcDynamicForeColor, "Column")
			this.highlightstyle = 0
		endif

		if this.lSaveGridProperties
			this.SaveGridProperties()
		endif
	ENDPROC


	*-- Sets current orderlsortheaders If this property is set to .F. the grid doesn't run initialize method
	PROCEDURE setorder
		LPARAMETERS tcControlSource, tcTagName, toHeader
		LOCAL lnCount, lcField, lcIndexExpr, lcCursor, lnIndexes, ;
			lcAscDesc, lnRecNo, loSelect, loException as Exception

		*this.MousePointer = 0 	&& avoid hourglasses

		LOCAL ARRAY laIndex[1]

		*----------------------------------------------------------
		*-- Get the field, cursor name for the ControlSource cursor
		*----------------------------------------------------------
		lcField = SUBSTR(m.tcControlSource, AT(".", m.tcControlSource) + 1)
		lcCursor = JUSTSTEM(m.tcControlSource)

		* Change by NN
		IF NOT USED(m.lcCursor) OR RECCOUNT(m.lcCursor) = 0 && empty cursor or complex expression
			RETURN
		ENDIF

		thisform.LockScreen = .t.
		lnRecNo = RECNO(m.lcCursor)

		TRY

		IF PCOUNT() &lt; 2 OR EMPTY(m.tcTagName)

		*----------------------------------------------
		*--- A tag name was not passed. Try to find a
		*--- tag for the current column's ControlSource
		*--- Get the number of indexes for the cursor.
		*----------------------------------------------
		*  lnIndexes = AIndexes(@laIndex, lcCursor)
		   lnIndexes = TAGCOUNT(m.lcCursor)
		*----------------------------------------------------------------
		*-- See if the field name is in the leftmost part of an index
		*-- expression OR simply within an UPPER() statement.
		*-- If so, SET ORDER TO the index.
		*----------------------------------------------------------------
			FOR lnCount = 1 TO m.lnIndexes
				lcIndexExpr = UPPER(SYS(14, m.lnCount, m.lcCursor))

		*--------------------------------------------------
		*--- Allow for character indexes that are simply
		*--- surrounded by "UPPER()". Remove "UPPER()".
		*--------------------------------------------------
				IF LEFT(m.lcIndexExpr,6) = 'UPPER('
					lcIndexExpr = SUBSTR(lcIndexExpr,7)
					lcIndexExpr = SUBSTR(lcIndexExpr,1,LEN(lcIndexExpr)-1)
				ENDIF

				IF lcIndexExpr = UPPER(m.lcField)
					tcTagName = TAG(m.lnCount, m.lcCursor)
					IF VARTYPE(m.toHeader) = 'O'
					   toHeader.CurrentTag = m.tcTagName
					ENDIF
					   
		*--- Index found...exit ---*
					EXIT
				ENDIF
			ENDFOR

		ENDIF

		*----------------------------------
		*--- Determine ASCENDING/DESCENDING
		*----------------------------------
		IF NOT EMPTY(tcTagName)
			IF NOT UPPER(tcTagName) == UPPER(THIS.cTagName)
		*--- Default to ascending ---*
				THIS.lAscending = .T.
			ELSE
		*--- Toggle ascending/descending ---*
				THIS.lAscending = NOT THIS.lAscending
			ENDIF

		*------------------------
		*--- SET ORDER to the tag
		*------------------------
			THIS.cTagName = m.tcTagName
			lcAscDesc = IIF(THIS.lAscending, 'ASCENDING', 'DESCENDING')
			SET ORDER TO tcTagName IN (lcCursor) &lcAscDesc

		*---------------------------------------------------
		*--- Refresh the grid and restore the record pointer
		*--- If done in the reverse order (GOTO, Refresh),
		*--- the grid is visually on the wrong record.
		*---------------------------------------------------
			THIS.REFRESH()

		*-- CHANGE - NN - January 25, 2005 - 09:37:53
			IF THIS.lGoTopAfterSorting
				GO TOP IN (m.lcCursor)
			ELSE
				IF RECCOUNT(m.lcCursor) &gt; 0
					GOTO m.lnRecNo IN (m.lcCursor) && This causes automatic save of the record in the record-buffered table
				ENDIF
			ENDIF

		*-- CHANGE - NN - January 25, 2005 - 09:38:59 - added a new property
		ENDIF

		CATCH TO loException
		  * ignore errors 
		ENDTRY

		thisform.LockScreen = .f.
		*this.MousePointer = 0 && Make sure - no hourglasses
	ENDPROC


	*-- Solution by Cesar Chalom to clear hourglass cursor
	PROCEDURE clearwaitcursor
		*!* ClearWaitCursor()
		Local lcPoint As String, lnx As Integer, lny As Integer

		lcPoint = 0h0000000000000000
		ClearWaitCursor_GetCursorPos(@m.lcPoint)

		lnx = CToBin(Substr(m.lcPoint, 1, 4), "4rs")
		lny = CToBin(Substr(m.lcPoint, 5, 4), "4rs")

		ClearWaitCursor_SetCursorPos(m.lnx, m.lny)
	ENDPROC


	PROCEDURE Init
		*DECLARE integer SetCursorPos in WIN32API integer, integer
		Declare Integer GetCursorPos In win32api As ClearWaitCursor_GetCursorPos String  @lpPoint
		Declare Integer SetCursorPos In win32api As ClearWaitCursor_SetCursorPos Integer nX, Integer nY

		RETURN DODEFAULT() AND IIF(this.lSortHeaders, this.Initialize(),.T.) 
	ENDPROC


	PROCEDURE Resize
		IF THIS.nOriginalWidth &gt; 0 && The grid's Init has been run already
			LOCAL lnRatio, loColumn
			lnRatio = THIS.WIDTH / THIS.nOriginalWidth

			FOR EACH loColumn IN THIS.COLUMNS
				loColumn.WIDTH = loColumn.nOriginalWidth * m.lnRatio
			NEXT
		ENDIF
	ENDPROC


	PROCEDURE AfterRowColChange
		lparameters nColIndex
		with this
			.nRecno = recno(this.recordsource)
			if .lManualHighlight
				if inlist(.rowcolchange, 1, 3) && Row change
					.refresh()
				endif
			endif
			.RefreshToolbars()
		endwith
		dodefault(m.nColIndex)
		thisform.lockscreen = .f.
		this.ClearWaitCursor()
	ENDPROC


	PROCEDURE Destroy
		this.oLastSorted = null
		return dodefault()
	ENDPROC


	PROCEDURE BeforeRowColChange
		*   Method: 	Beforerowcolchange() 
		*   Change Log:
		*       CREATED 	03/11/2005 - NN 
		*		MODIFIED
		*----------------------------------------------------------------
		lparameters nColIndex
		if this.lManualHighlight
			thisform.lockscreen = .t.
		endif

		dodefault(m.nColIndex)
	ENDPROC


ENDDEFINE
*
*-- EndDefine: grdsorting
**************************************************</pre>

Attached is the file

[grdSorting class][1]

 [1]: /wp-content/uploads/blogs/DataMgmt/grdSorting.zip "Grid Sorting"