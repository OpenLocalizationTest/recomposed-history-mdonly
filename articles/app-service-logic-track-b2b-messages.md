<properties 
   pageTitle="Track B2B messages" 
   description="This topic covers tracking of B2B processing" 
   services="app-service-logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/27/2015"
   ms.author="hariag"/>

##Tracking B2B Messages
B2B communication involves message processing between trading partners. The relationships are defined as agreements between two trading partners. Once communication is established then there needs to be a way to monitor if the communication is working as expected. 

As part of enabling B2B scenarios in Azure App Services we have enabled tracking data and the same is also surfaced via the Azure Portal. 

###AS2 Messages
Once you have created an instance of an AS2 API App then browse to that instance and go to the Tracking part. Herein one will be able to view all the AS2 tracking information and also filter it via the filter blades surfaced.

###EDIFACT Messages
Once you have created an instance of an EDIFACT API App then browse to that instance and go to the Tracking part. Herein one will be able to view all the EDIFACT tracking information and also filter it via the filters surfaced.

Additionally one will be able to view the interchange level data, group level data and transaction set level data as one steps into the view. 

If batches have been created as part of EDIFACT agreements in the associated Trading Partner Management API app then the Batching part will list all these batches. One would be able to step into a batch to see the message which constitute the active message (if any) and also the information for batches which have completed in the past.    

### X12 Messages
Once you have created an instance of a X12 API App then browse to that instance and go to the Tracking part. Herein one will be able to view all the X12 tracking information and also filter it via the filters surfaced.

Additionally one will be able to view the interchange level data, group level data and transaction set level data as one steps into the view. 

If batches have been created as part of X12 agreements in the associated Trading Partner Management API app then the Batching part will list all these batches. One would be able to step into a batch to see the message which constitute the active message (if any) and also the information for batches which have completed in the past.