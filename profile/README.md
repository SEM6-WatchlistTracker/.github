# About the project

## Follow the planning
https://github.com/orgs/SEM6-WatchlistTracker/projects/1


## Focus of the project

_(See the image below.)_

The chosen APIs are:
- [Gateway](https://github.com/SEM6-WatchlistTracker/wlt-gateway)
- [Auth Service](https://github.com/SEM6-WatchlistTracker/wlt-auth-service)
- [User Service](https://github.com/SEM6-WatchlistTracker/wlt-user-service)
- [FriendsWatchlist Service](https://github.com/SEM6-WatchlistTracker/wlt-friendswatchlist-service)
- [MediaStatistics Service](https://github.com/SEM6-WatchlistTracker/wlt-mediastatistics-service)
- [Kafka Messaging Bus](https://github.com/SEM6-WatchlistTracker/wlt-kafka-messaging)
- [Service discovery](https://github.com/SEM6-WatchlistTracker/wlt-discovery)

These were chosen because:

- Gateway & Auth Service for security learning outcomes.
- A combination of User, FriendsWatchlist and MediaStatistics to show synchronous calls and asynchronous messaging between services. E.g. FriendsWatchlist needs User data, FriendsWatchlist sends messages for updating MediaStatistics.
- FriendsWatchlist was chosen over Watchlist for possible Real-Time editing among friends.

<img src="https://raw.githubusercontent.com/SEM6-WatchlistTracker/.github/main/profile/Container%20diagram%20-%20project%20focus.png" width="100%">

## Explanation on interservice communication

Most of my requests require only the service itself to get, update or delete information from.\
There are only four exceptions to this:
1. Checking the authentication token and role (Auth) before the request is allowed to go through.
2. Getting friend connections (Friends). It requires the user information of each friend.
3. Getting watchlists between friends (FriendsWatchlist). It requires the user information of the collaborating friend.
4. Updating statistics of media when it has been added to a watchlist (Watchlist, FriendsWatchlist). It sends the media that has to be changed along with the statistics that have to be adjusted.

For 1, 2 and 3 I used synchronous http requests, because these:
- Need an immediate response.
- The result is needed to move forward in the process.
- Are allowed to fail without retry.

For 4 I used asynchronous messaging using the Kafka message bus, because:
- It can be done without having to send back a response, as long as the message has been acknowledged and sent.
- It is allowed to take more time. It is not crucial for the information to be updated as soon as possible, and it does not affect the result.
<br>

A complete clarification on the [interservice communication](https://walkingtreetech.medium.com/inter-service-communication-in-microservices-c54f41678998) in my system:

- Gateway has [synchronous](https://cdn.ttgtmedia.com/rms/onlineimages/serverside-synchronous_inter_service_communication-f.png) interservice communication to **Auth** service.
- Friends service is a microservice with [synchronous](https://cdn.ttgtmedia.com/rms/onlineimages/serverside-synchronous_inter_service_communication-f.png) interservice communication to **User** service.
- Watchlist service is a microservice with [asynchronous](https://cdn.ttgtmedia.com/rms/onlineimages/serverside-asynchronous_inter_service_communication-f.png) interservice communication to **MediaStatistics** via the Kafka messaging bus.
- FriendsWatchlist service is a microservice with [hybrid](https://cdn.ttgtmedia.com/rms/onlineimages/serverside-hybrid_inter_service_communication-f.png) interservice communication. It has synchronous interservice communication to User service, and asynchronous interservice communication to **MediaStatistics** via the Kafka messaging bus.

#### Sources
- https://www.theserverside.com/answer/Synchronous-vs-asynchronous-microservices-communication-patterns
- https://greeeg.com/issues/microservices-patterns-synchronous-vs-asynchronous
- https://walkingtreetech.medium.com/inter-service-communication-in-microservices-c54f41678998
