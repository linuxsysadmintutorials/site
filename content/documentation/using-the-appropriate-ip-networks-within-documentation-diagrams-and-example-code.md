Title: Using the appropriate IP networks within Documentation, Diagrams and Example Code
Date: 2014-09-15
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/using-the-appropriate-ip-networks-within-documentation-diagrams-and-example-code/
disqus_identifier: 934 http://www.linuxsysadmintutorials.com/?p=934
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: "Using the appropriate IP networks within Documentation, Diagrams and Example Code"

Good Sysadmins always produce great documentation and descriptive network diagrams. 

It's good form to use appropriate network addresses within documentation, example code and diagrams.

Ever seen a <a href="http://en.wikipedia.org/wiki/Fictitious_Internet_resource">Fictitious IP address</a> in a movie?

Well someone should of read <a href="http://tools.ietf.org/html/rfc5735">RFC5735</a>.

<blockquote>
192.0.2.0/24 - This block is assigned as "TEST-NET-1" for use in documentation and example code.  It is often used in conjunction with domain names example.com or example.net in vendor and protocol documentation. 
</blockquote>

<blockquote>
198.51.100.0/24 - This block is assigned as "TEST-NET-2" for use in documentation and example code.  It is often used in conjunction with domain names example.com or example.net in vendor and protocol documentation.
</blockquote>

<blockquote>
203.0.113.0/24 - This block is assigned as "TEST-NET-3" for use in documentation and example code.  It is often used in conjunction with domain names example.com or example.net in vendor and protocol documentation. 
</blockquote>
 
So next time you write some documentation, create a diagram or reference a network in example code consider using one of the networks in RFC5735.
 
