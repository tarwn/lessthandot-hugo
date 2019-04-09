---
title: How to return error messages from the SQL Server to the client using SQLDataSource
author: Naomi Nosonovsky
type: post
date: 2010-07-25T15:05:20+00:00
ID: 854
url: /index.php/webdev/webdesigngraphicsstyling/how-to-return-error-messages-from-the-sq/
views:
  - 14588
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling

---
The recent [thread][1] in ASP.NET forums is the primary reason for this blog post.

I want to show how error messages from the SQL Server were handled in our web pages. The site I was working on had most of the functionality developed by the previous developer. Unfortunately, most pages didn't use a separation of layers and used SQLDataSource for data manipulations.

The sample I want to show was called from InsertPerson.aspx page. For inserting a person we used the stored procedure I showed in my previous blog [How to insert information into multiple related tables and return ID using SQLDataSource][2].

For inserting a new person the InsertPerson.aspx page was used that had multiview control, FormView controls with different tabs using User Controls for each tab (the tab was a special control designed by my colleague).

Here is the beginning of the page's code:

```HTML
<asp:Label ID="UPDATE_OPENER" runat="server" Visible="false" Text=""></asp:Label>
    
    <form id="form1" runat="server">        
        
    <asp:MultiView ID="MltvwViewForms" runat="server">
        <asp:View ID="VwRegularPerson" runat="server">
            <asp:SqlDataSource runat="server" ID="PeopleNoneDataSource" ConnectionString="<%$ ConnectionStrings:FCCMSConnectionString %>"
                InsertCommand="PersonInsert" InsertCommandType="StoredProcedure" OnInserted="DataSource_Inserted">
                <InsertParameters>
                    <asp:Parameter Name="NewPersonID" Direction="Output" Type="Int32" />
                    <asp:ProfileParameter Name="SiteID" PropertyName="SiteID" Type="Int32" />
                    <asp:QueryStringParameter Name="PersonType" QueryStringField="ptype" Type="Char"
                        DefaultValue="N" />
                    <asp:Parameter Name="Title" />
                    <asp:Parameter Name="Occupation" />
                    <asp:Parameter Name="BusinessName" />
                    <asp:Parameter Name="BusAdd1" />
                    <asp:Parameter Name="BusAdd2" />
                    <asp:Parameter Name="BusCity" />
                    <asp:Parameter Name="BusState" />
                    <asp:Parameter Name="BusZip" />
                    <asp:Parameter Name="BusEMail" />
                    <asp:Parameter Name="Pager" />
                    <asp:Parameter Name="Fax" />
                    <asp:Parameter Name="BusinessPhone" />
                    <asp:Parameter Name="SpouseFN" />
                    <asp:Parameter Name="SpouseLN" />
                    <asp:Parameter Name="SpouseID" />
                    <asp:Parameter Name="Anniversary" />
                </InsertParameters>
            </asp:SqlDataSource>
            <asp:FormView runat="server" ID="PeopleNoneForm" DefaultMode="Insert" EditRowStyle-CssClass="details_row"
                DataSourceID="PeopleNoneDataSource" OnItemInserted="Form_ItemInserted" 
                OnItemInserting="Form_ItemInserting"  OnItemCommand="Form_ItemCommand">
                <InsertItemTemplate>
                    <div style="margin-top: 20px">
                        &nbsp;</div>
                    <div id="links">
                        <label class="link" id="tab1" onclick="showReport(1,<%=clseditmode.NumTabs %>)" title="Basic Info">
                            Basic Info</label>
                        <label class="link" id="tab2" onclick="showReport(2,<%=clseditmode.NumTabs %>)" title="Contact Info">
                            Contact Info</label>
                        <label class="link" id="tab3" onclick="showReport(3,<%=clseditmode.NumTabs %>)" title="User Info">
                            User Info</label>
                        <label class="link" id="tab4" onclick="showReport(4,<%=clseditmode.NumTabs %>)" title="Employment Info">
                            Employment Info</label>
                        <label class="link" id="tab5" onclick="showReport(5,<%=clseditmode.NumTabs %>)" title="Family Info">
                            Family Info</label>
                         <% if (clseditmode.NumTabs == 6) { %>   
                                 <label class="link" id="tab6" onclick="showReport(6,<%=clseditmode.NumTabs %>)" title="Membership Info">
                                    Membership Info</label> 
                                 <% } %>      
                    </div>
                    <div class="boxMain">
                       <UndoSave:UndoSaveButtons ID="btnRegular" runat="server" />
                        <!-- 1st -->
                        
                                <div id="info1" class="cl">
                                    <GenInfo:GeneralInfo ID="GenInfoRegular" runat="server" ShowTitle="true" DOB='<%# Bind("DOB") %>'
                                        FirstName='<%# Bind("FirstName") %>'
                                        LastName='<%# Bind("LastName") %>' MiddleName='<%# Bind("MiddleName") %>' Title='<%# Bind("Title") %>'
                                        Gender='<%# Bind("Gender") %>' ShowParents="false" SetGenderOnChangeEvent="true"
                                        ShowPicture ="true" DefaultPicture = '<%# Bind("DefaultPicture") %>'  />
                                
                                <div class="cl" style="padding-top: 26px">
                                    <script type="text/javascript">TabNav(1,<%=clseditmode.NumTabs %>)</script>
                                </div>
                            </div>
                        <!-- /1st -->
                        <!-- 2nd -->
                        <div id="info2" class="cl dn">
                            <AddrInfo:AddressInfo ID="AddressInfoRegular" runat="server" Address1='<%# Bind("Address1") %>'
                                Address2='<%# Bind("Address2") %>' CellPhone='<%# Bind("CellPhone") %>' City='<%# Bind("City") %>'
                                Zip='<%# Bind("Zip") %>' State='<%# Bind("State") %>' ScreenName='<%# Bind("ScreenName") %>'
                                HomePhone='<%# Bind("HomePhone") %>' 
                                Email='<%# Bind("Email") %>' 
                                SecondEmail='<%# Bind("SecondEmail") %>'/>                        
                        <div class="cl" style="padding-top: 15px">
                            <script type="text/javascript">TabNav(2,<%=clseditmode.NumTabs %>)</script>
                        </div>
                    </div>
                    <!-- /2nd -->
                    <!-- 3rd -->
                    <div id="info3" class="cl dn">
                        <UserInfo:UserInfo ID="UserInfoRegular" runat="server" Pwd='<%# Bind("Pwd") %>' UserName='<%# Bind("UserName") %>' />
                        <div class="cl" style="padding-top: 26px">
                            <script type="text/javascript">TabNav(3,<%=clseditmode.NumTabs %>)</script>
                        </div>
                    </div>
                    <!-- /3rd -->
                    <!-- 4th -->
                    <div id="info4" class="cl dn">
                        <BusInfo:BussinessInfo ID="BussinessInfoRegular" runat="server" Occupation='<%# Bind("Occupation") %>'
                            BusinessName='<%# Bind("BusinessName") %>' BusAdd1='<%# Bind("BusAdd1") %>' BusAdd2='<%# Bind("BusAdd2") %>'
                            BusCity='<%# Bind("BusCity") %>' BusState='<%# Bind("BusState") %>' BusZip='<%# Bind("BusZip") %>'
                            Pager='<%# Bind("Pager") %>' Fax='<%# Bind("Fax") %>' BusinessPhone='<%# Bind("BusinessPhone") %>' />
                        <div class="cl" style="padding-top: 26px">
                            <script type="text/javascript">TabNav(4,<%=clseditmode.NumTabs %>)</script>
                        </div>
                    </div>
                    <!-- /4th -->
                    <!-- 5th -->
                    <div id="info5" class="cl dn">
                    <div class="cl">
                        <label class="bx">
                            Spouse</label>
                            <span class="bx" id="spouseName">
                                
                                <Custom:AutoSuggestBox ID="asbSpouse" ToolTip="Start typing and select from the list"
                                    runat="server" DataType = '<%# Eval("Gender") =="M"?"Mother":"Father" %>' IncludeMoreMenuItem="True" KeyPressDelay="300"
                                    MaxSuggestChars="5" MoreMenuItemLabel="..." NumMenuItems="10" ResourcesDir="/asb_includes"
                                    SelectedValue='<%# Bind("SpouseID") %>' SelMenuItemCSSClass="asbSelMenuItem"
                                    OnPreRender="AsbPrerender" UseIFrame="True" MaxLength="80"></Custom:AutoSuggestBox>
                               
                            </span>
                    <span class="bx" style="width: 180px">
                    <a href=<%# clseditmode.CreateLink(Eval("SpouseID"),"AddressInfoRegular","Spouse") %></a></span>
                       <label class="bx">
                            Anniversary</label><span class="bx"><asp:TextBox ID="tbAnniversary" runat="server"
                                AutoCompleteType="Disabled" Text='<%# Bind("Anniversary", "{0:M/d/yyyy}") %>'></asp:TextBox>
                                <asp:RegularExpressionValidator ID="regexAnniversary" runat="server" ControlToValidate="tbAnniversary"
                                    Display="Dynamic" ErrorMessage="Incorrect Anniversary (m/d/yyyy)" 
                                    ValidationExpression="(?:(?:(?:0?[13578]|1[02])(/|-|.)31)1|(?:(?:0?[13-9]|1[0-2])(/|-|.)(?:29|30)2))(?:(?:1[6-9]|[2-9]d)?d{2})$|^(?:0?2(/|-|.)293(?:(?:(?:1[6-9]|[2-9]d)?(?:0[48]|[2468][048]|[13579][26])|(?:(?:16|[2468][048]|[3579][26])00))))$|^(?:(?:0?[1-9])|(?:1[0-2]))(/|-|.)(?:0?[1-9]|1d|2[0-8])4(?:(?:1[6-9]|[2-9]d)?d{2})"></asp:RegularExpressionValidator></span>
                        <label class="bx">Marital Status</label><span class="bx">
                        <asp:DropDownList runat="server" ID="drpMsts" SelectedValue='<%# Bind("MaritalStatus") %>'>
                            <asp:ListItem Value="M">Married</asp:ListItem>
                            <asp:ListItem Value="D">Divorced</asp:ListItem>
                            <asp:ListItem Value="S">Separated</asp:ListItem>
                            <asp:ListItem Value="W">Widowed</asp:ListItem>
                        </asp:DropDownList></span></div> 
                        
                        <div class="cl">
                            <label class="bx">
                                Father</label><span class="bx" id = "fatherName"><Custom:AutoSuggestBox ID="fatherName" Text='<%# Bind("Father") %>'
                                    DataType="Father" MaxLength="100" OnPreRender="AsbPrerender" ToolTip="Start typing and select from the list"
                                    runat="server" ResourcesDir="/asb_includes" /></span>
                                    <span class="bx" style="width: 180px"> 
                                    <a href=<%# clseditmode.CreateLink(Eval("FatherID"),"AddressInfoRegular","Father") %></a></span>
                           </div>
                           <div class="cl">
                            <label class="bx">
                                Mother</label><span class="bx" id ="motherName"><Custom:AutoSuggestBox ID="motherName" Text='<%# Bind("Mother") %>'
                                    DataType="Mother" MaxLength="100" OnPreRender="AsbPrerender" ToolTip="Start typing and select from the list"
                                    runat="server" ResourcesDir="/asb_includes" /></span>
                                    <span class="bx" style="width: 180px">
                                    <a href=<%# clseditmode.CreateLink(Eval("MotherID"),"AddressInfoRegular","Mother") %></a></span>
                        </div>
                        <div class="cl">
                            <label class="bx">
                                Comment</label><span class="bx"><asp:TextBox ID="txtComment" Width="120" Text='<%# Bind("Comment") %>'
                                    TextMode="MultiLine" runat="server" AutoCompleteType="Disabled" /></span>
                        </div>
                        <div class="cl" style="padding-top: 26px">
                            <script type="text/javascript">TabNav(5,<%=clseditmode.NumTabs %>)</script>
                        </div>
                    </div>
                    <!-- /5th -->
                     <% if (clseditmode.NumTabs == 6) { %> 
	                     
                                <!-- 6th -->
                                <div id="info6" class="cl dn">
                                    <MembInfo:MembershipInfo ID="MembershipInfoRegular" runat="server" 
                                    BillQuaterly ='<%# Bind("BillQuaterly") %>' IsMember ='<%# Bind("IsMember") %>'
                                    PaymentMethod='<%# Bind("PaymentMethod") %>' StartDate ='<%# Bind("StartDate") %>' />
                               <div class="cl" style="padding-top: 26px">
                                    <script type="text/javascript">TabNav(6,<%=clseditmode.NumTabs %>)</script>
                                </div>                                    
                                </div>
                                <!-- /6th -->
                                <% } %>   
                                <script type="text/javascript">showReport(1,<%=clseditmode.NumTabs %>);</script>
                   </div>
                </InsertItemTemplate>
                <EditRowStyle CssClass="details_row" />
            </asp:FormView>
        </asp:View>
        <!-- End of the Regular Person -->
```
etc.

The InsertPerson.cs (code behind) had the following code in regards to showing the error message:

```C#
/// <summary>
    /// Display message
    /// </summary>
    /// <param name="msg"></param>
    /// <param name="CloseWindow"></param>
    protected void DisplayMessage(string msg, bool CloseWindow)
    {
        string Script="";
        if (Request.QueryString["pid"] != null && Request.QueryString["pid"].Length > 0) {
            if (msg != "") { Script = string.Format("alert("{0}");", msg) + (CloseWindow ? "window.close;" : ""); } 
        }
        else
        {
            Script = string.Format("parent.DisplayMessage("{0}");", msg);}
        
        if (Script!="") 
           this.ClientScript.RegisterClientScriptBlock(this.GetType(), "Message", Script, true);
    }
```
The way the error was passed back to the client was handled in Form_ItemInserted event:

```c#
protected void Form_ItemInserted(object sender, FormViewInsertedEventArgs e)
    {
        string Text;
        bool CloseWindow = false;

        if (e.Exception == null)
        {
            if (Request.QueryString["pid"] != null && Request.QueryString["pid"].Length > 0)
            {               
                Text = string.Format("You just inserted {0} {1}", e.Values["FirstName"], e.Values["LastName"]);
                this.UPDATE_OPENER.Text = "<script language="JavaScript">changeParent('" + Request.QueryString["pid"] + "','" + e.Values["LastName"] + ", " + e.Values["FirstName"] + " " + (e.Values["MiddleName"] ?? "") + "'," + 
                    this.NewPersonID + ",'" + Text.Replace("'","\'")  + "');</script>";
                CloseWindow = true;
                this.UPDATE_OPENER.Visible = true;     
            }
            else
            {
                Text = string.Format("You just inserted {0} {1} <a href='peopleV.aspx?perid={2}'>" +
                   "Edit Here</a>", e.Values["FirstName"], e.Values["LastName"], this.NewPersonID);
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
    }
```

The page calling the InsertPerson page was called CreatePerson.aspx and had the following ASPX code:

```html
<%@ Page Language="C#" MasterPageFile="~/admin.master" AutoEventWireup="true" CodeFile="CreatePerson.aspx.cs"
    Theme="Profesional" Inherits="Coordinator_People_CreatePerson" Title="Create Users" %>

<asp:Content ID="Content1" ContentPlaceHolderID="ContentPlaceHolder1" runat="Server">
<div id="spnStatFrame" style="display:none;">
<table style="font-weight: bolder; vertical-align: middle; color: #FFFFFF; width: 32%; text-align: center;" cellpadding="0" cellspacing="0">
<tr>
    <td><img alt="" src="../../images/tl-LightBlue.gif" height="10" width="10" /></td>
    <td style="background-color: #5BA2EB;">&nbsp;</td>
    <td><img alt="" src="../../images/tr-LightBlue.gif" height="10" width="10" /></td>
</tr>
<tr style="background-color: #5BA2EB;">
    <td>&nbsp;</td>
    <td><span id="spnStat"></span></td>
    <td>&nbsp;</td>
</tr>
<tr>
    <td><img alt="" src="../../images/bl-LightBlue.gif" height="10" width="10" /></td>
    <td style="background-color: #5BA2EB;">&nbsp;</td>
    <td><img alt="" src="../../images/br-LightBlue.gif" height="10" width="10" /></td>
</tr>
</table>
</div>

<script type="text/javascript">
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
</script>

<div class="p5" style="text-align:right">
    View all People <a href="peopleV.aspx"><img alt="View People" src="../../images/id_card_view.png" title="View all People" class="peopleVNavIcon"/></a>
</div>


<div>
Create a
<select id="PersonType" name="PersonType" onchange="javascript:go();">
                        <option selected="selected" value="">--Choose a Person Type--</option>
                        <option value="InsertPerson.aspx?ptype=B">Special Friend</option>
                        <option value="InsertPerson.aspx?ptype=N">Regular Person</option>
                        <option value="InsertPerson.aspx?ptype=A">Adult Volunteer</option>
            -- other options --
</select>
</div>
<iframe id="myframe" scrolling="no" marginwidth="0" marginheight="0" 
frameborder="0" style="text-align: center; overflow: visible; 
    width:710px; display: none;" ></iframe>
<div id="loading" class="more">Loading...</div>

    <script type="text/ecmascript">
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
       
    </script>

</asp:Content>
```
So, hopefully you see the logic of the pages. The CreatePerson page had a place to show an error message or a link if the insert worked correctly. From the InsertPerson page we manipulated the parent CreatePerson page and displayed the appropriate message (either the error message or a link to edit a newly inserted person).

 [1]: http://forums.asp.net/t/1580016.aspx?PageIndex=1
 [2]: /index.php/WebDev/?p=553