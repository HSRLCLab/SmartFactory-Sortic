# SmartFactory-Sortic

SmartFactroy-Sortic Reposiroty provides an overview of the SmartFactroy project and its implementation into the Sortic usecase.

<!-- add Pagebreak: <div style="page-break-after: always;"></div> -->

## Table of Content

<!-- TOC Generated with https://magnetikonline.github.io/markdown-toc-generate/ -->

[TOC]

# Usecase Sortic

At the [HSR](<https://www.hsr.ch/en>), a virtual world will be set up within the context of the [DigitalLab@HSR](<http://digitallabathsr.ch/>) in order to discuss various concepts in the field of digitization (Industry 4.0, Industry 2025) and Closed Loop Product Lifecycle Management. 

This will be illustrated by the example of two companies. 
The company Sortic manufactures automation solutions.

These are sold according to the business model "machine as a service". The customer does not buy the machine itself, but only the " sorting capacity ". The following is offered:

* 98% Ready for operation
* Defined response times
* Service for upgrades and conversion of the plant with transparent costs and times

The second company - DropKick - is a user of the Sortic system and uses it to complete its business model. The company DropKick is a delivery service startup, which is strongly digitalized. 

<p align="center"><img src="./docs/images/plm.png" height="350"/>  </p>

The interaction of the two companies is the basis to explain and explore a digital ecosystem and closed-loop PLM.



[Source: SmartFactory ,Translated with [DeepL](www.DeepL.com/Translator)]

## Sorting-system

The [functional model of the sorting-machine](https://github.com/HSRLCLab/sortic150) was developed as a "pick and place" machine based on Lego and Arduino (electrics and software).  

This is a mechatronic product with the following features:

* The machine can recognize parts and transports them to different locations depending on their property/ID.
* It is a modular product, which is distributed/produced according to an "Assemble to Order" strategy.  
* It is self-configurable: components have an RFID chip, reader and software.
 The components recognize which configuration is present and adapt themselves.  
* It is IoT capable: It has an integrated web server for communication with the Internet.

*[todo add images]*

[Source: SmartFactory ,Translated with [DeepL](www.DeepL.com/Translator)]

## Package distribution system

The Sortic plant has been extended by one new stage. It now has an autonomous package distribution system which takes over the further processing of the packages from the Sortic plant to the transfer station.

<p align="center"><img src="./docs/images/Gametable.png" height="350"/></p>

This happens as follows: the sorting system fills smart boxes (left side). They recognize their fill level and communicate it with smart vehicles which bring the boxes to their target location (right side). 

By using a modular approach, the package distribution system is easily scalable and very flexible.  
How this system was developed will be discussed in more detail.

### Functional structure diagram  (FSD)

In order to identify the required functions the main function *handle package* is divided into sub-functions up to atomic functions.

<p align="center"><img src="./docs/images/FSD.png" height="500"/></p>

As you can see in the functional structure the main function divides in two subfunction; *Store package* and *transport package*. 

#### Store package

The *Store package*-function was realized as a smartbox. This box can fix the package and recognize when it is filled.  
This is evaluated with the function *Control and Communicate* and communicated to the environment.

<p align="center"><img src="./docs/images/SmartBox.jpeg" height="450"/></p>

While the functions *fix package* and *detect fill-level* are application independent, the control and communicate block is specific to the Sortic application and determines the communication, like handshakes, with the vehicle and the surrounding area.

The structure of the FSD can also be seen in the collaboration diagram of the source-code.

<p align="center"><img src="./docs/images/class_box_ctrl__coll__graph.png" height="250"/></p>

A possible implementation of the Box can be found in [SmartFactory_Box-Sortic](https://github.com/LMazzole/SmartFactory_Box-Sortic).  

The hardware was designed and assembled by Luciano Bettinaglio.

#### Transport package

The *Transport package*-function was realized with a smartvehicle. The vehicle recognizes its surroundings, can follow lines, raise and lower the box and communicate with the box and its environment.

<p align="center"><img src="./docs/images/HardwareSV.png" height="300"/></p>

The main tasks of the vehicle are picking up and putting down the box and navigating on the game table while communicating with the environment. Navigation requires a drive and recognition of the surroundings. 

While the functions *detect environment* and *drive* are application independent, the control and communicate block is specific to the Sortic application and determines the communication, like handshakes, with the box and the surrounding area. The *navigation*-function is also specifically adapted to Sortic and the corresponding Gametable.

The structure of the FSD can also be seen in the collaboration diagram of the source-code.

<p align="center"><img src="./docs/images/class_vehicle_ctrl__coll__graph.png" height="400"/></p>

A possible implementation of the Sortic dependent function can be found in [SmartFactory_Vehicle-Sortic](https://github.com/LMazzole/SmartFactory_Vehicle-Sortic) while the Sortic independent functions are in [SmartFactory_Vehicle-Basis](https://github.com/LMazzole/SmartFactory_Vehicle-Basis).

The hardware was designed and assembled by Glenn Patrick Huber and Robert Paly.

#### Communicate

The Communicate-Function which is used by *Store-* and *Transport-package* is implemented as the [SmartFactory_MQTTCommunication](https://github.com/LMazzole/SmartFactory_MQTTCommunication).  It provides basic communication functionalities and defines the message-format.

You'll need a WLAN-Connection and a MQTT-Broker. How to set this up is explained in [Setup](#setup).

## Communication via MQTT

In the Sortic scenario there are 4 different communication partners: Sortic, SmartBox, SmartVehicle and Transfer.

The following scalable Topic-Tree is used for the communication:

<p align="center"> <img src="./docs/images/MQTTTopics.png" height="600"/> </p>

Alg. load unload sequence

<p align="center"> <img src="./mermaid/LoadUnload-pseudo.svg" height="900"/> </p>



### Sortic - SmartBox

<p align="center"> <img src="./mermaid/SorticToSB-detailed.svg" height="400"/> </p>

### SmartBox - SmartVehicle

Handshake

<p align="center"> <img src="./mermaid/Handshake-detailed.svg" height="500"/> </p>

A more detailed 

### SmartVehicle - Transfer

add simple

<p align="center"> <img src="./mermaid/TransferToSV-detailed.svg" height="600"/> </p>

### Improvements

- [ ] Use a separate topic for each line in Gateway

## GUI

## Setup

### Raspberry Pi

#### Mosquitto

#### Node-Red

 

# The SmartFactory Project

The aim of the SmartFactory Project is to provide a number of different software modules which can be used as a basis to build a smart factory[.](<https://avatars0.githubusercontent.com/u/51330787?s=400&v=4>)  
Using this modular approach, you're able to build a flexible and scalable SmartFactory tailored for your needs. Thanks to clearly defined and documented interfaces, the modules are interchangeable. This not only simplifies  maintenance but also expandability and reusability.

These modules provides only the basic functions and can (and need to) be customized.

The SmartFactroy-Project provides an interface for a  smart vehicle, a smart box and communication via MQTT.

- Vehicle - [SmartFactory_Vehicle-Basis](https://github.com/LMazzole/SmartFactory_Vehicle-Basis)  
- Communication - [SmartFactory_MQTTCommunication](https://github.com/LMazzole/SmartFactory_MQTTCommunication)
- (Box -  [SmartFactory_Box-Sortic](https://github.com/LMazzole/SmartFactory_Box-Sortic) )  

## Outlook

There are still some things left to do and to optimize:

- [ ] Add an general communication class which allows you to chose a protocol.
- [ ] Add an basic class for the smart box.

# Contributors

- [Luca Mazzoleni](https://github.com/LMazzole)
- Luciano Bettinaglio
- Glenn Patrick Huber
- Robert Paly

# License

MIT License