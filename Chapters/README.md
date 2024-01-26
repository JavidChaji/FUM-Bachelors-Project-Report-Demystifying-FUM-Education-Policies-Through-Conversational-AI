
#### Make Rasa Bot accessible by Rocket.Chat

The Rasa bot should be reachable via Rocket.Chat.

*  If you are following the tutorial with docker-compose file then following is the URL to access the Rasa bot.
    ```bash
    http://bot_rasa:5005
    ```
    

* If you are trying to connect to a standalone Rocket.Chat instance or using Rasa CLI, lets user ngrok to get a 
public url for the Rasa Bot. (or just use localhost:5005)

    Install ngrok via: https://ngrok.com/download

    After downloading the ngrok navigate to the ngrok file in the downloded content and execute the following command. 
    This will provide a public URL to the Rasa bot

    ```bash
    ./ngork http 5005
    ```
    
    Following will be the output of ngrok
    ```sh                                                                                                                                                                                                      
    Session Status                online                                                                                                                                                                        
    Session Expires               7 hours, 59 minutes                                                                                                                                                           
    Version                       2.3.30                                                                                                                                                                        
    Region                        United States (us)                                                                                                                                                            
    Web Interface                 http://127.0.0.1:4040                                                                                                                                                         
    Forwarding                    http://e3d5a17b.ngrok.io -> http://localhost:5005                                                                                                                             
    Forwarding                    https://e3d5a17b.ngrok.io -> http://localhost:5005  
    ```
    Copy the http URL provided by ngrok: `http://e3d5a17b.ngrok.io`

### Configure Rocket.Chat webhook

Go to **Administration > New Integration > Outgoing webhook**.
Inside the configuration insert this:

```
Event Trigger: Message Sent
Enabled: True
Channel: #general
URLs: http://bot_rasa:5005/webhooks/rocketchat/webhook
Post as: bot_rasa
```

If you are using ngrok then replace the URL `http://bot:5005`, with the url obtained by ngrok.

```bash
URLs: http://ngrok_public_url/webhooks/rocketchat/webhook
```

**Save** all the changes.

### Example

Type `@bot_rasa hello` to start a conversation with the Rasa bot



### Additional Information

If you want the Rasa bot to direct message with the users create another webhook with the following configurations

```
Event Trigger: Message Sent
Enabled: True
Channel: all_direct_messages
URLs: http://bot_rasa:5005/webhooks/rocketchat/webhook
Post as: bot_rasa
```