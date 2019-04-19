---
title: 'Using SELECT * in views gotcha'
author: Kevin Conan
type: post
date: 2013-03-12T12:06:00+00:00
ID: 2029
excerpt: |
  If ever a DBA walked up a mountain and came back down with two stones that had 10 commandments written on them, “thou shalt not use SELECT *” would be one of them.  
  However, that same DBA would turn around and within 5 minutes use it themselves!
url: /index.php/datamgmt/dbadmin/using-select-in-views-gotcha/
views:
  - 7198
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin
tags:
  - sql
  - views

---
If ever a DBA walked up a mountain and came back down with two stones that had 10 commandments written on them, “thou shalt not use SELECT *” would be one of them. However, that same DBA would turn around and within 5 minutes use it themselves!

<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/moses.JPG?mtime=1363096883"><img alt="" src="/wp-content/uploads/users/kconan/moses.JPG?mtime=1363096883" width="233" height="311" /></a>
</div>

One place that DBAs use SELECT * is when they create views. The idea is that you do actually want every column available to the view because the query that hits that view should limit which columns it wants returned.

This works, but there is one big issue with it that most people learn the hard way. If the schema of the source table that the view is selecting from changes, the view will NOT automatically update to include those changes.

For example, let's create a simple table with some data and a view over it (because we are using really simple code, I'm not going format visually the way I normally do).

```sql
CREATE TABLE tblViewExample (col1 INT NULL);
GO
CREATE VIEW vViewExample AS SELECT * FROM tblViewExample
GO
INSERT INTO vViewExample (col1) VALUES (1);
INSERT INTO vViewExample (col1) VALUES (2);
INSERT INTO vViewExample (col1) VALUES (3);
GO
SELECT * FROM vViewExample;
```
<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/view1.JPG?mtime=1363096883"><img alt="" src="/wp-content/uploads/users/kconan/view1.JPG?mtime=1363096883" width="67" height="83" /></a>
</div>

I did break the rule of no SELECT * again but it's to demonstrate the point of this article.
  
So far everything is being returned as we expect it. Now let's change the schema of our table and see what happens.

```sql
ALTER TABLE tblViewExample ADD col2 INT NULL;
GO
UPDATE tblViewExample SET col2 = col1 * 5;
GO
SELECT * FROM vViewExample;
```
<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/view1.JPG?mtime=1363096883"><img alt="" src="/wp-content/uploads/users/kconan/view1.JPG?mtime=1363096883" width="67" height="83" /></a>
</div>

Notice that col2 is missing even though the view is using SELECT *. Let's see what happens if we try to use the view to insert a new record with data in col2.

```sql
INSERT INTO vViewExample (col1, col2) VALUES (5, 5);
```
<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/view2.JPG?mtime=1363096883"><img alt="" src="/wp-content/uploads/users/kconan/view2.JPG?mtime=1363096883" width="245" height="38" /></a>
</div>

To fix the “broken” view, we have to rebuild it.

```sql
DROP VIEW vViewExample;
GO
CREATE VIEW vViewExample AS SELECT * FROM tblViewExample
```

Now let's try the insert statement again and then the select statement.

```sql
INSERT INTO vViewExample (col1, col2) VALUES (5, 5);

SELECT * FROM vViewExample;
```
<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/view3.JPG?mtime=1363096883"><img alt="" src="/wp-content/uploads/users/kconan/view3.JPG?mtime=1363096883" width="108" height="102" /></a>
</div>

And of course, let's clean up after ourselves!

```sql
DROP VIEW vViewExample;
DROP TABLE tblViewExample;
```
