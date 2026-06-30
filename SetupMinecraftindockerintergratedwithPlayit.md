                              Setup Minecraft in docker integrated with Playit

The goal of this article is to be a walkthrough to help setup Minecraft and Playit into two seperate docker containers. The purpose of this setup is to maintain security while enabling accessibility to the Minecraft server. This article assumes you already have a general Ubuntu server created with the firewall set to deny incoming and allow outgoing with no ports exposed to the internet.

The steps below have been confirmed on Ubuntu 26.04 using Docker Run commands to create a Minecraft Java server. Your mileage may vary depending on your setup.

Step 1: Install screen trust me, you’ll want this
   
    • Input the below command to install screen for Ubuntu command will vary depending on distro
        ◦ sudo apt install screen

Step 2: Install Docker
   
    • There are plenty of ways to install docker to a server. Please check some of the options below
        ◦ https://docs.docker.com/engine/install/
        ◦ https://docs.docker.com/desktop/setup/install/windows-install/
   
    • As this article is written after being confirmed on Ubuntu 26.04, the docker article for this would be https://docs.docker.com/engine/install/ubuntu/
   
    • Here’s an article with the different variables you can use for Docker Run https://docs.docker.com/reference/cli/docker/container/run/

Note: if you run into the error in the picture, apparmor may be blocking docker
      <img width="1549" height="80" alt="image" src="https://github.com/user-attachments/assets/8939f488-da04-4e79-b867-501ebcab61d7" />


Step 3: Create a non-disposable docker container with playit intergrated into it. Follow the steps in order to achieve success
    
    • In the cli for your server enter the below to create a screen
        ◦ screen -S nameofscreen
            ▪ Example: screen -S PlayitDockerExample
        ◦ Note: if you wish to disconnect from the screen without closing it, click “ctrl A” then click D. If you wish to close the screen, type in “exit” and click enter. To reconnect to a disconnected screen, type in “screen -r nameofscreen
    
    • Go to playit.gg and log in
    
    • Open the following link 
        ◦ https://playit.gg/account/setup/wizard/new-account/docker/docker-name
    
    • In the above setup wizard, type in the name of your new agent and click “Create Agent” at the bottom right
      <img width="1578" height="890" alt="image" src="https://github.com/user-attachments/assets/1a213e47-835e-4767-9858-11fe7230f049" />

      
      • On the following page, copy the url in the section labeled “Docker Run” DO NOT RUN THE COMMAND YET, CHECK NEXT STEP
      
      <img width="1578" height="1220" alt="image" src="https://github.com/user-attachments/assets/3be38c39-7e99-4d13-bd4b-d2616bcd7f4a" />

      
    • Paste the command into the cli for your server do not click enter yet, we need to edit the command if you do not want it to be disposable. Replace “--rm”  with “-d” without the quotes. 
       
        ◦ Optional: add “--name” if you want to give it a non randomly generated name
            
            ▪ Ex: docker run -d -it -- name PlayitExample -- net=host -e SECRET_KEY (everything else in the copied command)
            
            ▪ If you have already created the docker container and didn’t give it a name, review below
               
                • Type this command in to get the name of the docker container
                    ◦ docker container ls -a this will get the names of all the docker containers available
             
                • Type this command in to change the name of the docker container
                    ◦ docker rename nameofcurrentdockercontainer newwantednameofdockercontainer
 
    • Click Enter on your keyboard if you have not at this point
    
    • Go to playit.gg and go to agents and see that your new agent is there!


Step 4: Create Docker container for your minecraft server and connect it to the Playit docker container

    • The below command will create a non-disposable docker container that is connected to the same network as the playit container created in the previous steps. This is critical to do before the next step
   
    • docker run -d -it --cap-add NET_ADMIN --network container:NAMEOFPLAYITDOCKER --name NAMEOFTHISCONTIANERNOTTHEPLAYITCONTAINER build-container-image
        
        ◦ Example of above
           
            ▪ docker run -d -it --cap-add NET_ADMIN --network container:PlayitExampleDockerContainer --name MinecraftExampleContainer ubuntu:24.04

Step 5: Set up minecraft server in docker container
    
    • Detach from the screen containing the Playit Docker container
       
        ◦ ctrl A, d you do not need to hold ctrl while you click d
  
    • Create new screen for your Minecraft docker container
      
        ◦ screen -S nameofscreen

    • Enter the Minecraft docker container
        ◦ docker exec -it nameofdocker /bin/bash
    
    • Update the docker container, install needed applications. You decide what you need, the command installs some possibly needed tools
      
        ◦ apt update && apt upgrade
       
        ◦ apt install sudo
      
        ◦ sudo apt install nano && sudo apt install wget && sudo apt install curl && sudo apt install unzip && sudo apt install net-tools
            
            ▪ the above will install nano its a text editor, wget this will help get the mods and downloads you’ll want, curl this will help with getting your public ip address, unzip this will let you unzip files for the server easier, and net-tools this will help with getting the ip address you’ll need for your tunnel
   
    • Install java. Use command below command will change depending on distro
        
        ◦ sudo apt install default-jre
    
    • Create a new user without admin privelages in the container. Use below command
        
        ◦ sudo useradd -s /bin/bash -d /home/nameofuser/ -m  nameofuser
  
    • Create password for new user. Use command below
       
        ◦ sudo passwd nameofuser
      
        ◦ create new password
   
    • Switch to new user. Use command below
        
        ◦ su nameofuser
   
    • Find ip address of docker container. You’ll need this for step 6
      
        ◦ Type in “ifconfig” without quotes and click enter
       
        ◦ In the list that showed up, look for the one labeled docker, and the IP address you want is labeled “inet”. Save that ipaddress for step 6
    
    • Change directory to the new user home folder. The commands below will help
       
       ◦ ls this command lists all the folders and files for the current directory you are in
       
        ◦ cd this command will let you change the directory
       
        ◦ Example use of cd
           
            ▪ cd /home/MinecraftExample/
  
    • When you are in the home directory for the user, create a new folder for your Minecraft server. See command below
      
        ◦ mkdir nameofnewfolder
      
        ◦ Change directory to the new folder
   
    • Download the server.jar file from the following link 
     
        ◦ https://www.minecraft.net/en-us/download/server
      
        ◦ Note: You can download different minecraft server files from different locations, its up to you to find the ones you want especially if you want a modded server.
       
        ◦ Tip: to make it super easy to download the files, download them to another device through your browser. Open the download in the browser and right click what you’ve downloaded, then click “Copy download link”. Then in the server cli, type in the following command
          
            ▪ wget pastedownloadurlhere 
      <img width="581" height="150" alt="image" src="https://github.com/user-attachments/assets/9ead691b-d3d1-4197-8ab3-25570737a722" />

      
   
    • Follow this article
        
        ◦ https://playit.gg/support/how-to-setup-a-mc-server/

Step 6: Create a tunnel for your new agent
   
    • Go to playit.gg and log in if you somehow got logged out during this process
  
    • Click “Tunnels”
      
      <img width="1695" height="825" alt="image" src="https://github.com/user-attachments/assets/effd27ba-04e9-4fac-afbf-256c4af642ad" />

      
      
    • Click “New Tunnel”
      
      <img width="1695" height="825" alt="image" src="https://github.com/user-attachments/assets/c523221f-f651-4903-ba31-63da8d57ba20" />

      
    • Name your tunnel whatever you want
   
    • In “Tunnel Type” select “Minecraft Java” then click next
      
      <img width="1619" height="1643" alt="image" src="https://github.com/user-attachments/assets/b0056d63-4bed-4fca-9a52-ecdee56c41cd" />

      
    • In “Public Endpoint” select the option best for you
    
    • In “Assign to Agent” select the agent that was created then click Next
      Note: Ignore that the screenshot says tunnel type not supported, yours will not say that. I just didn’t want to create an entirely new agent for myself to get these screenshots

      <img width="1619" height="1643" alt="image" src="https://github.com/user-attachments/assets/c08d91f2-bf6b-43c2-95e9-51a80edefc72" />

      
    • In “Origin Config” section, input the IP address for docker into the Local IP field. Leave the port alone unless you want to use a different port, and then click next, then click “create tunnel”
  
    • Wait about 2-5 minutes and then try to connect to your minecraft server using the address of the tunnel. If it does not seem to work, restart the playit agent by following the steps below.
       
        ◦ Detatch from the screen with your minecraft server running in it  by doing “ctrl A, d”
       
        ◦ Attach to the screen with the Playit Docker container by using the following command without the quotes “screen -r nameofscreen”
         
            ▪ Note: you can find the name of the screens by using the command without quotes “screen -ls”
        
        ◦ Stop the playit agent by doing the following “ctrl c”
       
        ◦ Stop the docker container by doing the below
         
            ▪ Type in “exit” without the quotes to exit the docker container
        
            ▪ Type in “docker stop nameofdockercontainer” to stop the playit container
          
            ▪ Type in “docker start nameofdockercontainer” to start the playit container
      
        ◦ Wait 2-5 minutes and connect to your minecraft server!
       
        ◦ Note: You may need to restart minecraft server, usually you don’t but you might.
