---
layout: post
title: Url rewrite for ASP.NET Web API SPA
author: Andreas Ågren
published: 2015-11-23
tags: asp.net,web api,spa,url,iis,web.config
---

![HTML JS CSS](/images/html-css-js.png)

I just wanted to share this thing I had to search a while for.

When making a SPA (Single Page Application) I divided my website into two parts: One part Asp.net web api and one part static html/js/css, as one usually does. However, I also wanted to use the html5 history api with no hash in the url. For example, imagine that `http://example.com/` returns a list of users, and when clicking on "Dolly Parton" we change the url to `http://example.com/users/dolly-parton`, an ajax request is made to a back-end api, which returns a JSON-string, which is rendered in the view. Now imagine that we bookmark that url and then open it later, what will happen? Well, the server will receive a request for that path and e.g. try to find a directory /users/dolly-parton with an index.html in it, none is found so 404 you get.

<!--more-->

In other words, we need to make sure that most incoming paths are routed to our spa index.html so that *it* can handle the path and show the right view. We also need to still be able to call our back-end api, and we also need to be able to fetch our static html-templates, js- and css-files. In my case I have my api at `/api` and all my static files in a directory `/public`.

I'm sure there are many ways to do this, but since I'm running an Asp.net Web API project I opted for IIS configuration in my web.config. Specifically this:

    <system.webServer>
      <rewrite>
        <rules>
          <rule name="RewriteUnknownToIndex" patternSyntax="ECMAScript" stopProcessing="true">
            <match url="^(public|api)/.*" negate="true"/>
            <action type="Rewrite" url="/public/index.html"/>
          </rule>
        </rules>
      </rewrite>
    </system.webServer>

This rule simply says: If the path does *not* start with "public" or "api", then return `/public/index.html`.


It is an OWIN application, so there are probably some neat way to do this using the `Owin.IAppBuilder` (comments are welcome), but right now I decided to go with the IIS config, mainly for the simplicity.
