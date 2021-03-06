# Core Eventing: Overview

The aiWARE Core Eventing system is, as its name suggests, Veritone’s answer for providing developers with a Pub-Sub solution that enables them to subscribe to existing events and/or create new events routed to a webhook that you define and handle in your own application.

## Why use aiWARE Eventing?

As with any Pub-Sub technology, the advantages of using aiWARE Eventing to is to be able to receive and react to an event occurring in aiWARE which is useful for getting alerts when an asynchronous process (like a Cognitive Engine task) has finished processing.

> If you are familiar with our engine processing GraphQL actions, aiWARE Eventing can be used as the primary mechanism for job status notifications to then trigger next steps in your application integrated with aiWARE.

!> As an important note, aiWARE Eventing is currently only available in the Core element of your Veritone organization’s environment. It is not an Edge element.

The aiWARE Eventing system enables you to configure flexible and powerful notifications to your applications through a few GraphQL API actions. (See below.)

However, if you’re looking for real-time (2-second-and-under response time) status notifications from cognitive engine jobs, please see our [aiWARE Edge APIs](/apis/edge/index.html).

As a quick note, a useful rule of thumb is that if your application does not need 2-second-and-under reporting, the aiWARE Core Eventing system will be effective for notifying your application of desired events occurring in your Veritone organization.

## GraphQL API Examples

You can use aiWARE GraphQL API to view and manipulate events and event subscriptions (actions) for aiWARE's eventing system running in Core.

### Events

The Event object represents the definition of a specific record that stores metadata like the schema of the event payload, when the event was created, and the type of aiWARE object (e.g. Task, Job, Temporal Data Object) that the event is categorized by.

#### Query Events

You can get a list of all events your organization has access to via a GraphQL query like this:

```graphql
query events{
  # The default application is "system"
  # You can create and associate your own events to your applications
  # You can use the offset to page through all the system events
  events(application:"system" offset:1){
    records{
      id
      public
      createdBy
      createdDateTime
      eventName
      eventType
      schemaData
    }
  }
}
```

You can also create new event definitions and associate them with your own application packages that you create and publish in the Veritone Developer App.

#### Create Events

```graphql
mutation createEvents{
  createEvent(input:{
    #You can create new custom App profiles in Developer App
    application:"<Expects Your Custom App's ID>"
    eventName:"<Name Your new Event!>"
    eventType:"<aiWARE Object your Event is assoc'd with>"
    #Do you want your event to be publicly viewable?
    public:true
    description:"<Help other developers understand what this event does>"
    schemaData:"<Optional, but helpful for other developers to quickly understand schema>"
  }){
    id
    eventName
    eventType
  }
}
```

You can learn more about creating and modifying your own Application packages [here](/developer/applications/app-tutorial/).

### Event Subscriptions

Event Subscriptions enable application developers to explicitly define which aiAWRE events they wish their application to subscribe to, and can even define some filter rules that determine which event payloads are finally sent to the subscriber.

#### Query Event Subscriptions

You can get a list of all of eventSubscriptions in your Veritone organization using this query:

```graphql
query evntSubs{
  #get eventSubs that trigger when Job events occur in your organization
  eventSubscriptions(eventType:"job"){
    records{
      id
      organizationId
      eventName
      eventType
      targetName
      consumerParams
      #Use conditions to filter specific events
      conditions
    }
  }
}
```

#### Create Event Subscriptions

This is an example of what the `subscribeEvent` GraphQL operation expects for a fully defined Event Subscription. We have included a sample `conditions` field value as well, although this is optional and may only be applicable for bespoke use-cases.

```graphql
mutation createEventSubs{
  subscribeEvent(input:{
    #You can create new custom App profiles in Developer App
    application:"<Expects Your Custom App's ID>"
    eventName:"<Name Your new Event!>"
    eventType:"<aiWARE Object your Event is assoc'd with>"
    #Object that expects the name of the delivery type
    # and also expects the delivery address
    delivery:{
      #In this case we're defining an event subscription
      # of type Webhook
      name:Webhook
      params:{
        uri:"https://yourAppAddress.com"
      }
    }
    #Conditions evaluate the event payload
    # to determine if it should be sent or not
    conditions: {
            operator: "and",
            conditions: [
              {
                field: "watchlistId",
                operator: "eq",
                value: 1
              }
            ]
          }
        })
      }
  })
}
```

#### Event Subscription Types

While we suspect most use-cases will simply require a “Webhook,” we have enumerated the full types of Event Subscription below:

- Webhook -- Webhook protocol should provide the following key-value pair in the json structure of "url":"<your_url>" "encoding":"[protobuf|protobuf_base64|json] "template": string
- SMS -- SMS protocol should provide the following kvp in the json structure of `"number": ##########`
- Email -- Email protocol should provide the following kvp in the json structure of `"address":"<your_address>@<your_domain>"`
- CreateJob -- This action creates a new engine job that runs with the specified parameters: `"targetId": "<ID of the TemporalDataObject trigger this request>" "engineId":"<ID of engine you want to run>"`
