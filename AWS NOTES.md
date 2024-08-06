


**route 53 + certificate issuance -** 
When you register a new security certificate in certificate manager you should choose DNS validation if you haven't set up WHOIS emails for your domain, this activates your certificate via a CNAME record in route53. One thing you need to make sure of is that your NS record nameservers in your hosted zone for your registered domain align with the nameservers that are listed in the registered domain, if they don't match then your certificate will pend indefinitely even if a CNAME is created.


