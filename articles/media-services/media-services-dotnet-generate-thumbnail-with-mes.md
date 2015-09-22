<properties 
	pageTitle="How to generate thumbnail using Media Encoder Standard with .NET" 
	description="This topic shows how to use .NET to encode an asset and generate the thumbnail at the same time using Media Encoder Strandard." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/20/2015"    
	ms.author="juliako"/>


#How to generate thumbnail using Media Encoder Standard with .NET

This topic shows how to use .NET to encode an asset and generate the thumbnail at the same time using Media Encoder Standard.

##Example

The following code example uses Media Services .NET SDK to perform the following tasks:

- Create an encoding job.
- Get a reference to the Media Encoder Standard encoder.
- Load the preset [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) or [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) that contain the encoding preset as well as information needed to generate thumbnails. You can save this  [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) or [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) in a file and use the following code to load the file.

			// Load the XML (or JSON) from the local file.
		    string configuration = File.ReadAllText(pathToLocalPresetFile);  
- Add a single encoding task to the job. 
- Specify the input asset to be encoded.
- Create an output asset that will contain the encoded asset.
- Add an event handler to check the job progress.
- Submit the job.
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset, string pathToLocalPresetFile)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		

		    // Load the XML (or JSON) from the local file.
		    string configuration = File.ReadAllText(pathToLocalPresetFile);

		
		    // Create a task
		    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		        processor,
		        configuration,
		        TaskOptions.None)
		
		    // Specify the input asset to be encoded.
		    task.InputAssets.Add(asset);
		    // Add an output asset to contain the results of the job. 
		    // This output is specified as AssetCreationOptions.None, which 
		    // means the output asset is not encrypted. 
		    task.OutputAssets.AddNew("Output asset",
		        AssetCreationOptions.None);
		
		    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		    job.Submit();
		    job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		    return job.OutputMediaAssets[0];
		}
		
		private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
		{
		    Console.WriteLine("Job state changed event:");
		    Console.WriteLine("  Previous state: " + e.PreviousState);
		    Console.WriteLine("  Current state: " + e.CurrentState);
		    switch (e.CurrentState)
		    {
		        case JobState.Finished:
		            Console.WriteLine();
		            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
		            break;
		        case JobState.Canceling:
		        case JobState.Queued:
		        case JobState.Scheduled:
		        case JobState.Processing:
		            Console.WriteLine("Please wait...\n");
		            break;
		        case JobState.Canceled:
		        case JobState.Error:
		
		            // Cast sender as a job.
		            IJob job = (IJob)sender;
		
		            // Display or log error details as needed.
		            break;
		        default:
		            break;
		    }
		}
		
		
		private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		{
		    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		    if (processor == null)
		        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		    return processor;
		}

##<a id="json"></a>Thumbnail JSON preset

For information about schema, see [this](https://msdn.microsoft.com/library/mt269962.aspx) topic.

	{
	  "Version": 1.0,
	  "Grid": {
	    "Enabled": true
	  },
	  "Sources": [
	    {
	      "InputFiles": [
	        {
	          "Path": "SmallVideo.mp4"
	        }
	      ],
	      "Streams": [],
	      "Filters": {},
	      "Pad": true
	    },
	    {
	      "InputFiles": [
	        {
	          "Path": "SmallVideo.mp4"
	        }
	      ],
	      "Streams": [],
	      "Filters": {},
	      "Pad": true
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "H264Layers": [
	        {
	          "Profile": "Baseline",
	          "Level": "2",
	          "Bitrate": 300,
	          "MaxBitrate": 300,
	          "BufferWindow": "00:00:05",
	          "Width": 400,
	          "Height": 200,
	          "ReferenceFrames": 3,
	          "EntropyMode": "Cavlc",
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Main",
	          "Level": "auto",
	          "Bitrate": 1000,
	          "MaxBitrate": 1000,
	          "BufferWindow": "00:00:05",
	          "Width": 800,
	          "Height": 400,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Chapters": [],
	      "Type": "H264Video"
	    },
	    {
	      "JpgLayers": [
	        {
	          "Quality": 20,
	          "Type": "JpgLayer",
	          "Bitrate": 1000,
	          "Width": 640,
	          "Height": 480,
	          "FrameRate": "0/1"
	        },
	        {
	          "Quality": 2,
	          "Type": "JpgLayer",
	          "Bitrate": 400,
	          "Width": 320,
	          "Height": 240,
	          "FrameRate": "0/1"
	        }
	      ],
	      "Chapters": [],
	      "Start": "10%",
	      "Step": "10",
	      "Range": "00:00:08",
	      "Type": "JpgVideo"
	    },
	    {
	      "PngLayers": [
	        {
	          "Type": "PngLayer",
	          "Bitrate": 1000,
	          "Width": 640,
	          "Height": 480,
	          "FrameRate": "0/1"
	        },
	        {
	          "Type": "PngLayer",
	          "Bitrate": 400,
	          "Width": 320,
	          "Height": 240,
	          "FrameRate": "0/1"
	        }
	      ],
	      "Chapters": [],
	      "Start": "{Best}",
	      "Range": "00:00:08",
	      "Type": "PngVideo"
	    },
	    {
	      "BmpLayers": [
	        {
	          "Type": "BmpLayer",
	          "Bitrate": 1000,
	          "Width": 640,
	          "Height": 480,
	          "FrameRate": "0/1"
	        },
	        {
	          "Type": "BmpLayer",
	          "Bitrate": 400,
	          "Width": 320,
	          "Height": 240,
	          "FrameRate": "0/1"
	        }
	      ],
	      "Chapters": [],
	      "Start": "{Best}",
	      "Range": "00:00:08",
	      "Type": "BmpVideo"
	    },
	    {
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Index}_{VideoBitrate}{Extension}",
	      "Format": {
	        "Type": "JpgFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}_{VideoBitrate}{Extension}",
	      "Format": {
	        "Type": "PngFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}_{VideoBitrate}{Extension}",
	      "Format": {
	        "Type": "BmpFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{VideoBitrate}{Extension}",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}

##<a id="xml"></a>Thumbnail XML preset

For information about schema, see [this](https://msdn.microsoft.com/library/mt269962.aspx) topic.

	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Grid Enabled="true" />
	  <Sources>
	    <Source>
	      <InputFiles>
	        <InputFile>
	          <Path>SmallVideo.mp4</Path>
	        </InputFile>
	      </InputFiles>
	      <Streams />
	      <Filters />
	      <Pad>true</Pad>
	    </Source>
	    <Source>
	      <InputFiles>
	        <InputFile>
	          <Path>SmallVideo.mp4</Path>
	        </InputFile>
	      </InputFiles>
	      <Streams />
	      <Filters />
	      <Pad>true</Pad>
	    </Source>
	  </Sources>
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>300</Bitrate>
	          <Width>400</Width>
	          <Height>200</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Baseline</Profile>
	          <Level>2</Level>
	          <BFrames>0</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cavlc</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>300</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>1000</Bitrate>
	          <Width>800</Width>
	          <Height>400</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Main</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1000</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	      <Chapters />
	    </H264Video>
	    <JpgVideo Start="10%" Step="10" Range="00:00:08">
	      <JpgLayers>
	        <JpgLayer>
	          <Bitrate>1000</Bitrate>
	          <Width>640</Width>
	          <Height>480</Height>
	          <FrameRate>0/1</FrameRate>
	          <Quality>20</Quality>
	        </JpgLayer>
	        <JpgLayer>
	          <Bitrate>400</Bitrate>
	          <Width>320</Width>
	          <Height>240</Height>
	          <FrameRate>0/1</FrameRate>
	          <Quality>2</Quality>
	        </JpgLayer>
	      </JpgLayers>
	      <Chapters />
	    </JpgVideo>
	    <PngVideo Start="{Best}" Range="00:00:08">
	      <PngLayers>
	        <PngLayer>
	          <Bitrate>1000</Bitrate>
	          <Width>640</Width>
	          <Height>480</Height>
	          <FrameRate>0/1</FrameRate>
	        </PngLayer>
	        <PngLayer>
	          <Bitrate>400</Bitrate>
	          <Width>320</Width>
	          <Height>240</Height>
	          <FrameRate>0/1</FrameRate>
	        </PngLayer>
	      </PngLayers>
	      <Chapters />
	    </PngVideo>
	    <BmpVideo Start="{Best}" Range="00:00:08">
	      <BmpLayers>
	        <BmpLayer>
	          <Bitrate>1000</Bitrate>
	          <Width>640</Width>
	          <Height>480</Height>
	          <FrameRate>0/1</FrameRate>
	        </BmpLayer>
	        <BmpLayer>
	          <Bitrate>400</Bitrate>
	          <Width>320</Width>
	          <Height>240</Height>
	          <FrameRate>0/1</FrameRate>
	        </BmpLayer>
	      </BmpLayers>
	      <Chapters />
	    </BmpVideo>
	    <AACAudio>
	      <Profile>AACLC</Profile>
	      <Channels>2</Channels>
	      <SamplingRate>48000</SamplingRate>
	      <Bitrate>128</Bitrate>
	    </AACAudio>
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}_{Index}_{VideoBitrate}{Extension}">
	      <JpgFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}_{VideoBitrate}{Extension}">
	      <PngFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}_{VideoBitrate}{Extension}">
	      <BmpFormat />
	    </Output>
	    <Output FileName="{Basename}_{VideoBitrate}{Extension}">
	      <MP4Format />
	    </Output>
	  </Outputs>
	</Preset>


##Media Services learning paths

You can view AMS learning paths here:

- [AMS Live Streaming Workflow](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS on Demand Streaming Workflow](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##See Also 

[Media Services Encoding Overview](media-services-encode-asset.md)