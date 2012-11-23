---
layout: default
title: How I use two dynos on heroku for free
tags : [heroku, deployment, rails, ruby]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

I use [heroku](http://heroku.com) all the time. I don't need to bother with sysadmin stuff. But mainly because it's free :) Free is limited to one dyno, and to whatever free addons that you want to use, but that is good enough most of the times.

Except this time, it's not good enough. I need to run delay jobs, and that would require another dyno for the worker. That will cost roughly $34.50 a month. Quite a lot for a simple fun not-for-profit project. I could potentially use other queues addons with pay per use costing, but I don't feel like changing the app.

So what I did instead is, to start another heroku app with one worker only. No web dyno. Then all I need to do is change the DATABASE_URL to be the same as my current web app. 

    heroku config --app app-web # list down the config
    heroku config:add DATABASE_URL=mysql2://app-web.database.url --app app-worker
    
Now I'll have to deploy to both app. Something like this should do it

    git remote add app-worker git@heroku.com:app-worker.git # one time only, add app-worker to git
    git push heroku && git push heroku-worker # deploy to both
    
Heroku will spin down idle web dyno. Worker dyno should be up all the time. I didn't specifically test this out, but from [https://devcenter.heroku.com/articles/dynos#dyno-idling](https://devcenter.heroku.com/articles/dynos#dyno-idling) it only talks about web dyno. And so far all my jobs are completed.