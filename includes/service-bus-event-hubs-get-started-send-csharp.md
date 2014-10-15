## Send messages to Event Hubs
In this section we will write a Windows console app to send events to your event hub.

1. In Visual Studio, create a new Visual C# Desktop App project using the **Console  Application** template. We will call it **Sender**.

   	![][7]

2. In the Solution Explorer, right-click the solution, then click **Manage NuGet Packages**. 

	This displays the Manage NuGet Packages dialog box.

3. Search for `Microsoft Azure Service Bus` and click **Install**, and accept the terms of use. 

	![][8]

	This downloads, installs, and adds a reference <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">Azure Service Bus library NuGet package</a>.

4. Add the following using statement in your **Program.cs** file:

		using Microsoft.ServiceBus.Messaging;

5. Add the following static fields in your **Program** class, and substitute the values with the name of the event hub you created in the previous section, and the connection string with send rights.

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. Add the following method to the **Program** class:

		static async Task SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now.ToString(), message);
                    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now.ToString(), exception.Message);
                    Console.ResetColor();
                }

                await Task.Delay(200);
            }
        }

	This method will continuously send events to your event hub with a 200ms delay.

7. Finally, add the following lines to your **Main** method:

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages().Wait();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png