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

<img src="Container diagram - project focus.png" width="100%">

_soon: explanation about sync/async calls._