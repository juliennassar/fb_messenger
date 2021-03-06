# Facebook Messenger API
**by Julien Nassar**  
**last update 19/08/2017**  
**MIT license**

## Quick description
This package is an api to help you connect a Flask server to Facebook Messenger using minimum amount of code (two lines actually)

This package currently supports all facebook messenger's basic event :
- messages : text, attachments (locations, images) and echoes
- read events
- postback events
- delivery events
- optin events

see [Facebook Messenger Webhook documentation](https://developers.facebook.com/docs/messenger-platform/webhook) for details

## How to use

### install 

as simple as :
 `pip install git+git://github.com/juliennassar/fb_messenger.git#egg=fb_messenger`

### Plug and play
To create your python-flask backend to Facebook, you must have a Facebook Page and an app with a Webhook subscription.

[Facebook documentation to create messenger app](https://developers.facebook.com/docs/messenger-platform/guides/setup)

With your Facebook **PAGE_ACCESS_TOKEN**, **VERIFY_ACCESS_TOKEN**, and **uri** of your choice, you can now plug messenger :
```
from flask import Flask
# first line here :
from facebook import FbMessenger

app = Flask('my_app')

# second line here :
FacebookMessengerApi(app= app,
                     uri='/your_uri_for_facebook',
                     verify_token=YOUR_VERIFY_TOKEN,
                     page_access_token=YOUR_PAGE_ACCESS_TOKEN)

if __name__ == '__main__':
  app.run()
```
**Boom**, Now your flask backend hanldes facebook's webhook callbacks

### Custom handling for facebook events/messages

Now you certainly want to handle incoming messages. Here is how it goes :

#### Objects :

Facebook callbacks generate Events
- Event class description :  
  - attributes :  
    `event_type` can be `message/postback/delivery/read/optin`
    `recipient_id, sender_id`: the recipient and sender id of the message
    `timestamp`: string timestamp (soon to be Datetime)
    `raw` : Raw data from Facebook (messaging)
    `content` is the content of the event depending of it's type

  - methods :
    Event has one method :
    *reply()* that takes one argument `msg` : the message to reply to the event sender's id. The data sent will be :
    ```
    {
      recipient: {
        id: sender_id,
      },
      message: msg,
    }
    ```
    usage example :
    ```
    event_instance.reply({'text': 'hello world !'})
    ```

#### Set up custom event handling :

To set up your custom functions to handle events, use the **callback_manager**, a class attribute which is an instance of CallbackManager.
The class has one public method `set_callback` to set custom callback methods.

**only one rule** : your custome callback takes exactly one argument : the event that will be parsed from facebook's webhook callback
example :
```
def my_custom_function_to_handle(e: Event):
  if e.event_type == 'message':
    e.reply({text: "Hello World !"})
```

then just set it up as your callback function for, i.e. Events of type `message`
```
from event import Event

Event.callback_manager.set_callback(my_custom_function_to_handle, 'message')
```

Now, for every message event, your custom callback will be called with the event as its argument !

-----------------------
That's all folks !  
**Happy coding !**  
Julien.
