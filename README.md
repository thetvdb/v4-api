# TVDB V4 API
We have two models for API access, both that provide funding that allows us to continue running and improving the site. The first is our negotiated license model, which allows commercial companies to negotiate access with us. The second is a user-subscription model, which allows end users to access the API if they are subscribed. We reserve the right to change our interfaces, fees, or licensing terms at any point without notice.

To create an API key, create an account and visit the [API keys page on your dashboard](https://www.thetvdb.com/dashboard/account/apikey).

## SSL/TLS
This API will enforce a minimum SSL/TLS version `TLSv1.2`.  This is supported by all modern programming languages. However if you are using an older version of a modern language, you may need to upgrade your SSL library.

## Documentation and Assistance
Before requesting assistance, please review our [API documentation](https://thetvdb.github.io/v4-api/). If you need additional assistance, please use our [support portal](https://support.thetvdb.com/).

For API related issues or questions, please use the github [issue tracker](https://github.com/thetvdb/v4-api/issues).

## Postman collections
- [Postman](https://www.getpostman.com/collections/7a9397ce69ff246f74d0)

## Official Libraries
- [Python](https://github.com/thetvdb/tvdb-v4-python)

## Best Practices
### Data Retrieval Method
We strongly recommend maintaining your own copy of the database or making use of a caching proxy if your end users make direct use of data from TheTVDB.

#### Maintaining a Full Database Copy
The benefit of maintaining a full copy of TheTVDB's data is that you can transform it as desired before providing to your end users, are not as susceptible to TheTVDB outages, can report across the entire data set, and can write your own endpoints that are specifically designed for your needs.

1. Iterate all records in each of the endpoints, and ingest as desired into your database.
2. Monitor the [/updates endpoint](https://thetvdb.github.io/v4-api/#/Updates/updates) and update your database with any changes to the records.
3. Some deletion updates include mergeToType and mergeToId information, if the deletion was the result of a duplicate record.  You can move any information, like follower counts or ratings, from the deleted record to the record specified by the two merge fields.

#### Providing a Caching Proxy
A caching proxy can provide an intermediary between your users and TheTVDB.  This protects your service from any potential outages or performance issues on TheTVDB, and reduces bandwidth for TheTVDB as well.  Additionally, you can transform the data as desired before delivering to your end users.  Finally, this approach allows you to make requests from TheTVDB's API using your API key, and using your own authentication model for your own users.  This approach limits you to the endpoints provided by TheTVDB.

1. Implement a caching proxy such as squid.  Set time to live for each endpoint based on the needs of your users.  For example, your users may expect to see completely up to date series and movie records but would be fine seeing a person record that is up to a week old.
2. Within your app, software, or service, retrieve records directly from your own caching proxy instead of TheTVDB.
3. (Optional) To improve responsiveness for your users, you may wish to write a cache warmer that will iterate the most popular records on TheTVDB and retrieve them from your caching proxy. This can be run whenever you need to reset your proxy's cache for any reason.

#### Direct API Access From End Users
Your project may not have the resources to provide its own API or caching proxy hosting, or you may not wish to deal with the complexities of either system.  In most cases you may write your app or software such that your users directly access TheTVDB's API.  You should contact us in advance to negotiate a contract (generally requiring attribution) or use a subscriber-supported API key that requires that each of your users has a $12/year TheTVDB subscription.

### Endpoints To Heavily Cache
Even if directly accessing the API for end users, you can heavily cache some endpoints that are unlikely to change that often.  These include artwork types, award categories, content ratings, entity types, genders, genres, inspiration types, languages, movie statuses, people types, series statuses, and source types.  In general, you can safely cache these for a week or even longer.  We recommend against hard-coding the values from these endpoints unless necessary.

### Scores
Most entities within the database include a score field.  This is currently an arbitrary number that provides relative popularity for the record.  Score can be used for sorting records or biasing within search results of a specific entity type.  Comparing scores across entity types (such as a person vs a movie) will yield meaningless results.

### Artwork
We have various artwork types for different entities, as defined within the /artwork/types endpoint.  The most popular artwork of the most popular types is provided on the entity base records themselves.  For example, the best movie poster is already in the base movie record, so it is generally unnecessary to retrieve the full artwork set.

Some entity types (series, movies, seasons) have more than one artwork of each type while others (episodes, companies, people) have a single artwork of a single type.
People may have a role-specific image, but within our site we fall back to the person's default headshot.  This is generally the best approach in most use cases.

Episode images may be 4:3 or 16:9 format.  Our site requires that the format matches the original broadcast of that episode.  User interface design should plan for these formats accordingly.

### Season Types
All series can have multiple seasons associated with them.  Episodes don't have to exist within every season type.  For example, when Netflix aired Money Heist, they completely re-cut the episodes.  The episodes representing the original order are different than those representing the Netflix order.  Both sets are assigned to the series using a different season type.

The series base record includes the id of the default season order, which is generally "Aired Order".  The episode base record includes season number and episode number information, which is that episode's information within the default season order.

Season types can also be named.  Using the Money Heist example, we don't display "Alternate Order" on the site but rather "Netflix".  This information is included when retrieving the seasons for a series.

### Search
Most systems will rely heavily on search, which is facilitated by our [search endpoint](https://thetvdb.github.io/v4-api/#/Search/getSearchResults).

## Legal Usage
We do not claim ownership of any of the images or data in the API. We comply with the Digital Millennium Copyright Act (DMCA) and expeditiously remove infringing content when properly notified. Any data and/or images you upload you expressly grant us a license to use. You are prohibited from using the images and/or data in connection with libelous, defamatory, obscene, pornographic, abusive or otherwise offensive content
