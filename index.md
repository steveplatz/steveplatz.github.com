---
layout: default
title: Home
description: The personal website of Steve Platz
---

# Hi, I'm Steve.

I'm a software engineer living in Raleigh-Durham, North Carolina. I work on the Commercial Excellence team at [LORD Corporation](https://www.lord.com) as the technical lead. I also help organize the local [CRMUG Chapter](https://www.crmug.com/crmug/communities/community-home?CommunityKey=3390d9e7-8e46-4314-89ab-399713c436d8).

You can follow me on [Twitter](https://www.twitter.com/steveplatz), check out my resumé on [LinkedIn](https://www.linkedin.com/in/steveplatz), browse my code on [GitHub](https://www.github.com/steveplatz), or see my questions and answers on [Stack Overflow](https://stackoverflow.com/users/48421/steve-platz). You can also [email me](mailto:steve.platz@gmail.com), if that's your sort of thing.

I also [blog](/blog.html) sometimes. But, if past performance is any indication of future results, don't expect more than one post every three years.

## Recent Posts
{% for post in site.posts limit:5 %}
  * <span>{{ post.date | date_to_string }}</span> &raquo; [{{ post.title }}]({{ post.url }})
{% endfor %}
