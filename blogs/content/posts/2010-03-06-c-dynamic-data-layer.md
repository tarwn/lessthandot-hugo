---
title: 'C# Dynamic Data Layer'
author: chaospandion
type: post
date: 2010-03-06T20:38:28+00:00
ID: 718
url: /index.php/desktopdev/mstech/c-dynamic-data-layer/
views:
  - 7398
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - .net
  - 'c#'
  - dynamic

---
**Update:** I added some code to handle DBNulls.

With the upcoming release of .NET 4.0 we will be seeing the new dynamic type introduced. The dynamic type bypasses static type checking and is instead checked at run-time. It was introduced to improve interoperability with dynamic languages like IronPython as well as simplifying the usage COM APIs that return objects such as the Microsoft Office API. How can we take advantage of this in other areas? ADO.NET is a perfect candidate for taking advantage of the dynamic type. The following code lays out the ground work for a nice simple class that returns dynamic objects from the database.

```CSharp
public class DynamicDataLayer : IDisposable
{
    private readonly DbConnection connection;

    public DynamicDataLayer(string providerName, string connectionString)
    {
        Contract.Requires(!string.IsNullOrWhiteSpace(providerName));
        Contract.Requires(!string.IsNullOrWhiteSpace(connectionString));

        connection = DbProviderFactories.GetFactory(providerName).CreateConnection();
        connection.ConnectionString = connectionString;
        connection.Open();
    }

    public IEnumerable<dynamic> ExecuteText(string text, params KeyValuePair<string, object>[] parameters)
    {
        Contract.Requires(!string.IsNullOrWhiteSpace(text));
        Contract.Requires(parameters != null);

        lock (this)
        {
            using (var command = connection.CreateCommand())
            {
                command.CommandText = commandText;
                foreach (var parameter in parameters)
                {
                    var p = command.CreateParameter();
                    p.ParameterName = parameter.Key;
                    p.Value = parameter.Value;
                    command.Parameters.Add(p);
                }
                using (var reader = command.ExecuteReader())
                {
                    while (reader.Read())
                    {
                        dynamic record = new ExpandoObject();
                        var recordMembers = (IDictionary<string, object>)record;
                        for (int i = 0; i < reader.FieldCount; i++)
                        {
                            recordMembers.Add(reader.GetName(i), GetDefaultIfDBNull(reader.GetValue(i), reader.GetFieldType(i)));
                        }
                        yield return record;
                    }
                }
            }
        }
    }

    public void Dispose()
    {
        connection.Dispose();
    }

    private object GetDefaultIfDBNull(object value, Type type)
    {
        var dbnull = value as DBNull;
        if (dbnull != null)
        {
            switch (Type.GetTypeCode(type))
            {
                case TypeCode.Boolean: return default(bool);
                case TypeCode.Byte: return default(byte);
                case TypeCode.Char: return default(char);
                case TypeCode.DateTime: return default(DateTime);
                case TypeCode.Decimal: return default(decimal);
                case TypeCode.Double: return default(double);
                case TypeCode.Int16: return default(short);
                case TypeCode.Int32: return default(int);
                case TypeCode.Int64: return default(long);
                case TypeCode.SByte: return default(sbyte);
                case TypeCode.Single: return default(float);
                case TypeCode.String: return default(string);
                case TypeCode.UInt16: return default(ushort);
                case TypeCode.UInt32: return default(uint);
                case TypeCode.UInt64: return default(ulong);
                default: return default(object);
            }
        }
        return value;
    }
}
```
Using this class, the code to retrieve records from a database becomes much cleaner.
  
Take notice of how we don't have to explicitly cast the dynamic type to the variable we assign it to.

```CSharp
using (var dataLayer = new DynamicDataLayer(Provider, ConnectionString))
{
    foreach (dynamic record in dataLayer.ExecuteText(CommandText))
    {
        int size = record.Size;
        string name = record.Name;
        // ...
    }
}
```