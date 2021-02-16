---
layout: post
status: publish
published: true
title: TSSJS Vegas 2009 - Mission-Critical Cloud / Enterprise Hybrid Deployments
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-03-19T10:30:48.0000Z'
comments: true
---
Eugene Ciurana's talk was on the new hotness, the buzzword du jour Cloud Computing. One of the main goals as a presenter which I learned watching Eugene, is that your role is not just to teach and blather on, but to entertain. Eugene definitely has an air of confidence while he speaks, and it makes the content of his talk much more interesting. He also gave away free books to the 2 individuals with the best and most engaging questions for him during his presentation, sneaky. His talk consisted of the how-to on designing, implementing and rolling out a cloud/enterprise based application based on his experiences in the last several companies he's been employed.

There are a wealth of technologies involved with Cloud Computing, and Eugene attempted to break down into logical areas where each item applied to the overall architecture. Eugene covered the two types of services offered in the Cloud:

<ul>
<li>Software as a Service (SaaS) - The vendor provides a full application responsible for scaling and meeting SLAs such as Salesforce.com</li>
<li>Platform as a service (PaaS) - The vendor provides a virtual OS and/or application stack along with a well-defined API for interacting with the system, e.g. Amazon Web Services, Google App Engine</li>
<li>Infrastructure</li>
</ul>
Can you define the term Cloud Computing and not come up with 10 different ways of doing so? His simple explanation was: Cloud = 3rd party, hosted on virtualized boxes with the ability to scale out by duplicating the virtual instances on-the-fly.

In general, the move toward the cloud comes with great benefits and cost savings, and a caution to fully evaluate the scalability and availability that your system needs to provide. Depending on your choices, the SLAs offered by the service provider should drive the architectural and technical decisions. Amazon's service is cheap, usually effective, but provides no formal SLA to customers of its service. The outage earlier this year brought problems for eBay, LeapFrog, and Apple's iTunes Store, and was cause for at the very least Apple re-evaluating the decision for them to use S3 and pull this data in-house where they could better control the availability of the resources.

Eugene thoroughly explored the landscape of Cloud Computing and offered real-world examples and case studies showing how you can define an architecture based around a hybrid of cloud/enterprise components while not sacrificing scalability and availability just for low cost.

<strong>Be sure to check out our talk on <a href="http://wicket.apache.org/" title="Apache Wicket" target="_blank">Apache Wicket</a> on Friday at 3pm in Breakout Room 1 - If you liked <a href="http://www.mysticcoders.com/blog/2009/03/09/5-days-of-wicket/" title="5 Days of Wicket" target="_top">5 Days of Wicket</a>, youâ€™ll love this in-depth hour and drop us a line if your company needs training in wicket: <a href="mailto:trainings@mysticcoders.com">trainings@mysticcoders.com</a></strong>

