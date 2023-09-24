## My 2023 Home Assistant Setup
This is my Home Assistant build in 2023. This setup utilizes multiple Add-Ons and Resources.

This setup utilizes Hyper-V

This honestly is documentation for me to refer to when building or rebuilding, but feel free to follow along.



## Table of Content <!-- omit in toc -->
- [Install Hyper-V](#install-hyper-v)
- [Install Home Assistant for Hyper-V](#install-home-assistant-for-hyper-v)
- [Add-Ons](#addons)
- [SSH & Web Terminal](#ssh--web-terminal)
- [HACS](#hacs)
- [Swiss Army Knife](#swiss-army-knife)
- [FTP](#ftp)
- [Samba Share](#samba-share)
- [Portainer](#portainer)
- [Homebridge Instance with Plugins](#homebridge-instance-with-plugins)


## Addons

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

### Swiss Army Knife

### FTP


### Samba Share


### Portainer


### Homebridge Instance with Plugins
