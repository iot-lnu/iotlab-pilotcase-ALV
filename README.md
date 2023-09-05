# Astrid Lindgrens Värld 
The purpose of the project is to connect the cabins in Astrid Lindgrens Värld by connecting sensors for temperature and humidity in the cabins. One of the objectives is to build a visual presentation of data from the cabins, so that the reception can observe if, for example, ventilation is required in a cabin or if the temperature is too high/low.

[toc]

# Architecture & Platform 

![](https://hackmd.io/_uploads/Sk4B9ZCTn.png)


# Sensors 

x20 [ERS2 Lite](https://www.elsys.se/shop/product/ers-lite-v2-0/?v=f003c44deab6)
Manual : [ERS2_Lite_manual](https://www.elsys.se/public/manuals/Operating%20Manual%20ERS.pdf)
![](https://hackmd.io/_uploads/Sk7A7YR6n.png)


x1 [People counter](https://imbuildings.com/products/people-counter-lorawan/)
Manual : [people_counter_manual](https://f.hubspotusercontent20.net/hubfs/4124978/LoRaWAN-People-Counter-Manual-v1.1-latest%20(1).pdf)
Advanced guide : [people_counter_manual_advanced](https://connectedthings.store/sk/index.php?controller=attachment&id_attachment=177)
![](https://hackmd.io/_uploads/rJaLEtRTh.png)


## Downlink
To modify the sensor parameters you can send downlinks. It may be useful to change the time interval between each message or the number of people between each message or many other things.
### How to make your own downlink
You can see all the settings in the ***advanced guide***, but here's a quick summary of what you can do:


**Set interval to 10 minutes**
> F1 01 03 1E ==0A==
> interval : 0A (correspond to 10 minutes)

**Change the distance**
> F1 01 04 50 ==1E 96==
> 
> Ignored distance : 0x1E (correspond to 30 cm)
> 
> Detection distance : 96 (correspond to 150 cm)

It is advisable to cascade several commands in a single downlink. In this case, you could add the save command, which would give the following downlink:

**Save command**
>F1 01 04 50 1E 96 ==03 C8 04==

To see all the different types of payloads you can check the ***advanced guide***.

### How to send downlinks
First, go to the Messages section.

![](https://hackmd.io/_uploads/SJYo-Pm02.png)

Then you click on the Send downlink button

![](https://hackmd.io/_uploads/BJ7-zw70h.png)

And now you can put in the payload (downlink) you want.

![](https://hackmd.io/_uploads/Hk7HfPm03.png)

### Battery
Setting the interval to every minute:

![](https://hackmd.io/_uploads/r1LfcrQR2.png)

Setting the interval to every hour: 

![](https://hackmd.io/_uploads/rJ6Q9rQRn.png)

You can see that by changing the interval from one minute to one hour, you can multiply the battery life by 10 for SF12 or by 3 for SF7.

# Portal
![](https://hackmd.io/_uploads/rJgNpSGAh.png)

It is possible to filter out sensors that are outside a set range on optional channels that the sensors send. The latest sensor value is then also displayed in the map view, as shown in the image below.

![](https://hackmd.io/_uploads/Bk4L6SM0n.png)

To do this, click on `filter channels` in the upper right corner of the map, and select the channel/value you want to filter based on. 
![](https://hackmd.io/_uploads/rytLDUzAh.png)



# API

It is also possible to use your login credentials (used to log in to Stadshubben) to log in to our API portal - perhaps you will need to complete the registration to be able to access the API portal:

Follow these steps to log in to the API portal: 
 
1. Navigate to: https://ke-apim.developer.azure-api.net/ .
2. Click on `sign in`:

![](https://hackmd.io/_uploads/S1lXHKCph.png)

3. Go to Azure Active Directory B2C, and use the credentials to log in. See the picture:

![](https://hackmd.io/_uploads/Hks4SY0T3.png)

At this stage it is clear. Explore the APIs and see all other endpoints.


To obtain the authentication token through the APIs, one needs to use the Customer Identity API:

 
POST: /v1/resource-owner/token 


When it comes to authentication via HTTPS Request, you must call the following endpoint https://api.kalmarenergi.se/customer-identity/v1/resource-owner/token with the following info in the Request:


- Username: "xxxx"
- Password: "yyyy"           
-	responseType: "token id_token"            
-	scope: "openid offline_access https://kalmarenergib2c.onmicrosoft.com/6b6a459a-2596-4213-b42b-153f1e0a9af7/Device.Read.Add"
-	grantType: "password"

See the image below for an example of how to fetch your token: 

![](https://hackmd.io/_uploads/Skn1bcE0h.png)

Header is: 

![](https://hackmd.io/_uploads/SJzhULM0n.png)

The response should be similar to the one shown in the image below. 

![](https://hackmd.io/_uploads/SyMwW9V02.png)

To refresh the token itself:
POST:  /v1/resource-owner/token 

The payload should be like this:
-	refreshToken: "xxxx"
-	grantType: "refresh_token"



The Node Id can be obtained through the APIs or from the URL which is in the last part of the URL and it starts right after the slash. The user must log in to Stadshubben to go to Nodes and then click on the target node. When the target node's page is opened, the Id can be obtained from the URL. See below:
![](https://hackmd.io/_uploads/Sy-u8YCp2.png)

The node ID for the example above is: 469f0f0f-12fc-440a-81db-56295513151w



## Device API
- To call all the nodes to see what they have for Id, ChannelId, ModelId, etc. make a GET request on the following endpoint:
GET https://api.kalmarenergi.se/device/v1/nodes 
 
- To call a node to see what it has for Id, ChannelId, ModelId, etc. make a GET Request on the following endpoint:
GET https://api.kalmarenergi.se/device/v1/nodes/{NodeId}
 
 
## Device Data API
-	To fetch data from a node:
GET https://api.kalmarenergi.se/device-data/v1/message-values?nodeId={NodeId}6&pageIndex=1&pageSize=-1&sortBy=Message.CreatedAt&sortDescending=false&channelIds={ChannelId}&channelIds={ChannelId}&channelIds={ChannelId} 
GET https://api.kalmarenergi.se/device-data/v1/message-values?NodeId={NodeId}
GET https://api.kalmarenergi.se/device-data/v1/message-values?ChannelIds={ChannelId}&NodeId={NodeId} 
 
 
 
## Device Data Latest API
-	To retrieve only the most recent message (data) that arrived from a node:
GET https://api.kalmarenergi.se/device-data-latest/v1/message-values?nodes={NodeId}&pageIndex=1&pageSize=100
 
 

