---
title: SOAP Client in PHP - example
tags: PHP backend
layout: post
---
I'm used to connect to other services through RESTful protocol. But there are other ways and recently I had to create an adapter which would send a request to a SOAP server and get its response. There is more to read about the protocol and [you can read about it on a Wikipedia](https://en.wikipedia.org/wiki/SOAP). I will explain it in few points quick points and I will let you go through an example which let me get my job done.
<!--more-->

So let's get it started. SOAP in a few points:

1. SOAP is an XML based protocol. You send a request in a form of an XML file and you get an XML file with the data or with an error message.
2. The XML files need to be formed correctly. SOAP message is enclosed in an envelope which consists of a header (optional) and a body.
3. The information exhange is transported over HTTP (also over SMTP).

Since it is XML files that are exchanged in SOAP protocol, there multiple ways to prepare a query and then to parse a response. While I was browsing the Web (ok, it was mostly StackOverflow) looking for answers to my questions, I've mostly seen people advising to use [SimpleXML element](http://php.net/simplexml). And it probably is not a bad choice if you have been dealing with XML files in PHP previously. Me, I haven't. So I've decided to go with a [SOAP library that is available in PHP](http://www.php.net/soap).

One thing to keep in mind if you want to follow my approach: make sure that the library is enabled in your PHP. If you have an access to `php.ini` config file then make sure that extension `php_soap.dll` is uncommented. On Amazon Web Service for example you need to install it with `sudo yum install php56-soap`.

If everything is set up, let's try and create the first request. In a standard use case, when you are going to be asked to retrieve some info from a server, you are going to get a specification of what needs to be included in a request, what functions are available on the server and hw is the data in the response organised. I'll show you an example of how I had to do that.

### Info needed to make a SOAP Client

In order to be able to create a SOAP client you should get:

1. WSDL server URL ([more on WSDL](https://en.wikipedia.org/wiki/Web_Services_Description_Language)).
2. Documantation on supported options.
3. Example requests and responses (nice to have)

### Enriching a default SOAP client

I needed more than a standard PHP library was offering. I wanted to the SOAP client to log its activities and I wanted to cache XML requests and responses for troubleshooting. Therefore I've created my own class:

{% highlight php %}
class LocalSoapClient extends SoapClient {

    private $logger;

    /**
     * LocalSoapClient constructor.
     * @param mixed $wsdl
     * @param array $options
     * @param Log $logger
     */
    function __construct($wsdl, $options, $logger) {
        $this->logger = $logger;
        parent::__construct($wsdl, $options);
    }

    function __doRequest($request, $location, $action, $version, $one_way = 0) {
        $this->logger->write(__METHOD__." sending a SOAP request to AAA.");
        // Intercepting to store request and response:
        $timestamp = str_replace(".", "", microtime(true));
        file_put_contents("logs/soap/soap-query-".$timestamp.".xml", str_replace('><', '>'."\n\r".'<', $request));
        // Execute a default, parent method:
        $response = parent::__doRequest($request, $location, $action, $version, $one_way);
        // Store the response:
        file_put_contents("logs/sap/soap-response-".$timestamp.".xml", str_replace('><', '>'."\n\r".'<', $response));
        $this->logger->write(__METHOD__." XML files written: logs/soap/soap-query-".$timestamp.".xml and logs/soap/soap-response-".$timestamp.".xml");
        return $response;
    }
}
{% endhighlight %}

Constructor sets WSDL server address and options, by calling parent constructor. Specs for this one [can be found on php.net](https://secure.php.net/manual/en/soapclient.soapclient.php). Additionally it sets the logger object which is later used in the overwritten `__doRequest` method. This one stores all the files and lets the default method run its chorus.

As a logger I use a [Fat Free Framework logger object](http://fatfreeframework.com/log) as I am working with this framework. But you can use any - basically it is there to let you be sure that everything works as expected.

### Making a request

This is an example of the request I should send (the URLs are replaced with example ones):

{% highlight xml %}
<?xml version='1.0' encoding='utf-8'?>
<soapenv:Envelope xmlns:soapenv="http://www.w3.org/2003/05/soap-envelope"
xmlns:ns3="http://www.example.com/product/schemas">

<soapenv:Header>
<aaa:COM_ID xmlns:aaa="http://web.example.com">company_id</aaa:COM_ID>
<aaa:NAME_USER xmlns:aaa="http://web.example.com">client_id</aaa:NAME_USER>
<aaa:PASS_USER xmlns:aaa="http://web.example.com">client_password</aaa:PASS_USER>
</soapenv:Header>

<soapenv:Body>
<ns3:WS_Get_Info_By_ID>
	<ns3:id>99ABC50</ns3:id>
</ns3:WS_Get_Info_By_ID>
</soapenv:Body> 

</soapenv:Envelope>
{% endhighlight %}

Let's make it happen in PHP.

{% highlight php %}
$logger = new Log('SOAPLog.log');

$client = new LocalSoapClient(
    "https://ws.example.com/product/services/info?wsdl",
    array(
        'soap_version' => SOAP_1_2,
        'exceptions' => false
    ),
    $logger
);
{% endhighlight %}

Now it's time to define headers. There are three with company_id, client_id and client_password. Each operates on the same namespace, so I defined one which will be added in the Envelope as you will see later on:

{% highlight php %}
// Define namespace:
$ns_s = 'http://web.example.com';
// Define headers which will use this namespace:
$h1 = new SOAPHeader($ns_s, 'COM_ID', 123456789, false);
$h2 = new SOAPHeader($ns_s, 'NAME_USER', "my_login", false);
$h3 = new SOAPHeader($ns_s, 'PASS_USER', "my_password", false);
$client->__setSoapHeaders(array($h1, $h2, $h3));
{% endhighlight %}

And finally the `Body` part. This one is made in SOAP as a method (in this example it is `WS_Get_Info_By_ID`) which needs a `stdClass` object with property `id` as a parameter. This is going to be a product which I want to enquire the SOAP server about.

{% highlight php %}
$obj = new \stdClass();
$obj->id = "99ABC50";

$this->logger->write(__METHOD__." Making a query...");
$ret = $client->WS_Get_Info_By_ID($immat);
{% endhighlight %}

Finally, it's good to see if there was no error returned (for example wrong authentication):

{% highlight php %}
if (is_soap_fault($ret)) {
    $this->logger->write(__METHOD__." ERROR: SOAP Fault: (faultcode: {$ret->faultcode}, faultstring: {$ret->faultstring})");
}
{% endhighlight %}

Upon execution of these lines, my instance of a custom SOAP Client class should dump the request XML file in the logs directory. This is what I should expect:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<env:Envelope xmlns:env="http://www.w3.org/2003/05/soap-envelope" xmlns:ns1="http://www.example.com/product/schemas" xmlns:ns2="http://web.example.com">
<env:Header>
<ns2:COM_ID>123456789</ns2:COM_ID>
<ns2:NAME_USER>my_login</ns2:NAME_USER>
<ns2:PASS_USER>my_password</ns2:PASS_USER>
</env:Header>
<env:Body>
<ns1:WS_Get_Info_By_ID>
<ns1:immat>99ABC50</ns1:immat>
</ns1:WS_Get_Info_By_ID>
</env:Body>
</env:Envelope>
{% endhighlight %}

There is a difference here which I mentioned before - the namespace for headers is defined in the Envelope.

### Further enchancements
In order to be 100% sure this will work, I should've done also a check to see if the function `WS_Get_Info_By_ID` still exists. This can be done by calling [SOAP Client `__getFunctions` method](https://secure.php.net/manual/en/soapclient.getfunctions.php)

### SOAP Response
What I like about the idea of using PHP's SOAP Client is that the reponse is an already parsed stdClass object with reply info set as its properties. It will be different depending on the SOAP server's response, but it will be easily accessible and it won't require any further processing to reach this info.