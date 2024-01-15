
## About The Project

Simurgh is your intelligent chatbot companion designed to assist Ferdowsi University of Mashhad students in finding answers to their educational and administrative queries. With Simurgh's help, navigating the university's academic landscape becomes effortless. Whether you're seeking course information, registration guidance, or campus resources, Simurgh is your trusted ally on the path to academic success.

## Getting Started

### Prerequisites

#### Rocket.Chat Prerequisites


* npm
  ```sh
  npm install npm@latest -g
  ```

1. You must have node version 14

    - Check the Node version and make sure it's 14
      ```sh
      node -v
      ```

    - If your node version was not 14
      ```sh
      sudo n 14.21.3
      ```

2. you must have meteor

    - install meteor
      ```sh
      npm install -g meteor
      ```

    - If having problem with installing
      ```sh
      curl https://install.meteor.com/\?release\=2.13.3 | sh
      sudo npm install -g meteor --unsafe-perm
      ```

3. install yarn

    - Install yarn for arch linux
      ```sh
      sudo npm install --global yarn 
      sudo pacman -S yarn
      ```

4. Clone the repo
    - Clone this repo: `git clone https://github.com/JavidChaji/FUM-Simurgh.git`
  
5. Run `yarn` to install dependencies



6. **Starting Rocket.Chat:**

    ```sh
    yarn dev # run all packages
    ```
    OR
    ```sh
    yarn dsv # run only meteor (front and back) with pre-built packages
    ```

---
#### Rasa Prerequisites

PYTHON3 SUPPORTED VERSIONS
 
> Currently, rasa supports the following Python versions: 3.7, 3.8, 3.9 and 3.10. Note that Python 3.10 is only supported for versions 3.4.x and upwards. Additionally, rasa installation on Apple Silicon with Python 3.10 is not functional in 3.4.x but will be supported starting from 3.5.x.

these steps are for linux

1. So We Install Python Version 3.10 in the virtual environment

    ```sh
    python3.10 -m venv .venv
    ```
    
    - we can move to the virtual environment by:

        ```sh
        source .venv/bin/activate
        ```

    - after moving to virtual environment we check if our python version are correct

        ```sh
        python -V
        ```

    - and also make sure pip is installed

        ```sh
        pip -V
        ```

    - Install Rasa and Upgrade pip (it's may take a while)

        ```sh
        python -m pip install --upgrade pip rasa
        ```

    - so to see switches of rasa we can use

        ```sh
        python -m rasa --help
        ```
    - to initiate ower rasa project we use this command (rasa will be asking you some questions and then you should be fine)

        ```sh
        python -m rasa --init
        ```


## Rasa and Rocket.Chat Connection
  
### Rocket.Chat Bot User Configurations

Create a Rasa bot user in Rocket.Chat. You can either manually login to Rocket.Chat and create a bot user via the 
user management page or can use the following script to create the bot user.

Run the following command to create the RASA bot.

**Note:** Please replace the user name and password of the RocketChat admin and bot user accordingly. 
```sh
python3 scripts/bot_config.py -an admin_username -ap admin_password -bn bot_username -bp bot_pass -r http://rocketchaturl
```

If you are using docker-compose following is a sample usage

```sh
python3 scripts/bot_config.py -an admin -ap admin -bn bot_rasa -bp bot_rasa -r http://localhost:3000
```

### Configure Rasa Bot

* Configure the Credentials file

    Update your `credentials.yml` file inside the `bot_rasa` folder with Rasa bot's username and password.
    ```sh
    rocketchat:
      user: "bot_rasa"
      password: "bot_rasa"
      server_url: "http://localhost:3000"
    ```

* Train the Machine Learning Model
    The Rasa bots machine learning model can built by using either Rasa CLI or Docker. After the training a machine 
    learning model will be created inside the `bot_rasa/models` folder.
    
    * **If using Docker**
        
        ```sh
        docker run -it -v $(pwd)/bot_rasa:/app rasa/rasa train
        ```
    
    * **If using Rasa CLI**
    
        ```bash
        pip3 install rasa
        cd bot_rasa
        rasa train
        ``` 
    
### Start Rasa server

Rasa bot can be started via the Docker or Rasa CLI. 

* **If using Docker-compose**

    ```sh
    docker-compose up -d bot_rasa
    ```

* **If using Rasa CLI**

    ```python
    cd bot_rasa
    rasa run --enable-api --debug
    ```

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