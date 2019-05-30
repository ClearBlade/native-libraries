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
