# authello.mit.edu
A simple service to allow MIT webapps to authenticate students with MIT certificates.

### Authentication Flow

There are two parts to the authentication flow. One takes place online when creating the webapp, the other happens when a user wants to log in.

**Offline Steps**

1. The application creator creates a new application on this service, providing:
  - A return URL: `RETURN_URL`
  - An application name: `APP_NAME`
2. The application creator receives a secret key `SECRET_KEY` and stores it somewhere private.

**Online Steps**

1. A user visits the host application, and signals the intent to authenticate.
2. The host application redirects the user to `https://authello.mit.edu/auth/(APP_NAME)`
3. The user is asked for a certificate, which is presented to this service.
4. This service calculates `SHA256((user's kerberos) + (current time) + SECRET_KEY)`, also known as the `TOKEN`
5. This service redirects the user back to `https://RETURN_URL?time=(current time)&user=(user's kerberos)&token=(TOKEN)`
6. The host application verifies the token is valid by performing the same calculation as this service, as well as checks to see if the time variable presented is within 5 seconds of the current time.
7. The host application has received a valid authentication and logs the user in.

### Design Goals

There are three major design goals as we build this service (in order of priority):

1. Developer Easy of Use
2. Simple Flow
3. Basic Security

**Developer Easy of Use** essentially means that the overhead for integrating this service into a new or existing application should be very low. There should be no need for extra dependencies or any assumptions made about the host application's design. In addition, This design goal is the most important out of the three.

**Simple Flow** means that a user being authenticated by this service should have no questions as to what they should do while being authenticate. One design decision made from this goal was that users are only asked for their certificates, not for their username and password or for kerberos tickets.

**Basic Security** means this service should not allow adversaries to forge the identity of someone else when authenticating through this service. *This service does not aim to provide bulletproof security from an adversary once a user has been authenticated. It is up to the receiving application to properly handle someone's authentication.*

### NOT Design Goals

This project does not aim to provide a completely secure way to provide authentication to web applications. Most importantly, use of this service shows implicit trust in the operators of this website. It is possible for the owner of this website to authenticate as any user to the connected web applications, though we pledge not to do so. Furthermore, if this website's internal database were to be hacked, the hackers would be able to authenticate as well.

Since the purpose of this tool is to make it easy for new developers to introduce basic authentication via certificates, these are trust considerations that we are okay with. *Once a web application has moved beyond proof-of-concept, the owners of that web application should work to directly accept certificates themselves, for better security.*
