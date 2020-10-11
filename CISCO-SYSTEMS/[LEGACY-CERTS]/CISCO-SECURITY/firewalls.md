# Understanding Firewalls

## Stateful

Firewall remembers who you are. All connections initiated from the inside will be remembered and allowed upon its return. Benefits of this is that we can have a policy that denies all outside traffic except for traffic that we explicitly  allow.

## Application based traffic

We can limit actual services per a web domain. Such as Google suite, or Facebook Messenger. We can get even more granular to see what the user is trying to do.

So say someone is using SSL, its encrypted, how do we block that traffic? The firewall can't completely tell. We can train our network by using SSL decryption and engage a man-in-the-middle to decrypt that traffic. The palo alto will act as a proxy.

Next Generation Firewall/Checkpoint

Policy Engine

App-ID

User-ID

Content-ID

Content that are users are downloading or uploading, for example, a PDF. It's easy to put malicious data in a PDF file. URL filtering is another facet, but we can determine the content. We can actually sandbox a URLs content and validate it before it reaches the users computer.

What about cloud based services?

Services like wildfire can analyze information and then push an update to all of the firewalls in a patch. We can detect information that is say, geo-location based in China. It mitigates some of the possibilities of Zero-Day attacks. Looking for malicious traits or patterns that act as an anomoly.

Integrate with Active Directory
Compliance and logging

Accredited Configuration Engineer (ACE)
Palo Alto Networks Certified Network Security Engineer (PCNSE6)
