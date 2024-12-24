---
title: Mapping Complex types to/from JSON with JSON.Net
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-07-07T11:54:10+00:00
ID: 8680
url: /index.php/webdev/serverprogramming/aspnet/mapping-complex-types-tofrom-json-with-json-net/
views:
  - 6901
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - 'C#'
tags:
  - json
  - json.net

---
In an [earlier post][1] I introduced a strongly typed Identity object I am using in an ASP.Net Core application to make my code and error messages more readable. I didn't wanted that extra complexity reflected in my database or over the wire with an API. In this post we'll look at a simple method to map my strongly typed properties in C# to simpler values in JSON.

This is my desired state:

<div id="attachment_8690" style="width: 634px" class="wp-caption aligncenter">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/07/TransparentIdentityType.png" alt="Transparent Server Side Identity Type" width="624" height="121" class="size-full wp-image-8690" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/07/TransparentIdentityType.png 624w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/07/TransparentIdentityType-300x58.png 300w" sizes="(max-width: 624px) 100vw, 624px" />
  
  <p class="wp-caption-text">
    Transparent Server Side Identity Type
  </p>
</div>

I want a strongly typed Identity object in my API backend that transparently converts into a simple int value to/from the database and converts to an int or `null` for the front-end (null in cases where a permanent ID hasn't been assigned yet). The right side of this was handled in that earlier post with a PetaPoco IMapper implementation registered globally for IIdentity<int> types. JSON.Net supports a similar method that I can register with ASP.Net Core.

This is what my ASP.Net MVC Method looks like:

```csharp
[HttpGet()]
public async Task<List<ApplicationDTO>> GetAllAsync()
{
	return await _databaseStore.Applications.GetAllAsync();
}
```
And this is what we see over the wire:

```javascript
[
  {"id":2, "organizationId":1, "name":"Fictitious Co, LLC Application"}
]
```
Here is the definition of the ApplicationDTO object:

```csharp
public class ApplicationDTO
{   
    [Obsolete("Serialization use only", true)]
    public ApplicationDTO() { }
 
    public ApplicationDTO(AppId id, OrganizationId organizationid, string name)
    {
        Id = id;
        OrganizationId = organizationid;
        Name = name;
    }
    
    public AppId Id { get; set; }
        
    public OrganizationId OrganizationId { get; set; }
        
    public string Name { get; set; }
}
```
Here is the definition of the OrganizationId Identity:

```csharp
public class OrganizationId : IIdentity<int>
{   
	[Obsolete("Serialization use only", true)]
	public OrganizationId() { }
	 
	public OrganizationId(int id)
	{
		RawValue = id;
	}
	 
	public int RawValue { get; set; }
}
```
These are both generated code, with some of the extras left out (potentially a future post).

## Implementing a JSON.Net Mapper

JSON.Net supports custom JsonConverter implementations that will let us transparently convert between IIdentity<int> objects in C# and int values JSON:

```csharp
public class IdentityJsonConverter<T> : JsonConverter
{
    public override void WriteJson(JsonWriter writer, object value, JsonSerializer serializer)
    {
        writer.WriteValue(((IIdentity<T>)value).RawValue);
    }

    public override object ReadJson(JsonReader reader, Type objectType, object existingValue, JsonSerializer serializer)
    {
        if (reader.Value != null)
        {
            var ctor = objectType.GetConstructor(new Type[] { typeof(T) });
            var obj = ctor.Invoke(new object[] { (T)Convert.ChangeType(reader.Value, typeof(T)) });
            return obj;
        }
        else
        {
            return null;
        }
    }

    public override bool CanConvert(Type objectType)
    {
        return typeof(IIdentity<T>).IsAssignableFrom(objectType);
    }
}
```
How it works:

`CanConvert` identifies any implementations of IIdentity<int> as something this converter can handle. 

`WriteJson` is called when JSON.Net is converting to JSON and simply returns the underlying value of my Identity. 

`ReadJson` is called to convert from raw JSON to C#. Converting JSON is slightly more complex, as I allow for null values from the client (it doesn't generate id's) so when the JSON value is null I pass that on as a null Identity. If it's not null, I use reflection on the concrete Identity to find the single parameter constructor and invoke it with incoming JSON converted to the expected type (`int` for the examples above). 

If I wasn't generating these Identity classes, there would be some risk in assuming the presence of a constructor of that shape. Because I'm generating it, I can save time because i know it's all or nothing, either all of the Identity objects will work or none of them will. 

## Employing it in ASP.Net

To use this custom JSONConverter when ASP.Net is serializing/deserializing Action responses and inputs, I add JSON options to MVC during the ConfigureServics call of Startup:

**Startup.cs**

```csharp
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    // Add framework services.
    services.AddMvc()
            .AddJsonOptions(options => {
                options.SerializerSettings.Converters.Add(new IdentityJsonConverter<Int32>());
            });

   // ...
}
```
Now all attempts to deserialize a value into an IIdentity property and serialization to respond with one of these values will pass through the custom mapper and I have the benefit of my custom type in my server-side logic without any extra overhead in my client-side app or code to write as I add new models or properties.

 [1]: /index.php/desktopdev/mstech/csharp/mapping-complex-types-tofrom-the-db-with-petapoco/