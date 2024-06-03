Title: From X25 to Kubernetes: Key Takeaways from AutoCon1
Slug: autocon1
Date: 2024-06-03 20:00
Author: ubaumann


While I'm more comfortable with technical content, I'd like to share my insights from the recent AutoCon1 conference on network automation, with some help from Grammarly and ChatGPT. First and foremost, thank everyone who made this forum possible, from the presentations and discussions to the creative boost provided by the beer.

The forum asked: "Why haven't we seen full adoption of network automation yet?" After attending my second conference, I asked myself: Are we any closer to achieving this? Unfortunately, not really. Don't get me wrong, we are a passionate, constructive, and highly motivated group. However, the network sector progresses exceptionally slowly.
I won't delve into the reasons for this sluggishness, but I was surprised to learn that a US ISP still provides X25, T1, Frame Relay, and other services while managing over 100 different platforms. In budget planning, the network often gets overlooked. Due to our responsibility for delivering critical infrastructure, we usually find ourselves pleading for change windows. This generalisation doesn't apply to everyone, of course, but take my words with a grain of salt.

At AutoCon, experienced professionals with a decade or more of expertise convene alongside newcomers embarking on their journey. Here's a concise summary of some key takeaways from the forum for beginners: "Don't be afraid of failing," "It is a journey," "Just start," "Take advantage of crises," "Kill your babies," and "Have a vision, set achievable goals."
(For context, the advice to "kill your babies" comes from Robin Gilijamse, who explained that sometimes it's necessary to let go of a product you've developed and move on. For more details, check out the video of his presentation, which will be available in 2 or 3 weeks. However, I want to point out to management that replacing a tool with hundreds of engineering hours covering countless edge cases with Function As A Service (FaaS) over a weekend can be challenging.)


One key takeaway for me is the need to make it easier for beginners to enter the network automation field. The wide variety of tools and possibilities can be overwhelming. I also reaffirmed my belief in the value of open source. Sharing best practices and more technical success stories could be incredibly beneficial.

What else would help? If you have any ideas, please let me know. I want to spread one crucial message: "It does not need to be 100% perfect to be shared," and I try to demonstrate this every time I upload a video, write a post, or start a new project (so any imperfections you find are intentional). As we all love open source, it is vital to contribute to projects. Would anyone be interested in watching a live stream or a session recording where I write code and contribute to an open-source project without a clear agenda? Just thinking aloud.


## Unicorns

A key point of debate was the claim that proficient network engineers cannot thrive as software engineers and that those who do are rare 'unicorns.' I strongly disagree. If this were true, I would be incredibly fortunate, as I've encountered many such unicorns in my career. 
Is it impossible for a top chef to be an excellent baker? Can't a Michelin chef also be a brilliant baker? When we examine the fundamental skills of network and software engineers, they align quite well: problem-solving, creative solution-finding, logical thinking, and many more.
Could I pass the CCIE written exam tomorrow without studying? - No; Could I configure the CCIE R&S v5 Lab in 5 hours? I could definitely do it in 2 days (and faster with automation). Whether this defines a good network engineer is another big question I want to avoid delving into here.
Is a brilliant C++ developer necessarily a brilliant Haskell programmer? Not necessarily, but they have the prerequisites to learn it quickly. My brain capacity is definitely limited, and maybe sometimes I hallucinate, thinking I saw, read, or experienced something but can't prove it afterwards. We all are getting older.

However, my point is that I have met many engineers who are excellent in both network engineering and software engineering. This doesn't mean everyone needs to be brilliant in both domains. We will always need engineers specialised in network architecture and technologies, and vice versa. But, I want to encourage every network or software engineer to believe that it is possible to be good in both areas if they want to.


## KubeNet

Wim Henderickx from Nokia presented the launch of [KubeNet](https://learn.kubenet.dev/), a community-driven initiative to help network engineers understand the vast ecosystem surrounding Kubernetes and how it can benefit network automation. I highly appreciate this effort and am convinced that Kubernetes can significantly boost our progress. Wim puts his heart and soul into this project, but it can only thrive if many join the community, support each other, and create fantastic content.

Want to see a real-life use case? I recommend watching the video ["How We Are Moving from GitOps to Kubernetes Resource Model in 5G Core"](https://www.youtube.com/watch?v=crmTnB6Zwt8) from the last KubeCon.


## Standards

Standards and YANG models were a hot topic at AutoCon. It's important to avoid mixing different types of standards. For example, when discussing models/schemas, we shouldn't bring protocols into the discussion. So, when talking about OpenConfig models, we should not mix in points regarding NetConf or gRPC. Each layer should be addressed separately.
We have and need standards for communicating with devices (e.g., CLI over Telnet or SSH, SNMP over UDP or TCP, NetConf RPCs over SSH or gRPC). At the encoding level, we also have a variety of options, though modern interfaces often use verbose formats like XML and JSON or binary-encoded options like ProtoBuf or CBOR.

At the schema layer, where we describe models and structure the data we send or receive, we need two distinct discussions: one about the language itself and the other about the defined models. Schema languages include XSD (widely used for SOAP), YANG (inspired by XSD and used for NetConf, RestConf, and gRPC for network devices), and JSONSchema (used by OpenAPI 3.1 and commonly in REST APIs, especially when interacting with third-party systems).
For me, the key is having a comfortable way to work with data schema languages within a robust ecosystem. Unfortunately, YANG has advanced features that make auto-generating SDKs and working with it difficult. Unless the industry demands better solutions, we won't see much change. Remember, the hardware we install now will likely run for a long time.
Regarding standardised models for device communication, we need to be realistic. SNMP, CLI, OpenConfig, and IETF YANG models have shown that it's not practical to have one model that rules them all. Even different platforms or NOS versions will have slight deviations in the models, necessitating a translation layer. Therefore, we should focus on making schema handling more user-friendly.
One point to consider: Why is YANG not easily consumable? Why do so many render the XML payload for NetConf with jinja2 and treat it like a text file? Is this really the preferred method?


On the abstraction layer, where we model the intent of the network, a unified approach seems more feasible. We could translate and normalise data from device APIs into abstract models. The IETF is working on many YANG models, such as in the areas of Digital Map and Digital Twin. For more details, check out this IETF draft.
Creating the abstract data model for an automation task is one of the hardest challenges. It would be helpful to provide best practices and a collection of models. Generating a model that fits 80% of needs is possible but requires extensive work and must be extendable for edge cases. We need to work as a community toward this goal and closely examine and contribute to the IETF's efforts.


## SSoT

As usual, the Single Source of Truth (SSoT) was another big topic. I don't want to delve into details, but I want to highlight a few recurring points. One issue is that, like many buzzwords, interpretations of SSoT vary widely. We need to be clearer about what we are discussing.
I believe we should focus more on a declarative approach to describing our networks and the intended functions they must fulfill. Additionally, we should expand our focus beyond OSI layers 1-4 to include applications and services. Do we really need to define the VLAN in our abstract network automation model?

While I can't argue against the statement, "A greenfield network will be a brownfield network next week," it's imperative that we shift our mindset. Rather than striving to discover and update every aspect of our management systems, we must reconsider our approach. However, this shift in thinking will undoubtedly face resistance, particularly from software vendors whose primary business involves navigating the complexities created by network engineers over time. Unless we alter our approach to a declarative approach, we risk impeding the progress of automation and perpetuating the question: "Why haven't we seen full adoption of network automation yet?"

As the bus driver at our social event wisely said, "If you look to your right, you will not see something on the left." Let's keep looking in all directions to find new solutions for network automation.
