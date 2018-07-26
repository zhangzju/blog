## GDPR 概述

通用数据保护条例(General Data Protection Regulation, GDPR) 是欧盟针对Cambridge Analytica使用Facebook数据丑闻以及类似事件制定的旨在保护欧洲公民的个人用户数据(personal data)，防止被服务提供者滥用，泄露而修订的严格法令，限制了服务者对用户数据的使用，存储和传输。

## GDPR 主要更新

通用数据保护条例在1995年已经实时生效，目前我们所指的GDPR主要是指的2016年通过，2018年5月5日开始实施的修订更新。此次更新的主要内容包括有：

1. 数据泄露通知

针对数据泄露的通知。在GDPR框架下，服务者必须在72小时内告知所有泄露数据的用户，他们的数据被泄露以及相对应的风险和他们被侵犯的权利有哪些，后果是什么。对于数据处理服务者，则需要在得知数据泄露之后不能够有任何延迟地告知客户。

 2. 数据获取权利

 服务者在必须在取得用户同意的情况下才能处理其数据，换言之，用户必须知晓自己的数据以及哪些数据被处理。服务者需要免费提供一份正在处理的用户数据的拷贝给用户，一份之外的可以收取费用。

 3. 被遗忘权利

 用户有权要求删除服务者存储的用户数据，除五种特别情形外，服务者不得在用户撤销许可的情况下存储任何用户数据。

 4. 数据可移植性

 用户可以以有序的，机器友好的，不侵犯他人权利的方式对其存储在服务者一方的数据进行移植处理，在技术可行的情况下，服务者需要协助用户将数据迁移到其他适用的地方。

 5. 隐私条款设计

  在隐私条款设计中，服务者必须阐释清楚所需要和存储，处理的数据的原因。服务者只能获取对于服务所必须的数据，同时需要限制服务者获取对于数据处理过程中不需要的数据的获取。

 6. 数据保护专员

 此项变更主要是要求服务者需要有专门负责用户数据保护这项工作的员工并且需要达到一定的比例。

 上面是GDPR此次生效的修订的核心更改，除此之外，还有一些处罚措施，使用范围，概念解释等等内容。

 ## GDPR 的影响

GPDR对于软件开发的影响主要涉及对于用户数据的保存和处理上，GDPR对于“用户数据”的定义的原文是：

> The GDPR applies to ‘personal data’ meaning any information relating to an identifiable person who can be directly or indirectly identified in particular by reference to an identifier. This definition provides for a wide range of personal identifiers to constitute personal data, including name, identification number, location data or online identifier, reflecting changes in technology and the way organisations collect information about people.

其中需要注意的点是，用户数据指的是能够关联到用户个人的数据，包括登陆账号的用户名和密码，位置信息，行为习惯以及组织关系等等。

对于嵌入式设备来说，涉及到的用户数据非常少，最为直观的就是**用户登陆数据，即用户名和密码**，在用户登陆之前进行提示，同意使用用户的用户名和密码，即可符合GDPR对此部分的要求。

GDPR对服务者的分类进行了区别，分为数据加工者和数据控制者（**data processor and a data controller**），两者的区别的原文是：

> A controller is the entity that determines the purposes, conditions and means of the processing of personal data, while the processor is an entity which processes personal data on behalf of the controller. 

对于嵌入式设备来说，数据并没有上传到云端服务器，也没有任何额外的处理，仅仅是为了确认用户身份而必须进行的数据获取，并没有角色差异。

如附录三所提及的针对GPDR的checklist条款，绝大部分条款由于我们的设备数据不会传送到云端服务器而无需考虑。

需要注意的是，软件开发人员了解GPDR条款也是GPDR本身要求的一条，并且被写入到Checklist中。

除此之外，数据在设备上的存储，处理数据时的关键信息的保护也是对GDPR关于数据防止泄露条款的补充操作。

 ## 参考文献

  1. [https://www.eugdpr.org/key-changes.html](https://www.eugdpr.org/key-changes.html)
 2. [https://www.eugdpr.org/gdpr-faqs.html](https://www.eugdpr.org/gdpr-faqs.html)
 3. https://gdprchecklist.io/