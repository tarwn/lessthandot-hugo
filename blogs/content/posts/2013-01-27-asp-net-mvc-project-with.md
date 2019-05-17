---
title: ASP.NET MVC project with Modal dialogs and Flexigrid
author: Naomi Nosonovsky
type: post
date: 2013-01-27T20:24:00+00:00
ID: 1943
excerpt: 'In the last few months I started working on my first ASP.NET MVC project. I already had a Visual FoxPro application I wrote in a few days and I wanted to replicate it for the Web using ASP.NET MVC. This project is still in its infancy, but I want to sha&hellip;'
url: /index.php/webdev/uidevelopment/ajax/asp-net-mvc-project-with/
views:
  - 14469
rp4wp_auto_linked:
  - 1
categories:
  - AJAX

---
In the last few months I started working on my first ASP.NET MVC project. I already had a Visual FoxPro application I wrote in a few days and I wanted to replicate it for the Web using ASP.NET MVC. This project is still in its infancy, but I want to share my experience and some success I have with it.

I also first want to thank people who helped me to get where I am now with this project: Viv Phillips and Paul Mrozowski from UniversalThread, Darin Dimitrov from stackoverflow, Alex Ullrich and Sergey Kosik and also Jazzen Chen from MS. I also want to thank authors of many blogs and articles I read while working on this project.

I started my project from defining my models using Entity Framework Code first pattern since I already had the database defined. I am going to discuss only one model "Client" for the Clients table.

This is how the table looks in the database:

```sql
CREATE TABLE [dbo].[Clients](
	[ClientID] [int] IDENTITY(1,1) NOT NULL,
	[client_no] [smallint] NOT NULL,
	[client_name] [varchar](30) NULL,
	[Contact1] [varchar](100) NULL,
	[C1_Email] [varchar](100) NULL,
	[Contact2] [varchar](100) NULL,
	[C2_Email] [varchar](100) NULL,
	[C1_Phone] [varchar](10) NULL,
	[C1_Ext] [varchar](5) NULL,
	[C2_Phone] [varchar](10) NULL,
	[C2_Ext] [varchar](5) NULL,
	[Address] [varchar](max) NULL,
	[EnteredBy] [char](6) NOT NULL,
	[EnteredOn] [smalldatetime] NULL,
	[ModifiedBy] [char](6) NULL,
	[ModifiedOn] [smalldatetime] NULL,
 CONSTRAINT [PK_Clients_ClientID] PRIMARY KEY CLUSTERED 
(
	[ClientID] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
GO
SET ANSI_PADDING OFF
GO
CREATE NONCLUSTERED INDEX [idxClients_Client_No] ON [dbo].[Clients] 
(
	[client_no] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, SORT_IN_TEMPDB = OFF, IGNORE_DUP_KEY = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]

/****** Object:  Default [DF_Clients_EnteredBy]    Script Date: 12/19/2012 07:00:35 ******/
ALTER TABLE [dbo].[Clients] ADD  CONSTRAINT [DF_Clients_EnteredBy]  DEFAULT ('ADMIN') FOR [EnteredBy]
GO
/****** Object:  Default [DF_Clients_EnteredOn]    Script Date: 12/19/2012 07:00:35 ******/
ALTER TABLE [dbo].[Clients] ADD  CONSTRAINT [DF_Clients_EnteredOn]  DEFAULT (getdate()) FOR [EnteredOn]
GO
/****** Object:  Check [CK_Clients_C1_Phone]    Script Date: 12/19/2012 07:00:35 ******/
ALTER TABLE [dbo].[Clients]  WITH CHECK ADD  CONSTRAINT [CK_Clients_C1_Phone] CHECK  (([C1_Phone] like '[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]'))
GO
ALTER TABLE [dbo].[Clients] CHECK CONSTRAINT [CK_Clients_C1_Phone]
GO
/****** Object:  Check [CK_Clients_C2_Phone]    Script Date: 12/19/2012 07:00:35 ******/
ALTER TABLE [dbo].[Clients]  WITH CHECK ADD  CONSTRAINT [CK_Clients_C2_Phone] CHECK  (([C2_Phone] like '[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]'))
GO
ALTER TABLE [dbo].[Clients] CHECK CONSTRAINT [CK_Clients_C2_Phone]
GO
/****** Object:  ForeignKey [FK_Clients_Operators_EnteredBy]    Script Date: 12/19/2012 07:00:35 ******/
ALTER TABLE [dbo].[Clients]  WITH CHECK ADD  CONSTRAINT [FK_Clients_Operators_EnteredBy] FOREIGN KEY([EnteredBy])
REFERENCES [dbo].[Operators] ([op_code])
GO
ALTER TABLE [dbo].[Clients] CHECK CONSTRAINT [FK_Clients_Operators_EnteredBy]
GO
/****** Object:  ForeignKey [FK_Clients_Operators_ModifiedBy]    Script Date: 12/19/2012 07:00:35 ******/
ALTER TABLE [dbo].[Clients]  WITH CHECK ADD  CONSTRAINT [FK_Clients_Operators_ModifiedBy] FOREIGN KEY([ModifiedBy])
REFERENCES [dbo].[Operators] ([op_code])
GO
ALTER TABLE [dbo].[Clients] CHECK CONSTRAINT [FK_Clients_Operators_ModifiedBy]
GO
```
Last 2 constraints in the above script refer to the Operators table I am not showing here.

Let's add some data to insert:

```sql
GO
SET IDENTITY_INSERT [dbo].[Clients] ON

INSERT [dbo].[Clients] ([ClientID], [client_no], [client_name], [Contact1], [C1_Email], [Contact2], [C2_Email], [C1_Phone], [C1_Ext], [C2_Phone], [C2_Ext], [Address], [EnteredBy], [EnteredOn], [ModifiedBy], [ModifiedOn]) VALUES (3, 8096, N'Wachusett', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ADMIN ', CAST(0x9F9002DD AS SmallDateTime), NULL, NULL)
INSERT [dbo].[Clients] ([ClientID], [client_no], [client_name], [Contact1], [C1_Email], [Contact2], [C2_Email], [C1_Phone], [C1_Ext], [C2_Phone], [C2_Ext], [Address], [EnteredBy], [EnteredOn], [ModifiedBy], [ModifiedOn]) VALUES (4, 6700, N'Buck Hill', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ADMIN ', CAST(0x9F9002DD AS SmallDateTime), NULL, NULL)
INSERT [dbo].[Clients] ([ClientID], [client_no], [client_name], [Contact1], [C1_Email], [Contact2], [C2_Email], [C1_Phone], [C1_Ext], [C2_Phone], [C2_Ext], [Address], [EnteredBy], [EnteredOn], [ModifiedBy], [ModifiedOn]) VALUES (5, 6238, N'Jamz', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ADMIN ', CAST(0x9F9002DD AS SmallDateTime), NULL, NULL)
INSERT [dbo].[Clients] ([ClientID], [client_no], [client_name], [Contact1], [C1_Email], [Contact2], [C2_Email], [C1_Phone], [C1_Ext], [C2_Phone], [C2_Ext], [Address], [EnteredBy], [EnteredOn], [ModifiedBy], [ModifiedOn]) VALUES (7, 8363, N'Okemo', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ADMIN ', CAST(0x9F9002DD AS SmallDateTime), NULL, NULL)
INSERT [dbo].[Clients] ([ClientID], [client_no], [client_name], [Contact1], [C1_Email], [Contact2], [C2_Email], [C1_Phone], [C1_Ext], [C2_Phone], [C2_Ext], [Address], [EnteredBy], [EnteredOn], [ModifiedBy], [ModifiedOn]) VALUES (8, 1161, N'Shawnee Mountain', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ADMIN ', CAST(0x9F9002DD AS SmallDateTime), NULL, NULL)
INSERT [dbo].[Clients] ([ClientID], [client_no], [client_name], [Contact1], [C1_Email], [Contact2], [C2_Email], [C1_Phone], [C1_Ext], [C2_Phone], [C2_Ext], [Address], [EnteredBy], [EnteredOn], [ModifiedBy], [ModifiedOn]) VALUES (9, 7497, N'Southern Star Obs Wheel', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ADMIN ', CAST(0x9F9002DD AS SmallDateTime), NULL, NULL)
INSERT [dbo].[Clients] ([ClientID], [client_no], [client_name], [Contact1], [C1_Email], [Contact2], [C2_Email], [C1_Phone], [C1_Ext], [C2_Phone], [C2_Ext], [Address], [EnteredBy], [EnteredOn], [ModifiedBy], [ModifiedOn]) VALUES (10, 1636, N'Mount Rose', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ADMIN ', CAST(0x9F9002DD AS SmallDateTime), NULL, NULL)
INSERT [dbo].[Clients] ([ClientID], [client_no], [client_name], [Contact1], [C1_Email], [Contact2], [C2_Email], [C1_Phone], [C1_Ext], [C2_Phone], [C2_Ext], [Address], [EnteredBy], [EnteredOn], [ModifiedBy], [ModifiedOn]) VALUES (11, 3951, N'Crystal Mtn Washington', NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL, N'ADMIN ', CAST(0x9F9002DD AS SmallDateTime), NULL, NULL)
```
It took me many iterations and consultations with various blogs about Entity Framework to come up with the following model:

```c#
using System;

using System.ComponentModel.DataAnnotations;
using System.ComponentModel;

using DataAnnotationsExtensions;
using System.ComponentModel.DataAnnotations.Schema;
using System.Collections.Generic;

namespace CardNumbers.Objects
{
    [ComplexType]
    public class PhoneInfo
    {
        [DataType(DataType.PhoneNumber)]
        
        [DisplayName("Phone")]
        [RegularExpression(@"^(((d{3})|d{3})s?)?d{3}[-s]?d{4}s*$", ErrorMessage = "Please enter valid Phone Number")]
        public virtual string Phone { get; set; }

        [StringLength(5)]
        [DisplayName("Ext")]
        public virtual string Ext { get; set; }

        public bool HasValue
        {
            get
            {
                return (Phone != null || Ext != null);
            }
        }

    }

      [ComplexType]
      public class ContactDetail
      {
          //Constructor
          public ContactDetail()
          {
              phoneInfo = new PhoneInfo();
          }

          [StringLength(100)]
          [DisplayName("Contact Name")]
          [DisplayFormat(NullDisplayText = "")]
          public virtual string Contact { get; set; }

          [Email]
          [StringLength(100)]
          [DataType(DataType.EmailAddress)]
          [DisplayName("Email")]
          public virtual string Email { get; set; }

          public virtual PhoneInfo phoneInfo { get; set; }
          public bool HasValue
          {
              get
              {
                  return (Contact != null || Email != null || phoneInfo.HasValue);
              }
          }
      }

/// <summary>
/// Client class (Client No, Client Name, Address, Contact1, Contact2 info, Created By, Modified By (operator and date)
/// </summary>
    public class Client
    {
        public Client()
        {
            Contact1 = new ContactDetail();
            Contact2 = new ContactDetail();
     }

        [Key]
        [Editable(false)]
        [Column("ClientId",TypeName = "int")]
        public virtual int Id { get; set; }
        
        //[Required]
        [DisplayName("Client No")]      
        [Column("client_no", TypeName = "smallint")]
        public virtual Int16 Number { get; set; }
        
        //[Required]
        [Column("client_name", TypeName = "varchar")]
        [DisplayName("Client Name")]
        [MaxLength(30, ErrorMessage = "Client Name should not be longer than 30 characters" )]
        [MinLength(3, ErrorMessage = "Client Name is too short")]

        public virtual string Name { get; set; }

        [DataType(DataType.MultilineText)]
        public virtual string Address { get; set; }

        public virtual ContactDetail Contact1 {get; set;}
        public virtual ContactDetail Contact2 {get; set;}

        [ForeignKey("EnteredByOperator")]
        public string EnteredBy { get; set; }

        [InverseProperty("ClientsEnteredBy")]
        public virtual Operator EnteredByOperator { get; set; }

        [ForeignKey("ModifiedByOperator")]
        public string ModifiedBy { get; set; }

        [InverseProperty("ClientsUpdatedBy")]
        public virtual Operator ModifiedByOperator { get; set; }

        [DataType(DataType.DateTime)]
        [DisplayName("Created on")]
        public DateTime EnteredOn { get; set; }

        [DataType(DataType.DateTime)]
        [DisplayName("Modified on")]
        public DateTime? ModifiedOn { get; set; }

        public virtual ICollection<ClientOrder> ClientOrders { get; set; }
        
        public virtual ICollection<Reorder> Reorders { get; set; }
    }
}
```

The interesting thing in this model is 2 Complex Types. Originally this model didn't have Complex Types, but I found later that I need them in order to create one Editor for each Contact information (as we see, the table is not properly normalized and contains 2 contacts columns). 

I created all my models in one project called CardNumbers.Objects. I later was criticized for such project name, but I used the same naming convention that was used by our 3 days course teacher and therefore left it as is. In my next project I am going to be more thoughtful in regards to project names.

I also created another project called CardNumbers.Data where I placed repository objects. I also originally built them the same way we were taught by the instructor, but as I found later it was not the best way, so I re-designed based on some blog posts and StackOverflow questions this way:

IClientRepository.cs:

```C#
using CardNumbers.Objects;
using System;
using System.Collections.Generic;
using System.Linq;
 
namespace CardNumbers.Data
{
    public interface IClientRepository:IDisposable
    {
        IQueryable<Client> Clients { get; }
        Client GetClientById(int clientId);
        void Commit();

        void AddClient(Client client, bool autoCommit = true);

        void DeleteClient(Client client, bool autoCommit = true);
        
        void DeleteClient(int clientId);

        void UpdateClient(Client client, bool autoCommit = true);
    }
}
```

and the actual implementation of the above interface:

```C#
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Entity;
using System.Linq;
using CardNumbers.Objects;

namespace CardNumbers.Data
{
    public class ClientRepository : IClientRepository, IDisposable 
    {
        private CardNumbersContext context;

        public ClientRepository(CardNumbersContext context)
        {
            this.context = context;
        }        

        IQueryable<Client> IClientRepository.Clients
        {
            get { return this.context.Clients; }
        }
       
        public void Commit()
        {
            this.context.SaveChanges();
        }

        public void AddClient(Client client, bool autoCommit = true)
        {
            client.EnteredOn = DateTime.Now;
            this.context.Clients.Add(client)   ;
            if (autoCommit) this.Commit();
        }

        public void DeleteClient(Client client, bool autoCommit = true)
        {
            this.context.Clients.Remove(client);
            if (autoCommit) this.Commit();
        }

        public void DeleteClient(int id)
        {
            this.context.Clients.Remove(this.GetClientById(id));
            this.Commit();
        }

        public Client GetClientById(int id)
        {
            return this.context.Clients.Find(id);
        }

        public void UpdateClient(Client client, bool autoCommit = true)
        {
            client.ModifiedOn = DateTime.Now;
            this.context.Entry(client).State = EntityState.Modified;
            if (autoCommit) this.Commit();
        }

        private bool disposed = false;

        protected virtual void Dispose(bool disposing)
        {
            if (!this.disposed)
            {
                if (disposing)
                {
                    context.Dispose();
                }
            }
            this.disposed = true;
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }
        
    }
}
```
and I show CardNumbersContext.cs for completion:

```C#
using System;
using System.Collections.Generic;
using System.Data.Entity;
using CardNumbers.Objects;
using System.Data.Entity.ModelConfiguration.Conventions;
using CardNumbers.Objects.Mapping;

namespace CardNumbers.Data
{
    public class CardNumbersContext : DbContext

    {
        public DbSet<Client> Clients { get; set; }
        public DbSet<Operator> Operators { get; set; }
        public DbSet<Reorder> Reorders { get; set; }
        public DbSet<ClientOrder> ClientOrders { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
           // base.OnModelCreating(modelBuilder);
            modelBuilder.Configurations.Add(new ClientMap());
            modelBuilder.Configurations.Add(new OperatorMap());
            modelBuilder.Configurations.Add(new ClientOrdersMap());            
            modelBuilder.Configurations.Add(new ReorderMap());
        }
    }
}
```
So, the above two projects describe my models and means to work with them.

Now I am going to describe the CardNumbers.Web project. 

I started from designing the view for working with the Clients. Here I didn't want to go with the standard shown in all tutorials ways of displaying all clients information in a one page grid with inline Edit/Delete buttons. I didn't like this interface much. So, I started my research as what existing grids I may try to implement. I looked up a few different grid implementations and at some point someone suggested me to look into [flexigrid][1] CodePlex project. I really liked its look and decided to go with it. Also, I believe that same person suggested me [his site][2] which was enough to get me started. I got the Clients view working relatively quickly based on that information.

However, I didn't want to start from displaying all Clients. I wanted to simulate my current VFP application where I started from the empty grid, typed some letters in the client name search textbox and based on that show only small subset of rows initially.

This took me a while to figure out. I was almost ready to give up, but finally found addFormData method (which was there all the time) and figured out how to use it for my purpose. It may seem silly to most of the experienced jquery developers, but for me it was my first mini-success.

So, I will show you how my Client View now looks and the relevant portions of Clients.js script file and the ClientController.cs file:

```html
@model CardNumbers.Models.ClientViewModel

@section scripts {
    <script src="@Url.Content("~/Scripts/Clients.js")" type="text/javascript" ></script>
}

<form id="frmClientsSearch">
    <label for="clientNo">Client No: </label>
    <input type="number" name="searchClientNo" class="numericOnly" /><br />
    <label for="clientName">Client Name: </label>
    <input type="search" size="25" value="Please enter the search value" class="SelectOnEntry"
        name="searchClientName" />

    <input type="button" id="btnClientsSearch" value="Find / Refresh" />
</form>
<div style="padding-left: 150px; padding-top: 50px; padding-bottom: 50px;" id="ClientsResults">
    <table id="flexClients" style="display: none">
    </table>
</div>

 <div id="add-edit-dialog" style="display: none" title="Add / Edit Client"> 
    @Html.Partial("_ClientForm", Model)
</div>
```
As you can see, this main view has just few controls and lines of code. At the top I display two search controls and at the bottom the flexigrid placeholder and the dialog (invisible initially) for my modal Add/Edit dialogs.

Now I want to show the Clients.js part that is responsible for the flexigrid and passing Search form data to the controller through form's collection:

```javascript
$("#flexClients").flexigrid({
    url: '/Client/Client/',
    dataType: 'json',
    colModel: [
    { display: 'Client Id', name: 'Id', width: 100, sortable: true, align: 'center', hide: true },
    { display: 'Client #', name: 'Number', width: 100, sortable: true, align: 'center' },
    { display: 'Name', name: 'Name', width: 350, sortable: true, align: 'center' },
    { display: 'Contact 1', name: 'Contact1', width: 350, sortable: true, align: 'center' },
    ],
    buttons: [
    { name: 'Add', bclass: 'add', onpress: add },
    { name: 'Edit', bclass: 'edit', onpress: edit },
    { name: 'Delete', bclass: 'delete', onpress: del },
    { separator: true }
    ],
    searchitems: [
    { display: 'Client Name', name: 'Name' }
    ],
    sortname: "Name",
    sortorder: "asc",
    usepager: true,
    title: 'Clients',
    useRp: true,
    rp: 15,
    rpOptions: [5, 10, 15, 20, 25, 40],
    showTableToggleBtn: true,
    width: 900,
    onSuccess: bindDblClick,
    onSubmit: addFormData,
    hideOnSubmit: false,
    height: 'auto',
    singleSelect: true
});

//$('.flexigrid').on('dblclick', 'tr[id*="row"]', function () {
//    // console.log('mouseenter rowId: ' + $(this).attr('id').substr(3));
//    alert($(this).attr('id').substr(3));
//    //Edit();
//});

function bindDblClick()
{
    $('#flexClients tr').dblclick(function () {          
       edit('Edit');
    });
}

//This function adds parameters to the post of flexigrid. You can add a verification as well by return to false if you don't want flexigrid to submit			
function addFormData() {

    //passing a form object to serializeArray will get the valid data from all the objects, but, if the you pass a non-form object, you have to specify the input elements that the data will come from
    var dt = $("#add-edit-form").serializeArray();
    dt = dt.concat($('#frmClientsSearch').serializeArray());
  
    $("#flexClients").flexOptions({ params: dt });
    return true;
}
```
So, the first part defines flexigrid (you can see that it used Client controller Client method to return its data). Then the next piece of code binds grid's double click with the same method that is called by the Edit button (in other words, I want to either click on the Edit button or double click on the selected row in a grid to get the same behavior) and the last piece of code (addFormData) is used to pass both Search form and add-edit-form data back to my controller. 

And this is how Client method of the controller looks like (I separated this method into 2 based on the idea I took from looking at the [following solution][3])

```C#
[HttpPost]
        public ActionResult Client(FormCollection formValues)
        {
            // Assume we want to select everything
            var clients = Db.Clients; // Should set type of clients to IQueryable<Clients>
            int searchClientNo = 0;
            
            int.TryParse(formValues["searchClientNo"], out searchClientNo );
            string searchClientName = (formValues["searchClientName"]??"").ToString();

            if (searchClientNo == 0 && searchClientName == "Please enter the search value")
                clients = clients.Where(c => (c.Number == searchClientNo));
            else
            {
                if (searchClientNo != 0) //Number was supplied
                    clients = clients.Where(c => (c.Number == searchClientNo));

                // If clientNo was supplied, clients is now filtered by that. If not, it still has the full list. The following will further filter it.
                if (!String.IsNullOrWhiteSpace(searchClientName)) // Part of the name was supplied
                    clients = clients.Where(c => (c.Name.Contains(searchClientName)));

            }
            int page = int.Parse(formValues["page"] ?? "1");

            int rp = int.Parse((formValues["rp"]??"15"));
            string qtype = (formValues["qtype"]??"").ToString();

            string query = (formValues["query"] ?? "").ToString();

            string sortname = (formValues["sortname"] ?? "Name").ToString();
            string sortorder = (formValues["sortorder"] ?? "asc").ToString();           

            if (!string.IsNullOrEmpty(sortname) && !string.IsNullOrEmpty(sortorder))
            {
                clients = clients.OrderBy(sortname, (sortorder == "asc"));
            }

            if (!string.IsNullOrEmpty(qtype) && !string.IsNullOrEmpty(query))
            {
                clients = clients.Like(qtype, query);
            }

            int Total = clients.Count();

            clients = clients.Skip((page - 1) * rp).Take(rp);
            return CreateFlexiJson(clients, page, Total);           

        }

        private JsonResult CreateFlexiJson(IEnumerable<Client> items, int page, int total)
        {
            return Json(
                    new
                    {
                        page,
                        total,
                        rows =
                            items
                            .Select(x =>
                                new
                                {
                                    id = x.Id,
                                    // either use GetPropertyList(x) method to get all columns 
                                    cell = new { Id = x.Id, Number = x.Number, Name = x.Name, Contact1 = x.Contact1.Contact ?? String.Empty }
                                })
                    }, JsonRequestBehavior.AllowGet);
        }
```
So far so good. Now I want to describe the modal dialogs implementation for Add and Edit buttons. This really took me a very long time to get working correctly and I was lucky to finally got this resolved with Jazzen Chen help.

First I want to show the partial view _ClientForm. There is nothing too interesting in that view besides using special EditorForm I implemented based on [this blog post][4] by Sergey Barskiy:

```html
@using CardNumbers.Helper
@model CardNumbers.Models.ClientViewModel
  <form id="add-edit-form">
    <fieldset>
        <legend>Client Info</legend>

        @Html.ValidationSummary(true)
       
        <input type="hidden" id="fntype" name="fntype">

        @Html.HiddenFor(m => m.ClientId)
        @Html.EditorFor(m => m.ClientNumber, EditorTemplate.TextBox)

        @Html.EditorFor(m => m.ClientName, EditorTemplate.TextBox)

        @Html.EditorFor(m => m.Client.Address, EditorTemplate.EditBox)

        <div id="ContactsInfo">

            <div id="Contact1">

                @Html.EditorFor(m => m.Client.Contact1)

            </div>

            <div id="Contact2">

                @Html.EditorFor(m => m.Client.Contact2)
            </div>
        </div>

       @* <div id="SaveCancel" class="float-right">
            <button id="btnSave">Save</button>
            <button type="reset" id ="btnCancel" name="Reset">Cancel</button>
        </div>*@
    </fieldset>

 </form>
```

This is the Clients.js relevant code which drives that Add, Edit and Delete (for completeness) buttons:

```javascript
function del(com, grid) {
    try {
        var clientName = $('.trSelected td:eq(2)').text();
        if (clientName) //Variable is defined and not empty
        {
            if (confirm("Are you sure you want to delete " + $.trim(clientName) + "?")===false)
                return false;            

            $('.trSelected', grid).each(function () {
                var id = $(this).attr('id');
                id = id.substring(id.lastIndexOf('row') + 3);

                addFormData(); $('#flexClients').flexOptions({ url: '/Client/Delete/'+ id }).flexReload();
            });
        }
    }
    catch(e) {
        alert('Error ' + e);
    }
}

var validator = $("#add-edit-form").validate();

var $dlg = $("#add-edit-dialog").dialog({
    autoOpen: false,
    show: "blind",
    closeOnEscape: true,
    resizable: true,
    width: 1200,
    height: 750,
    minHeight: 600,
    minWidth: 950,
    buttons: {
        "Save": function () {

            if ($("#add-edit-form").valid()) {
                // jobPost.setVals(txtId.val(), txtRole.val(), txtLocation.val(), txtJobType.val(), txtDescription.val());                            

                $.ajax({
                    type: 'POST',
                    //data: JSON.stringify(clientInformation),
                    url: '/Client/Save',
                    dataType: 'json',
                    contentType: 'application/json',
                    success: function (result) {
                        // insert new list into grid
                        $('#flexClients').flexAddData(result);
                    }
                });
                $(this).dialog('close');
            } else return false;

        },
        Cancel: function () {
            $(this).dialog("close");
            clearForm();
            if (validator)
                validator.resetForm();
        }
    },
    close: function () { clearForm(); },
    open: function () {
        //$("#add-edit-dialog").parent().appendTo($("#add-edit-form"));
    }
});

function RunModalDialog(title, url)
{    
    if (title)
        $dlg.dialog("option", {"title": title });

    if (url)
    {        
        $dlg.load(url).dialog("option", { "title": title }).dialog("open");
    }
        //$dlg.load(url, function () {
        //    var validator = $("#sform").validate();
        //    if (validator)
        //         validator.resetForm();
        //    $dlg.dialog("option", { "title": title }).dialog("open");
        //});
    else {
        
        $dlg.dialog("open");
    }
}

function add(com, grid) {
    $('#fntype').val('Add');
    var url = '/Client/Add/';
    //location.replace(url);
    RunModalDialog("Create New Client");
   // clearForm();    
}
 
function edit(com, grid)
{
        $('.trSelected', grid).each(function () {
          
                var id = $(this).attr('id');
                id = id.substring(id.lastIndexOf('row') + 3);
                currentId = id;
                $('#fntype').val('Edit');
                var ClientName;
                ClientName =$('.trSelected td:eq(2)').text();
                var url = '/Client/Edit/' + id ;

                $.get(url, function (html) {
                    //  setFormControls(data.Id, data.Role, data.Location, data.JobType, data.Description);
                    // alert(data);
                    $($dlg).html(html);
                });
               //location.replace(url);
               RunModalDialog("Edit Client: " + ClientName);
        });

    }
```
The most complicated part in the above script was to figure out how to make a server trip to get data for the Edit button and return that page back for editing.
  
This is done with these two lines of code:

```javascript
$.get(url, function (html) {                   
                    $($dlg).html(html);
                });
```

And to make the whole picture complete I'll show the Edit method of my controller that returns the above data:

```c#
public ActionResult Edit(int id)
        {
            ClientViewModel model = new ClientViewModel(); 
            var client = Db.GetClientById(id);
            model.Client = client;
            
            return PartialView("_ClientForm",model);
        }
```

As you can see, just a few lines of code to return the model data and the partial view.

This all may seem very easy now, but I can tell you it was not easy to figure all the pieces out.

I am not showing certain functionality here such as my view model or EditorFor custom editors. I also don't show the RemoteValidator used to provide uniqueness of the client number and client name. If there will be an interest, I may discuss these details in the separate blog post.

Hope this blog post can help people struggling with the modal dialogs implementation in ASP.NET MVC application.

\*** **Remember, if you have a Web application related question, try our [Web Developer][5] forum.**<ins></ins>

 [1]: http://flexigrid.info/
 [2]: http://mvc4beginner.com/Sample-Code/Insert-Update-Delete/Asp-.Net-MVC-Ajax-Insert-Update-Delete-Using-Flexigrid.html
 [3]: http://www.s4sme.com/Blog/Post/aspnet-mvc-4-application-with-flexigrid-jquery-ui-and-jquery-validation
 [4]: http://dotnetspeak.com/index.php/2012/10/asp-net-mvc-template-and-knockout-js/
 [5]: http://forum.lessthandot.com/viewforum.php?f=6&sid=ebc29b65310d77fa409af74cf8d5cd70