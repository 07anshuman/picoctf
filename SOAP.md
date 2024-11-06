# picoCTF - SOAP

**FLAG**: `picoCTF{XML_3xtern@l_3nt1t1ty_0dcf926e}`

opened the challenge web portal  
watched the Loi Liang video provided in TP2.pdf, understood that this is supposed to be done via an XXE attack. googled more about XXE and watched videos to understand how requests work  
(including TP2.pdf's second link under web exploitation).  
Opened the Networks tab in inspect, looked for a POST request which came up after clicking the 'details' button. In Loi's video, he modified the request and dumped the etc/passwd file in the comment space itself  
I tried to modify the request in the same format  
`<!DOCTYPE change-log [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>`  
did not work  
googled and looked through the structure of POST requests and understood that a 'content-type' needs to be specified for requests  
so `<data><ID>2</ID></data>` needed to be changed to `<data><ID>&file;</ID></data>`  
revisited Loi's video and he did change it, I had apparently missed it.  

### also
learned that POST requests need a specific endpoint, in our case it was "/data" appended to the original link. Very interesting stuff honestly. If only end-sems weren't near.
