---
layout: single
categories: reflections
tags: reflections
title: 11 Cool Coding Project Ideas and How to Make Them
refl: true
labels: educational project
---

*<span style="font-size: 0.8em">I'm brainstorming project ideas and want to share some of the things I've made in the past or set aside so that someone else can try it out. If my website has a comments section at the bottom of this article by the time you read this, feel free to share the projects you've been inspired to make from this post.</span>*

I'll be adding images to this article sometime in the future. Hopefully it's still really easy and digestible, and gives you some ideas for your future projects

## Foreword

Coming up with a project idea can be difficult if you haven't coded much before or simply don't have experience trying to come up with a project idea. Since we are currently in the summer holidays (at least in Australia) here's a few interesting project ideas in many different languages and a few tips to build them.

## Getting started

Before jumping into some cool project ideas, there are a few different things you should consider before starting a project.

### Version Control

One of the super important things you should setup before starting a project is some sort of version control. Version control is basically software that allows you to create separate versions of your project so that your work is always saved to the internet (in case you lose it) and so that you can go back to a previous version of your program if you accidentally add some code that breaks everything. The simplest and most popular way to setup version control for beginners is to use [GitHub](https://github.com). Firstly, you will have to install [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and then open your terminal (Powershell on Windows or Terminal on Mac).

In order to use Git, you will need to setup a repository on Github and then follow Github's instructions to setup a local repository that you can add code to on your own laptop and then send to Github to save it online. One important thing to remember is that Git is the software you will be using locally (on your own laptop) to manage your files, and Github is the platform online that will store your code. A good video to get started is linked [here](https://www.youtube.com/watch?v=HkdAHXoRtos).

### IDEs and Code Editors

An IDE (Integrated Development Environment) is an application you install on your own laptop to write and compile code. Depending on what project you choose, there might be a different IDE that is slightly better or worse. One other option is to use a code editor like [Visual Studio Code](https://code.visualstudio.com/download) or [Atom](https://github.com/atom/atom). Visual Studio Code is super simply to get setup and has a lot of great support for different useful extensions. If you choose to use this code editor, you will need to install extensions for each language you use but you can search up what you need on Google and it should be really straightforward.

### Task Management and Planning

Make sure that before you start, you make a plan for what you're going to do. If you choose a complicated project, try to map out a minimum viable product (MVP) before adding more complex features. Try to find different ways to simplify the tasks you have to do, whether that means creating scripts at the start to automate some tedious tasks, or finding a Python library that does exactly what you need for a certain feature so that you don't need to reinvent the wheel.

Map out what tasks you need to do first so that you can break down your project into small and simple tasks. Typically you should aim to break down your project into 3-5 hour tasks that have a well-defined start and end point so that you know exactly where your code is when you start and know exactly when you've finished that task. Keeping each task really small will help you stay focused and make it more fun to tick off each task.

**NOTE:** Each of the following projects has a difficulty rating of easy, medium, or hard. Easy projects are beginner friendly, medium projects have a greater challenge but are great if you've completed a few terms of uni, and hard projects are much more complex and require significant external research and learning.
{: .notice--success}

# Project Ideas

## Machine Learning and Data Science

### Create a mask detection application (easy)

Using a platform like [Edge Impulse](https://www.edgeimpulse.com) makes it really simple to create simple deep learning models to do tasks like mask detection. These can then be deployed onto a smartphone without any code at all! The beauty of Edge Impulse is that it creates tiny machine learning models (most of which are smaller than 1 MB). In order to make a project like this, you have a few different options.

1. Use a pre-existing dataset such as [this](https://www.kaggle.com/datasets/omkargurav/face-mask-dataset). By choosing this method, you won't need to come up with your own data and could probably finish this project within a few hours and minimal research.
2. Collect your own data! This is much more fun but requires some extra work. Firstly, you will need a face mask and a smartphone. Next, you will have to take many images with different settings, lighting levels, and angles of yourself with and without your mask. One thing to note is that if you want your model to be better at predicting other people wearing masks as well, you will need to have to collect data of other people as well.<br>Then you will need to crop your images so that the face is in the centre of the photo and there is a little bit of the background visible. Every photo doesn't have to (and shouldn't be) perfect but try to crop out most of the background simply because you won't have a big enough dataset for the model to learn about the background. Then label each of the images (put them in separate folders labelled with_mask and without_mask) and use those with Edge Impulse. Try to take at least 50 images for each label but the more the better!
3. Use a combination of both of the above. This one will work the best as it will train to fit your face well but also understand what wearing a mask looks like more broadly.

### Build a mask detection program with actual code (hard)

This project will to a similar program as the above one but is significantly harder. The first level of difficulty comes from learning how to make a tiny machine learning application for computer vision. The second hurdle is finding a way to deploy it. Some pointers for the first hurdle are:

1. Learn TensorFlow in Python - when you make a neural network with TensorFlow, you can condense this into a tiny machine learning model using TensorFlow Lite and TensorFlow Lite Micro. These using some amazing maths to shrink your model and allow you to create a tiny model that can be deployed on your phone or a microcontroller.
2. Use Transfer Learning - TensorFlow allows you to use transfer learning models like MobileNet which are small convolutional neural networks (CNNs) that basically extract important features from your images and make it much easier for a small neural network to make predictions really accurately and make training your model really fast.

Some pointers for the second hurdle are:

1. Depending on whether you use an Android or iOS smartphone, you will need to either intall Android Studio or XCode. These are IDEs that allow you to write code for your phone. Each one runs on a different language and you will need to learn how to code in either Java or Swift depending on which phone you have. Some documentation and tutorials for Android TensorFlow development can be found [here](https://www.tensorflow.org/lite/android/quickstart).
2. Try to watch some video tutorials for how others have deployed similar applications using a smartphone camera and a TensorFlow Lite model as this is exactly what you will be doing. Alternatively, download one of the applications from the linked tutorials above and then modify that code with your TensorFlow Lite model.

When I created this project myself, it took about 25-30 hours spanning the time from collecting my own dataset to training the model to deploying it and testing it. This is a really fun and powerful project but is definitely hard so it's best to try the above project before this one.

### Visualise COVID-19 data and find trends around the world (medium)

One really rich dataset for COVID-19 data can be found [here](https://www.kaggle.com/datasets/carlaoliveira/country-data-on-covid19). It has a lot of interesting columns and analysing this data could be a really fun and insightful task. Some cools ideas are:

1. Load and clean the data in this dataset using Pandas in Python. This will allow you to do a lot of calculations and visualisations, and run the data through different machine learning models to find different relationships.
2. Visualise the data using Plotnine or Seaborn. Plotnine is a Python library that originated from the R programming language. Luckily, you can use it completely in Python. One really cool thing is that it uses similar syntax in both Python and R so you can use documentation online for either language and it should all work in Python! Some different plots to try are bar plots, line graphs, density plots, and scatterplots.
3. Run Principal Component Analysis (PCA) using Sklearn on different features of the data to reduce the dimensionality of the data (convert a whole bunch of different features into one big combined feature) so try and spot trends across multiple columns.
4. Run KMeans clustering to see if you can cluster the data if you find a scatterplot with a lot of clumps of data.
5. Run a decision tree model or random forest ensemble to see if you can get a machine learning model to estimate the data and visualise the trend. Beware of overfitting the data and make sure to view your model so that you can check whether it captures the general trend of the data (good model) or is simply following the curve completely (overfitting model).


## Web Development

### Make a website with Jekyll (easy)

Make a website like this one super easily using [Jekyll](https://jekyllrb.com). There are some super simple and short videos on Youtube to learn how to use Jekyll very quickly and easily. Jekyll allows you to make a website (like mine) without writing almost any code. If you want to add extra features like dark mode or colourful tags for different articles, you will need to make a few changes to the HTML, CSS, and Javascript that Jekyll uses to build your website but these are definitely not necessary to build an aesthetic and functional website.

You can also get a domain name online and learn how to host your website so that other people on the internet can view your content. I use [Netlify](https://www.netlify.com) to host my website for free and it's super simple to add code and deploy changes. I use a continous deployment pipeline (you don't need to know what that is but it basically automates deploying changes to my website so that others can see what I changed) and everything is super simple to setup with a little bit of research.

### Make a website with HTML, CSS, and Javascript (medium)

If you're looking to make a website (the hard way) you can make a website using HTML and CSS, and if you want then you can add some cool features using Javascript. HTML is a markup language that basically acts like Microsoft Word but for a website. You can specify paragraphs, italics, lists, and many other things. CSS acts as the styling for your HTML and lets you change the colour, font, position, and other aspects of your HTML. Javascript is a coding language that lets you add code that does stuff like change different things when you click buttons or add forms to your site.

Here's a [video](https://www.youtube.com/watch?v=pQN-pnXPaVg) to help you get started with building your first website.

### Make a To-Do List web app with React (medium)

This is a cool application that you can make using all the stuff you learnt above. React can be difficult to learn but if you have a solid grasp of HTML, CSS, and Javascript you will definitely be able to learn it within a few weeks! There are some cool tutorials for learning React that you can follow but beware that they can be really dense and hard to understand so I would recommend watching a little at a time and trying out what you learn after each new topic. [Here's an article](https://medium.com/codex/building-a-to-do-list-app-using-react-hooks-and-styled-component-7e413a16b91e) on how to make your own To-Do List with react. [Here's a video](https://www.youtube.com/watch?v=pCA4qpQDZD8) to do the same thing if you prefer that.

I love the aesthetic of [Ora](https://ora.pm) so if you make something like that, it'd be a really cool project to showcase and could be super cool to use for yourself.

## Further Web-related Projects

### Make a web app with Flask (easy)

Flask is super easy to learn, especially if you already know Python and it could be the bridge between an aesthetic frontend and feature-rich backend for your website. [Here's a tutorial](https://www.youtube.com/watch?v=dam0GPOAvVI) for how to make a website with Flask.

### Try a Web-scraping project (medium-hard)

The first few projects above covered a lot of data-heavy projects. What I recommended there used already existing datasets because they are quick and easy to setup. One really cool project is to web-scrape data from a website like Wikipedia and use that for your own data analysis project. A cool tool to try web-scraping is Beautiful Soup in Python. There are lots of tutorials you can find on Google if you're interested in this project such as [this one](https://www.dataquest.io/blog/web-scraping-python-using-beautiful-soup). Additionally, you should refer to the data analysis projects I covered above if you want some ideas on what to analyse.

## Miscellaneous

### Build a Chrome Tab Management App (medium)

I love using [Toby](https://chrome.google.com/webstore/detail/toby-for-chrome/hddnkoipeenegfoeaoibdmnaalmgkpip?hl=en) for Chrome to manage the billions of tabs I usually have open. However, I don't like how it needs to be setup as the entire homepage of my Google Chrome. One thing that I haven't got around to doing but would be really cool would be to make this application as a Chrome Extension. This would allow users to save important chrome tabs and sort out the other ones without cluttering your Chrome homepage and could be extremely popular if it is useful and aesthetic.

The first thing to do would be to setup a basic Chrome Extension. You can find a guide for that [here](https://developer.chrome.com/docs/extensions/mv3/getstarted). Then, you will need to learn some basic HTML, CSS, and Javascript which will allow you to control the aesthetic and functionality of your application. The best part of this project is that you can run it on your own laptop and test everything out as soon as you make a change. Beware that this project could become really complicated depending on what features you want to add so make sure to break down your project and start with something simple.

### Remake Git (hard)

After creating every single project above, you're probably really fed up with Git. You make one mistake and it's all over - you need to rebase, pull, push, fix a billion merge conflicts, and nothing works even after all of that. Why not try to make Git yourself but make a simpler and better version for yourself.

You can make this project in a bunch of different languages like Bash or Python. I'd recommend that if you've already been using Python that you try using Bash as it is a bit hard to learn but is really cool for small scripting applications. I won't be providing a lot of help for this project but some important git features to include are

1. git init
2. git status
3. git add
4. git commit
5. git push

There are more advanced features that are much harder but could be really cool to implement such as

1. git branch
2. git merge
3. merge conflicts
4. git stash
5. git pop
6. git rebase
7. git pull

### Build your own online discussion platform (easy-hard)

This project is super open-ended and could vary from really easy to really hard depending on what features you want to include. Some cool ideas are:

1. Make a really aesthetic messaging app using HTML, CSS, and JS.
2. Connect your app to a database so that you can store all the messages and profiles of different people using your app.
3. Create your own search result algorithm so that when someone searches for a particular word, you can quickly display all the messages with that word.
4. Create your own recommendation system to recommend potential friends to people based on the messages they send (you can try to make this using many different algorithms and Google will be your best friend in this endeavour).
5. Add dark mode.
6. Add emojis and Latex rendering so that people can type maths equations on your platform.
7. Make your platform into a phone application or a desktop application which could be really hard but add a new level of interactivity with your platform.

There are so many other features you could add and even if you never end up actually deploying your application, the act of choosing a new feature to implement, researching how to code it, and then actually bringing your ideas to fruition will equip you with some essential skills for future projects.

## Afterword

In addition to creating your own projects, you could also try a hackathon. This will help you restrict your project to a weekend if you're short on time and there are so many cool skills you learn from working in a team to solve a problem in a short timespan. You can read more about Hackathons and Personal Projects [here](https://dhruvagrawal.org/posts/personal-projects-and-hackathons).
