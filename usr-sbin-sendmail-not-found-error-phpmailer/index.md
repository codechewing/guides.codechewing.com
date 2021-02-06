# /usr/sbin/sendmail: not found error with PHPMailer

If you’ve stumbled into this PHP Mailer error when trying to send an email, this might be why...

I’m running a Ubuntu web server, and my PHP mail script was outputting this to my error logs:

```
sh: 1: /usr/sbin/sendmail: not found
```

What I didn’t realise is that I hadn’t already installed **sendmail** onto the Ubuntu web server. Doh!
So the fix was as simple as logging into the server and installing sendmail with the following commands:

```
$ sudo apt-get update
$ sudo apt-get install sendmail
```

FYI, my PHP Mailer script looked like this:

```
$mail = new PHPMailer();
 
$body = 'An email test!';
 
$mail->AddReplyTo('abc@example.com', 'Clark Kent');
$mail->SetFrom('abc@example.com', 'Clark Kent');
$mail->AddAddress('me@example.com', 'Code Chewing Guides');
$mail->Subject = 'Test email';
$mail->MsgHTML( $body );
 
if( ! $mail->Send() ) {
  echo "Mailer Error: " . $mail->ErrorInfo;
}
```


### Farewell

If this was the simple fix for you too - happy emailing!
