---
title: Into The Multi-cloud
date: 2024-10-21 00:00:00 Z
categories:
- Cloud
summary: Cloud computing is a major part of modern day software development. Big cloud
  providers work hard to get your business and keep it but there is a school of thought
  which says we shouldn't put all our eggs in one cloud shaped basket. In this post
  I'll take a look at the concept of the multi-cloud and ask if it's something we
  should consider more often when designing software solutions.
author: dogle
image: "/uploads/Into%20the%20multi-cloud.png"
---

## Introducing, the Multi-cloud

<img src="{{site.baseurl}}/dogle/assets/multicloud/sm.jpg" alt="Spiderman" title="With great cloud provider comes great problems" style="display: block; margin: 1rem auto;" />

Today most software projects utilise some form of cloud computing to host their infrastructure, often choosing one of the big three providers, Google (GCP), Microsoft (Azure) or Amazon (AWS). What I think is less common, is to consider multiple cloud providers when looking to host resources although it is something which is mentioned from time to time. For example, Monzo has taken steps to address resiliency though running a [live alternative system](https://www.linkedin.com/posts/joemerriman1989_monzo-banks-multi-cloud-environment-is-now-activity-7196510667674132481-7o5y/) in an alternate cloud provider. DataStax provides services to help deploy the [Cassandra database across multi-cloud](https://www.datastax.com/blog/datastax-helps-enterprises-achieve-multi-cloud-deployments-open-source-apache) and [MongoDB's Atlas also facilitates multi-cloud deployment](https://www.mongodb.com/resources/basics/multicloud). Solutions and implementations are out there then, but what is the problem that we're solving here and why would we bother?

## Why Multi-cloud?

<img src="{{site.baseurl}}/dogle/assets/multicloud/pota.jpg" alt="Planet Of The Apes final scene" title="You blew it up! Damn you! Goddamn you all to hell!" style="display: block; margin: 1rem auto;" />

One argument is that multi-cloud deployment provides better resiliency. If a cloud provider such as AWS were to go down for whatever reason, we can mitigate the risk by placing some of our resources with other providers. Sounds great on the face of it, however is it really a concern? Distributing resources across multi-cloud is hard as a rule, that's why companies such as DataStax provide solutions to help. Vendors such as AWS go out of their way to make sure it's easy to keep everything in their cloud and as it's not a common use case to split across multiple clouds, there is not much documentation out there on how to do it. Add to that providers like AWS are huge, if you've followed best practice and built in redundancy and resilience into your resources by spreading them across multiple regions and availability zones, the odds of the whole thing going down are incredibly small. Even if it did happen for whatever reason, it's more than likely most other things would be down as well and you would have bigger issues. So it seems overall there isn't much value right now in distributing over multiple clouds from a resilience perspective. It's most likely going to be hard and the use-case for needing it is highly unlikely. There are however other reasons to consider a multi-cloud approach...

## Vendor Lock-in

<img src="{{site.baseurl}}/dogle/assets/multicloud/li.jpg" alt="Lock inn pub sign" title="You can check out any time you like, but you can never leave" style="display: block; margin: 1rem auto;" />

Vendor lock-in is fast becoming a real issue. Over the last few years the three big players in Cloud Computing (AWS, GCP, Azure) have dominated the market with AWS especially taking the lead. The problem is of course, once you're in, it's very hard to get out again. Cloud providers are falling over themselves to help you move into the cloud via all sorts of solutions from software to literally driving a truck up to your office full of hard drives.

<img src="{{site.baseurl}}/dogle/assets/multicloud/aws-snowmobile.jpg" alt="AWS Snowmobile" title="AWS Snowmobile" style="display: block; margin: 1rem auto;" />

What they are less keen on, unsurprisingly, is helping you move your resources out of their particular cloud. As governments have moved public services into the cloud as part of digital strategies [the issue of vendor lock-in has been raised as a risk](https://www.theregister.com/2024/04/04/uk_cddo_admits_cloud_spending_lock_issues_exclusive/). It's costly and difficult to move out of a cloud provider once you're there. To be clear this isn't an accident or unavoidable necessity of working in the cloud, cloud providers make efforts to ensure that their home-grown services are easier to use and provide a greater range of built in functionality than other services they offer and they make sure to promote these services as much as possible. As an example, when studying for an AWS exam recently I couldn't help but notice that many of the questions in the exam were tailored to promote AWS native services or around how you might move your existing infrastructure into AWS easily.

You could make a concerted effort from the start to not let yourself get too dependant on a single cloud provider's offering, steering clear of SaaS offerings native to a given cloud provider and trying to use more generic resources common to all providers. The problem is that even these common resource are not truly generic. An Amazon EC2 instance is not configured in the same way as an Azure Virtual Machine (AVM), even if the end result is similar. This means that while technically, if you had to, you could recreate everything in a different provider, the cost of doing so in real terms would be huge. It would be a massive undertaking as you can't just drag and drop existing architecture into a different cloud, but would need to rebuild from scratch each resource. It would be much too costly to be a consideration in anything but the most extreme case.

What's the problem though? If a given cloud provider supplies redundancy over multiple regions and data centers, security, is easy to use, blazingly fast and has highly configurable resources then why not go all in? It isn't just that cloud providers can charge whatever they want once you're locked in and have a strangle-hold on competition, there are other concerns here as well. What if you discover a provider is allowing foreign governments to access your data? What if they are involved with companies and practices you don't agree with ethically? What if they don't align with your environmental standards and aspirations? Even if you do hit the eject button and put the time and money into recreating all your infrastructure in another cloud, you would do so only to find yourself back in the same problem somewhere else.

## Breaking Free

<img src="{{site.baseurl}}/dogle/assets/multicloud/I_Want_To_Break_Free.jpg" alt="Queen band" title="I want to break free" style="display: block; margin: 1rem auto;" />

What then can we do? [Infrastructure as Code](https://aws.amazon.com/what-is/iac/#:~:text=Infrastructure%20as%20code%20(IaC)%20is,%2C%20database%20connections%2C%20and%20storage.) (IAC) is a practice which may help us out here in the longer term. It allows us to define resources in a given programming language and commit it as code. Resources are defined in detail and we can either deploy or tear-down these resources with a single command. This, in my opinion, starts down the right path to helping solve the problem of vendor lock-in. The main drawback however is, currently the IAC we would write for one cloud provider is not the same as for another. What we could do to work around this is develop resources for multiple clouds in tandem, e.g. if implementing a virtual machine, we could write it out as both an EC2 and AVM resource, this incurs a time cost but allows us to keep essentially a clone of our infrastructure for different cloud providers. That might work well enough with a smaller set of infrastructure, for larger projects this starts to become unmanageable and of course, unless we are deploying and using the codebase for each cloud provider we would have little confidence in it working when we need it.

A step further would be to provide an abstraction on top of the current APIs. Tools like [Terraform](https://www.terraform.io/) allow us to write infrastructure in their own [custom language](https://developer.hashicorp.com/terraform/language). As we've discussed, the implementation of an EC2 and AVM are different, if we had an abstraction which allowed us to define a generic virtual machine definition which could be transformed programmatically under the hood to EC2 or AVM dependant on user preference, that would allow us, theoretically, to write the code once and deploy to any cloud provider. Following this thought to conclusion, in an ideal world you would write your IAC once, select your cloud provider and hit deploy.

A step further again would be for cloud providers to adhere to a common API as a standard, removing the need to transform our IAC to the correct structure for the given provider upfront. Efforts such as the [Finos Common Cloud Controls Project](https://blog.scottlogic.com/2024/09/23/intro-finos-ccc.html) are moving in this direction while specifically looking at compliance issues for the finance sector, but in my opinion, until cloud providers are forced to conform to a standard API for deployment of common resources they will continue to be able to hold all those using their services, from governments to banks, over a barrel.

## Conclusion

<img src="{{site.baseurl}}/dogle/assets/multicloud/sunrise.jpg" alt="Sunrise" title="A new dawn" style="display: block; margin: 1rem auto;" />

Cloud computing has come a long way in the last few years, today one of the first things to consider when looking to get a new project off the ground is which cloud provider you will use to host your infrastructure. Providers are keen to lure as many projects as they can to their service offering free tier resources, official training and certifications with an ever expanding list of services and tools to make things as simple as possible. The main providers in this space have grown so big that they are arguably able to provide enough resilience by themselves for most use cases without the need to consider multi-cloud. What is important in my opinion is not the ability to deploy resources to multiple clouds but rather the ability to deploy to any cloud provider. That is, the real challenge is how we push back against the ever growing issue of vendor lock-in and create a landscape where users can swap out their cloud provider as easily as they might change their internet provider.
