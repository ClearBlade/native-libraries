The module offers a way to send SMTP based emails from a ClearBlade service.

* mailer().createTransport(options)
* transport.sendMail(options)
* transport.close()

_Search for `mailer-example` at [ipm.clearblade.com](ipm.clearblade.com) for an installable example._

## mailer().createTransport(options)

options

- host : [HOSTNAME]
- port : [PORT]


Creates and returns a transport object used for sending mail to an appropriate SMTP server.

**Example:**
~~~javascript
	function sendMailGmail(req, resp){
    var transport = mailer().createTransport({
        host:"<SMTP_SERVER>",
        port:"587"
    });
~~~

## transport.sendMail(mail_options, callback)

Sends a mail object to the SMTP server for delivery

mail_options

 - from: 		[FROM_ADDRESS]
 - username: [ACCOUNT_USERNAME]
 - secure: [STARTTLS(false), TLS(true)]
 - password: 	[ACCOUNT_PASSWORD]
 - to:			[TO_ADDRESS]
 - subject:		[MAIL_SUBJECT_TEXT]
 - text:		[EMAIL_BODY]


callback = function(error, response)

**Example:**
~~~javascript
	transport.sendMail({
        from: "mail_id",
        password:"your_password",
        to: "receiver_email",
        subject: "Test subject",
        text: "Hi. How are you doing today?"
    }, function(error, response){
        transport.close();
        if(error){
            resp.error(error);
        }else{
            resp.success("Success");
        }
    });
};
~~~

## transport.close()

Closes the connection to the transport object

## Gmail Example
Below is a complete example that will use a gmail based address.  

**NOTE:** Google protects unauthorized uses of gmail accounts uses multi-factor verification.  In order to make this send work you will need to ensure that the privacy and permissions on your gmail account allow for third party sending.

**Example:**
~~~javascript
	function sendMailGmail(req, resp){
    var transport = mailer().createTransport({
        host:"smtp.gmail.com",
        port:"587"
    });

    function sendEmail(requestObj){
        transport.sendMail({
            from: "your_gmail_id",
            password:"your_password",
            to: "receiver_email",
            subject: "Test subject",
            text: "Hi. How are you doing today?"
        }, function(error, response){
            transport.close();
            if(error){
                resp.error(error);
            }else{
                resp.success("Thank you "+req.params.firstName+" "+req.params.lastName+".  Your request has been submitted");
            }
        });
    }

    ClearBlade.init({request:req});
    sendEmail();
};
~~~

# V8 version
Note: the V8 version of the library differs from the duktape version.

## Example

```
const client = new Mailer.Client({
    host: "smtp.gmail.com",
    port: 587,
    auth: {
        username: "user@gmail.com",
        password: "app_password"
    },
    secure: false,
    dryRun: true
});

client.send({
    from: "user@gmail.com",
    to: "recipient1@example.com,recipient2@example.com",
    subject: "Multi-recipient Test",
    body: "Testing multiple recipients"
}).then(function(result) {
    console.log(result);
});
```

## Objects

<dl>
<dt><a href="#Mailer">Mailer</a> : <code>object</code></dt>
<dd></dd>
</dl>

## Typedefs

<dl>
<dt><a href="#ClientConfig">ClientConfig</a> : <code>Object</code></dt>
<dd></dd>
<dt><a href="#MailMessage">MailMessage</a> : <code>Object</code></dt>
<dd></dd>
<dt><a href="#SendResult">SendResult</a> : <code>Object</code></dt>
<dd></dd>
</dl>

<a name="Mailer"></a>

## Mailer : <code>object</code>
**Kind**: global namespace  

* [Mailer](#Mailer) : <code>object</code>
    * [.Client](#Mailer.Client)
        * [new Client(config)](#new_Mailer.Client_new)
        * [.Client#send(message)](#Mailer.Client.Client+send) ⇒ [<code>Promise.&lt;SendResult&gt;</code>](#SendResult)

<a name="Mailer.Client"></a>

### Mailer.Client
**Kind**: static class of [<code>Mailer</code>](#Mailer)  

* [.Client](#Mailer.Client)
    * [new Client(config)](#new_Mailer.Client_new)
    * [.Client#send(message)](#Mailer.Client.Client+send) ⇒ [<code>Promise.&lt;SendResult&gt;</code>](#SendResult)

<a name="new_Mailer.Client_new"></a>

#### new Client(config)
Mailer client for sending emails


| Param | Type |
| --- | --- |
| config | [<code>ClientConfig</code>](#ClientConfig) | 

<a name="Mailer.Client.Client+send"></a>

#### Client.Client#send(message) ⇒ [<code>Promise.&lt;SendResult&gt;</code>](#SendResult)
Send an email

**Kind**: static method of [<code>Client</code>](#Mailer.Client)  

| Param | Type |
| --- | --- |
| message | [<code>MailMessage</code>](#MailMessage) | 

<a name="ClientConfig"></a>

## ClientConfig : <code>Object</code>
**Kind**: global typedef  
**Properties**

| Name | Type | Description |
| --- | --- | --- |
| host | <code>string</code> | SMTP host |
| port | <code>number</code> | SMTP port |
| [auth] | <code>Object</code> | Authentication credentials |
| auth.username | <code>string</code> | Username for authentication |
| auth.password | <code>string</code> | Password for authentication |
| [secure] | <code>boolean</code> | Use TLS (default: false) |
| [dryRun] | <code>boolean</code> | Validate without sending (default: false) |

<a name="MailMessage"></a>

## MailMessage : <code>Object</code>
**Kind**: global typedef  
**Properties**

| Name | Type | Description |
| --- | --- | --- |
| from | <code>string</code> | Sender email address |
| to | <code>string</code> | Recipient email address(es), comma-separated |
| subject | <code>string</code> | Email subject |
| body | <code>string</code> | Email body (HTML supported) |

<a name="SendResult"></a>

## SendResult : <code>Object</code>
**Kind**: global typedef  
**Properties**

| Name | Type | Description |
| --- | --- | --- |
| sent | <code>boolean</code> | Whether email was actually sent |
| host | <code>string</code> | SMTP host used |
| port | <code>number</code> | SMTP port used |
| from | <code>string</code> | Sender address |
| to | <code>Array.&lt;string&gt;</code> | Recipient addresses |
| subject | <code>string</code> | Email subject |
| authMethod | <code>string</code> | Auth method used (none, PLAIN, LOGIN) |
| secure | <code>boolean</code> | Whether TLS was used |
| bodyLength | <code>number</code> | Size of email body in bytes |


