## My 2023 Home Assistant Setup
This is my Home Assistant build in 2023. This setup utilizes multiple Add-Ons and Resources.

This setup utilizes Hyper-V

This honestly is documentation for me to refer to when building or rebuilding, but feel free to follow along.



## Table of Content <!-- omit in toc -->
- [Install Hyper-V](#install-hyper-v)
- [Install Home Assistant for Hyper-V](#install-home-assistant-for-hyper-v)
- [SSH & Web Terminal](#ssh--web-terminal)
- [HACS](#hacs)
- [Samba Share](#samba-share)
- [Swiss Army Knife](#swiss-army-knife)
- [Portainer](#portainer)
- [Homebridge Instance with Plugins](#homebridge-instance-with-plugins)
- [Add-Ons](#addons)
- [Configuration.yaml](#configuration-yaml)
- [Adding Emby](#adding-emby)




### Additional Information
I utilize Blue Iris for my NVR and send a 30 second clip to Home Assistant for review in a card after it convertes to mp4. 
I will review how I handle disk space with automations in Home Assistant to maintain the disk space on the Home Assistant VM.
I utilize Emby for my home media server, and will review how to show what is playing on what device as well as the latest downloaded movies.

### Install Hyper-V
On the Windows desktop, select the Start button and type any part of the name Windows PowerShell.<br>

Right-click Windows PowerShell and select Run as Administrator.<br>

```yaml
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
```

### Install Home Assistant for Hyper-V
- Download Home Assistant for Hyper-V - [Home Assistant Install Options - Hyper-V](https://www.home-assistant.io/installation/windows/ "Home Assistant Install Options - Hyper-V")
- Extract the Zip downloaded
- Open Hyper-V
- Action > New > Virtual Machine
- Select Generation 2 during install
- I usually give it 4096 MB (4 GB of Memory)
- Dont use Dynamic Memory
- Connect it to your NIC
- Use an existing disk
- Select the .vhdx you extracted earlier from the downloaded Home Assistant appliance (the HA download)
- Do not start the VM yet
- Right-Click the VM and select Settings
- Select Security > Un-check 'Enable Secure Boot' then click apply
- Select Hard Drive > Edit > Expand > Next > Size: 200 GB plus> Next > Finish
- I do this because we are going to send Blue Iris .mp4 clips to a folder structure and need more storage
- Once the drive is completed select Apply/OK
- Power on your new HA VM (right-click start)
- Note: Look into Checkpoints they can help you restore along the way. I suggest maybe making one daily as you edit.
- If you got an error stating it could not find a boot device, ensure you disabled secure boot on the VM.
- You can now see your IP if you right-click your VM and select connect.
- In Example: 192.168.1.69:8123
- After a few minutes home assistant will prompt you for some info, fill out the form.



### SSH & Web Terminal
 - Log in to Home Assistant
 - Select your user icon in the bottom left corner
 - Enable Advanced Mode for your user
 - Select Settings from the left hand side menu
 - Select Add-Ons
 - Select the Add-On Store button in the lower right-hand corner
 - Search SSH
 - Install Terminal & SSH or Advanced SSH & Web Terminal (I prefer the Advanced Add-On)
 - Select Start on boot, Auto update, and Show in sidebar. Click Start
 - Watch the log to ensure it starts properly, and address any errors
 - Sample Config for the Advanced Add-On:

```yaml
Username: root (Note: Has to be root)
Password: Password123
authorized_keys: []
sftp: true
compatibility_mode: false
allow_agent_forwarding: false
allow_remote_port_forwarding: false
allow_tcp_forwarding: false
```
 - Ensure you click Save after changing your config (the first save not the bottom one)
 - The add-on will not start without a password configured
 - Root is required for using external apps
 - I use MobaXTerm Personal free portable edition for SSH
   
### HACS
Read more on HACS here: https://hacs.xyz/

We are going to install HACS via terminal with a easy install command.

 - Select the new SSH icon in the left side menu
 - Type the following into the terminal
   ```yaml
   wget -O - https://get.hacs.xyz | bash -
   ```
 - Now you have HACS installed
 - Select  Setting from the left side menu > System > Select the Power button in the top right corner > Restart Home Assistant
 - Now go to Settings > Devices & Services > Add Intergration > Search HACS > Select all the acknowledgments > Submit > Follow the directions to enter the github key.
 - I add HACS to a new area titled 'Backend'
 - HACS now appears in the left hand menu


### Samba Share

I use a SMB sometimes to do a quick edit, or move stuff around. I map the drive to my computer for quick browsing, or backups. Lets install a Samba add-on.

- Settings > Addons > Add-On Store > Search Samba or find it on the list fromt he repositories.
- Click Install
- Auto Update
- Select Configuration from up-top
- Set a Username/Password
- Set a allowed subet
- In Example if my home router DHCP is broadcasting 192.168.1.0 255.255.255.0 I would want to allow 192.168.1.0/24
- Read more about CIDR here: https://docs.netgate.com/pfsense/en/latest/network/cidr.html
- More about RFC1918 Private Subnets here: https://en.wikipedia.org/wiki/Private_network
- More about mapping shares to your computer here: [https://superuser.com/questions/570928/mapping-a-smb-share-in-windows](https://www.techrepublic.com/article/how-to-connect-to-linux-samba-shares-from-windows-10/)
- In Example Map: \\192.168.1.69\config
  

### Portainer

I am installing Portainer. I utilize Portainer for HomeBridge. HomeBridge is a virtual Apple HomeKit that allows me to add a few things easier. Sometimes if products are not supported in Home Assistant, or the process is way too much, then ill just see if I can add it to HomeBridge. In this document I will add my Google Nest Thermostats, SwitchBot Tilt Blinds, and my Home Alarm using HomeBridge.

- In Home Assistant select Settings > Add-ons > Add-On Store
- Click the three dots in the top right-hand corner, we need to add a repository
- Add: https://github.com/alexbelgium/hassio-addons, then close
- Refresh the Add-On Store page now
- Uptop in the search field search for Portainer
- Select Portainer
- Select Install
- Once its installed enable Auto Update, and Show in Sidebar
- Disable Protection Mode
- Select the Configuration
- Change the password and save
- Go back to Info and click Start
- Select Open in UI
- Default User is admin (lowercase)
- Password is what you set earlier in the Portainer configuration
- Its going to require you to change your password, your choice, you can select remind me later.
- Select Live Connect
- Select Stacks
- Select Add stack
- Name your stack uptop ie: homebridge (this must be lowercase aplhanumeric with underscores or dashes only)
- Enter the following into the Web Editor

```yaml
version: '2'
services:
  homebridge:
    image: oznu/homebridge:latest
    restart: always
    network_mode: host
    environment:
      - PGID=0
      - PUID=0
      - HOMEBRIDGE_CONFIG_UI=1
      - HOMEBRIDGE_CONFIG_UI_PORT=8581
    volumes:
      - /mnt/data/supervisor/homeassistant/homebridge:/homebridge
``` 

- Click Deploy Stack
- Let it cook
- Once you see it was successfully deployed you can now visit your homebridge stack
- IP Address is your_ha_ip:8581
- In Example: 192.168.1.69:8581
- You will be welcomed by your HomeBridge select Get Started
- Set a username and password

The first thing I do is update the GUI. 

- Plugins > Homebridge UI > Update
- It will reboot

Lets install a plugin. Im going to install my thermostats. The current Nest procedure is way too much. I find this simpler.

### HomeBridge Instance with Plugins

I have two Google Nest Thermostats. I am going to install the plugin for those in HomeBridge, and then add them to Home Assistant fromt he HomeBridge.

- Click Plugins
- Search for Nest
- Install the Verified Homebridge nest plugin

In order to add the thermostats to HomeBridge you have to get a couple pieces of information from your Google account. You will want to do this inside a Chromium based browser (Chrome, Edge)

-	Open a Chromium based browser tab in Incognito/Private Mode (or clear your cache).
-	Open Developer Tools (View/Developer/Developer Tools) or press F12.
-	Click on 'Network' tab. Make sure 'Preserve Log' is checked.
-	In the 'Filter' box, enter issueToken
-	Go to home.nest.com, and click 'Sign in with Google'. Log into your account.
-	One network call (beginning with iframerpc) will appear in the Dev Tools window. Click on it.
-	In the Headers tab, under General, copy the entire Request URL (beginning with https://accounts.google.com). This is your "issueToken" in config.json.
-	In the 'Filter' box, enter oauth2/iframe
-	Several network calls will appear in the Dev Tools window. Click on the last iframe call.
-	In the Headers tab, under Request Headers, copy the entire cookie (include the whole string which is several lines long and has many field/value pairs - do not include the cookie: name). This is your "cookies" in config.json.
-	Do not log out of home.nest.com, as this will invalidate your credentials. Just close the browser tab.
-	Restart HomeBridge
-	Do you see the Nest products list in the console of HomeBridge? Cool. If not retry.

Now we need to add the HomeBridge to Home Assistant.

- Go to Home Assistant
- Go to Settings
- Go to Devices and Services
- Home Assistant should have discovered the HomeBridge
- Select Configure on the Homebridge Card
- Use the Pairing Code (###-##-###) from the Homebridge Console to pair it to Home Assistant
- Now your thermostats are in Home Assistant
- This is a reliable solution.
- I have never had it fail and have been running it for years without changes other than updating plugins, or adding devices.
- I have never got Homebridge to sync with my Home App on my iPhone.

## Addons

Here is a list of addons that I use for various things throughout my build. I think I have a few in here that I dont actually use, im not sure so I added them all. I also havent finished my dashboard. I tried to add something from a repository that was unmaintained and broke my Home Assistant and thats what prompted me to document. I ended up restoring the core and recovering my instance, but I didnt think I was going to. When I completed my dashboard I planned on documenting and sharing, but im here early. As I add to my build I will update this document with my latest additions. One thing I will be adding to this dashboard is a sidebar. I am always open to critism, so if there is a better way to do something please let me know.

:white_check_mark: **Add-Ons List**.
| Name  | URL LINK |
| ---- | ----------- |
| apexcharts-card | https://github.com/RomRider/apexcharts-card |
| bar-card | https://github.com/custom-cards/bar-card |
| button-card | https://github.com/custom-cards/button-card |
| custom-icons | https://github.com/Mariusthvdb/custom-icons |
| kiosk-mode | https://github.com/maykar/kiosk-mode |
| lovelace-layout-card | https://github.com/thomasloven/lovelace-layout-card |
| lovelace-more-info-card | https://github.com/thomasloven/lovelace-more-info-card |
| lovelace-mushroom | https://github.com/piitaya/lovelace-mushroom |
| lovelace-slider-entity-row | https://github.com/thomasloven/lovelace-slider-entity-row |
| swipe-card | https://github.com/bramkragten/swipe-card |
| tabbed-card | https://github.com/kinghat/tabbed-card |
| mini-media-player | https://github.com/kalkih/mini-media-player |
| upcoming-media-card | https://github.com/custom-cards/upcoming-media-card |
| refreshable-picture-card | https://github.com/dimagoltsman/refreshable-picture-card |
| mini-graph-card | https://github.com/kalkih/mini-graph-card |
| simple-thermostat | https://github.com/nervetattoo/simple-thermostat |
| sidebar-card | https://github.com/DBuit/sidebar-card |
| simple-weather-card | https://github.com/kalkih/simple-weather-card |
| logbook-card | https://github.com/royto/logbook-card |
| gallery-card | https://github.com/TarheelGrad1998/gallery-card |
| tabbed-card | https://github.com/kinghat/tabbed-card |
| browser_mod_icon | https://github.com/thomasloven/hass-browser_mod |
| fonts.css | local and added to resources |
| Swiss Army Knife | https://github.com/AmoebeLabs/swiss-army-knife-card |

The first thing im going to do is add all these addons. I usuallay add these via HACS. First im going to make a checkpoint on my Hyper-V instance in case I need to rollback.

- Open Hyper-V
- Select the VM
- Right-click and select checkpoint

Now if I need to rollback I have a easy path. I put my lovelace into yaml mode, then I place all of these add-ons into my config.yaml. 

## Configuration Yaml


You will want to add the resources one at a time, and reboot your Home Assistant after every install to ensure there is not errors. This will take the guess work out of which change you made caused an issue. All of these are done within HACS so it should be pretty straight forward. If I had to do anything extra I will notate it here.

- Open HACS
- Select Frontend
- Select Explore and Download Repositories
- Search the card name
- Click Download
- It will refresh your Home Assistant
- The card should appear in HACS


Then I add the resources directly to the configuration.

```yaml
# /=======================\
# | RESOURCES DECLARE     |
# \=======================/
# All the resources added
  resources:
    [
      { url: /hacsfiles/apexcharts-card/apexcharts-card.js,                         type: module },
      { url: /hacsfiles/bar-card/bar-card.js,                                       type: module },
      { url: /hacsfiles/button-card/button-card.js,                                 type: module },
      { url: /hacsfiles/custom-icons/custom-icons.js,                               type: module },
      { url: /hacsfiles/kiosk-mode/kiosk-mode.js,                                   type: module },
      { url: /hacsfiles/lovelace-layout-card/layout-card.js,                        type: module },
      { url: /hacsfiles/lovelace-more-info-card/more-info-card.js,                  type: module },
      { url: /hacsfiles/lovelace-mushroom/mushroom.js,                              type: module },
      { url: /hacsfiles/lovelace-slider-entity-row/slider-entity-row.js,            type: module },
      { url: /hacsfiles/swipe-card/swipe-card.js,                                   type: module },
      { url: /hacsfiles/tabbed-card/tabbed-card.js,                                 type: module },
      { url: /hacsfiles/mini-media-player/mini-media-player-bundle.js,              type: module },
      { url: /hacsfiles/upcoming-media-card/upcoming-media-card.js,                 type: module },
      { url: /hacsfiles/refreshable-picture-card/refreshable-picture-card.js,       type: module },
      { url: /hacsfiles/mini-graph-card/mini-graph-card-bundle.js,                  type: module },
      { url: /hacsfiles/simple-thermostat/simple-thermostat.js,                     type: module },
      { url: /hacsfiles/sidebar-card/sidebar-card.js,                               type: module },
      { url: /hacsfiles/simple-weather-card/simple-weather-card-bundle.js,          type: module },
      { url: /hacsfiles/logbook-card/logbook-card.js,                               type: module },
      { url: /hacsfiles/gallery-card/gallery-card.js,                               type: module },
      { url: /hacsfiles/tabbed-card/tabbed-card.js,                                 type: module },

      { url: '/local/browser_mod_icon.js?v=0.5',                                    type: module },
      { url: '/local/fonts.css?v=2.1',                                              type: css }
    ]
```

Lets put the configuration into yaml mode.

Lovelace in Yaml Mode:
```yaml
# /=======================\
# | YAML MODE             |
# \=======================/

lovelace:
  mode: yaml
```
Sidebar Card: Go to the hacs store and use the repo url https://github.com/DBuit/sidebar-card and add this as a custom repository under settings.
- HACS
- Intergrations
- Three dots menu in the top right
- Add custom repositories
- Repository: https://github.com/DBuit/sidebar-card
- Category: Lovelace
- Add
- Close the window
- Frontend
- Explore and Download
- Now add sidebar

BrowserMod:
 - HACS
 - Intergrations
 - Three dots menu in the top right
 - Search BrowserMod
 - This will require a reboot
 - Go to Settings > Intergrations > Add Intergrations
 - Search Browser Mod and Install
 - Browser Mod now appears in the lefthand menu.
 - You have to go into Browser Mod and Register the browser
 - Auto Register as well
 - Lock Register
 - Click Cast Register

I plan on using Browser Mod to pop-up the doorbell then someone ring it on all screens. If you dont plan on using this mod its not mandatory to install.

Lets go edit the Configuration.yaml, but before doing so lets install Visual Studio Code first for easier editing.

- Settings >Addons > Search
- Search for Studio Code Server
- Install
- Select Auto Update and Show in Sidebar
- Start
- Click Studio Code Server on side menu
- Trust the author(s)
- The Addon will not turn green until you trust the install

Now would be a good time to create another checkpoint on your VM. Go to Hyper-V right click the VM and create one.

### Adding Emby 

Lets add Emby to the install. In the above list there was a upcoming media card addon. We will use this to show what new movies Emby has added lately. I personally use Radarr, etc to auto download media so you never know what latest popular movie will add itself to the server.

- Open Emby and login as an administrator
- Go to Settings in Emby
- Under Advanced in Emby select API Keys
- Create a new API key
- Copy your API key
- Open Home Assistant
- Go to your configuration.yaml
- Add the following:
```yaml
media_player:
    - platform: emby
      host: 192.168.1.69
      ssl: False
      port: 8096
      api_key: <paste_your_api_key_here>
```

Now we need to add a sensor entry into configuration.yaml for the upcoming media card. You need to collect your used ID for this entry from Emby.

- Open Emby and go to settings
- Select profile from the left side menu
- Look at the URL in the browser window
- You will see the usedID= in the URL followed by & serverID=
- Copy just the used id value after the equals sign down to the '&'
- Add the following to your configuration.yaml:
```yaml
sensor:
- platform: emby_upcoming_media
  api_key: <enter_your_api_key>
  user_id: <enter_your_user_id>
  host: 192.168.1.69
  port: 8096
  ssl: False
  max: 10
  use_backdrop: true
  group_libraries: false
  include:
    - Movies
    - TV Shows
```

Notice the include entery. I have Movies and TV Shows in the list. Those are the media folders I have on my Emby server. You may have different names for your media folder. You will want to use your media names. Now we can use that sensor on a dashboard. Enter the following on one of your dashboards.
```yaml
type: custom:upcoming-media-card
entity: sensor.emby_latest_movies
title: Latest Movies
image_style: fanart
max: 6
```
These are the settings I prefer. You can read more about this addon in the link located in the addons table.










  




  


