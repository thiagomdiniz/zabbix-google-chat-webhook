# Google Chat webhook

This guide describes how to integrate Zabbix 6.4 with Google Chat using the Zabbix webhook feature. This guide will provide instructions on setting up a media type, a user and an action in Zabbix. 
This integration is supported only for **Chat** as part of Google Workspace. Note, that **Chat** free plan does not support [incoming webhook](https://developers.google.com/chat/how-tos/webhooks#step_1_register_the_incoming_webhook) feature.

## Setting up webhook in Google Chat

Because The icon for manage webhooks is only available from a web browser, webhooks must be set up from the Chat web app. Webhooks are not configurable from the Chat mobile app.

First, you need to get a webhook URL for the space:

- Create **Incoming webhook** for your space.
(See **Chat** [documentation](https://developers.google.com/chat/how-tos/webhooks#step_1_register_the_incoming_webhook) for the step-by-step instructions).


## Setting up webhook in Zabbix 
1\. In the Zabbix web interface go to Administration → Macros. Setup the global macro "{$ZABBIX.URL}" which will contain the URL to the Zabbix frontend. 
<br>The URL should be either an IP address, a fully qualified domain name or localhost. Specifying a protocol is mandatory, whereas port is optional.
Good examples:<br>
http://zabbix.com<br>
https://zabbix.lan/<br>
http://server.zabbix.lan/</br>
http://localhost<br>
http://127.0.0.1:8080<br>

Bad examples:<br>
zabbix.com<br>
http://zabbix/<br>

[<img src="images/1.png" width="300"/>](images/1.png)

2\. In the *Alerts > Media types* section, import the [media_google_chat.yaml](media_google_chat.yaml)

3\. Open the newly added **Google Chat** media type and replace placeholder *&lt;PLACE WEBHOOK URL HERE&gt;* with:
- The **incoming webhook URL**, created during the webhook setup in Google Chat.<br>
This way, the Google Chat webhook will support integration with a **single Space**.
- The macro **{ALERT.SENDTO}**.<br>
This way, the Google Chat webhook will support integration with **multiple Spaces** by simply creating a Zabbix user for each Space.

[<img src="images/2.png" width="300"/>](images/2.png)

4\. You can also choose between two notification formats. Set *"use_default_message"* parameter:
- **false** (default)
    - Use preformatted message with predefined set of fields for trigger-based notifications.<br>
    In internal, autoregistration, discovery and service notifications *{ALERT.MESSAGE}* as a body of the message will be used.
    In this case you can customize the message template for trigger-based notifications by adding additional fields and buttons with URLs.
        - To add an additional field to message card, put a parameter with prefix **label_** and field name. For example, *"label_Data center"* as key and *{EVENT.TAGS.dc}* as value.
        - To create a new button with a link to an external resource, add a parameter with prefix **openUri_** and button name. The value should be a valid URL. For example, *"openUri_Link to Zabbix.com"* as key and *https://www.zabbix.com/* as value.<br>

- **true**
    - Use {ALERT.MESSAGE} as a body of the message in all types of notifications.

[<img src="images/3.png" width="300"/>](images/3.png)

5\. You can also choose to group messages by threads when sending them to Google Chat. Set *"group_messages_by_thread"* parameter:
- **{EVENT.ID}** (default)
    - Messages will be grouped by threads based on the Zabbix event ID.<br>
    The first notification of a specific event will appear in Google Chat as a new thread and subsequent notifications for the same event will appear in Google Chat as replies to the initial message (grouped in the same thread).<br>
    You can also use other macros to group messages in a different way, such as **{TRIGGER.ID}** (send notifications of a specific trigger always in the same thread).

- **false**
    - Messages will not be grouped by threads. Each notification will appear in Google Chat as a new thread.

6\. To receive Zabbix notifications in Google Chat, you need to create a **Zabbix user** and add **Media** with the **Google Chat media type**.<br>
In the *Users → Users section*, click *Create user* button in the top right corner. In the *User* tab, fill in all required fields (marked with red asterisks). In the *Media* tab, add a new media and select **"Google Chat"** type from the drop-down list. The "*Send to*" field must be filled in according to the configuration made in *step 3*:
- In step 3, the **incoming webhook URL** was used (*support single space*):
    - Though a "*Send to*" field is not used in this case, it cannot be empty. To comply with the frontend requirements, you can put any symbol there.
- In step 3, the **{ALERT.SENDTO}** macro was used (*support multiple spaces*):
    - Enter the **incoming webhook URL** in the "*Send to*" field. If you need to send notifications to other spaces, simply follow this same procedure to create a new Zabbix user but use the **incoming webhook URL** of another space in the "*Send to*" field.

Make sure this user has access to all hosts for which you would like problem notifications to be sent to Google Chat.<br>

[<img src="images/4.png" width="300"/>](images/4.png)

7\. Now for Zabbix to send notifications, it is necessary to [configure an **Action**](https://www.zabbix.com/documentation/6.4/en/manual/config/notifications/action) that contains the operation of sending a message.

8\. Great! You can now start receiving alerts!

For more information see [Zabbix](https://www.zabbix.com/documentation/6.4/manual/config/notifications) and [Google Chat webhook](https://developers.google.com/chat/how-tos/webhooks) documentations.

## Supported Versions
Zabbix 6.4
