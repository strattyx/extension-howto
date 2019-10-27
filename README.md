# extension-howto
For now this is more of a timeline of when things will be ready

## Going forward
We move pretty fast and work on a lot of different tasks, but here's what our timeline for extensions/templates/packages is looking like.
#### 2019.11.1:
- Build out developer portal
- Add template system to app
- **Begin working with developers on making extensions**

#### 2019.11.15
- Extension/Template/Package Marketplace working
- **Begin working with developers on populating markeplace**
- Preliminary Payment system
- bulk timeline operation support (improve backtest performance)

#### 2019.11.25
- Fully automated system for deployment of extensions
  - Manual Review still required for templates/packages
- Fully automated payment system
- Polish developer portal

<!--
#### 2019.12.1
- Desktop MVP operational
  - Makes extensions more intuitive and allows users to make more advanced strategies
- Template editor
- Events (with mutex protections)
- Non-binary strategies
-->


## What's an Extension?
StrattyX has an internal scripting language that we use to represent user strategies. Via an extension operator we can call externally hosted functions. These functions can do pretty much anything and because the user's strategy is a program they can completely change the functionality. 

## How do Extensions Work?
Currently, strategies have two basic operations
- `realtime`: Running on a chron
- `timeline`: Generating backtests using historical data
The extension operator uses a webhook call to the url that you set in the developer portal with a request that corresponds with the mode of operation, as well as any relevant data and arguments.

### Endpoints
In order for your extension to fully integrate with our system you must have the following endpoints configured
#### **POST** `/invoke/realtime`:
  - request body will include a field called `arguments` conataining a dictionary with user provided arguments
    - ie: `{ arguments: { Timezone: 'UTC' } }`
  - We expect the webhook to return json containing a return value
    - ie: `{ return: "Sunday" }`
#### **POST** `/invoke/timeline`:
  - This extension isn't always required, however it's important for most users and will be displayed in marketplace
  - request body will include a field for the following
    - `arguments`: arguments for operator
    - `period`: start and end of backtest period
    - `ts`: [timestamp](#) for particular datapoint (note: "start", "*", "default" have special meaning)
    ```js
    body:  {
      arguments: { Timezone: 'UTC' },
      period: [ '2019-10-20T15:50:46.486Z', '2019-10-27T15:50:46.486Z' ]
    }
    ```
  - We expect the webhook to return json containing a value timeline, for example:
    ```js
    {
      timeline: {
        start: { value: 'Sunday' },
        '2019-10-21T00:00:00Z': { value: 'Monday' },
        '2019-10-22T00:00:00Z': { value: 'Tuesday' },
        '2019-10-23T00:00:00Z': { value: 'Wednesday' },
        '2019-10-24T00:00:00Z': { value: 'Thursday' },
        '2019-10-25T00:00:00Z': { value: 'Friday' },
        '2019-10-26T00:00:00Z': { value: 'Saturday' },
        '2019-10-27T00:00:00Z': { value: 'Sunday' }
      },
      warn: [],
      resolutions: []
    }     
    ```
  - 
