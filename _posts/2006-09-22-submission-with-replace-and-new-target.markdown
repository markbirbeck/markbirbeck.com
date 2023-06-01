---
layout: post
title: 'submission with @replace="text", and the new target attribute'
date: 2006-09-22
comments: true
---
This week, Mark came back from the XForms Working Group face-to-face meeting
with news of a potential new XForms feature--the ability to handle non-XML
responses from submissions.

<!-- more -->

  
The motivation for this feature is that there are many services that don't
return XML, but which are crucial to an application; if the form author has no
control over these services then it is important to be able to process the
non-XML responses. In standard XForms there is no way to get to the response.

  
(A good example of such a service is the [GData authentication
mechanism](http://code.google.com/apis/accounts/AuthForWebApps.html).)

  
The mechanism for handling this is by defining an additional value for the
`replace` attribute on `submission` (`text`), and a new attribute telling the
processor whereabouts in the instance data to insert the retrieved data
(`target`).

  
For example:

  

    
      
    <model>  
      <instance id="inst">  
        <x><y /></x>  
      </instance>  
      <submission  
       method="get" action="http://www.example.com/SomeTextFile.txt"  
       replace="text" target="y"  
      />  
    </model>  
    

  
  
In this example, the contents of `SomeTextFile.txt` would be loaded into the
node `y` in the instance.

  
[submission-text-
target.html](http://svn.x-port.net/svn/public/samples/submission/submission-
text-target.html) demonstrates the use of these new attributes to insert both
text and XML into an instance document. A more in-depth look at this feature
can be seen in [submission with replace="text", and the target
attribute](http://skimstone.x-port.net/node/422) in _Using formsPlayer_.

