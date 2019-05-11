---
title: Intranet site for Reporting Services Reports
author: pmch22
type: post
date: 2008-11-28T17:40:40+00:00
ID: 224
url: /index.php/webdev/webdesigngraphicsstyling/intranet-site-for-reporting-services-rep/
views:
  - 10449
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling

---
Recently I was asked if I could design an intranet site to provide a single point access to all reports developed using Reporting Services. To begin with I developed a site where the Report URL was saved as a hyperlink on the page. Soon I realized that there were 50 odd reports and new reports keep getting added every now and then. This way I would have to keep updating the hyperlinks forever. I had to make the site dynamic by getting the latest reports from the report server. From security standpoint, access of the reports should be based on the login and any changes in security should be reflected realtime.

Reporting Services web service is the simplest way to meet the above requirements. The web service resides on your reporting server. The Web Service includes a ton of methods that enable you to read and manage the report server and its contents. Let me dive right into the implementation part.
  
1. Open Visual Studio, create a new website.
  
2. Add the web service reference to your application using Add Web Reference. The path of the web service would be http://reportservername/ReportServer/ReportService.asmx
  
Change the web reference name say ReportingService and click Add Web Reference. This name will become the namespace for the web service proxy. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/RSWebService.JPG" alt="" title="" width="640" height="400" />
</div>

Once the reference is added,open the web.config file. You'll notice a new section has been added the web.config

```xml
<appSettings>
	<add key="Reporting.ReportService" value="http://reportservername/ReportServer/ReportService.asmx"/>
	</appSettings>
```

3. Next, we need add reference to the Microsoft.ReportViewer.WebForms inorder to use the Report Viewer.Go to Website. Add Reference. Select
  
Microsoft.ReportViewer.WebForms. Click Add. This would add the Report Viewer to the toolbox and new sections to the web.config.

```xml
<assemblies>
				<add assembly="Microsoft.ReportViewer.WebForms, Version=8.0.0.0, Culture=neutral, PublicKeyToken=B03F5F7F11D50A3A"/>
			</assemblies>
			<buildProviders>
				<add extension=".rdlc" type="Microsoft.Reporting.RdlBuildProvider, Microsoft.ReportViewer.Common, Version=8.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"/>
			</buildProviders>

<httpHandlers>
			<add verb="*" path="Reserved.ReportViewerWebControl.axd" type="Microsoft.Reporting.WebForms.HttpHandler, Microsoft.ReportViewer.WebForms, Version=8.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"/>
		</httpHandlers>

```
4. Now we move on to creating a new page and designing the layout of the page. Typically the design would be given by user group but in my case I had to create a layout and present it for approval. As I browsed the report manager, I realized that we had several folders and each folder had reports related to a department or task. I had to organize the reports in such a way it would be easy for novice users to use it without any help. I cannot list all the reports as that would show too many reports to the user (depending on their access level of course) and that can be overwhelming!! Therefore I decided to use 2 combo boxes, one to list the folders and the second to list the reports in each folder. I also have a Run Report button the page. User can select a report in the combo box and click Run Report. This would display the in a new window.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/reportcatalog.JPG" alt="" title="" width="640" height="400" />
</div>

5. We start by including the Reporting Services namespace to the code behind file. Then create an instance of the ReportingService proxy class on the page load event. Pass the client login credentials.

```csharp
using ReportingServices;
 protected void Page_Load(object sender, EventArgs e)
 {
     if (!Page.IsPostBack)
        {
            ReportingService rs = new ReportingService();
            rs.Credentials = System.Net.CredentialCache.DefaultCredentials;

            foreach (CatalogItem item in rs.ListChildren("/", true))
            {
                if (item.Type == ItemTypeEnum.Folder && item.Name != "Data Sources")
                {
                    //skip datasource folder.
                    //DropDownList1.Items.Add(new ListItem(item.Name, item.Path));
                    lbModules.Items.Add(new ListItem(item.Name, item.Path));
                }
            }
        }
 }
```
ListChildren method requires 2 parameters. The first parameter is the path of the folder to look in and the second parameter is a flag to indicate if the method should recurse through subdirectories.
  
The method returns an array of catalogitem objects based on the user permissions. The catalog items can be reports,data sources,folders Once we get the array of catalog items, we loop through the array and read the properties of each catalogitem. A catalogitem properties give us information about the item like Name,Created By, Description, Path, Type etc. Here we mainly make use of the type and Name. If an item is of type "Folder" we add the item to combo box. Since we don't want to show data sources folder to users, we check for the name "Data Sources" and ignore it.

6.The next method ListReports is written for SelectedIndexChanged event of the folders combobox which means when an item in the folders combobox is selected this event is fired. As the name suggests this method lists all the reports in the selected folder item. We pass the path of the selected folder as a parameter to the List Children method. This time we check if the Item.type is Report.

```csharp
protected void ListReports(object sender, EventArgs e)
    {
        string path = "";

        lbReports.Items.Clear();
        ReportingService rs = new ReportingService();
        rs.Credentials = System.Net.CredentialCache.DefaultCredentials;
       
                path = lbModules.SelectedValue;
  
        foreach (CatalogItem item in rs.ListChildren(path, true))
        {
            if (item.Type == ItemTypeEnum.Report)
                lbReports.Items.Add(new ListItem(item.Name, item.Path));
        }

        if (lbReports.Items.Count > 0)
            lbReports.Visible = true;
    }
}

```
7. Now to run the report. This is a simple javascript function to open the report in a new window. Pass the path of the report the new window.

```javascript
function OpenReport()
    {
    var path=document.getElementById("lbReports").value;
    alert(path);
    window.open("OpenReport.aspx?&Path="+path,"");
    } 
```
8. Our next task is to display the report on a web page. Start by creating a new page. Drag and drop the report viewer control from the toolbox. In the code behind file get the path of the selected report from the query string and set the path of the report viewer.

```csharp
using Microsoft.Reporting.WebForms;

public partial class OpenReport : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        string ReportPath="";
        ReportPath = Request.QueryString["Path"];
        if (ReportPath != "" || ReportPath != null)
        {
            if (!Page.IsPostBack)
            {
                ReportViewer1.ShowParameterPrompts = true;
                ReportViewer1.ProcessingMode = ProcessingMode.Remote;

                ServerReport svrReport = ReportViewer1.ServerReport;
     svrReport.ReportServerUrl = new Uri("http://reportservername/ReportServer/");
                svrReport.ReportPath = ReportPath;


            }
        }

    }
}


```
Deployment -Where to host?

The simplest way to deploy the application on your intranet is by hosting the site on the reporting server. When the application and the reporting server are on the same machine, enable impersonation in web config file. Doing so will pass the user credentials directly to the report server.

```xml
<authentication mode="Windows"/>
<identity impersonate="true"/>
<--To allow only authorized users to access the intranet site, include-->
<authorization>
	<allow users="*"/>
	<deny users="?"/>
</authorization>
```
When the reporting server and web server are on different machines, Kerberos authenication protocol is required to pass the user credentails from web server to reporting server.

That's about it. Creating a repository of reports is pretty straighforward using Reporting Services web service. I hope you'll find this example useful.