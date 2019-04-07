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

<pre><asp:Label ID="UPDATE_OPENER" runat="server" Visible="false" Text=""&gt;</asp:Label&gt;
    
    <form id="form1" runat="server"&gt;        
        
    <asp:MultiView ID="MltvwViewForms" runat="server"&gt;
        <asp:View ID="VwRegularPerson" runat="server"&gt;
            <asp:SqlDataSource runat="server" ID="PeopleNoneDataSource" ConnectionString="<%$ ConnectionStrings:FCCMSConnectionString %&gt;"
                InsertCommand="PersonInsert" InsertCommandType="StoredProcedure" OnInserted="DataSource_Inserted"&gt;
                <InsertParameters&gt;
                    <asp:Parameter Name="NewPersonID" Direction="Output" Type="Int32" /&gt;
                    <asp:ProfileParameter Name="SiteID" PropertyName="SiteID" Type="Int32" /&gt;
                    <asp:QueryStringParameter Name="PersonType" QueryStringField="ptype" Type="Char"
                        DefaultValue="N" /&gt;
                    <asp:Parameter Name="Title" /&gt;
                    <asp:Parameter Name="Occupation" /&gt;
                    <asp:Parameter Name="BusinessName" /&gt;
                    <asp:Parameter Name="BusAdd1" /&gt;
                    <asp:Parameter Name="BusAdd2" /&gt;
                    <asp:Parameter Name="BusCity" /&gt;
                    <asp:Parameter Name="BusState" /&gt;
                    <asp:Parameter Name="BusZip" /&gt;
                    <asp:Parameter Name="BusEMail" /&gt;
                    <asp:Parameter Name="Pager" /&gt;
                    <asp:Parameter Name="Fax" /&gt;
                    <asp:Parameter Name="BusinessPhone" /&gt;
                    <asp:Parameter Name="SpouseFN" /&gt;
                    <asp:Parameter Name="SpouseLN" /&gt;
                    <asp:Parameter Name="SpouseID" /&gt;
                    <asp:Parameter Name="Anniversary" /&gt;
                </InsertParameters&gt;
            </asp:SqlDataSource&gt;
            <asp:FormView runat="server" ID="PeopleNoneForm" DefaultMode="Insert" EditRowStyle-CssClass="details_row"
                DataSourceID="PeopleNoneDataSource" OnItemInserted="Form_ItemInserted" 
                OnItemInserting="Form_ItemInserting"  OnItemCommand="Form_ItemCommand"&gt;
                <InsertItemTemplate&gt;
                    <div style="margin-top: 20px"&gt;
                        &nbsp;</div&gt;
                    <div id="links"&gt;
                        <label class="link" id="tab1" onclick="showReport(1,<%=clseditmode.NumTabs %&gt;)" title="Basic Info"&gt;
                            Basic Info</label&gt;
                        <label class="link" id="tab2" onclick="showReport(2,<%=clseditmode.NumTabs %&gt;)" title="Contact Info"&gt;
                            Contact Info</label&gt;
                        <label class="link" id="tab3" onclick="showReport(3,<%=clseditmode.NumTabs %&gt;)" title="User Info"&gt;
                            User Info</label&gt;
                        <label class="link" id="tab4" onclick="showReport(4,<%=clseditmode.NumTabs %&gt;)" title="Employment Info"&gt;
                            Employment Info</label&gt;
                        <label class="link" id="tab5" onclick="showReport(5,<%=clseditmode.NumTabs %&gt;)" title="Family Info"&gt;
                            Family Info</label&gt;
                         <% if (clseditmode.NumTabs == 6) { %&gt;   
                                 <label class="link" id="tab6" onclick="showReport(6,<%=clseditmode.NumTabs %&gt;)" title="Membership Info"&gt;
                                    Membership Info</label&gt; 
                                 <% } %&gt;      
                    </div&gt;
                    <div class="boxMain"&gt;
                       <UndoSave:UndoSaveButtons ID="btnRegular" runat="server" /&gt;
                        <!-- 1st --&gt;
                        
                                <div id="info1" class="cl"&gt;
                                    <GenInfo:GeneralInfo ID="GenInfoRegular" runat="server" ShowTitle="true" DOB='<%# Bind("DOB") %&gt;'
                                        FirstName='<%# Bind("FirstName") %&gt;'
                                        LastName='<%# Bind("LastName") %&gt;' MiddleName='<%# Bind("MiddleName") %&gt;' Title='<%# Bind("Title") %&gt;'
                                        Gender='<%# Bind("Gender") %&gt;' ShowParents="false" SetGenderOnChangeEvent="true"
                                        ShowPicture ="true" DefaultPicture = '<%# Bind("DefaultPicture") %&gt;'  /&gt;
                                
                                <div class="cl" style="padding-top: 26px"&gt;
                                    <script type="text/javascript"&gt;TabNav(1,<%=clseditmode.NumTabs %&gt;)</script&gt;
                                </div&gt;
                            </div&gt;
                        <!-- /1st --&gt;
                        <!-- 2nd --&gt;
                        <div id="info2" class="cl dn"&gt;
                            <AddrInfo:AddressInfo ID="AddressInfoRegular" runat="server" Address1='<%# Bind("Address1") %&gt;'
                                Address2='<%# Bind("Address2") %&gt;' CellPhone='<%# Bind("CellPhone") %&gt;' City='<%# Bind("City") %&gt;'
                                Zip='<%# Bind("Zip") %&gt;' State='<%# Bind("State") %&gt;' ScreenName='<%# Bind("ScreenName") %&gt;'
                                HomePhone='<%# Bind("HomePhone") %&gt;' 
                                Email='<%# Bind("Email") %&gt;' 
                                SecondEmail='<%# Bind("SecondEmail") %&gt;'/&gt;                        
                        <div class="cl" style="padding-top: 15px"&gt;
                            <script type="text/javascript"&gt;TabNav(2,<%=clseditmode.NumTabs %&gt;)</script&gt;
                        </div&gt;
                    </div&gt;
                    <!-- /2nd --&gt;
                    <!-- 3rd --&gt;
                    <div id="info3" class="cl dn"&gt;
                        <UserInfo:UserInfo ID="UserInfoRegular" runat="server" Pwd='<%# Bind("Pwd") %&gt;' UserName='<%# Bind("UserName") %&gt;' /&gt;
                        <div class="cl" style="padding-top: 26px"&gt;
                            <script type="text/javascript"&gt;TabNav(3,<%=clseditmode.NumTabs %&gt;)</script&gt;
                        </div&gt;
                    </div&gt;
                    <!-- /3rd --&gt;
                    <!-- 4th --&gt;
                    <div id="info4" class="cl dn"&gt;
                        <BusInfo:BussinessInfo ID="BussinessInfoRegular" runat="server" Occupation='<%# Bind("Occupation") %&gt;'
                            BusinessName='<%# Bind("BusinessName") %&gt;' BusAdd1='<%# Bind("BusAdd1") %&gt;' BusAdd2='<%# Bind("BusAdd2") %&gt;'
                            BusCity='<%# Bind("BusCity") %&gt;' BusState='<%# Bind("BusState") %&gt;' BusZip='<%# Bind("BusZip") %&gt;'
                            Pager='<%# Bind("Pager") %&gt;' Fax='<%# Bind("Fax") %&gt;' BusinessPhone='<%# Bind("BusinessPhone") %&gt;' /&gt;
                        <div class="cl" style="padding-top: 26px"&gt;
                            <script type="text/javascript"&gt;TabNav(4,<%=clseditmode.NumTabs %&gt;)</script&gt;
                        </div&gt;
                    </div&gt;
                    <!-- /4th --&gt;
                    <!-- 5th --&gt;
                    <div id="info5" class="cl dn"&gt;
                    <div class="cl"&gt;
                        <label class="bx"&gt;
                            Spouse</label&gt;
                            <span class="bx" id="spouseName"&gt;
                                
                                <Custom:AutoSuggestBox ID="asbSpouse" ToolTip="Start typing and select from the list"
                                    runat="server" DataType = '<%# Eval("Gender") =="M"?"Mother":"Father" %&gt;' IncludeMoreMenuItem="True" KeyPressDelay="300"
                                    MaxSuggestChars="5" MoreMenuItemLabel="..." NumMenuItems="10" ResourcesDir="/asb_includes"
                                    SelectedValue='<%# Bind("SpouseID") %&gt;' SelMenuItemCSSClass="asbSelMenuItem"
                                    OnPreRender="AsbPrerender" UseIFrame="True" MaxLength="80"&gt;</Custom:AutoSuggestBox&gt;
                               
                            </span&gt;
                    <span class="bx" style="width: 180px"&gt;
                    <a href=<%# clseditmode.CreateLink(Eval("SpouseID"),"AddressInfoRegular","Spouse") %&gt;</a&gt;</span&gt;
                       <label class="bx"&gt;
                            Anniversary</label&gt;<span class="bx"&gt;<asp:TextBox ID="tbAnniversary" runat="server"
                                AutoCompleteType="Disabled" Text='<%# Bind("Anniversary", "{0:M/d/yyyy}") %&gt;'&gt;</asp:TextBox&gt;
                                <asp:RegularExpressionValidator ID="regexAnniversary" runat="server" ControlToValidate="tbAnniversary"
                                    Display="Dynamic" ErrorMessage="Incorrect Anniversary (m/d/yyyy)" 
                                    ValidationExpression="(?:(?:(?:0?[13578]|1[02])(/|-|.)31)1|(?:(?:0?[13-9]|1[0-2])(/|-|.)(?:29|30)2))(?:(?:1[6-9]|[2-9]d)?d{2})$|^(?:0?2(/|-|.)293(?:(?:(?:1[6-9]|[2-9]d)?(?:0[48]|[2468][048]|[13579][26])|(?:(?:16|[2468][048]|[3579][26])00))))$|^(?:(?:0?[1-9])|(?:1[0-2]))(/|-|.)(?:0?[1-9]|1d|2[0-8])4(?:(?:1[6-9]|[2-9]d)?d{2})"&gt;</asp:RegularExpressionValidator&gt;</span&gt;
                        <label class="bx"&gt;Marital Status</label&gt;<span class="bx"&gt;
                        <asp:DropDownList runat="server" ID="drpMsts" SelectedValue='<%# Bind("MaritalStatus") %&gt;'&gt;
                            <asp:ListItem Value="M"&gt;Married</asp:ListItem&gt;
                            <asp:ListItem Value="D"&gt;Divorced</asp:ListItem&gt;
                            <asp:ListItem Value="S"&gt;Separated</asp:ListItem&gt;
                            <asp:ListItem Value="W"&gt;Widowed</asp:ListItem&gt;
                        </asp:DropDownList&gt;</span&gt;</div&gt; 
                        
                        <div class="cl"&gt;
                            <label class="bx"&gt;
                                Father</label&gt;<span class="bx" id = "fatherName"&gt;<Custom:AutoSuggestBox ID="fatherName" Text='<%# Bind("Father") %&gt;'
                                    DataType="Father" MaxLength="100" OnPreRender="AsbPrerender" ToolTip="Start typing and select from the list"
                                    runat="server" ResourcesDir="/asb_includes" /&gt;</span&gt;
                                    <span class="bx" style="width: 180px"&gt; 
                                    <a href=<%# clseditmode.CreateLink(Eval("FatherID"),"AddressInfoRegular","Father") %&gt;</a&gt;</span&gt;
                           </div&gt;
                           <div class="cl"&gt;
                            <label class="bx"&gt;
                                Mother</label&gt;<span class="bx" id ="motherName"&gt;<Custom:AutoSuggestBox ID="motherName" Text='<%# Bind("Mother") %&gt;'
                                    DataType="Mother" MaxLength="100" OnPreRender="AsbPrerender" ToolTip="Start typing and select from the list"
                                    runat="server" ResourcesDir="/asb_includes" /&gt;</span&gt;
                                    <span class="bx" style="width: 180px"&gt;
                                    <a href=<%# clseditmode.CreateLink(Eval("MotherID"),"AddressInfoRegular","Mother") %&gt;</a&gt;</span&gt;
                        </div&gt;
                        <div class="cl"&gt;
                            <label class="bx"&gt;
                                Comment</label&gt;<span class="bx"&gt;<asp:TextBox ID="txtComment" Width="120" Text='<%# Bind("Comment") %&gt;'
                                    TextMode="MultiLine" runat="server" AutoCompleteType="Disabled" /&gt;</span&gt;
                        </div&gt;
                        <div class="cl" style="padding-top: 26px"&gt;
                            <script type="text/javascript"&gt;TabNav(5,<%=clseditmode.NumTabs %&gt;)</script&gt;
                        </div&gt;
                    </div&gt;
                    <!-- /5th --&gt;
                     <% if (clseditmode.NumTabs == 6) { %&gt; 
	                     
                                <!-- 6th --&gt;
                                <div id="info6" class="cl dn"&gt;
                                    <MembInfo:MembershipInfo ID="MembershipInfoRegular" runat="server" 
                                    BillQuaterly ='<%# Bind("BillQuaterly") %&gt;' IsMember ='<%# Bind("IsMember") %&gt;'
                                    PaymentMethod='<%# Bind("PaymentMethod") %&gt;' StartDate ='<%# Bind("StartDate") %&gt;' /&gt;
                               <div class="cl" style="padding-top: 26px"&gt;
                                    <script type="text/javascript"&gt;TabNav(6,<%=clseditmode.NumTabs %&gt;)</script&gt;
                                </div&gt;                                    
                                </div&gt;
                                <!-- /6th --&gt;
                                <% } %&gt;   
                                <script type="text/javascript"&gt;showReport(1,<%=clseditmode.NumTabs %&gt;);</script&gt;
                   </div&gt;
                </InsertItemTemplate&gt;
                <EditRowStyle CssClass="details_row" /&gt;
            </asp:FormView&gt;
        </asp:View&gt;
        <!-- End of the Regular Person --&gt;</pre>

etc.

The InsertPerson.cs (code behind) had the following code in regards to showing the error message:

<pre>/// <summary&gt;
    /// Display message
    /// </summary&gt;
    /// <param name="msg"&gt;</param&gt;
    /// <param name="CloseWindow"&gt;</param&gt;
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
                this.UPDATE_OPENER.Text = "<script language="JavaScript"&gt;changeParent('" + Request.QueryString["pid"] + "','" + e.Values["LastName"] + ", " + e.Values["FirstName"] + " " + (e.Values["MiddleName"] ?? "") + "'," + 
                    this.NewPersonID + ",'" + Text.Replace("'","\'")  + "');</script&gt;";
                CloseWindow = true;
                this.UPDATE_OPENER.Visible = true;     
            }
            else
            {
                Text = string.Format("You just inserted {0} {1} <a href='peopleV.aspx?perid={2}'&gt;" +
                   "Edit Here</a&gt;", e.Values["FirstName"], e.Values["LastName"], this.NewPersonID);
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

<pre><%@ Page Language="C#" MasterPageFile="~/admin.master" AutoEventWireup="true" CodeFile="CreatePerson.aspx.cs"
    Theme="Profesional" Inherits="Coordinator_People_CreatePerson" Title="Create Users" %&gt;

<asp:Content ID="Content1" ContentPlaceHolderID="ContentPlaceHolder1" runat="Server"&gt;
<div id="spnStatFrame" style="display:none;"&gt;
<table style="font-weight: bolder; vertical-align: middle; color: #FFFFFF; width: 32%; text-align: center;" cellpadding="0" cellspacing="0"&gt;
<tr&gt;
    <td&gt;<img alt="" src="../../images/tl-LightBlue.gif" height="10" width="10" /&gt;</td&gt;
    <td style="background-color: #5BA2EB;"&gt;&nbsp;</td&gt;
    <td&gt;<img alt="" src="../../images/tr-LightBlue.gif" height="10" width="10" /&gt;</td&gt;
</tr&gt;
<tr style="background-color: #5BA2EB;"&gt;
    <td&gt;&nbsp;</td&gt;
    <td&gt;<span id="spnStat"&gt;</span&gt;</td&gt;
    <td&gt;&nbsp;</td&gt;
</tr&gt;
<tr&gt;
    <td&gt;<img alt="" src="../../images/bl-LightBlue.gif" height="10" width="10" /&gt;</td&gt;
    <td style="background-color: #5BA2EB;"&gt;&nbsp;</td&gt;
    <td&gt;<img alt="" src="../../images/br-LightBlue.gif" height="10" width="10" /&gt;</td&gt;
</tr&gt;
</table&gt;
</div&gt;

<script type="text/javascript"&gt;
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
</script&gt;

<div class="p5" style="text-align:right"&gt;
    View all People <a href="peopleV.aspx"&gt;<img alt="View People" src="../../images/id_card_view.png" title="View all People" class="peopleVNavIcon"/&gt;</a&gt;
</div&gt;


<div&gt;
Create a
<select id="PersonType" name="PersonType" onchange="javascript:go();"&gt;
                        <option selected="selected" value=""&gt;--Choose a Person Type--</option&gt;
                        <option value="InsertPerson.aspx?ptype=B"&gt;Special Friend</option&gt;
                        <option value="InsertPerson.aspx?ptype=N"&gt;Regular Person</option&gt;
                        <option value="InsertPerson.aspx?ptype=A"&gt;Adult Volunteer</option&gt;
            -- other options --
</select&gt;
</div&gt;
<iframe id="myframe" scrolling="no" marginwidth="0" marginheight="0" 
frameborder="0" style="text-align: center; overflow: visible; 
    width:710px; display: none;" &gt;</iframe&gt;
<div id="loading" class="more"&gt;Loading...</div&gt;

    <script type="text/ecmascript"&gt;
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
       
    </script&gt;

</asp:Content&gt;</pre>

So, hopefully you see the logic of the pages. The CreatePerson page had a place to show an error message or a link if the insert worked correctly. From the InsertPerson page we manipulated the parent CreatePerson page and displayed the appropriate message (either the error message or a link to edit a newly inserted person).

 [1]: http://forums.asp.net/t/1580016.aspx?PageIndex=1
 [2]: /index.php/WebDev/?p=553