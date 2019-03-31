---
title: Creating a Conceptual Data Model
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-08-10T11:20:20+00:00
excerpt: 'There are three types of data models: Conceptual, Logical, and Physical. The conceptual model provides a high-level view of the data, defining the general entities and entity relationships using the language of the business or organization. The logical model adds attributes to these entities, providing a technology-agnostic foundation for a database design. The physical model assigns table names, column names, and data types to the entities and attributes defined in the prior models. Defining the data model in distinct layers helps us manage the complexity of design and focus as we refine our understanding of the problem space.'
url: /index.php/datamgmt/datadesign/creating-a-conceptual-data-model/
views:
  - 12535
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - conceptual model
  - modeling
  - visio
  - whiteboard

---
There are three types of data models: Conceptual, Logical, and Physical. The conceptual model provides a high-level view of the data, defining the general entities and entity relationships using the language of the business or organization. The logical model adds attributes to these entities, providing a technology-agnostic foundation for a database design. The physical model assigns table names, column names, and data types to the entities and attributes defined in the prior models. Defining the data model in distinct layers helps us manage the complexity of design and focus as we refine our understanding of the problem space.

<div style="background-color: #ffffcc; border: 2px solid #cccccc; padding: .5em; margin: .5em .5em 1em .5em;">
  This topic is a sub-set of my presentation later this week at Baton Rouge, <a href="http://www.sqlsaturday.com/28/eventhome.aspx">SQL Saturday #28</a>. If you are planning on being in the area I urge you to sign up, there are going to be a wide range of speakers presenting on SQL and .Net development topics and so far it sounds like we have over 500 attendees (read: networking opportunities). My slides are posted up on <a href="http://tiernok.com/presentation.php">my website</a>.
</div>

## Building the Conceptual Model

My approach is to start with the business drivers for the data and progress through external materials, technical inputs, and an analysis phase. Whether we are in a university environment, software development house, manufacturing company or non-profit organization, there is generally one key business driver. That driver has a business owner, technical or non-technical, and it is there that I prefer to start. Conversations with the business owner will often include less than specific terms and examples of competing services or existing data stores (think excel and MS Access) in the organization. Factors like maintainability, reliability, scalability, and so on will be mentioned in some cases, so we will want to document these if they come up. This provides the raw materials we need to build our data model.

### What is the purpose of [fill in the blank]?

<div style="text-align: center; color: #666666; float: right; margin: 1em;">
  <a href="http://picasaweb.google.com/lh/photo/-0iJqUji9TzP6kINPqkyqw"><img src="http://tiernok.com/LTDBlog/conceptual/dogcatdinner.jpg" alt="Context and expectations are key" title="Context and expectations are key" style="width: 260px" /></a><br />Context and expectations are key<br />Unnamed Picture on <a href="http://picasaweb.google.com/lh/photo/-0iJqUji9TzP6kINPqkyqw" title="See original on Picasa">Picasa</a>
</div>

Start with the purpose of the project or initiative. As the business owner describes the purpose of the project, we collect the nouns and specific terms they use and how those words are related. A notepad can be sufficient but a small recorder is probably the best tool (especially if your memory and handwriting are less than stellar). The terms we collect during this conversation generally fall into three buckets: initial entities to populate a rough model with, attributes that may need to be analyzed in later steps, and existing materials/systems/projects/people to follow up with. In this step I often find it easier to deal with non-technical people, as a technical person will sometimes communicate the design they have started in their head rather than the original business case.

### What does the business do?

It may not always be necessary to ask the business this (hopefully if you work there you already know the answer), but at a minimum it should be written somewhere near your other notes. The business provides context for the data and drives the relative importance of different terms and entities. It&#8217;s important to remember that one company&#8217;s definition of &#8220;customer&#8221; could be entirely different from another company&#8217;s. Knowing the context helps us determine how important the terms from the prior conversation are and the wide variety of contexts is the reason there isn&#8217;t one world-wide standard for the &#8220;Customer&#8221; entity.

<div style="text-align: center; color: #666666; float: left; margin: 1em;">
  <a href="http://www.flickr.com/photos/miranda_jc/3406275289/"><img src="http://tiernok.com/LTDBlog/conceptual/sneakycat.png" alt="Evaluate outside influences" title="Evaluate outside influences" /></a><br />Outside influences can sneak up<br />Sneaky Cat on <a href="http://www.flickr.com/photos/miranda_jc/3406275289/" title="See original on Flickr">Flickr</a>
</div>

### About those other materials&#8230;

Any additional materials, products, or people referenced in that first conversation need to be tracked down. Materials will expose a much more detailed list of potential data fields then you will hear in a conversation, competing products or products priced out of the organizations range will provide additional terminology and purpose, while other people will provide both a sounding board for the assumptions so far and a source of secondary translations and assumptions. The important part is that we all started with the same base story (the initial business owner) before getting this far, as that frames these conversations and investigations. And yes, I really want to use the word _context_ again.

### What&#8217;s sneaking up on us?

The last source of information we want to address is projects that are starting in the near future, strategic initiatives, and similar surprises waiting only a few weeks down the road. Going back to the business to ask about planned projects in the near future can often uncover additional information that will be critical to the model. Changes in business direction, assumptions of geographic growth or acquisitions, and even projects that are intended to tie into this data source can all mean the difference between a completed model and a redo of the modeling effort.

### Put it all together&#8230;

So by now we should be able to put it all together, right? Well, maybe. Probably not.

Now that we have all of those raw inputs, all of those conversations, all of those identified terms and topics we need to start trying to fit it all into a cohesive structure. So as we start drawing pieces (and usually I am drawing all along) we are going to run into more questions, we are going to have moments where we stare dumbfounded at our whiteboards, and there will be a number of odd moments of clarity (generally in the shower or coffee line). 

<div style="text-align: center; color: #666666; float: right; margin: 0em 1em;">
  <a href="http://www.flickr.com/photos/mcnutcase/2229434187/"><img src="http://tiernok.com/LTDBlog/conceptual/catbasket.jpg" alt="Turn the problem sideways" title="Turn the problem sideways" /></a><br />Turn the problem sideways<br />Cat in Basket on <a href="http://www.flickr.com/photos/mcnutcase/2229434187/" title="See original on Flickr">Flickr</a>
</div>

This last step incorporates the &#8220;Analysis&#8221; phase that I mentioned above. This is where we look at all of the potential attributes we have collected (you kept a list to make the logical phase easier, right?) and consider them from multiple different directions.

As we look through our list of entities and potential attributes, we want to question them on the dimensions of time, availability, location/person, and categorization:

  * Do we need to track the value as it changes over time?
  * Do we need to provide a mechanism to enter future values?
  * Do these values get deleted, flagged as inactive, or &#8230;?
  * Do different variations of the value exist based on Location? Language? Currency? Person viewing the data?
  * Can entities be re-categorized (ie, relationships changed)?
  * Can relationships/categorizations be duplicated? Many-to-Many? Time sensitive?

These questions can often spark the creation of new entities or extra information to consider in later modeling efforts. This extra analysis (and the rigor with which we gathered information) is what brings everything together and, in my mind, is where our expertise and experience should shine. 

### And Done-ish

There will be a time when you have to call it done (for now). I probably spend the most time on the conceptual model because the clearer and better a fit it is, the easier it is to come back and start attaching attributes and to communicate amongst the business and other technical personnel. But even so, perfect is the enemy of good, so we want to reach good and call it a day.

## The Finished Conceptual Model

At the point we call it &#8220;Finished [for now]&#8221;, we will have one or more diagrams that display the different entities and relationships, using business terms. We will also have a whole slew of notes on what those terms meant, other terms that were mentioned during the exploration (potential attributes, technical requirements, etc), and a partially worn out whiteboard. These our our deliverables from the conceptual modeling process and provide the foundation for our next steps.