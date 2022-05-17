# About the project

## Follow the planning
https://github.com/orgs/SEM6-WatchlistTracker/projects/1


## Focus of the project

- [Gateway](https://github.com/SEM6-WatchlistTracker/wlt-gateway)
- [Auth Service](https://github.com/SEM6-WatchlistTracker/wlt-auth-service)
- [User Service](https://github.com/SEM6-WatchlistTracker/wlt-user-service) (complete)
- [FriendsWatchlist Service](https://github.com/SEM6-WatchlistTracker/wlt-friendswatchlist-service) (complete)
- [MediaStatistics Service](https://github.com/SEM6-WatchlistTracker/wlt-mediastatistics-service) (complete)
- [Kafka Messaging Bus](https://github.com/SEM6-WatchlistTracker/wlt-kafka-messaging) (complete)
- [Service discovery](https://github.com/SEM6-WatchlistTracker/wlt-discovery) (complete)

<img src="https://raw.githubusercontent.com/SEM6-WatchlistTracker/.github/main/profile/Container%20diagram%20-%20project%20focus.png" width="100%">
<br>

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
- FriendsWatchlist service is a microservice with [hybrid](https://cdn.ttgtmedia.com/rms/onlineimages/serverside-hybrid_inter_service_communication-f.png) interservice communication. It has synchronous interservice communication to **User** service, and asynchronous interservice communication to **MediaStatistics** via the Kafka messaging bus.

### Sources
- https://www.theserverside.com/answer/Synchronous-vs-asynchronous-microservices-communication-patterns
- https://greeeg.com/issues/microservices-patterns-synchronous-vs-asynchronous
- https://walkingtreetech.medium.com/inter-service-communication-in-microservices-c54f41678998
<br>

## Data

### Database structure & Models

<img src="https://raw.githubusercontent.com/SEM6-WatchlistTracker/.github/main/profile/Services%20Data%20Objects.png" width="100%">

Each microservice has its own database. Each model has its own table in that database with a respected name. For example, Watchlist service has a database 'watchlist-database' with tables 'lists' and 'media'.
<br>

### Flow of data usage

<img src="https://raw.githubusercontent.com/SEM6-WatchlistTracker/.github/main/profile/Example%20Data%20Flow.png" width="100%">

Above is an example of the data usage and how the User service is used in a request for collaborative watchlists and friends. For some get methods like these two, additional information and actions are required to provide a tailored response. 

### Data consistency 
Most data in my system is always going to be consistent, due to the fact that almost everything is and stays within the services themselves. The only data that gets created or updated based on another service are in User service and MediaStatistics service. 
- User service: a user gets created based on the id provided from Auth service. These must match. Both services must be online at the same time.
- MediaStatistics service: statistics for a media are updated based on what's updated in Watchlist and FriendsWatchlist service. MediaStatistics does not have to be online, but the Kafka messaging bus must be online. When the service comes back online, it would retrieve all missed messages.

To keep this data further consistent in case of downtime of services, I could choose to save all unsent requests or do a daily sync.
