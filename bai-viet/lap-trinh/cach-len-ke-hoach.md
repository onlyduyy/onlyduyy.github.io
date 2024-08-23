## Honestly? Just start!

After doing a bit of research about the tools you want to use. Create a note about how it should work purely functionally and just start building.

What I found, this (for me personally) it the only way to learn new technologies. Because while starting to learn something I don't have the knowledge to think of everything ahead of time.

What I can recommend is before programming a functionality always answer the following questions:

1.  What do I want to build?
2.  How should I build this?
    1.  If you have no idea, research first
    2.  If you think you know how to build it, write it down in small steps
3.  Follow your own instructions up to the point you get stuck or finish the functionality
    1.  If you get stuck, repeat the process.

Going through these steps allows me to structure my thoughts, which makes it so much easier. It also gives me some insight on which areas I need to learn more.

After a while you can do these steps "within your head", but I would suggest starting with writing everything down.

## Kĩ hơn

Here's the steps and process I usually go through to create projects.

**1\. Basic Idea**\
I usually start by getting some idea stuck in my head. It'll come to me while on the train, maybe laying in bed, or while watching TV. If it's a good idea, I'll think about it a bit more, and start to think about features. At this point, it's all in my head.

**2\. Features and User Stories**\
Once I've rolled an idea around in my head for a while, it starts to evolve and get uniquely defined features. At this point, I'll start by writing the features out in a notebook somewhere (for me, hard copy helps my ideas flow better than using a computer). After a rough point-form list of features is done, I make them more details as "User Stories", which look like this:

```
As A User:
  I want to be able to register for an account by entering my email
  and password.

  I want to be able to post notifications and have them automatically
  be emailed to all of my subscribers

As A Subscriber:
  I want to automatically receive emails whenever one of the users
  I follow submits a notification.

```

You'll notice these are basically lists of "Wants". Also notice that the list is fairly fine grained, and can be quite long. Once you know what you want, you can go a step further.

**3\. Mockups**\
If my application is going to have a user interface (like a desktop or web application), I'll draw a few sketches in a notebook. I am not a designer, artist, or User Experience professional, so these sketches always look terrible and never stick, but they help me to find the rough "look and feel" that I'm going for.

If you're more technically inclined, you can get [Balsamiq Mockups](http://www.balsamiq.com/products/mockups) and create interactive mockups on the computer. I highly recommend this product.

**4\. Deciding on Tools**\
Ok, so now I know exactly what I want to build, from features to (rough) design. The next step is to decide on my toolset. I'm familiar with many different languages, libraries, and frameworks, so I sit down and do a bit of research to find the best one(s) for this project.

For example, if I'm creating a real-time, auto-updating web application, I'll do some research to find out which web servers I'm familiar with have the best support for WebSockets, and might find out that [I can integrate my HTTP and WebSocket server if I use Tornado and SockJS](https://github.com/mrjoes/sockjs-tornado/).

**5\. Planning**\
Once the tools are decided upon, I plan the project out. I rarely need to come up with my own directory structure, as whatever framework I'm using will dictate one for me, whether it's [Ruby on Rails](http://rubyonrails.org/) or [Django](https://www.djangoproject.com/).

To start my planning, I create a new board on [Trello](https://trello.com/), and give it four columns, labelled "To Do", "In Process", "To Verify", and "Done". I convert each user story from Step 2 into a task in my "To Do" list. I also add a few other tasks that relate to the general "start up" process for a project, such as setting up a [Github](https://github.com/) repository, etc.

**6\. Development**\
Ok, I know what all my features are. I know what tools I will use to build the site. I know what all my tasks are. I'm ready to start. I go through the tasks in whatever order I feel makes sense (keep in mind, some tasks will require others to be completed first), and add those features to my program. When I start a task, I move it to the "In Process" list in Trello, and when I'm done it, it goes into "To Verify". **It does not go in "Done"!**

After about a week has passed, I'll go back to the "To Verify" task in Trello and read the user story I put together. I then try to do what it says in my application, and ensure it works. If you have two or more developers on a team, it is good practice to only verify someone elses work. The reason I wait a week is so that I "forget" some of the "quick tricks" you learn while repeatedly trying/testing during development. Being stuck in this routine while verifying can cause you to miss things.

Once a task is verified, it goes into the "Done" column. Once all tasks are in the Done column, the project is "complete". Many times, I'll do a project over multiple iterations, so this set of steps will happen once or twice, with a different starting point each time.

## Really?

I am going to buck against the other devs here. Everyone is screaming at you to use a waterfall approach. PLEASE DON'T DO THIS. First remember THERE IS NO CORRECT WAY TO DO ANYTHING. There are ways that are well tested, and patterns that people repeat because they worked before but there is no concept of "this is how you achieve X every time in every situation"

You know what you want your program to do right?

Lets say you want to make a simple system to look up, I dunno, recipes for cooking? And lets say you already know how you want to access/generally use such a system:

-   I want to access it on the web
-   I want to be able to search by recipe name or contents
-   I want to store as many recipes as I want
-   I want to be able to add more recipes.

So you know that for your program you want it to be able to do 4 things at a high level. Now just start coding one of those things. Seriously, just dig into google, grab some libs and start building. Don't worry about "right or wrong" as much at this stage. As you look for info on how to do what you want to do you will find comments, notes and articles that give you hints to some good ways to do what you want.

Once you have the basic code working, do some cleanup and re-factoring following these basic principals:

-   Each class/method should be uniquely responsible for a single task
-   Do not repeat yourself
-   Encapsulate your code as best as possible - As a general rule, if you can't take a class file you wrote out of your program and use it elsewhere without bringing a bunch of other stuff with it, its not encapsulated. Look to common patterns to find ways to achieve what you need.

When writing code try not to think like you are creating something that is in stone. Rather treat it like an english paper. The first time you write, its going to be a rough draft, just getting the general idea out. Then over multiple passes and time to think over your work, you refine, simplify and improve the code. Don't be afraid to throw out code you have written either. Once you have done something it's much easier the second time and you'll skip mistakes you made the first time. In most of my applications I will throw out huge chunks of code for re-write 2 or 3 times before it's really ready.

The important thing to remember is that just because it works, does not mean its ready, if it does not fit the basic rules above you likely have more work ahead of you before it's really ready for use.