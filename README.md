# HTTP Requests

## Learning Goals

- Make HTTP requests in Angular.

## Introduction

Since we are quite limited in the amount of time we have to put together our
sample application, we are naturally not going to be able to build a complete
application. That said, we do want to make our application able to communicate
with an external API, so that all our data doesn't have to be hardcoded locally.

In a real-life scenario, we would connect to an API that would then itself
connect to a database to get/manage persistent data. In our limited scenario
here, we are going to enable our application to get information from an API that
we're going to build with the Java Spring knowledge we acquired earlier in this
course. This API will _not_ persist data or connect to an external data source.

> Note: if you're not familiar or comfortable with Java Spring, you can stand up
> the API we are using in this section with any technology stack. For example,
> you could use `json-server` (https://www.npmjs.com/package/json-server) as an
> alternative.

## Java Spring API

Let's create a new controller class in a basic Java Spring project (start a new
project with Sprint Initializr if you need to start from scratch):

```java
@RestController
public class MessagingController {
    @CrossOrigin(origins = "http://localhost:4200")
    @GetMapping("/api/get-user-messages")
    public List<Message> getUserMessages() {
        return null;
    }

    @CrossOrigin(origins = "http://localhost:4200")
    @GetMapping("/api/get-sender-messages")
    public List<Message> getSenderMessages() {
        return null;
    }
}
```

Before we add actual functionality to our endpoints, let's make a couple of
notes:

1. You can make the URLs whatever you'd like, but make sure you match them up to
   the Angular-side code below
2. Since our Angular application runs on port `4200`, we have to explicitly
   allow those requests to come through to our backend application server, which
   will by default run on port `8080`. That's what the `@CrossOrigin` annotation
   does

In order to give our endpoints actual functionality, we need to create the same
models on the Java side that we have in our Angular app. We create those classes
locally in the same source file as our controller class:

```java
class Message {
    public User sender;
    public String text;
    public int conversationId;
    public int sequenceNumber;

    public Message(User sender, String text, int conversationId, int sequenceNumber){
        this.sender = sender;
        this.text = text;
        this.conversationId = conversationId;
        this.sequenceNumber = sequenceNumber;
    }

}

class User {
    public String firstName;
    public boolean isOnline = false;

    public User(String firstName) {
        this.firstName = firstName;
    }

    public User(String firstName, boolean isOnline) {
        this.firstName = firstName;
        this.isOnline = isOnline;
    }
}
```

Now we can add specific values for our endpoints to return. Again, as stated
above, these values are hardcoded here while they would be fetched from a data
store in a real-life application. Nonetheless, this allows our Angular
application to know how to interact with an external API, and whether that API
connects to another data source or not doesn't affect the basic mechanism we're
establishing in the Angular application itself.

Let's add simple code to our endpoints to return actual data:

```java
@RestController
public class MessagingController {
    @CrossOrigin(origins = "http://localhost:4200")
    @GetMapping("/api/get-user-messages")
    public List<Message> getUserMessages() {
        List<Message> messages = new ArrayList<Message>();
        messages.add(
                new Message(
                        new User("Aurelie"),
                        "Message from Lilly",
                        1, 2
                )
        );
        return messages;
    }

    @CrossOrigin(origins = "http://localhost:4200")
    @GetMapping("/api/get-sender-messages")
    public List<Message> getSenderMessages() {
        List<Message> messages = new ArrayList<Message>();
        messages.add(
                new Message(
                        new User("Ludovic", true),
                        "Message from Ludo",
                        1, 0
                )
        );
        messages.add(
                new Message(
                        new User("Jessica", false),
                        "Message from Jess",
                        1, 1
                )
        );
        return messages;
    }
}
```

Notice that we're making our message text here slightly different from what they
are on the Angular side - that will make easier to make sure we're getting
messages from the right source when we make updates on the Angular side.

Our final controller class, with the local domain classes as well, looks like
this:

```java
package com.flatiron.spring.FlatironSpring;

import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;
import java.util.List;

@RestController
public class MessagingController {
    @CrossOrigin(origins = "http://localhost:4200")
    @GetMapping("/api/get-user-messages")
    public List<Message> getUserMessages() {
        List<Message> messages = new ArrayList<Message>();
        messages.add(
                new Message(
                        new User("Aurelie"),
                        "Message from Lilly",
                        1, 2
                )
        );
        return messages;
    }

    @CrossOrigin(origins = "http://localhost:4200")
    @GetMapping("/api/get-sender-messages")
    public List<Message> getSenderMessages() {
        List<Message> messages = new ArrayList<Message>();
        messages.add(
                new Message(
                        new User("Ludovic", true),
                        "Message from Ludo",
                        1, 0
                )
        );
        messages.add(
                new Message(
                        new User("Jessica", false),
                        "Message from Jess",
                        1, 1
                )
        );
        return messages;
    }
}

class Message {
    public User sender;
    public String text;
    public int conversationId;
    public int sequenceNumber;

    public Message(User sender, String text, int conversationId, int sequenceNumber){
        this.sender = sender;
        this.text = text;
        this.conversationId = conversationId;
        this.sequenceNumber = sequenceNumber;
    }

}

class User {
    public String firstName;
    public boolean isOnline = false;

    public User(String firstName) {
        this.firstName = firstName;
    }

    public User(String firstName, boolean isOnline) {
        this.firstName = firstName;
        this.isOnline = isOnline;
    }
}
```

## HTTP Request

Angular has native support for making `HTTP` requests to `REST` endpoints
through its `HttpClient` module. In order to be able to use that module, we have
to import it in our `app.module.ts` file:

- Import the class to let TypeScript know about it:

```typescript
import { HttpClientModule } from "@angular/common/http";
```

- Add the module to the array of imports to let Angular know about it:

```typescript
  imports: [
    BrowserModule,
    FormsModule,
    HttpClientModule
  ],
```

Our updated `app.module.ts` file looks like this:

```typescript
import { NgModule } from "@angular/core";
import { BrowserModule } from "@angular/platform-browser";
import { FormsModule } from "@angular/forms";

import { AppComponent } from "./app.component";
import { HeaderComponentComponent } from "./header-component/header-component.component";
import { ApplicationComponentComponent } from "./application-component/application-component.component";
import { ConversationControlComponentComponent } from "./application-component/conversation-control-component/conversation-control-component.component";
import { ContactListComponentComponent } from "./application-component/contact-list-component/contact-list-component.component";
import { ConversationHistoryComponentComponent } from "./application-component/conversation-history-component/conversation-history-component.component";
import { ConversationThreadComponentComponent } from "./application-component/conversation-history-component/conversation-thread-component/conversation-thread-component.component";
import { SendMessageComponentComponent } from "./application-component/conversation-history-component/send-message-component/send-message-component.component";
import { ContactComponentComponent } from "./application-component/contact-list-component/contact-component/contact-component.component";
import { SenderMessageComponentComponent } from "./application-component/conversation-history-component/conversation-thread-component/sender-message-component/sender-message-component.component";
import { UserMessageComponentComponent } from "./application-component/conversation-history-component/conversation-thread-component/user-message-component/user-message-component.component";
import { LoggingService } from "./logging.service";
import { MessagingDataService } from "./messaging-data.service";
import { HttpClientModule } from "@angular/common/http";

@NgModule({
  declarations: [
    AppComponent,
    HeaderComponentComponent,
    ApplicationComponentComponent,
    ConversationControlComponentComponent,
    ContactListComponentComponent,
    ConversationHistoryComponentComponent,
    ConversationThreadComponentComponent,
    SendMessageComponentComponent,
    ContactComponentComponent,
    SenderMessageComponentComponent,
    UserMessageComponentComponent,
  ],
  imports: [BrowserModule, FormsModule, HttpClientModule],
  providers: [LoggingService, MessagingDataService],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

Now we can inject the `HttpClient` module in any service or component where we
need it.

Our Message Data Service in `messaging-data.service.ts` is where we manage our
application's data, so that's where we're going to implement our interface with
our new endpoint.

- We start by importing the `HttpClient` class so TypeScript knows about it in
   this service:

```typescript
import { HttpClient } from "@angular/common/http";
```

- Then we ask Angular to inject an instance of the module in our service by
   adding it to our constructor:

```typescript
    constructor(
        private loggingSvce: LoggingService,
        private httpClient: HttpClient
        ) {
        loggingSvce.log("Messaging Data Service constructor completed");
    }
```

- Now we're ready to change the implementation of `getSenderMessages()` and
   `getUserMessages()` to fetch their data from an API instead of having it
   hardcoded locally

The `HttpClient` module has methods that correspond to all the `HTTP` verbs, so
we have methods to handle `POST`, `GET`, ... requests. We're going to use the
`GET` functionality, since our data endpoints are just to retrieve existing
information.

Let's start with `userMessages`:

```typescript
    getUserMessages() {
        this.httpClient.get<Message[]>("http://localhost:8080/api/get-user-messages").subscribe(
            (messages: Message[]) => {
                console.log(messages);
                this.userMessages = messages;
                this.userMessagesChanged.emit(this.userMessages);
            }
        )
        return this.userMessages.slice();
    }
```

Let's break down this code:

1. The `get()` method on the `httpClient` accepts generics, so we can specify
   the type of what we expect to get back from our API endpoint.
   > We don't _have_ to specify the expected type, but it helps with type
   > validation and auto-complete functionality if we do
2. The `get()` method returns an "Observable", which we will cover in another
   section. For now, let's just say that an Observable must be subscribed to in
   order for us to be able to get the results of the call when it completes
3. The `subscribe()` method takes 2 parameters:
   1. The first parameter will be the return from the API call when it returns
   2. The second parameter is the callback function that will be executed when
      the API call returns
4. In the callback function, we do the following:
   1. Log the response just for debugging purposes
   2. Update the local copy of `userMessages` with the response from the API
   3. Emit an event to let our subscribers, in this case the conversation thread
      component, know that an update has occurred, and it should refresh itself

Since we're getting user messages from an API endpoint now, we no longer need
the hardcoded value, so we initialize the array to empty:

```typescript
    private userMessages: Message[] = [];
```

When you run your application now, you should see the user message you set in
your API endpoint instead of the value you had hardcoded before.
