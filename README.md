# check_xsievents
                     
check_xsievents is a Nagios Plugin-style perl script that checks the time it takes to establish a XSI Events channel, subscribe 
for "Basic Call" events and then wait until the first SubscriptionEvent is received on the event channel.

Status + response time is reported in Nagios Plugin-style and have been tested with LibreNMS to generate generate graphs over variation in response time.

If you are a BroadWorks based operator with customers that rely on client apps using XSI (such as my Call Control for BroadWorks iOS+Android apps), you 
should ensure that average response time reported with this plugin is less 500mS. 

Usage is
```
./check_xsievents -u username -p password -H hostname 
```
with optional parameters
```
-X xsievent_prefix    Default xsievent_prefix is "com.broadsoft.xsi-events"
-v                    Verbose - prints send and received data.
-c crtitical_timeout  Seconds to wait before returning Nagios_CRITICAL - default is 30s
-w warning_timeout    Seconds to wait before returning Nagios_WARNING - default is 2s
```

Example:
```
kimw@m15:~/$ ./check_xsievents -u username@qsc.de -p xxxxxxxx -H xsp4.bmcag.com 
XSIEvents OK - 0.507755 seconds response time|time=0.507952s;2.000000;30.000000;0.0;31.0
kimw@m15:~/$
```
and
```
kimw@m15:~/$ ./check_xsievents -u username@qsc.de -p xxxxxxxx -H xsp4.bmcag.com -v
2022-03-18 15:53:11.678903 Connecting to xsp4.bmcag.com:443
2022-03-18 15:53:11.833003 Connecteded to xsp4.bmcag.com:443, saying hello with:
POST /com.broadsoft.async/com.broadsoft.xsi-events/v2.0/channel HTTP/1.1
Host: xsp4.bmcag.com
Connection: keep-alive
Content-Length: 227
Authorization: Basic XXXXXXXXXXXXXXXXXXXXX 

<?xml version="1.0" encoding="UTF-8"?><Channel xmlns="http://schema.broadsoft.com/xsi"><channelSetId>KIWI-040D0D020D06040A0902080E010B0A0C</channelSetId><priority>1</priority><weight>50</weight><expires>3600</expires></Channel>
2022-03-18 15:53:11.918046 rx: HTTP/1.1 200 OK
2022-03-18 15:53:11.918345 rx: TrackingID: NA_7ea1ccb3-52fb-4372-95b5-16317464511a
2022-03-18 15:53:11.918491 rx: X-Frame-Options: SAMEORIGIN
2022-03-18 15:53:11.918620 rx: X-Content-Type-Options: nosniff
2022-03-18 15:53:11.918750 rx: X-XSS-Protection: 1; mode=block
2022-03-18 15:53:11.918874 rx: Set-Cookie: JSESSIONID=C46820BA7E48BFE71FFF513FBD00CE3F; Path=/com.broadsoft.xsi-events; Secure; HttpOnly
2022-03-18 15:53:11.918995 rx: Expires: Thu, 01 Jan 1970 00:00:00 GMT
2022-03-18 15:53:11.919113 rx: Content-Type: text/xml;charset=UTF-8
2022-03-18 15:53:11.919236 rx: Transfer-Encoding: chunked
2022-03-18 15:53:11.919329 rx: Date: Fri, 18 Mar 2022 15:53:11 GMT
2022-03-18 15:53:11.919421 rx: Keep-Alive: timeout=4
2022-03-18 15:53:11.919512 rx: Connection: keep-alive
2022-03-18 15:53:11.919607 rx: Server: HTTP Server
2022-03-18 15:53:11.919741 rx: 
2022-03-18 15:53:11.919904 rx: b5
2022-03-18 15:53:11.920014 rx: <?xml version="1.0" encoding="UTF-8"?>
2022-03-18 15:53:11.920118 rx: <Channel xmlns="http://schema.broadsoft.com/xsi"><channelId>64eddc79-b707-4827-aacd-487fb5cb8427</channelId><expires>86400</expires></Channel>
2022-03-18 15:53:11.929878 subscribe.POST(https://xsp4.bmcag.com/com.broadsoft.xsi-events/v2.0/user/username@qsc.de), xml: <?xml version="1.0" encoding="UTF-8"?>
<Subscription
    xmlns="http://schema.broadsoft.com/xsi">
    <event>Basic Call</event>
    <expires>120</expires>
    <channelSetId>KIWI-040D0D020D06040A0902080E010B0A0C</channelSetId>
    <applicationId>KIWI-XSIEvents responsetime checker 1.0.3</applicationId>
</Subscription>
2022-03-18 15:53:12.209787 subscribe.rx(https://xsp4.bmcag.com/com.broadsoft.xsi-events/v2.0/user/username@qsc.de): <?xml version="1.0" encoding="UTF-8"?>
<Subscription xmlns="http://schema.broadsoft.com/xsi"><subscriptionId>98ed3939-c113-419c-8012-092556b85c66</subscriptionId><expires>86400</expires></Subscription>
2022-03-18 15:53:12.209984 subscribe.rx(https://xsp4.bmcag.com/com.broadsoft.xsi-events/v2.0/user/username@qsc.de): got a subscriptionId: 98ed3939-c113-419c-8012-092556b85c66
2022-03-18 15:53:12.211861 rx: 2b4
2022-03-18 15:53:12.212165 rx: <?xml version="1.0" encoding="UTF-8"?><xsi:Event xmlns:xsi="http://schema.broadsoft.com/xsi" xmlns:xsi1="http://www.w3.org/2001/XMLSchema-instance" xsi1:type="xsi:SubscriptionEvent"><xsi:eventID>957d0482-6cdd-4189-b477-19c6793f113a</xsi:eventID><xsi:sequenceNumber>1</xsi:sequenceNumber><xsi:userId>username@qsc.de</xsi:userId><xsi:externalApplicationId>KIWI-XSIEvents responsetime checker 1.0.3</xsi:externalApplicationId><xsi:subscriptionId>98ed3939-c113-419c-8012-092556b85c66</xsi:subscriptionId><xsi:channelId>64eddc79-b707-4827-aacd-487fb5cb8427</xsi:channelId><xsi:eventData xsi1:type="xsi:CallSubscriptionEvent"><xsi:hookStatus>On-Hook</xsi:hookStatus></xsi:eventData></xsi:Event>
2022-03-18 15:53:12.212806 XSIEvents OK - 0.533490 seconds response time|time=0.534090s;2.000000;30.000000;0.0;31.0
kimw@m15:~/$

```


