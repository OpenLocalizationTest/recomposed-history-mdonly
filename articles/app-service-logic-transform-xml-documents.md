<properties 
	pageTitle="Transform XML documents" 
	description="Learn how to transform XML documents from one schema to another." 
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/05/2015"
	ms.author="anuragdalmia"/>

#Transform XML documents

## **BizTalk Transform API App Overview**

BizTalk Transform API App converts data from one format to another format. You can create a transformation - a 'Map' between a 'Source Schema (XML Schema)' and a 'Target Schema (XML Schema)' in the Transform Designer in Visual Studio using the 'Microsoft Azure BizTalk Services SDK'.  You can use various inbuilt functions while creating your map, including string manipulations, conditional assignments, arithmetic expressions, date time formatters, and looping constructs. You can upload the map created in Visual Studio (.trfm) into the BizTalk Transform API App.

You create a map by expressing a graphical correlation between two XML schemas that defines a relationship between elements in those schemas. The transformation defined in a map can be simple, such as copying a name and address from one document to another. You can express a direct data copy by using a link, which is shown in BizTalk Mapper as a line connecting the appropriate elements in the source schema with their counterparts in the destination schema. You can also specify more complex transformations by using various out of box map operations. See [Create a Map in Visual Studio](http://aka.ms/createamapinvs) for more details.


## **Create a new BizTalk Transform API App**

1.	Login to Azure Portal and go to homepage.

2.	Click on New -> API App. If the ‘API App’ entry does not show up, click on ‘Everything’ and select ‘API App’ from the available list. 

	   ![][1]
 
3.	Alternatively, you can click on ‘Marketplace’ on the homepage and select ‘API App’ from the available list.

	   ![][2]
 
4.	All the API Apps available in the marketplace are shown.

	   ![][3]
 
5.	Browse for ‘BizTalk Transform’ by typing Transform and select ‘BizTalk Transform’.

	   ![][4] 
 
6.	In the new blade that opens, click on ‘Create’

       ![][5]
 
7.	In the 'API App Settings' blade, enter the following information and click ‘Create’


	- Name – give a name for your Transform API App 
	- Web Hosting Plan – select or create a web hosting plan 
	- Pricing Tier – Choose the pricing tier you want this App to reside in 
	- Resource Group – Select or create the resource group where the App should reside in 
	- Location – Choose the geographic location where you would like the App to be deployed
	
	   ![][6]

8.	Click on Create. Within a few minutes your BizTalk Transform API App will be created. 


### Add a Map

You can add the map (.trfm file) created in Visual Studio (VS) using ‘BizTalk Services SDK’ to the ‘BizTalk Transform API App’. See [Create a Map in Visual Studio](http://aka.ms/createamapinvs) for more details.

If the map is modified after it is uploaded, you can upload the updated map and it will replace the existing map in the Transform API App.


1.	Click on ‘Browse’ on Azure Portal (left of the screen) and select ‘API Apps’.

2.	If the ‘API Apps’ entry does not show up, click on ‘Everything’ and select ‘API Apps’ from the available list.

	![][7]

2.	The list of all ‘API Apps’ created in your Azure subscription are shown.

	![][8]

3.	Select the ‘BizTalk Transform API App’ created in the previous section.

4.	The configuration blade for the API App shows up. You can see ‘Maps’ in Components section. 

	![][9]

5.	Select ‘Maps’ and a new blade with the list of maps shows up.

6.	Click on ‘Add Map’ icon on the top and the ‘Add Map' blade shows up.

	![][10]

7.	Click on the File icon and browse for a ‘.trfm’ file from your local machine.

8.	Click on OK and a new map is created. It is shown in the list of maps.

### Use a BizTalk Transform APIApp in a Logic App

Once the map has been authored and tested, it is now ready for consumption. Users can create a new Logic App by doing New->Logic App. 

1. Within the Logic App, BizTalk Transform should be available in the gallery to the right. This can now be dragged and dropped onto the designer surface. 

2. Once this is done, there will be an option to choose which Transform API App to target. 

3. Provide the below parameters to complete the 'Transform' action configuration.
		 
	- Input XML
		- Specify the valid XML content that will conform to the source schema of a map in Transform API App. This can be an output from previous action in Logic App App such as ‘Call RFC – SAP’ or ‘Insert Into Table – SQL’.
		
	- Map Name
		- Specify a valid map name that was already uploaded in Transform API App. If no map is specified, the map is automatically picked based on the source schema to which input XML conforms to.


4. The output of the action 'Output XML' can be used in subsequent actions in the Logic App.

<!--Image references-->
[1]: ./media/app-service-logic-transform-xml-documents/Create_Everything.png
[2]: ./media/app-service-logic-transform-xml-documents/Create_Marketplace.png
[3]: ./media/app-service-logic-transform-xml-documents/Create_APIApp.png
[4]: ./media/app-service-logic-transform-xml-documents/Search_TransformAPIApp.png
[5]: ./media/app-service-logic-transform-xml-documents/Select_TransformAPIApp.png
[6]: ./media/app-service-logic-transform-xml-documents/New_TransformAPIApp_Blade.png
[7]: ./media/app-service-logic-transform-xml-documents/Browse_APIApps.png
[8]: ./media/app-service-logic-transform-xml-documents/Select_APIApp_List.png
[9]: ./media/app-service-logic-transform-xml-documents/Configure_Transform_APIApp.png
[10]: ./media/app-service-logic-transform-xml-documents/Add_Map.png




