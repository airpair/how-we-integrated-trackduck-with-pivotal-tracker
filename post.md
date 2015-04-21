I’m Yauhen, CTO at [TrackDuck](https://trackduck.com/en/). We develop a visual bug-reporting tool for web developers and designers that allows you to report bugs, assign people and more right on live websites or image files.

Truth to be told, we've spent a lot of time on integrations, as a result of which right now TrackDuck integrates with 17 major project management and collaboration tools, including Pivotal Tracker. 

[Pivotal Tracker](http://www.pivotaltracker.com) is a handy tool for agile product development and management. It features user stories, bugs, epics, milestones and basically everything you need to be agile. 

The problem that many development companies face with, however, is how to involve non tech-savvy clients in their agile working practices, as not so many of them are aware of what the epic is and how to use PM tools properly.

We’ve solved some part of that problem by integrating TrackDuck with Pivotal Tracker. As a result of the integration, a client can mark issues directly on a website or image by simply highlighting an area and writing a comment with TrackDuck’s toolbar, while TrackDuck automatically captures all necessary details and documents them on Pivotal Tracker.

In short, the integration works in this way:
* User marks an area on a live website or image file and leaves a comment with TrackDuck;
* TrackDuck captures a screenshot, comment and details of reporter’s software (browser, resolution, OS);
* The bug report comes to Pivotal Tracker as a story with all details attached to it;
* All changes made in both tools are synced in real time;

How did we do that? Actually, it was pretty easy since Pivotal Tracker has a powerful and well-documented API that helps you to develop an integration without a hitch. Here I’ll explain how we made one with TrackDuck.

## Authentication

The starting point to get integrated with Pivotal Tracker is authentication. Fortunately, this system has the easiest way to setup and utilize their API - API Tokens. Just follow the [link](https://www.pivotaltracker.com/profile) and get it on you profile. 

![Authentication](https://blog.trackduck.com/wp-content/uploads/2015/04/Authentication.png)

This token is provided with each request that requires authentication, which goes with the token in X-TrackerToken HTTP header or as the value of a token parameter in query string. 

Keep in mind that tokens provided to a third party system by clients give access to client's projects and story data via the API. So, be sure to keep them encrypted and safe.

## Configuration

Once you authenticate and provide your credentials, you need to select a project you want to get feedback and bug reports to. That’s pretty easy to do as Pivotal Tracker supports the [main standard methods for project resource](https://www.pivotaltracker.com/help/api/rest/v5#Projects) due to the REST principles.

![Authentication](https://blog.trackduck.com/wp-content/uploads/2015/04/Configuration.png)

Then you need to select a format - a type of an issue - in which bug reports from TrackDuck come in on Pivotal Tracker.

By default, Pivotal Tracker has 4 types of stories that can be assigned to a task: *feature, bug, chore, release.*

## Exporting stories and comments

In order to send bug reports to Pivotal tracker, we use the [story](https://www.pivotaltracker.com/help/api/rest/v5#Stories) and [comment](https://www.pivotaltracker.com/help/api/rest/v5#Comments) APIs. The stories support different statuses: *accepted, delivered, finished, started, rejected, planned, unstarted, unscheduled.*

To make a story appear in an icebox you should set the status to *unscheduled*, while if you want it to go directly to a backlog the status should be set to *unstarted*.

As long as Pivotal Tracker has markdown support out of the box, a bug report comes in with all relevant information attahced to it, including a screenshot and  technical details of reporter's software, which help you to better reproduce the issue on your website.

![Exporting stories and comments](https://blog.trackduck.com/wp-content/uploads/2015/04/export1.png)

![Exportiny stories and comments](https://blog.trackduck.com/wp-content/uploads/2015/04/export2.png)

## Getting updates

To make bug reporting more convenient, we made a both-side integration with Pivotal Tracker. As a result, not only feedback is exported to Pivotal Tracker, but also all changes made in Pivotal Tracker are synced with TrackDuck in real time.

That is possible due to the support of webhooks on the level of a project that is easy to setup via project [webhook api](https://www.pivotaltracker.com/help/api/rest/v5#Project_Webhooks) (underdevelopment).

However, different network issues may occur during the synchronization. To make sure all updates are synchronized, we also check updates by time using a powerful [search API](https://www.pivotaltracker.com/help/api/rest/v5#Stories) that helps to catch updates made since the last synchronization of a project.

## SDK

Our solution is based on the Node.js platform. There are two main libraries that can be used for the integration:
* [https://github.com/Wizcorp/node-pivotal](https://github.com/Wizcorp/node-pivotal)
* [https://github.com/generalui/pivotaltracker](https://github.com/generalui/pivotaltracker)

As the API is pretty clear and well documented, we’d recommend to use the most suitable http client for your platform directly (our choice for Node.js is [request](https://github.com/request/request)) and monitor API updates.