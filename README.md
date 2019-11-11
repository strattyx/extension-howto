# Developer Intro
## What's an Extension?
StrattyX has an internal scripting language that we use to represent user strategies. Via an extension operator we can call externally hosted functions. These functions can do pretty much anything and because the user's strategy is a program they can completely change the functionality. 

## How do Extensions Work?
Currently, strategies have two basic operations
- `realtime`: Running on a chron
- `timeline`: Generating backtests using historical data
The extension operator uses a webhook call to the url that you set in the developer portal with a request that corresponds with the mode of operation, as well as any relevant data and arguments.

### Endpoints
In order for your extension to fully integrate with our system you must have the following endpoints configured
#### **POST** `/invoke/live`:
  - request body will include a field called `arguments` conataining a dictionary with user provided arguments
    - ie: `{ "arguments": { "Timezone": "UTC" } }`
  - We expect the webhook to return json containing a return value
    - ie: `{ "return": "Sunday", "resolutions": [] }`
  - If there is a external link that would be useful in explaining the descision your extension made (ie - article link, tweet, etc.), you can include it in the "resolutions" field. 
    - this example isn't ideal application: `{ "return": "Sunday", "resolutions" : ["it's Sunday 2019-10-27 in timezone UTC"] }`
#### **POST** `/invoke/historic`:
  - This extension isn't always required, however it's important for most users and will be displayed in marketplace
  - request body will include a field for the following
    - `arguments`: arguments for operator
    - `period`: start and end of backtest period
    - `ts`: [timestamp](#) for particular datapoint (note: "start", "*", "default" have special meaning)
    ```js
    {
      "arguments" : { "Timezone" : "UTC" },
      "period" : [ "2019-10-20T15:50:46.486Z", "2019-10-27T15:50:46.486Z" ],
      "ts" : "*"
    }
    ```
  - We expect the webhook to return json containing a value timeline, for example:
    ```js
    {
      "timeline" : {
        "start" : { "value" : "Sunday" },
        "2019-10-21T00:00:00Z": { "value": "Monday" },
        "2019-10-22T00:00:00Z": { "value": "Tuesday" },
        "2019-10-23T00:00:00Z": { "value": "Wednesday" },
        "2019-10-24T00:00:00Z": { "value": "Thursday" },
        "2019-10-25T00:00:00Z": { "value": "Friday" },
        "2019-10-26T00:00:00Z": { "value": "Saturday" },
        "2019-10-27T00:00:00Z": { "value": "Sunday" }
      },
      "warn": [],
      "resolutions": []
    }     
    ```
  - warnings should go in the warn array
    - ie: 
    ```json
    "warn" : [
      "Twitter: invalid twitter user", 
      "Forex: USD->USD xrate always 1", 
      "RSS: limited historic data, only 20 most recent posts"
    ],
    ```
  - resolutions have same meaning as in realtime, but will likely be ignored

# FAQ
## What if I don't have a domain name/server?
- Option 1: Use a free-tier hosting service (ie - heroku, aws freetier, etc.) to host your extension
- Option 2: Reach out to [@dvtate](https://t.me/ridderhoff), we may be able to host it for you

## I only have one server but want to make multiple extensions
- Option 1: run different extensions on different port numbers
  - ie: 4chan extension can have host `strattyx.com:5050` and weather extension could use `strattyx.com:5051`
- Option 2: Put them in different directories
  - ie: topic-modelling extension on `strattyx.com/ext/news-topic` and stocktwits extension on `strattyx.com/ext/stocktwits`

## How will I use my extensions?
- Coming soon: see Outlook, interested in feedback
- Register your extension via the developer portal
- it will be available to you in template system and desktop client

## What's a template?
- Templates are web-based procedurally generated forms that produce expressions in our langauge. This allows us to add new features to the app without writing native code or application updates. 
- It allows users to create more advanced strategy conditions by using output of operators as inputs
- When you submit your package you reccomend templates for us to make for it and we will add them in the approval process

## How will User use my extensions?
- Coming soon: see Outlook, interested in feedback
- Register your extension(s) via developer portal
- Using developer portal create a Package containing related extensions
- fill in relevant details and submit for approval
- We will make the template UI's based on your suggestions
- Package will appear in marketplace

## How will marketplace work?
- It's still a WIP, so this isn't final
- Potential Package Pricing Models: 
  - free: primarily first-party or FOSS extensions
  - one-time cost: front-loaded payment
    - ie: giving static dataset access
  - subscription: recurring fee for having access to extension
    - ie: bloomberg news feed
  - on-demand: payment based on number of strategies it's used in (or pay-per-use)
    - good for cpu intensive applications
    - ie: ai-based 
- on demand may not be offered initially
- subscription will likely require paying in advanced
- developer will get largest cut of revenue from sales
  - StrattyX and maybe the payment system (ie apple takes 30% of inapp payments) also take smaller cuts


# Outlook
We move pretty fast and work on a lot of different tasks, but here's what our timeline for extensions/templates/packages is looking like. None of these dates are set in stone. Also note that even if we have things added to frontend, user may not see it for an additional week or so due to a variety of factors.

#### 2019.11.1:
- [ ] Build out developer portal
- [X] Build out template system
- [ ] Add template system to app
- [X] **Begin working with developers on making extensions**

#### 2019.11.15
- [ ] Features (Package,Template,Extension) Marketplace working
- [ ] **Begin working with developers on populating markeplace**
- [ ] Preliminary Payment system
- [ ] bulk timeline operation support (improve backtest performance)

#### 2019.11.25
- [ ] Features Marketplace in app
- [ ] Fully automated system for deployment of extensions
  - [ ] Manual Review still required for templates/packages
- [ ] Fully automated payment system
- [ ] Polish developer portal

<!--
#### 2019.12.1
- [ ] Desktop MVP operational
  - [ ] Makes extensions more intuitive and allows users to make more advanced strategies
- [ ] Template editor
- [ ] Events (with mutex protections)
- [ ] Non-binary strategies
-->
