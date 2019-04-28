---
title: 'Learning Ruby on windows: First steps in rails – adding an image to our plants'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-31T10:18:00+00:00
ID: 1284
excerpt: In my last post I made a model plant with 3 attributes, namely name, height and color. Now I would like to add an image to that because that always seems to be the most difficult thing to do. I also decided that it would be a good idea to keep the image in the database, just because I like to be different.
url: /index.php/webdev/serverprogramming/rubyonrails/learning-ruby-on-windows-first-2/
views:
  - 4543
categories:
  - Ruby-on-Rails
tags:
  - rails
  - ruby
  - rubymine

---
## Introduction

In my last post I made a model plant with 3 attributes, namely name, height and color. Now I would like to add an image to that because that always seems to be the most difficult thing to do. I also decided that it would be a good idea to keep the image in the database, just because I like to be different.
  
And again the guide to read for me was [migrations on the ruby on rails guide][1].

## Migrate

After some swearing and cursing and reading lots of out of date websites, I finally found what I was looking for when it comes to adding a column to a table/model.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/migrate1.png?mtime=1312098695"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/migrate1.png?mtime=1312098695" width="435" height="208" /></a>
</div>

Some things to not that are very important in this case or it won&#8217;t work. 

The name is very important, I stress VERY. It only knows I want to add the column Image to the table plants because that is what&#8217;s in the name. and then I add the columnname and the type.

After you do this. YOu will see there is an extra file in the migrate folder. Something like this.

```ruby
class AddImageToPlants &lt; ActiveRecord::Migration
  def change
    add_column :plants, :image, :binary
  end
end```
And the name of that file could be this; 20110731074732\_add\_image\_to\_plants. The timestamp is important because it tells rails what it already has in the database and what to do if it does not yet have that. We can now run rake db:migrate or in rubymine just right-click on the file and select run db:migrate.

We should now see that schema.rb has gone from this 

```
ActiveRecord::Schema.define(:version =&gt; 20110731074732) do

  create_table "plants", :force =&gt; true do |t|
    t.string   "name"
    t.integer  "height"
    t.string   "color"
    t.datetime "created_at"
    t.datetime "updated_at"
  end

end```
to this.

```
ActiveRecord::Schema.define(:version =&gt; 20110731074732) do

  create_table "plants", :force =&gt; true do |t|
    t.string   "name"
    t.integer  "height"
    t.string   "color"
    t.datetime "created_at"
    t.datetime "updated_at"
    t.binary   "image"
  end

end```
Woohoo one added field.

Now let&#8217;s show that on the screen.

## Image upload

This is the point where Google was of not so much help but I found that several sites had several pieces of the puzzle. 

The most help I got [from a forumpost][2] at the railsforum. But it was in no way copy/paste. So here it goes.

First I changed the listing to show me the image and a link to upload the image. Which is the index.html.erb file in views/plants

```ruby
&lt;h1&gt;Listing plants&lt;/h1&gt;

&lt;table&gt;
  &lt;tr&gt;
    &lt;th&gt;Name&lt;/th&gt;
    &lt;th&gt;Height&lt;/th&gt;
    &lt;th&gt;Color&lt;/th&gt;
    &lt;th&gt;Image&lt;/th&gt;
    &lt;th&gt;&lt;/th&gt;
    &lt;th&gt;&lt;/th&gt;
    &lt;th&gt;&lt;/th&gt;
  &lt;/tr&gt;

&lt;% @plants.each do |plant| %&gt;
  &lt;tr&gt;
    &lt;td&gt;&lt;%= plant.name %&gt;&lt;/td&gt;
    &lt;td&gt;&lt;%= plant.height %&gt;&lt;/td&gt;
    &lt;td&gt;&lt;%= plant.color %&gt;&lt;/td&gt;
    &lt;td&gt;&lt;%= image_tag("/plants/picture_path/#{plant.id}", :alt =&gt; plant.name) %&gt;&lt;/td&gt;
    &lt;td&gt;&lt;%= link_to 'Show', plant %&gt;&lt;/td&gt;
    &lt;td&gt;&lt;%= link_to 'Edit', edit_plant_path(plant) %&gt;&lt;/td&gt;
    &lt;td&gt;&lt;%= link_to 'Upload image', upload_plant_path(plant) %&gt;&lt;/td&gt;
    &lt;td&gt;&lt;%= link_to 'Destroy', plant, confirm: 'Are you sure?', method: :delete %&gt;&lt;/td&gt;
  &lt;/tr&gt;
&lt;% end %&gt;
&lt;/table&gt;

&lt;br /&gt;

&lt;%= link_to 'New Plant', new_plant_path %&gt;
```
First I added an image tag <code class="codespan">&lt;%= image_tag("/plants/picture_path/#{plant.id}", :alt =&gt; plant.name) %&gt;</code> and as you can see I refer to a controlleraction called picture_path.

You can add this action to the plants_controller.rb.

```ruby
def picture_path
    @plant = Plant.find(params[:id])
    send_data @plant.image, :filename =&gt; @plant.name + '.jpg', :type =&gt; 'image/jpeg', :disposition =&gt; 'inline'
  end```
Which will give you this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/migrate3.png?mtime=1312113231"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/migrate3.png?mtime=1312113231" width="359" height="147" /></a>
</div>

This was before I added the link it will show you the alt text since we don&#8217;t have an image in the database yet.

And I also added the link. like this.

```ruby
&lt;%= link_to 'Upload image', upload_plant_path(plant) %&gt;```
And I added an upload action to the plans controller.

```ruby
# GET /plants/1/upload
  def upload
    @plant = Plant.find(params[:id])
  end```
If you now run this than you will notice that this does not work. You get this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/migrate2.png?mtime=1312112937"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/migrate2.png?mtime=1312112937" width="726" height="112" /></a>
</div>

This because the scaffolding does not set the routing to allow another route which the upload is. 

So go to routes.rb and change the 

```ruby
resources :plants do
```
to this

```
resources :plants do
    member do
      get 'upload'
      end
  end```
And now it will work.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/migrate4.png?mtime=1312113731"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/migrate4.png?mtime=1312113731" width="440" height="157" /></a>
</div>

Before I can click the upload image link I should also add a view for this action. So just add a file called upload.html.erb and then add this code to it.

```ruby
&lt;h1&gt;Upload image&lt;/h1&gt;

&lt;%= form_for(@plant, :url=&gt;{:action=&gt;'update'}, :html=&gt;{:multipart=&gt;true}) do |f| %&gt;
  &lt;%= f.file_field :file %&gt;
  &lt;%= f.submit 'Upload' %&gt;
&lt;% end %&gt;

&lt;%= link_to 'Show', @plant %&gt; |
&lt;%= link_to 'Back', plants_path %&gt;
```
and if I now click the link I get this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/migrate5.png?mtime=1312114026"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/migrate5.png?mtime=1312114026" width="316" height="112" /></a>
</div>

I can now upload the picture.

Well, not really since I first have to change my model a little.

```ruby
class Plant &lt; ActiveRecord::Base
  def file=(input_data)
    self.image = input_data.read
  end
end
```
where I tell the model to put the data from the fileupload routine into the image field.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/migrate6.png?mtime=1312114358"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/migrate6.png?mtime=1312114358" width="474" height="232" /></a>
</div>

And that is it. I have no uploaded the file, put it in the database and shown it on the screen.

## Conclusion

The next steps might be to add the image to the show view. And other things but I can work with it from here. I hope I did not forget a step somewhere when making this post since nothing went right the first time, most things didn&#8217;t even work the fifth time. So I might have forgotten some things from time to time.

 [1]: http://guides.rubyonrails.org/migrations.html
 [2]: http://railsforum.com/viewtopic.php?id=4642