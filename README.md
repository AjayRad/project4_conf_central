Conference Central App Engine application .

## Products
- [App Engine][1]: The product is a app engine based set of APIs that provides functionality to setup and manage conferences. It can be accessed via a web based front end but can be extended to other mobile apps. It supports user authentication via google accounts, managing conferences, sessions and user profiles. 
- App is currently hosted at : https://prj2-hola-app.appspot.com

## APIs
- [Google Cloud Endpoints][2]

## Updated functionality 
1. Added Session functionality to an already avaliable app:
    - getConferenceSessions-- Given a conference, return all sessions
    - getConferenceSessionsByType -- Given a conference, return all sessions of a specified type (eg lecture, keynote, workshop)
    - getSessionsBySpeaker -- Given a speaker, return all sessions given by this particular speaker, across all conferences
    - createSession-- open to the organizer of the conference
Implemented Session class and corresponding form. Setup sessions as a child to the conference model as sessions cannot exist without a conference (parent-child model). This makes querying for sessions via conference as ancestor. It also means that a session is "tied" to a conference ; seems like a reasonable expectation as we cannot have sessions without conferences.

The model design:
| Property        | Type             |
|-----------------|------------------|
| name            | string, required |
| highlights      | string           |
| speaker         | string, required |
| duration        | integer          |
| typeOfSession   | string, repeated |
| date            | date             |
| startTime       | time             |
| organizerUserId | string           |



2. Added functionality to add sessions to a users wishlist 
    - addSessionToWishlist -- adds the session to the user's list of sessions they are interested in attending
    - getSessionsInWishlist -- query for all the sessions in a conference that the user is interested in
 As a wishlist is always associated with a user, modified Profile to contain a wishlist as a repeated key called sessionWishlist

3. Additional Queries
    - Added two additional endpoints suppporting the following functionality : 
       - getSessionsBySpeaker -- returns all sessions for a speaker across all conferences
       - getConferenceSessionsToDate -- Returns all sessions in a conference to date (till today) sorted by date and time 

4. get Non Workshop sessions before 7:00 p.m. 
       - getNonWorkshopDaySessions -- gets all sessions that are not type workshop and are before 7:00 p.m. 
       - Note: In implementing this we run into pecularity with ndb where queries are allowed to have only one inequality filter. Worked around it by making it a 2-step filtering process where sessions marked "workshop" are removed from results by looping thru. Another option is to reverse the filter and loop steps to get the same end result. 
       
5. Featured Speaker 
   - getFeaturedSpeaker -- returns all sessions of a featured speaker. Added caching via memcache to support this feature
   - the cache is set using GAE's task queues mechanisim thus making this functionality asynchronous and not hoding up user request threads
   
## Setup 
1. Clone this repository. 
2. Update the value of application in app.yaml to the app ID. You need to resigter via the App Engine admin console before this step
3. Update the values at the top of settings.py to reflect the client IDs you have registered in the Google Developer Console.
4. Update the value of CLIENT_ID in static/js/app.js to the Web client ID

## References
1. [1]: https://developers.google.com/appengine
2. [2]: https://developers.google.com/appengine/docs/python/endpoints/
