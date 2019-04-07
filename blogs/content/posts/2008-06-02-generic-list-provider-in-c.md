---
title: 'Generic List Provider in C#'
author: Alex Ullrich
type: post
date: 2008-06-02T11:27:13+00:00
ID: 38
url: /index.php/desktopdev/mstech/generic-list-provider-in-c/
views:
  - 6786
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'

---
Since this is probably the most-often reused piece of code I&#8217;ve written recently, I thought it may be worth sharing. It is pretty raw still, so I will post it to the wiki as well, and hopefully people will see ways to improve it (and show us!). Here is the link to the wiki page: [Generic List Provider in C#][1].

I wrote this because I found myself constantly writing code to fill lists of objects from database commands. I was too far into the project and the project was a little to small to justify moving to an ORM like NHibernate, but I thought there must be a better way to do this. 

It turns out we can do it, using a couple of my favorite features in .NET 2.0, Reflection and Generics. By making this ListProvider a generic class, we can return a list of objects of any type. And by using reflection we can interrogate the type we are working with at runtime, and assign its&#8217; properties. 

There is one pretty major limitation of this method, and that is that your property names need to match the column names that you return from the database. When limiting access to the database to stored procs, this is pretty easy to enforce, so I have not needed to find a way around it. I&#8217;m sure there is a way to do this by adding a list of database field names and their associated properties that the calling code can provide, but I have not needed to do it yet. Hopefully someone will add to the wiki page and do it for me 😀

Anyways here is the code, it is pretty self explanatory:

```C#
using System;
using System.Collections.Generic;
using System.Text;
using System.Reflection;
using System.Data;

namespace MyApp.Utilities
{
    public class ListProvider<T> where T: new()
    {
        public List<T> FindAll(IDbCommand com, IDbConnection con)
        {
            //ensure that command object's connection is set, open connection
            com.Connection = con;
            con.Open();

            //create data reader used in filling objects
            IDataReader rdr = com.ExecuteReader();

            //instantiate new list of <T> that will be returned
            List<T> returnList = new List<T>();

            //need a Type and PropertyInfo object to set properties via reflection
            Type tType = new T().GetType();
            PropertyInfo pInfo;

            //x will hold the instance of <T> until it is added to the list
            T x;

            //use reader to populate list of objects
            while (rdr.Read())
            {
                x = new T();

                //set property values
                //for this to work, command's column names must match property names in object <T>
                for (int i = 0; i<rdr.FieldCount; i++)
                {
                    pInfo = tType.GetProperty(rdr.GetName(i));
                    pInfo.SetValue(x, rdr[i], null);
                }

                //once instance of <T> is populated, add to list
                returnList.Add(x);
            }


            //clean up -- assumes you don't need command anymore
            con.Close();
            com.Dispose();
            rdr.Dispose();

            return returnList;
        }
    }
}
```
I tried to remove most of the comments that didn&#8217;t add much (parameter descriptions and what not) but I think its&#8217; still pretty easy to understand. As I said this is a work in progress (I just need to run into a reason to actually need the progress 😉 ) so don&#8217;t be too rough on me. And feel free to <del>do my job for me</del> offer suggestions to make this better!

 [1]: http://wiki.ltd.local/index.php/Generic_List_Provider_in_CSharp