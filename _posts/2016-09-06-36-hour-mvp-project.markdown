---
layout: post
title:  "36 Hour MVP Project"
date:   2016-09-06 18:10:47 -0500
categories: javascript minimum-viable-product hackreactor MEAN angular mongo node express
comments: true
permalink: /:title/
---

Aren't sure how much you do or do not know? Give yourself 36 hours to build a full-stack web app from scratch. That was the challenge we were given last week. <!--excerpt-->This was really the first time that we were given the liberty to build whatever we wanted from scratch, so naturally you start thinking of all the amazing functionality you're going to build out and how it will definitely, possibly take off and allow you to never have to work again.

You realize pretty quickly **a)** that's not going to be possible (at least not when it's your first go at building a full-stack app from scratch), **b)** that's not the point of building an MVP, and **c)** the value of a team. While the time constraint at first seemed frustrating, it was a great exercise, in that it forced me to manage my time more wisely, it brought together everything I learned in the last 5 weeks, and challenged me to break functionality down into as small pieces as I could. 

I ended up choosing to use the MEAN stack to create 'So Fetch' - an app to help coordinate your pet's care.

![/downloads/soFetch2.png](/downloads/soFetch2.png){:class="img-responsive"}

I enjoyed working with the MEAN stack and felt like it all flowed pretty seemlessly. On the front-end, Angular was extremely easy and quick to set up. Additionally, having the routing being done by the UI Router greatly simplified the back-end. 

**The overall hits of the project:**

1. Got a login/signup working
2. Pages routing correctly
3. Able to log (POST) meds, feedings, and walks
4. Able to update or delete logs (PUT, DELETE)
5. Able to retrieve logs from database (GET)
6. Some user cusomization 
7. Successfully launched to Heroku
8. Look decent enough to call attention to users

**The overall misses of the project:**

1. No use of token or session so users can access certain pages without being logged in
2. All users see all logs - need to create link between users table, log table, and pets table
3. User unable to create pet profile
4. Ideally with logs, the 'pet name' field would be a dropdown with the names of user's pets
5. Ideally with logs, the 'posted by' field would auto populate to the name of the user

Looking forward I would have a couple of suggestions to future students starting MVP and to myself as we move into the project phase. First, communication is a word that always comes up in group projects, but it's also incredibly important in working solo. By this I mean be clear of what your goals and priorities are. At some point, your curiosity is going to want to lead you down a rabbit hole. Keep reminding yourself of those goals and priorities to keep yourself on track. Is this getting me closer to my goal of 'x'? Is it necessary for me to know 'y' in order to do 'x'?

For example, I spent some time looking at Angular-xeditable, which is an awesome bundle that I think would really improve a user's experience. At this point my goal was to be able to allow the user to edit their logs. Could I accomplish this without Angular-xeditable? Yes. Was figuring out Angular-xeditable getting me closer to my goal? Mmm, kind of, but it was taking me further from my goal first. This was certainly one rabbit hole I could have avoided and maybe come back to only after getting the basic edit functionality down.

The other piece of advice I would have is to break tasks down into as simple a task as you can. It may feel tedious, but it will ensure that you do not miss any steps and if you're able to test at each step, you can catch any possible bugs. Take the example above of editing a form. What do you have to do in order to edit a form? At first I took this to be one easy step - make logs editable.  Well, not quite - you need to give the user a button, when they need to be redirected to a different page, the button needs to bring up a pre-populated version of that form, the user needs to be able to delete and edit information on that form, you need to store that information, there needs to be a button to allow the user to save the edited information, when the user clicks save that information must be updated in the database, once the user saves they need to be redirected back, the log now needs to reflect the updated information, and so on.  

Essentially, it's great to be excited and think big, but start small, test each new bit of functionality no matter how insignificant it may seem, and build out from there. 

You can checkout the code [here on GitHub].

Or try out the site [on Heroku]. Heads up - once you log in or sign up you may need to refresh the page in order to see the nav bar (another bug!).

[here on GitHub]: https://github.com/yctercero/sofetch
[on Heroku]: https://sofetch-mvp.herokuapp.com/#/login