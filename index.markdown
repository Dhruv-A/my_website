---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: splash
---
<br>

### I'm a computer science student at UNSW.

Visit Project Onecore below for quick and simple coding tutorials or the other links for my posts and reflections!


[Onecore](https://onecore.tech){: .btn} [Posts](/posts){: .btn} [Reflections](/reflections){: .btn}[Subject Review](/subjects){: .btn}{: .text-center}
{: .text-center}

## About

I am a student currently studying computer science. I currently study at the University of New South Wales and have studied at the University of Pennsylvania in the past. I am interested in embeddded systems and machine learning :)

I use this website to post about projects I've worked on, university subjects I've taken, and any interesting past experiences and advice I have. You can find source code for this [here](https://github.com/Dhruv-A/my_website).

[Onecore](https://onecore.tech) is a project I created in 2020 to teach students coding for the first time. If you wish to learn coding, make sure to check it out! I plan to revamp the website design in the future and continue adding more courses!

## Posts

The [posts](https://dhruvagrawal.org/posts) on my site are educational and focus on past projects of mine and advice I have for others. Here are the most recent ones:

{% for post in site.tags.posts limit:3 %}
    {% include archive-single.html %}
{% endfor %}

## Reflections

[Reflections](https://dhruvagrawal.org/reflections) are most about my personal thoughts on events from the past. Here are the latest ones:

{% for post in site.tags.reflections limit:3 %}
    {% include archive-single.html %}
{% endfor %}

## Subject Reviews

I write a [detailed review](https://dhruvagrawal.org/subjects) for each subject I take at university. Here are a few from the past few months:

{% for post in site.tags.subjects limit:3 %}
    {% include archive-single.html %}
{% endfor %}
