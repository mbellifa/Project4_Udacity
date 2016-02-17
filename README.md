App Engine application for the Udacity training course.

## Products
- [App Engine][1]

## Language
- [Python][2]

## APIs
- [Google Cloud Endpoints][3]

## Setup Instructions
1. Update the value of `application` in `app.yaml` to the app ID you
   have registered in the App Engine admin console and would like to use to host
   your instance of this sample.
1. Update the values at the top of `settings.py` to
   reflect the respective client IDs you have registered in the
   [Developer Console][4].
1. Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
1. (Optional) Mark the configuration files as unchanged as follows:
   `$ git update-index --assume-unchanged app.yaml settings.py static/js/app.js`
1. Run the app with the devserver using `dev_appserver.py DIR`, and ensure it's running by visiting your local server's address (by default [localhost:8080][5].)
1. (Optional) Generate your client library(ies) with [the endpoints tool][6].
1. Deploy your application.


[1]: https://developers.google.com/appengine
[2]: http://python.org
[3]: https://developers.google.com/appengine/docs/python/endpoints/
[4]: https://console.developers.google.com/
[5]: https://localhost:8080/
[6]: https://developers.google.com/appengine/docs/python/endpoints/endpoints_tool

## Session and Speaker Implementation

Sessions are implemented as a new entity with the given conference being marked as the parent of the entity. This was done so that an ancestor query in datastore could efficiently retrieve all of the sessions belonging to a particular conference.

Speakers are implemented simply as string attributes of the Session entity which holds the name of the speaker. This implementation was chosen to allow for maximum flexibility if it was required to point to a user profile key instead the data would be more normalized but it would restrict adding a speaker who was not also a conference participant.
 
## The Problematic Query

The problematic query is problematic because datastore does not allow inequality filters on more than one property. From online research (http://nick.zoic.org/python/multiple-inequalities-in-google-appengine/) it appears that the best solution is to perform one inequality filter on the datastore side and perform the other inequality check in python.

Ex:
```python
import datetime
query = Session.query()
query.filter(Session.startTime < datetime.time(19))
results = [session for session in query if session.typeOfSession != 'Workshop']
```

## Additional Queries

getMorningConferenceSessions(websafeConferenceKey) returns sessions that begin in the morning (specifically before noon and after 5am). 

getAfternoonConferenceSessions(websafeConferenceKey) similarly grabs sessions that begin at noon to 5pm.

Both queries use AND with two inequality filters on the time property to find the range of times.