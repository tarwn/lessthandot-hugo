---
title: 'Generating DataSet Definition for ASP.net Client Reports from C#'
author: Alex Ullrich
type: post
date: 2010-05-13T18:45:00+00:00
url: /index.php/webdev/webdesigngraphicsstyling/generating-dataset-definition-for-asp-ne/
views:
  - 17607
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling

---
Over the past couple of years, we&#8217;ve been moving from a &#8220;custom-developed&#8221; (read: terrible) reporting system towards SSRS-type reports. We do this using the ASP.net client reports, but with a twist. We had a lot of problems using the built in data-binding, especially when making schema changes (which are frequent as we try to move the database to a more sane design). In order to get around these problems, we created a report definition class that encapsulates the stored procedure used to get the report data, the parameters, and anything else we need to display on the report viewer page. We use this class, along with custom parameter input pages and a dedicated report viewer page, to late-bind the report to a data table retrieved at runtime. This would conceivably make it easier to report from a different data source as well. I won&#8217;t go too much farther off topic, but to make a long story short this forces us to define data sets in our reports manually, which is of course a pain in the neck.

For the uninitiated, when viewing a .rdlc file (or .rdl) as an XML document, the dataset definition looks something like this. Only yours probably have a real datasource 😉

<pre>&lt;DataSets&gt;
    &lt;DataSet Name="ReportData_ReportName"&gt;
      &lt;Fields&gt;
        &lt;Field Name="LastName"&gt;
          &lt;DataField&gt;LastName&lt;/DataField&gt;
          &lt;rd:TypeName&gt;System.String&lt;/rd:TypeName&gt;
        &lt;/Field&gt;
        &lt;Field Name="FirstName"&gt;
          &lt;DataField&gt;FirstName&lt;/DataField&gt;
          &lt;rd:TypeName&gt;System.String&lt;/rd:TypeName&gt;
        &lt;/Field&gt;
        &lt;Field Name="LicenseNumber"&gt;
          &lt;DataField&gt;LicenseNumber&lt;/DataField&gt;
          &lt;rd:TypeName&gt;System.Int32&lt;/rd:TypeName&gt;
        &lt;/Field&gt;
        &lt;Field Name="LicenseExpirationDate"&gt;
          &lt;DataField&gt;LicenseExpirationDate&lt;/DataField&gt;
          &lt;rd:TypeName&gt;System.DateTime&lt;/rd:TypeName&gt;
        &lt;/Field&gt;
      &lt;/Fields&gt;
      &lt;Query&gt;
        &lt;DataSourceName&gt;DummyDataSource&lt;/DataSourceName&gt;
        &lt;CommandText /&gt;
        &lt;rd:UseGenericDesigner&gt;true&lt;/rd:UseGenericDesigner&gt;
      &lt;/Query&gt;
    &lt;/DataSet&gt;
  &lt;/DataSets&gt;</pre>

Pretty simple stuff, but defining it can make setting up a new report somewhat daunting. As we start getting requests for more and more new reports, automating this process looks more and more appealing, and this morning it was finally looking appealing enough that I dropped everything else for about half an hour to figure it out. It ended up being much easier than I expected.

What makes it so easy is the GetSchemaTable method ([see returned table layout][1]) on the IDataReader interface. The first thing we need to do is get the schema table. In this instance its&#8217; set up to just take a complete query string (parameters and all) &#8211; this is for a command line app used by developers, and SQL injection / plan caching / etc&#8230; is simply not as valuable as the ability to enter whatever query we want into the CLI and have it run. We&#8217;re interested in the ColumnName and DataType columns in this case. Not much to this method: 

<pre>public static DataTable SchemaTable(String source_query)
{
	using (IDbConnection connection = new SqlConnection(_ConnectionString))
	{
		IDbCommand command = new SqlCommand();
		command.Connection = connection;
		command.CommandText = source_query;
		connection.Open();

		using (IDataReader reader = command.ExecuteReader())
		{
			return reader.GetSchemaTable();
		}
	}
}</pre>

From there, we need to build up an XML string, similar to what is posted above. It doesn&#8217;t need to be a complete document, just a fragment that can be pasted into our template when creating a new report. I used string manipulation to build it because I didn&#8217;t feel like wrestling with the .net classes to get the namespace &#8220;rd&#8221; defined (it proved tricky since I don&#8217;t want to build the entire document). The formatting is a little funny, just to make the output easy to read:

<pre>/// &lt;summary&gt;Build an XML String representing the data set returned by provided query&lt;/summary&gt;
		public static String BuildDefinition(String data_set_name, String source_query)
		{
			var data_set_template = @"&lt;DataSets&gt;
	&lt;DataSet Name=""{0}""&gt;
		&lt;Fields&gt;
{1}
		&lt;/Fields&gt;
		&lt;Query&gt;
			&lt;DataSourceName&gt;DummyDataSource&lt;/DataSourceName&gt;
			&lt;CommandText /&gt;
			&lt;rd:UseGenericDesigner&gt;true&lt;/rd:UseGenericDesigner&gt;
		&lt;/Query&gt;
	&lt;/DataSet&gt;
&lt;/DataSets&gt;";

			var node_template = @"		        &lt;Field Name=""{0}""&gt;
				&lt;DataField&gt;{0}&lt;/DataField&gt;
				&lt;rd:TypeName&gt;{1}&lt;/rd:TypeName&gt;
			&lt;/Field&gt;";

			var nodes = new System.Text.StringBuilder();

			var schema = Database.SchemaTable(source_query);

			foreach (System.Data.DataRow dr in schema.Rows)
			{
				nodes.AppendLine(String.Format(node_template, dr["ColumnName"], dr["DataType"]));
			}

			return String.Format(data_set_template, data_set_name, nodes.ToString());
		}</pre>

Finally, we need to provide a way to take the user input (dataset name and query) and put it someplace the user can get it (text file). This ought to do:

<pre>static void Main(string[] args)
{
	Console.Title = "Report Data Set Definition Generator";

	//need a place to put file
	var dir = String.Format("{0}\Output", Environment.CurrentDirectory);
	if (Directory.Exists(dir))
	{
		Directory.CreateDirectory(dir);
	}

	do
	{
		Console.Write("nEnter desired DataSet name: ");
		var data_set_name = Console.ReadLine();

		Console.Write("Enter source query: ");
		var source_query = Console.ReadLine();

		var text_value = Generator.BuildDefinition(data_set_name, source_query);

		var file_name = String.Format("{0}\{1}_{2}.txt", dir, data_set_name, DateTime.Now.ToString("yyyyMMdd"));

		Console.WriteLine(
			String.Format("Output:nn{0}nnWritten To: {1}", text_value, file_name));

		File.WriteAllText(file_name, text_value);

		Console.Write("Would you like to generate another? Press Y if yes, any other key to exit. ");
	}
	while (Console.ReadKey().KeyChar.ToString().ToUpper() == "Y");
}</pre>

You can then use the app like so:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/generating-report-definition-for-asp-net/ReportDataSetDefinitionGenerator.PNG" alt="" title="" width="671" height="335" />
</div>

And it would generate the output shown above in a file in /Output, all ready to paste into your .rdl(c) file. I&#8217;ve used this for 2008 .rdlc files (which correspond to 2005 .rdl files), not sure if there were any changes to DataSet definitions that would prevent this from being used with 2008 .rdl&#8217;s.

I&#8217;m sure this could be made more fully featured but we don&#8217;t really create that many new reports so for now it does what we need to streamline the process just fine.

 [1]: http://msdn.microsoft.com/en-us/library/0kct2kw6%28vs.71%29.aspx