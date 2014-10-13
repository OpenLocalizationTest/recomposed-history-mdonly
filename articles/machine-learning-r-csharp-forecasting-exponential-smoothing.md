<properties title="Forecasting-Exponential Smoothing" pageTitle="Forecasting-Exponential Smoothing | Azure" description="Web service: Forecasting-Exponential Smoothing" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#Forecasting-Exponential Smoothing 
  

This [web service]( https://datamarket.azure.com/dataset/aml_labs/ets) implements Exponential Smoothing model (ETS) to produce predictions based on the historical data provided by the user. Will the demand for a specific product increase this year? Can I predict my product sales for the Christmas season, so that I can effectively plan my inventory? Forecasting models are apt to address such questions. Given the past data, these models examine hidden trends and seasonality to predict future trends.  
 
>While this web service could be consumed by users – potentially through a mobile app, website, or even on a local computer for example, the purpose of the web service is also to serve as an example of how Azure ML can be used to create web services on top of R code. With just a few lines of R code and clicks of a button within the Azure ML Studio, an experiment can be created with R code and published as a web service. The web service can then be published to the Azure Marketplace and consumed by users and devices across the world with no infrastructure set-up by the author of the web service.
 
##Consumption of Web Service 
 
This service accepts 4 arguments and calculates the ETS forecasts.
The input arguments are:

* Frequency: indicates the frequency of the raw data (daily/weekly/monthly/quarterly/yearly)
* Horizon: future forecast time-frame
* Date: add in the new time series data for time
* Value: add in the new time series data values

The output of the service is the calculated forecast values.

Sample input could be: 

* Frequency:12
* Horizon: 12
* Date:1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012;
1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013;
1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Value: 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511;
3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509
 
>This service as hosted on the Microsoft Azure Marketplace is an OData service; these may be called through POST or GET methods. 

There are multiple ways of consuming the service in an automated fashion (an example app is [here](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx)).

###Starting C# code for web service consumption:

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}

##Creation of Web Service 

>This web service was created using Azure ML. For a free trial, as well as introductory videos on creating experiments and publishing web services, please see [azure.com/ml](http://azure.com/ml). Below is a screenshot of the experiment that created the web service and example code for each of the modules within the experiment.

From within Azure ML, a new blank experiment was created. A sample input data was uploaded with a pre-defined data schema. Linked to the data schema is an “Execute R Script” module which generates the ARIMA forecasting model using ‘ets’ and ‘forecast’ functions from R. 


![Experiment flow][2]

####Module 1:
 
	# Add in the CSV file with the data in the format shown below 
![Data sample][3]	

####Module 2:
	# Apply time series model from forecast package 
	# Input data 
	data <- maml.mapInputPort(1) # class: data.frame library(forecast) 
 
	# preprocessing 
	colnames(data) <- c("frequency", "horizon", "dates", "values") … 
 
	# fit model 
	train_ts<- ts(values, frequency=frequency) fit1 <- ets(train_ts) 
	train_model <- forecast(fit1, h = horizon) 
	data.forecast <- as.data.frame(t( train_model$mean)) 
 
	# output 
	maml.mapOutputPort("data.forecast");

 
##Limitations 

This is a very simple example for ETS forecasting. As can be seen from the example code above, no error catching is implemented and the service assumes that all the variables are continuous/positive values and the frequency should be an integer greater than 1. The length of the date and value vectors should be the same. The date variable should adhere to the format ‘mm/dd/yyyy’.

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png