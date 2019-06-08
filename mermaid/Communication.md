# Communication
File for Mermaide Sequence Diagramm in [Typora](https://typora.io/) or [Mermaide Live Editor](https://mermaidjs.github.io/mermaid-live-editor)


[TOC]

## Handshake
### simplified Version

Image:  

<img src="./Handshake-simple.svg" height="500" />

Code:  


``` mermaid
sequenceDiagram
    participant SV1 as SmartVehicle1
    participant SB1 as SmartBox1
    participant SBn as SmartBoxN
    Note left of SV1 :simplified Async. <br/> MQTT Handshake
    loop wait for response from SB
        SV1 -->> SB1: SV1/available {sector, line}
        activate SV1
        SV1 -->> SBn: SV1/available {sector, line}
    end 
    
	SB1 -->> SV1: SB1/handshake {request SV1}
	activate SB1
	SBn -->> SV1: SB1/handshake {request SV1}
	Note over SBn,SV1: Request from SBn will be ignored
	SV1 -->> SB1: SV1/handshake {request SB1}
	SB1 -->> SV1: SB1/handshake {acknoledge SV1, position, cargo}
	SV1 -->> SB1: SV1/handshake {acknoledge SB1}
	deactivate SB1
	Note over SV1, SB1: Drive to Target-Position.
	SV1 -->> SB1: SB1/position {position}
	deactivate SV1

```
### detailed Version
Image:  

<img src="./Handshake-detailed.svg" height="500" />

Code:  
``` mermaid
sequenceDiagram
    participant SV1 as SmartVehicle1
    participant SV1AV as MQTT Topic <br/> /SV1/available
    participant SV1HS as MQTT Topic <br/>/SV1/handshake
    participant SB1HS as MQTT Topic <br/>/SB1/handshake
    participant SB1 as SmartBox1
    participant SBnHS as MQTT Topic <br/>/SBn/handshake
    participant SBn as SmartBoxN
    Note left of SV1 : Detailed Async. <br/> MQTT Handshake
    loop wait for response from SB
        SV1 -->> SV1AV: sector, line
        activate SV1AV
        activate SV1HS
        SV1AV -->> SB1: sector, line
        SV1AV -->> SBn: sector, line
    end 
    
	SB1 -->> SB1HS: request SV1
	activate SB1HS
	deactivate SV1AV
	SB1HS -->> SV1: request SV1
Note over SB1: Wait for Req. <br/> Timeout possible
	
	SBn -->> SB1HS: request SV1
	SBnHS -->> SV1: request SV1
	
	SV1 -->> SV1HS: request SB1
	SV1HS -->> SB1 : request SB1
Note over SV1: Wait for Ack. <br/> Timeout possible

	SB1 -->> SB1HS: acknoledge SV1, position, cargo
	SB1HS -->> SV1: acknoledge SV1, position, cargo

	
loop send 5 times
	SV1 -->> SV1HS: acknoledge SB1
Note over SB1: Wait for Ack. <br/> Timeout possible
	SV1HS -->> SB1: acknoledge SB1
end
	deactivate SV1HS
	deactivate SB1HS
	
	Note over SV1, SV1AV: Drive to Target-Position.
loop send 5 times
	SV1 -->> SB1: SB1/position {position}
end
```

## Sortic

### simplified Version
Image:  

<img src="./SorticToSB-Simple.svg" height="500" />

Code:  
``` mermaid
sequenceDiagram
    participant Sortic
    participant SB1 as SmartBox1
    participant SBn as SmartBoxN
    Note left of Sortic : Communication <br/> Sortic to SB
    loop send once per second
        Sortic -->> SB1: Handover {line: 1, cargo}
        Sortic -->> SB1: Handover {line: N, cargo}
        Sortic -->> SBn: Handover {line: 1, cargo}
        Sortic -->> SBn: Handover {line: N, cargo}
    end 
    
```

### detailed Version
Image:  

<img src="./SorticToSB-detailed.svg" height="500" />

Code:  
``` mermaid
sequenceDiagram
    participant Sortic
    participant SorticH as MQTT Topic <br/> Sortic/Handover
    participant SB1 as SmartBox1
    participant SBn as SmartBoxN
    Note left of Sortic : Communication <br/> Sortic to SB
    loop send once per second
        Sortic -->> SorticH: line: 1, cargo
        SorticH -->> SB1: line: 1, cargo
        SorticH -->> SBn: line: 1, cargo
        Sortic -->> SorticH: line: N, cargo
        SorticH -->> SB1: line: N, cargo
        SorticH -->> SBn: line: N, cargo
    end 
```

## Transfer
Image:  

<img src="./Handfshake-detailed.svg" height="500" />

Code:  
``` mermaid

```

### simplified Version
Image:  

<img src="./Handfshake-detailed.svg" height="500" />

Code:  
``` mermaid

```

### detailed Version
Image:  

<img src="./TransferToSV-detailed.svg" height="500" />

Code:  
``` mermaid
sequenceDiagram
participant SV1 as SmartVehicle1

participant TransferH as MQTT Topic <br/> Transfer/Handover
participant Transfer
participant SB1 as SmartBox1
participant SVn as SmartVehicleN

Note left of SV1: Communication <br/> Vehicle &Transfer
Note over SV1: Look for free Line <br/>with matching cargo
activate TransferH
loop once per second
    Transfer-->> TransferH : line: 1, cargo
    TransferH -->> SV1: line: 1, cargo
    Transfer-->> TransferH : line: 2, cargo
    TransferH -->> SV1: line: 2, cargo
    Transfer-->> TransferH : line: 3, cargo
    TransferH -->> SV1: line: 3, cargo
end
SB1-->> TransferH : line: 1, id
TransferH -->> SV1: line: 1, id
SVn-->> TransferH : line: 2, id
TransferH -->> SV1: line: 2, id
deactivate TransferH 
Note over SV1: Choose Line 3
loop all 0.5 seconds
SV1 -->> TransferH: line: 3, id
end
```

## load/undload process 
Image:  

<img src="./LoadUnload-pseudo.svg" height="500" />

Code:  
``` mermaid
sequenceDiagram
participant SB as SmartBox
participant SV as SmartVehicle
participant Sortic
participant Gateway
participant Transfer
    

Note left of SB : Comm-Sequence <br/> Load/Unload
activate Sortic
Note over SV : In Position Sortic <br/>  Line 1 <br/>  available

activate SV
Note over SB :  In Position Sortic <br/>  Line 3 <br/> Loaded <br/>  Wait for Cargo-Info
activate SB
Sortic -->> SB: Your cargo are apples
Note over SB : Wait for Vehicle
SB -->> SV: I need a Vehcile
SV -->> SB: I'm availabel
Note over SB : Choose Vehicle<br/>Handshake
SB -->> SV: I'm in Sortic 3 and have apples
deactivate SB 

Note over SV :  Sortic Gateway<br/> Listen if blocked
SV -->> Gateway: Are you free?
Gateway -->> SV: Yes
activate Gateway
Note over SV :  Block Gateway
deactivate Gateway

Note over SV :  Drive to SB <br/> load SB
SV -->> Transfer: Where do you need apples?
Transfer -->> SV: Line 2
Note over SV : Listen if Line 2 is blocked
 SV -->> Transfer: Is Line 2 free?
Transfer -->> SV: Yes
activate Transfer

Note over SV : Block Line 2 in Transfer
activate Gateway
Note over SV : Sortic Gateway<br/> Listen if blocked
SV -->> Gateway: Are you free?
Gateway -->> SV: No
Note over SV :  Wait some rand. time <br/> then check again
deactivate Gateway
SV -->> Gateway: Are you free?
Gateway -->> SV: Yes
activate Gateway
Note over SV :   Block Gateway <br/> Drive to Transfer
deactivate Gateway
deactivate Sortic

Note over SV :  Transfer Gateway<br/> Listen if blocked
SV -->> Gateway: Are you free?
Gateway -->> SV: Yes
activate Gateway
Note over SV :  Block Gateway
deactivate Gateway

SV -->> SB: You're at Transfer Line 2
Note over SV :  Unload Box
deactivate SV
Note over SV :  In Position Transfer<br/>  Line 2 <br/> available

Note over SB :  Wait for Unload Cargo
Note over SB : Wait for Vehicle
 deactivate Transfer


```
