# 31-days Design Document

## 1. Problem Statement

In the ever-changing world of technology, we need to constantly push ourselves to the limit to keep up with
the latest. Every professional needs to have a balance between work life and personal life. Sometimes, however,
that balance scale ends up tipping over too much on one side. The modern working professional requires
a service that can help them regain harmony in their life.

This design document describes 31-days, a new service that will provide productivity tools to meet our hardworking
professionals' time tracking needs. It is designed to interact with the professionals to record their day-to-day
activities and will return personalized feedback generated from their data to help guide their personal development.



## 2. Top Questions to Resolve in Review

1. Should we have separate tables to store the Activities based on the day, week and month? Or could we utilize GSIs to be able to retrieve those Activities by the day, week or month? 
2. Should there be a GET endpoint for all Activities for the user? Is that going to slow the service response time if there are a large number of Activities to load? Could we utilize Pagination?
3. What is the unique identifier for an Activity? Do we require a composite key? If the primary key is the UserId, do we need to utilize an ActivityId as the sort key to locate a specific Activity?
4. Should we have separate tables to store the Balance data based on the day, week and month? Or could we utilize GSIs to be able to retrieve those Balances by the day, week or month?
5. How is the Balance set by the user going to interact with the data in Activities? If the primary key for a Balance is the UserId, do we need to utilize a BalanceId as the sort key to locate a specific Balance?
6. What should be the extent of the data we request from the user? Does it matter what the specific activity entailed other than the fact that it was either for work or leisure?
7. How is the frontend going to interact with the backend to visually display the Activities and the ratios of work/life Balance?
8. If we decide later on to collect more data to provide greater personalized results to the user, will our current design make that too painful of a transition?
9. How will the weekly and monthly Balance ratios and productivity/happiness scales be calculated? From a fixed start date (i.e. Monday) to a fixed end date (i.e. Sunday) or a dynamically updating ratio of the past 7 days and the past 31 days?
10. Will calculating the Balance ratio for each day provide enough value to the User compared to the extra computing process it will require?
11. Should the Balance be set by the User during or after account creation? What are the technical challenges and pro/cons to both approaches?
## 3. Use Cases

U1. As a 31-days customer, I want to create a password-encrypted User account associated with my work-life Balance ratio.

U2. As a 31-days customer, I want to create an Activity that contains information about what I did from this exact time to that exact time, whether it was for work or leisure, and how satisfied (on a scale) I was with the activity.

U3. As a 31-days customer, I want to update my Activities.

U4. As a 31-days customer, I want to update my work-life Balance ratio.

U5. As a 31-days customer, I want to delete my Activities.

U6. As a 31-days customer, I want to view my Activity list for the current day in a visually appealing layout when I log into the main page of the website.

U7. As a 31-days customer, I want to view my Activity list and the Balance ratio for the current week when I click to view my weekly results.

U8. As a 31-days customer, I want to view my Activity list and the Balance ratio for the current month when I click to view my monthly results.

U9. As a 31-days customer, I want to view my average daily, weekly, and monthly productivity scale for my work Activities.

U10. As a 31-days customer, I want to view my average daily, weekly, and monthly happiness scale for my leisure Activities.

U11. As a 31-days customer, I want to view my target Balance.

## 4. Project Scope

### 4.1. In Scope

* Creating a User account with password-encryption
* Creating, updating, and deleting an Activity
* Creating and updating a Balance
* Retrieving Activities
* Retrieving target Balance
* Sorting Activities by the day, week, and month
* Retrieving productivity scale by the day, week, and month
* Retrieving happiness scale by the day, week, and month
* Sorting Balance by the week and month

### 4.2. Out of Scope

* Compare productive and happiness scale from one period (i.e. week, month) to another
* Deleting User account and all the associated Activities
* Retrieve all Activities
* Retrieve Balance by the day
* Sharing Balance (i.e. dashboard) between Users
* Integration with social media platforms such as LinkedIn, Facebook etc.

# 5. Proposed Architecture Overview

*Describe broadly how you are proposing to solve for the requirements you
described in Section 2.*

*This may include class diagram(s) showing what components you are planning to
build.*

*You should argue why this architecture (organization of components) is
reasonable. That is, why it represents a good data flow and a good separation of
concerns. Where applicable, argue why this architecture satisfies the stated
requirements.*

This initial iteration will provide the minimum lovable product (MLP) including creating, updating, deleting and 
retrieving an Activity, as well as updating and retrieving current and target Balance.

We will use API Gateway and Lambda to create seven endpoints (GetActivity, CreateActivity, UpdateActivity, 
DeleteActivity, CreateBalance, UpdateBalance and GetBalance) that will handle the creation, update, and retrieval of 
Activities and Balance to satisfy our requirements.

We will store current Balances available for Users in a table in DynamoDB. Activities themselves will also be 
stored in DynamoDB. For simpler computations of current Balances, we will continually update the list of Activities 
in a given time period (i.e. 7 days) directly in the Balances table.

31-days will also provide a web interface for Users to manage their Activities. 
A main page providing a list view of all of their Activities for either the day, week or month will let them create new 
Activities, view current productivity/happiness scales, update or delete their Activities. They can link off to another 
page to view their current Balance ratios.

# 6. API

## 6.1. Public Models

```
// ActivityModel

String userId; // unique identifier for User // partition key
String activityId; // unique identifier for the Activity // sort key
String activity; // what did you do
ZonedDateTime startTime; // from this exact time
ZonedDateTime endTime; // to that exact time
String type; // either for work or leisure (ENUM)
int scale; // from a satisfaction scale of 1 - 10
```

```
// BalanceModel

String userId; // unique identifier for User // partition key
String period; // either weekly or monthly (ENUM) // sort key
List<Activity> activities; // all the Activities from the period
float balance; // percentage of work activities over leisure activities
float productivity; // calculated from the average of the satifaction scales from work activities
float happiness; // calculated from the average of the satisfaction scales from leisure activities 
```

## 6.2. *First Endpoint*

*Describe the behavior of the first endpoint you will build into your service
API. This should include what data it requires, what data it returns, and how it
will handle any known failure cases. You should also include a sequence diagram
showing how a user interaction goes from user to website to service to database,
and back. This first endpoint can serve as a template for subsequent endpoints.
(If there is a significant difference on a subsequent endpoint, review that with
your team before building it!)*

*(You should have a separate section for each of the endpoints you are expecting
to build...)*

## 6.3 *Second Endpoint*

*(repeat, but you can use shorthand here, indicating what is different, likely
primarily the data in/out and error conditions. If the sequence diagram is
nearly identical, you can say in a few words how it is the same/different from
the first endpoint)*

# 7. Tables

*Define the DynamoDB tables you will need for the data your service will use. It
may be helpful to first think of what objects your service will need, then
translate that to a table structure, like with the *`Playlist` POJO* versus the
`playlists` table in the Unit 3 project.*

# 8. Pages

*Include mock-ups of the web pages you expect to build. These can be as
sophisticated as mockups/wireframes using drawing software, or as simple as
hand-drawn pictures that represent the key customer-facing components of the
pages. It should be clear what the interactions will be on the page, especially
where customers enter and submit data. You may want to accompany the mockups
with some description of behaviors of the page (e.g. “When customer submits the
submit-dog-photo button, the customer is sent to the doggie detail page”)*
