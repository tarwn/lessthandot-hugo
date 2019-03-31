---
title: How to return error messages from the SQL Server to the client using SQLDataSource
author: Naomi Nosonovsky
type: post
date: 2010-07-25T15:05:20+00:00
url: /index.php/webdev/webdesigngraphicsstyling/how-to-return-error-messages-from-the-sq/
views:
  - 14588
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling

---
The recent [thread][1] in ASP.NET forums is the primary reason for this blog post.

I want to show how error messages from the SQL Server were handled in our web pages. The site I was working on had most of the functionality developed by the previous developer. Unfortunately, most pages didn&#8217;t use a separation of layers and used SQLDataSource for data manipulations.

The sample I want to show was called from InsertPerson.aspx page. For inserting a person we used the stored procedure I showed in my previous blog [How to insert information into multiple related tables and return ID using SQLDataSource][2].

For inserting a new person the InsertPerson.aspx page was used that had multiview control, FormView controls with different tabs using User Controls for each tab (the tab was a special control designed by my colleague).

Here is the beginning of the page&#8217;s code:

<pre>&lt;asp:Label ID="UPDATE_OPENER" runat="server" Visible="false" Text=""&gt;&lt;/asp:Label&gt;
    
    &lt;form id="form1" runat="server"&gt;        
        
    &lt;asp:MultiView ID="MltvwViewForms" runat="server"&gt;
        &lt;asp:View ID="VwRegularPerson" runat="server"&gt;
            &lt;asp:SqlDataSource runat="server" ID="PeopleNoneDataSource" ConnectionString="&lt;%$ ConnectionStrings:FCCMSConnectionString %&gt;"
                InsertCommand="PersonInsert" InsertCommandType="StoredProcedure" OnInserted="DataSource_Inserted"&gt;
                &lt;InsertParameters&gt;
                    &lt;asp:Parameter Name="NewPersonID" Direction="Output" Type="Int32" /&gt;
                    &lt;asp:ProfileParameter Name="SiteID" PropertyName="SiteID" Type="Int32" /&gt;
                    &lt;asp:QueryStringParameter Name="PersonType" QueryStringField="ptype" Type="Char"
                        DefaultValue="N" /&gt;
                    &lt;asp:Parameter Name="Title" /&gt;
                    &lt;asp:Parameter Name="Occupation" /&gt;
                    &lt;asp:Parameter Name="BusinessName" /&gt;
                    &lt;asp:Parameter Name="BusAdd1" /&gt;
                    &lt;asp:Parameter Name="BusAdd2" /&gt;
                    &lt;asp:Parameter Name="BusCity" /&gt;
                    &lt;asp:Parameter Name="BusState" /&gt;
                    &lt;asp:Parameter Name="BusZip" /&gt;
                    &lt;asp:Parameter Name="BusEMail" /&gt;
                    &lt;asp:Parameter Name="Pager" /&gt;
                    &lt;asp:Parameter Name="Fax" /&gt;
                    &lt;asp:Parameter Name="BusinessPhone" /&gt;
                    &lt;asp:Parameter Name="SpouseFN" /&gt;
                    &lt;asp:Parameter Name="SpouseLN" /&gt;
                    &lt;asp:Parameter Name="SpouseID" /&gt;
                    &lt;asp:Parameter Name="Anniversary" /&gt;
                &lt;/InsertParameters&gt;
            &lt;/asp:SqlDataSource&gt;
            &lt;asp:FormView runat="server" ID="PeopleNoneForm" DefaultMode="Insert" EditRowStyle-CssClass="details_row"
                DataSourceID="PeopleNoneDataSource" OnItemInserted="Form_ItemInserted" 
                OnItemInserting="Form_ItemInserting"  OnItemCommand="Form_ItemCommand"&gt;
                &lt;InsertItemTemplate&gt;
                    &lt;div style="margin-top: 20px"&gt;
                        &nbsp;&lt;/div&gt;
                    &lt;div id="links"&gt;
                        &lt;label class="link" id="tab1" onclick="showReport(1,&lt;%=clseditmode.NumTabs %&gt;)" title="Basic Info"&gt;
                            Basic Info&lt;/label&gt;
                        &lt;label class="link" id="tab2" onclick="showReport(2,&lt;%=clseditmode.NumTabs %&gt;)" title="Contact Info"&gt;
                            Contact Info&lt;/label&gt;
                        &lt;label class="link" id="tab3" onclick="showReport(3,&lt;%=clseditmode.NumTabs %&gt;)" title="User Info"&gt;
                            User Info&lt;/label&gt;
                        &lt;label class="link" id="tab4" onclick="showReport(4,&lt;%=clseditmode.NumTabs %&gt;)" title="Employment Info"&gt;
                            Employment Info&lt;/label&gt;
                        &lt;label class="link" id="tab5" onclick="showReport(5,&lt;%=clseditmode.NumTabs %&gt;)" title="Family Info"&gt;
                            Family Info&lt;/label&gt;
                         &lt;% if (clseditmode.NumTabs == 6) { %&gt;   
                                 &lt;label class="link" id="tab6" onclick="showReport(6,&lt;%=clseditmode.NumTabs %&gt;)" title="Membership Info"&gt;
                                    Membership Info&lt;/label&gt; 
                                 &lt;% } %&gt;      
                    &lt;/div&gt;
                    &lt;div class="boxMain"&gt;
                       &lt;UndoSave:UndoSaveButtons ID="btnRegular" runat="server" /&gt;
                        &lt;!-- 1st --&gt;
                        
                                &lt;div id="info1" class="cl"&gt;
                                    &lt;GenInfo:GeneralInfo ID="GenInfoRegular" runat="server" ShowTitle="true" DOB='&lt;%# Bind("DOB") %&gt;'
                                        FirstName='&lt;%# Bind("FirstName") %&gt;'
                                        LastName='&lt;%# Bind("LastName") %&gt;' MiddleName='&lt;%# Bind("MiddleName") %&gt;' Title='&lt;%# Bind("Title") %&gt;'
                                        Gender='&lt;%# Bind("Gender") %&gt;' ShowParents="false" SetGenderOnChangeEvent="true"
                                        ShowPicture ="true" DefaultPicture = '&lt;%# Bind("DefaultPicture") %&gt;'  /&gt;
                                
                                &lt;div class="cl" style="padding-top: 26px"&gt;
                                    &lt;script type="text/javascript"&gt;TabNav(1,&lt;%=clseditmode.NumTabs %&gt;)&lt;/script&gt;
                                &lt;/div&gt;
                            &lt;/div&gt;
                        &lt;!-- /1st --&gt;
                        &lt;!-- 2nd --&gt;
                        &lt;div id="info2" class="cl dn"&gt;
                            &lt;AddrInfo:AddressInfo ID="AddressInfoRegular" runat="server" Address1='&lt;%# Bind("Address1") %&gt;'
                                Address2='&lt;%# Bind("Address2") %&gt;' CellPhone='&lt;%# Bind("CellPhone") %&gt;' City='&lt;%# Bind("City") %&gt;'
                                Zip='&lt;%# Bind("Zip") %&gt;' State='&lt;%# Bind("State") %&gt;' ScreenName='&lt;%# Bind("ScreenName") %&gt;'
                                HomePhone='&lt;%# Bind("HomePhone") %&gt;' 
                                Email='&lt;%# Bind("Email") %&gt;' 
                                SecondEmail='&lt;%# Bind("SecondEmail") %&gt;'/&gt;                        
                        &lt;div class="cl" style="padding-top: 15px"&gt;
                            &lt;script type="text/javascript"&gt;TabNav(2,&lt;%=clseditmode.NumTabs %&gt;)&lt;/script&gt;
                        &lt;/div&gt;
                    &lt;/div&gt;
                    &lt;!-- /2nd --&gt;
                    &lt;!-- 3rd --&gt;
                    &lt;div id="info3" class="cl dn"&gt;
                        &lt;UserInfo:UserInfo ID="UserInfoRegular" runat="server" Pwd='&lt;%# Bind("Pwd") %&gt;' UserName='&lt;%# Bind("UserName") %&gt;' /&gt;
                        &lt;div class="cl" style="padding-top: 26px"&gt;
                            &lt;script type="text/javascript"&gt;TabNav(3,&lt;%=clseditmode.NumTabs %&gt;)&lt;/script&gt;
                        &lt;/div&gt;
                    &lt;/div&gt;
                    &lt;!-- /3rd --&gt;
                    &lt;!-- 4th --&gt;
                    &lt;div id="info4" class="cl dn"&gt;
                        &lt;BusInfo:BussinessInfo ID="BussinessInfoRegular" runat="server" Occupation='&lt;%# Bind("Occupation") %&gt;'
                            BusinessName='&lt;%# Bind("BusinessName") %&gt;' BusAdd1='&lt;%# Bind("BusAdd1") %&gt;' BusAdd2='&lt;%# Bind("BusAdd2") %&gt;'
                            BusCity='&lt;%# Bind("BusCity") %&gt;' BusState='&lt;%# Bind("BusState") %&gt;' BusZip='&lt;%# Bind("BusZip") %&gt;'
                            Pager='&lt;%# Bind("Pager") %&gt;' Fax='&lt;%# Bind("Fax") %&gt;' BusinessPhone='&lt;%# Bind("BusinessPhone") %&gt;' /&gt;
                        &lt;div class="cl" style="padding-top: 26px"&gt;
                            &lt;script type="text/javascript"&gt;TabNav(4,&lt;%=clseditmode.NumTabs %&gt;)&lt;/script&gt;
                        &lt;/div&gt;
                    &lt;/div&gt;
                    &lt;!-- /4th --&gt;
                    &lt;!-- 5th --&gt;
                    &lt;div id="info5" class="cl dn"&gt;
                    &lt;div class="cl"&gt;
                        &lt;label class="bx"&gt;
                            Spouse&lt;/label&gt;
                            &lt;span class="bx" id="spouseName"&gt;
                                
                                &lt;Custom:AutoSuggestBox ID="asbSpouse" ToolTip="Start typing and select from the list"
                                    runat="server" DataType = '&lt;%# Eval("Gender") =="M"?"Mother":"Father" %&gt;' IncludeMoreMenuItem="True" KeyPressDelay="300"
                                    MaxSuggestChars="5" MoreMenuItemLabel="..." NumMenuItems="10" ResourcesDir="/asb_includes"
                                    SelectedValue='&lt;%# Bind("SpouseID") %&gt;' SelMenuItemCSSClass="asbSelMenuItem"
                                    OnPreRender="AsbPrerender" UseIFrame="True" MaxLength="80"&gt;&lt;/Custom:AutoSuggestBox&gt;
                               
                            &lt;/span&gt;
                    &lt;span class="bx" style="width: 180px"&gt;
                    &lt;a href=&lt;%# clseditmode.CreateLink(Eval("SpouseID"),"AddressInfoRegular","Spouse") %&gt;&lt;/a&gt;&lt;/span&gt;
                       &lt;label class="bx"&gt;
                            Anniversary&lt;/label&gt;&lt;span class="bx"&gt;&lt;asp:TextBox ID="tbAnniversary" runat="server"
                                AutoCompleteType="Disabled" Text='&lt;%# Bind("Anniversary", "{0:M/d/yyyy}") %&gt;'&gt;&lt;/asp:TextBox&gt;
                                &lt;asp:RegularExpressionValidator ID="regexAnniversary" runat="server" ControlToValidate="tbAnniversary"
                                    Display="Dynamic" ErrorMessage="Incorrect Anniversary (m/d/yyyy)" 
                                    ValidationExpression="(?:(?:(?:0?[13578]|1[02])(/|-|.)31)1|(?:(?:0?[13-9]|1[0-2])(/|-|.)(?:29|30)2))(?:(?:1[6-9]|[2-9]d)?d{2})$|^(?:0?2(/|-|.)293(?:(?:(?:1[6-9]|[2-9]d)?(?:0[48]|[2468][048]|[13579][26])|(?:(?:16|[2468][048]|[3579][26])00))))$|^(?:(?:0?[1-9])|(?:1[0-2]))(/|-|.)(?:0?[1-9]|1d|2[0-8])4(?:(?:1[6-9]|[2-9]d)?d{2})"&gt;&lt;/asp:RegularExpressionValidator&gt;&lt;/span&gt;
                        &lt;label class="bx"&gt;Marital Status&lt;/label&gt;&lt;span class="bx"&gt;
                        &lt;asp:DropDownList runat="server" ID="drpMsts" SelectedValue='&lt;%# Bind("MaritalStatus") %&gt;'&gt;
                            &lt;asp:ListItem Value="M"&gt;Married&lt;/asp:ListItem&gt;
                            &lt;asp:ListItem Value="D"&gt;Divorced&lt;/asp:ListItem&gt;
                            &lt;asp:ListItem Value="S"&gt;Separated&lt;/asp:ListItem&gt;
                            &lt;asp:ListItem Value="W"&gt;Widowed&lt;/asp:ListItem&gt;
                        &lt;/asp:DropDownList&gt;&lt;/span&gt;&lt;/div&gt; 
                        
                        &lt;div class="cl"&gt;
                            &lt;label class="bx"&gt;
                                Father&lt;/label&gt;&lt;span class="bx" id = "fatherName"&gt;&lt;Custom:AutoSuggestBox ID="fatherName" Text='&lt;%# Bind("Father") %&gt;'
                                    DataType="Father" MaxLength="100" OnPreRender="AsbPrerender" ToolTip="Start typing and select from the list"
                                    runat="server" ResourcesDir="/asb_includes" /&gt;&lt;/span&gt;
                                    &lt;span class="bx" style="width: 180px"&gt; 
                                    &lt;a href=&lt;%# clseditmode.CreateLink(Eval("FatherID"),"AddressInfoRegular","Father") %&gt;&lt;/a&gt;&lt;/span&gt;
                           &lt;/div&gt;
                           &lt;div class="cl"&gt;
                            &lt;label class="bx"&gt;
                                Mother&lt;/label&gt;&lt;span class="bx" id ="motherName"&gt;&lt;Custom:AutoSuggestBox ID="motherName" Text='&lt;%# Bind("Mother") %&gt;'
                                    DataType="Mother" MaxLength="100" OnPreRender="AsbPrerender" ToolTip="Start typing and select from the list"
                                    runat="server" ResourcesDir="/asb_includes" /&gt;&lt;/span&gt;
                                    &lt;span class="bx" style="width: 180px"&gt;
                                    &lt;a href=&lt;%# clseditmode.CreateLink(Eval("MotherID"),"AddressInfoRegular","Mother") %&gt;&lt;/a&gt;&lt;/span&gt;
                        &lt;/div&gt;
                        &lt;div class="cl"&gt;
                            &lt;label class="bx"&gt;
                                Comment&lt;/label&gt;&lt;span class="bx"&gt;&lt;asp:TextBox ID="txtComment" Width="120" Text='&lt;%# Bind("Comment") %&gt;'
                                    TextMode="MultiLine" runat="server" AutoCompleteType="Disabled" /&gt;&lt;/span&gt;
                        &lt;/div&gt;
                        &lt;div class="cl" style="padding-top: 26px"&gt;
                            &lt;script type="text/javascript"&gt;TabNav(5,&lt;%=clseditmode.NumTabs %&gt;)&lt;/script&gt;
                        &lt;/div&gt;
                    &lt;/div&gt;
                    &lt;!-- /5th --&gt;
                     &lt;% if (clseditmode.NumTabs == 6) { %&gt; 
	                     
                                &lt;!-- 6th --&gt;
                                &lt;div id="info6" class="cl dn"&gt;
                                    &lt;MembInfo:MembershipInfo ID="MembershipInfoRegular" runat="server" 
                                    BillQuaterly ='&lt;%# Bind("BillQuaterly") %&gt;' IsMember ='&lt;%# Bind("IsMember") %&gt;'
                                    PaymentMethod='&lt;%# Bind("PaymentMethod") %&gt;' StartDate ='&lt;%# Bind("StartDate") %&gt;' /&gt;
                               &lt;div class="cl" style="padding-top: 26px"&gt;
                                    &lt;script type="text/javascript"&gt;TabNav(6,&lt;%=clseditmode.NumTabs %&gt;)&lt;/script&gt;
                                &lt;/div&gt;                                    
                                &lt;/div&gt;
                                &lt;!-- /6th --&gt;
                                &lt;% } %&gt;   
                                &lt;script type="text/javascript"&gt;showReport(1,&lt;%=clseditmode.NumTabs %&gt;);&lt;/script&gt;
                   &lt;/div&gt;
                &lt;/InsertItemTemplate&gt;
                &lt;EditRowStyle CssClass="details_row" /&gt;
            &lt;/asp:FormView&gt;
        &lt;/asp:View&gt;
        &lt;!-- End of the Regular Person --&gt;</pre>

etc.

The InsertPerson.cs (code behind) had the following code in regards to showing the error message:

<pre>/// &lt;summary&gt;
    /// Display message
    /// &lt;/summary&gt;
    /// &lt;param name="msg"&gt;&lt;/param&gt;
    /// &lt;param name="CloseWindow"&gt;&lt;/param&gt;
    protected void DisplayMessage(string msg, bool CloseWindow)
    {
        string Script="";
        if (Request.QueryString["pid"] != null && Request.QueryString["pid"].Length &gt; 0) {
            if (msg != "") { Script = string.Format("alert("{0}");", msg) + (CloseWindow ? "window.close;" : ""); } 
        }
        else
        {
            Script = string.Format("parent.DisplayMessage("{0}");", msg);}
        
        if (Script!="") 
           this.ClientScript.RegisterClientScriptBlock(this.GetType(), "Message", Script, true);
    }</pre>

The way the error was passed back to the client was handled in Form_ItemInserted event:

<pre>protected void Form_ItemInserted(object sender, FormViewInsertedEventArgs e)
    {
        string Text;
        bool CloseWindow = false;

        if (e.Exception == null)
        {
            if (Request.QueryString["pid"] != null && Request.QueryString["pid"].Length &gt; 0)
            {               
                Text = string.Format("You just inserted {0} {1}", e.Values["FirstName"], e.Values["LastName"]);
                this.UPDATE_OPENER.Text = "&lt;script language="JavaScript"&gt;changeParent('" + Request.QueryString["pid"] + "','" + e.Values["LastName"] + ", " + e.Values["FirstName"] + " " + (e.Values["MiddleName"] ?? "") + "'," + 
                    this.NewPersonID + ",'" + Text.Replace("'","\'")  + "');&lt;/script&gt;";
                CloseWindow = true;
                this.UPDATE_OPENER.Visible = true;     
            }
            else
            {
                Text = string.Format("You just inserted {0} {1} &lt;a href='peopleV.aspx?perid={2}'&gt;" +
                   "Edit Here&lt;/a&gt;", e.Values["FirstName"], e.Values["LastName"], this.NewPersonID);
                this.DisplayMessage(Text, CloseWindow);
                this.MltvwViewForms.Visible = false;
            }
        }
        else // Exception case
           {
                Text = "The data was entered in an incorrect format, please check it over." +
                       " " + e.Exception.Message.Replace(Environment.NewLine, "\n").Replace(""", "\"");
                this.DisplayMessage(Text, CloseWindow);

                e.ExceptionHandled = true;
                e.KeepInInsertMode = true;
            }
    }</pre>

The page calling the InsertPerson page was called CreatePerson.aspx and had the following ASPX code:

<pre>&lt;%@ Page Language="C#" MasterPageFile="~/admin.master" AutoEventWireup="true" CodeFile="CreatePerson.aspx.cs"
    Theme="Profesional" Inherits="Coordinator_People_CreatePerson" Title="Create Users" %&gt;

&lt;asp:Content ID="Content1" ContentPlaceHolderID="ContentPlaceHolder1" runat="Server"&gt;
&lt;div id="spnStatFrame" style="display:none;"&gt;
&lt;table style="font-weight: bolder; vertical-align: middle; color: #FFFFFF; width: 32%; text-align: center;" cellpadding="0" cellspacing="0"&gt;
&lt;tr&gt;
    &lt;td&gt;&lt;img alt="" src="../../images/tl-LightBlue.gif" height="10" width="10" /&gt;&lt;/td&gt;
    &lt;td style="background-color: #5BA2EB;"&gt;&nbsp;&lt;/td&gt;
    &lt;td&gt;&lt;img alt="" src="../../images/tr-LightBlue.gif" height="10" width="10" /&gt;&lt;/td&gt;
&lt;/tr&gt;
&lt;tr style="background-color: #5BA2EB;"&gt;
    &lt;td&gt;&nbsp;&lt;/td&gt;
    &lt;td&gt;&lt;span id="spnStat"&gt;&lt;/span&gt;&lt;/td&gt;
    &lt;td&gt;&nbsp;&lt;/td&gt;
&lt;/tr&gt;
&lt;tr&gt;
    &lt;td&gt;&lt;img alt="" src="../../images/bl-LightBlue.gif" height="10" width="10" /&gt;&lt;/td&gt;
    &lt;td style="background-color: #5BA2EB;"&gt;&nbsp;&lt;/td&gt;
    &lt;td&gt;&lt;img alt="" src="../../images/br-LightBlue.gif" height="10" width="10" /&gt;&lt;/td&gt;
&lt;/tr&gt;
&lt;/table&gt;
&lt;/div&gt;

&lt;script type="text/javascript"&gt;
    var dv = document.getElementById("spnStatFrame");
    var Spn = document.getElementById("spnStat");
    
    function DisplayMessage(Message)
    {   if (Message !=""){         
            dv.style.display = "inline";        
            Spn.innerHTML = Message;
            setTimeout("dv.style.display = 'none'", 300000);}
        else
            dv.style.display = "none";            
            
    }
&lt;/script&gt;

&lt;div class="p5" style="text-align:right"&gt;
    View all People &lt;a href="peopleV.aspx"&gt;&lt;img alt="View People" src="../../images/id_card_view.png" title="View all People" class="peopleVNavIcon"/&gt;&lt;/a&gt;
&lt;/div&gt;


&lt;div&gt;
Create a
&lt;select id="PersonType" name="PersonType" onchange="javascript:go();"&gt;
                        &lt;option selected="selected" value=""&gt;--Choose a Person Type--&lt;/option&gt;
                        &lt;option value="InsertPerson.aspx?ptype=B"&gt;Special Friend&lt;/option&gt;
                        &lt;option value="InsertPerson.aspx?ptype=N"&gt;Regular Person&lt;/option&gt;
                        &lt;option value="InsertPerson.aspx?ptype=A"&gt;Adult Volunteer&lt;/option&gt;
            -- other options --
&lt;/select&gt;
&lt;/div&gt;
&lt;iframe id="myframe" scrolling="no" marginwidth="0" marginheight="0" 
frameborder="0" style="text-align: center; overflow: visible; 
    width:710px; display: none;" &gt;&lt;/iframe&gt;
&lt;div id="loading" class="more"&gt;Loading...&lt;/div&gt;

    &lt;script type="text/ecmascript"&gt;
    var loading = document.getElementById("loading");
    function go()
    {
        var SelInsert = document.getElementById("PersonType");
        if(SelInsert.selectedIndex != 0){
            loading.style.display = 'block'
            var frm =loadintoIframe('myframe', SelInsert.options[SelInsert.selectedIndex].value);
            DisplayMessage("");   
        //setTimeout("loading.style.display = 'none'", 5000);    
        //var x=document.getElementById("myframe").contentDocument;
          // This doesn't work for User Controls    
                
          //document.frames["myframe"].onload = function() {showReqFld(); };
          
        } 
                 
    }   
       
    &lt;/script&gt;

&lt;/asp:Content&gt;</pre>

So, hopefully you see the logic of the pages. The CreatePerson page had a place to show an error message or a link if the insert worked correctly. From the InsertPerson page we manipulated the parent CreatePerson page and displayed the appropriate message (either the error message or a link to edit a newly inserted person).

 [1]: http://forums.asp.net/t/1580016.aspx?PageIndex=1
 [2]: /index.php/WebDev/?p=553