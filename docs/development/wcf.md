# WCF

## Soap message structure

You can use `wcftestclient.exe` to get the Soap XML message structure that will be sent.

Select the `.svc` file in the Visual Studio solution explorer and choose Start (Without) Debugging. The WCF service and WCF Test Client tool will start. Select the message you want to see the structure of. Add some data and go to the XML tab.

## Trace and message logging

Enable the WCF tracing and message logging in the web.config. See a configuration example below.

```xml
<configuration>
    <system.diagnostics>
        <sources>  
            <source name="System.ServiceModel" switchValue="Information, ActivityTracing" propagateActivity="true">  
                <listeners>  
                    <add name="traces" type="System.Diagnostics.XmlWriterTraceListener"
                         initializeData= "C:\Temp\traces.svclog" />  
                </listeners>  
            </source>  
            <source name="System.ServiceModel.MessageLogging">
                <listeners>
                    <add name="messages" type="System.Diagnostics.XmlWriterTraceListener"
                         initializeData="C:\Temp\messages.svclog" />
                </listeners>
            </source>
        </sources>  
    </system.diagnostics>

    <system.serviceModel>
        <diagnostics>
            <messageLogging 
                logEntireMessage="true" 
                logMalformedMessages="true"
                logMessagesAtServiceLevel="true" 
                logMessagesAtTransportLevel="true"
                maxMessagesToLog="5000"
                maxSizeOfMessageToLog="2000000000"/>
        </diagnostics>
    </system.serviceModel>
</configuration> 
```

To view the logging. Open `SvcTraceViewer.exe` from a Visual Studio Developer Prompt. Then open both `.svclog` files.