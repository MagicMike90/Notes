# General Notes
Search Method
- *Soundex* is a phonetic algorithm for indexing names by sound as pronounced in English. It is appropriate when you want to do name lookups and allow users to find correct results despite minor differences in spelling.

- *Fuzzy matching* is the technique of finding strings that match a string, approximately, not exactly. The closeness of a match is measured in terms of operations necessary to convert the string into an exact match.

- *Levenshtein*. It's a simple algorithm that provides good results, but it's not supported by MongoDB. Measuring the Levenshtein distance must thus be done by fetching the entire result set and then applying the algorithm for the search query on all the strings. The speed of the operation grows linearly with the number of documents in your database, so unless you have a very small document set, this is most likely not
worth doing

Website Documentation
http://patternlab.io/

# Reading list 
- Learning Scrapy
- Vagrant Virtual Development enviroment Cookbook
- Pro MERN stack


# OAUTH2
## Abstract Protocol Flow
Resource Owner: User

The resource owner is the user who authorizes an application to access their account. The application's access to the user's account is limited to the "scope" of the authorization granted (e.g. read or write access).

Resource / Authorization Server: API

The resource server hosts the protected user accounts, and the authorization server verifies the identity of the user then issues access tokens to the application.

From an application developer's point of view, a service's API fulfills both the resource and authorization server roles. We will refer to both of these roles combined, as the Service or API role.

Client: Application

The client is the application that wants to access the user's account. Before it may do so, it must be authorized by the user, and the authorization must be validated by the API.

1. The application requests authorization to access service resources from the user
2. If the user authorized the request, the application receives an authorization grant
3. The application requests an access token from the authorization server (API) by presenting authentication of its own identity, and the authorization grant
4. If the application identity is authenticated and the authorization grant is valid, the authorization server (API) issues an access token to the application. Authorization is complete.
5. The application requests the resource from the resource server (API) and presents the access token for authentication
6. If the access token is valid, the resource server (API) serves the resource to the application
