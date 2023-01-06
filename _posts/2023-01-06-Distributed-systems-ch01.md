---
layout: post
title: 第一章 简介
categories: Distributed-Systems
tags: [Distributed-Systems]
---

## 一、简介

这个关于分布式系统的8讲课程构成了《并发和分布式系统》的后半部分。虽然前半部分侧重于在同一台计算机上运行的多个进程或线程之间的并发性，但后半部分通过研究由多台通信计算机组成的系统来进一步深入。

单台计算机上的并发也称为共享内存并发（shared-memory concurrency），因为在同一进程中运行的多个线程可以访问同一地址空间。因此，数据可以轻松地将数据从一个线程传递到另一个线程：对一个线程有效的变量或指针对另一个线程也有效。

当我们转向分布式系统时，这种情况会发生变化。我们在分布式系统中仍然具有并发性，因为不同的计算机可以并行执行程序。然而，我们通常没有共享内存，因为分布式系统中的每台计算机都在自己的地址空间中，使用该计算机内置的内存运行自己的操作系统。不同的计算机只能通过网络相互发送消息进行通信。

（一些超级计算机和研究系统中存在有限形式的分布式共享内存，并且有远程直接内存访问(remote direct memory access, RDMA)等技术允许计算机通过网络访问彼此的内存。此外，数据库在某种意义上可以被视为共享内存，但与字节可寻址内存（byte-addressable memory）相比具有不同的数据模型。但是，从广义上讲，大多数实用的分布式系统都是基于消息传递的。）

分布式系统中的每台计算机都称为节点（node）。在这里，“计算机”的解释非常广泛：节点可能是台式计算机、数据中心的服务器、移动设备、联网汽车、工业控制系统、传感器或许多其他类型的设备。在本课程中，我们不区分它们：节点可以是任何类型的通信计算设备。

> #### 分布式系统是：
>
> * “…一个系统，其中一台您甚至不知道其存在的计算机出现故障，可能导致您自己的计算机无法使用”——Leslie Lamport
> * 通过网络通信的多台计算机
> * 试图一起完成一些任务
> * 由“节点”（计算机、电话、汽车、机器人……）组成
> 
> ![](../../../../assets/images/distributed-system/slide1.png)
>
> **slide 1**

### 1.1 关于分布式系统

这些笔记和讲座录音应该是独立的，但如果你想阅读更多细节，有几本推荐的教科书：

* Maarten van Steen and Andrew S. Tanenbaum. Distributed Systems. ISBN 978-1543057386。下载地址https://www.distributed-systems.net/index.php/books/ds3/ (third edition, 2017)。本书对广泛的分布式系统主题进行了广泛的概述，并提供了许多来自实际系统的示例。
* Christian Cachin, Rachid Guerraoui, and Lu´ıs Rodrigues.Introduction to Reliable and Secure Distributed Programming. Second edition, Springer, 2011. ISBN 978-3-642-152597. Ebook download for Cambridge users: https://link.springer.com/book/10.1007/978-3-642-15260-3 then click Log in → via your Institution → type University of Cambridge → log in with Raven.T This book is more advanced, going into depth on several important distributed algorithms, and proving their correctness. Recommended if you want to explore the theory in greater depth than this course covers.
* Martin Kleppmann. Designing Data-Intensive Applications, O’Reilly, 2017. ISBN978-1449373320. This book goes more in the direction of databases, but also covers a number of distributed systems topics. It is designed for software engineers in industry working with distributed databases.
* Jean Bacon and Tim Harris. Operating Systems: Concurrent and Distributed Software Design. Addison-Wesley, 2003. ISBN 978-0321117892

在适当的情况下，这些讲义还包含对研究论文的参考和其他有用的背景阅读（这些在方括号中给出，详细信息出现在本文档的末尾）。但是，只有讲义和视频中涵盖的材料是可以检查的。

>#### 推荐阅读
>
>* Van Steen & Tanenbaum.“Distributed Systems” (anyed), free ebook available
>* Cachin, Guerraoui & Rodrigues.“Introduction to Reliable and Secure Distributed Programming”(2nd ed), Springer 2011
>* Kleppmann.“Designing Data-Intensive Applications”,O’Reilly 2017
>* Bacon & Harris.“Operating Systems: Concurrent and Distributed Software Design”, Addison-Wesley 2003
>
>**Slide 2**

至于其他课程，过去的试题可以在https://www.cl.cam.ac.uk/teaching/exams/pastpapers/t-ConcurrentandDistributedSystems.html 找到。本课程的教学大纲、幻灯片和讲义在2020/21年进行了大幅更新和修订。由于教学大纲变更，以下历年考题不再适用：2018年P5 Q8；2015年P5 Q8；2014年P5 Q9(a)；2013年P5 Q9；2011年P5 Q8(b).

这些笔记还包含练习，这些练习是建议在监督中讨论的材料。课程网页上提供了针对监考老师的解决方案说明。

本课程与tripos中的其他几门课程相关，如Slide 3所示。

>#### 与其他课程的关系
>
>* 并发系统——Part IB（每一个分布式系统都是并发的）
>* 操作系统——Part IA（进程间通信，调度）
>* 数据库——Part IA（许多现代数据库是分布式的）
>* 计算机网络——Part IB Lent term（分布式系统涉及网络通信）
>* FurtherJava——Part IB Michaelmas（分布式编程实战练习）
>* 安全——Part IB Easter term（具有加密和身份验证的网络协议）
>* 云计算——Part II（处理大量数据的分布式系统）
>
>**Slide 3**

创建分布式系统有很多原因。一些应用程序本质上是分布式的：如果你想从你的手机向你朋友的手机发送消息，该操作不可避免地需要这些手机通过某种网络进行通信。

一些分布式系统可以做一些原则上单台计算机可以做的事情，但它们做起来**更可靠**（more reliably）。单台计算机可能会发生故障并且可能需要不时重新启动，但如果你使用多个节点，则一个节点可以在另一个节点重新启动时继续为用户提供服务。因此，分布式系统有可能比单台计算机更可靠，至少在设计良好的情况下（与Lamport在Slide1中的引述有些矛盾）！

分布式的另一个原因是为了**更好的性能**（better performance）：如果一项服务的用户遍布世界各地，并且他们都必须访问一个节点，那么英国或（和）新西兰的用户会发现它很慢。通过将节点放置在世界各地的多个位置，我们可以通过将每个用户路由到附近的节点来解决光速缓慢的问题。

最后，一些大规模的数据处理或计算任务实在太大而无法在一台计算机上执行，或者会慢得无法忍受。例如，CERN的大型强子对撞机由全球计算基础设施提供支持，该基础设施具有100万个CPU内核用于数据分析，以及1艾字节（$10^{18}$字节）的存储空间！请参阅https://wlcg-public.web.cern.ch/。

>#### 为什么要做分布式系统？
>
>* 它本质上是分布式的：例如从您的手机发送消息到您的朋友的电话
>* 为了更好的可靠性：即使一个节点发生故障，整个系统仍然保持运作
>* 为了获得更好的性能：从附近的节点而不是地球另一端的节点获取数据
>* 解决数据量大的问题：海量数据，一台机器装不下
>
>**Slide 4**

然而，分布式系统也有缺点，因为事情可能会出错，而系统需要处理这样的故障。网络可能会出现故障，导致节点无法通信。

>![](../../../../assets/images/distributed-system/slide5.png)
>
>**Slide 5**

另一件可能出错的事情是节点可能崩溃，或者运行速度比平时慢得多，或者以其他方式出现异常（可能是由于软件错误或硬件故障）。如果我们希望一个节点在另一个节点崩溃时接管，我们需要检测崩溃是否已经发生；正如我们将看到的，即使那样也不是直截了当的。网络故障和节点故障随时可能发生，没有任何警告。

在一台计算机中，如果一个组件出现故障（例如，其中一个RAM模块出现故障），我们通常不希望计算机继续工作：它将来只可能会崩溃。不需要以明确处理故障RAM的方式编写软件。然而，在分布式系统中，我们通常确实希望容忍系统的某些部分被破坏，而让其余部分继续工作。例如，如果一个节点崩溃（部分故障，a partial failure），其余节点可能仍然能够继续提供服务。

如果系统的一个组件停止工作，我们称之为故障（fault），许多分布式系统努力提供容错能力（fault tolerance）：也就是说，尽管出现故障，整个系统仍继续运行。与对单个计算机进行编程相比，处理故障是分布式计算的根本区别，而且通常更难。一些分布式系统工程师认为，单台计算机上解决一个问题基本上是很容易！不过，为了公平起见，我们在计算机科学其他领域的同事们可能并不这么认为。

>#### 为什么不做分布式系统？
>
>分布式系统存在一些问题：
>* 通信可能中断（甚至我们都没意识到通信已断开）
>* 进程可能会崩溃（我们也可能不知道）。
>* 所有这些都可能不确定地发生。
>
>**容错（Fault tolerance**）：即使某些部分出现故障，我们仍希望整个系统能够继续工作。
>这是很难的。
>编写在单台计算机上运行的程序是比较容易的？！
>
>**Slide 6**

### 1.2 分布式系统和计算机网络

在研究分布式系统时，我们通常使用硬件的高级抽象。

>#### 分布式系统与计算机网络
>
>我们使用一个通信的简单抽象：  
>
><img src="../../../../assets/images/distributed-system/slide7.png" style="zoom: 25%;" />                
>
>现实要复杂得多：
>* 各种网络运营商：eduroam、家庭DSL、蜂窝数据、咖啡店wifi、海底电缆，卫星。
>* 物理方式通信：电流、无线电波、激光、货车中的硬盘驱动器。..
>
>**Slide 7**

在本课程中，我们只是假设一个节点可以通过某种方式向另一个节点发送消息。我们并不特别关心该消息是如何在物理上表示或编码的，即网络协议，非正式地称为线上字节（bytes on the wire）——因为即使是使用特定的网络技术进行传输，发送和接收消息的基本原则是保持不变。“电线”实际上可能是无线电波、激光、某人口袋中的U盘，甚至是货车中的硬盘。

>#### 货车中的硬盘？！
>
><img src="../../../../assets/images/distributed-system/slide8.png" alt="https://docs.aws.amazon.com/snowball/latest/ug/using-device.html" style="zoom:33%;" />
>       
>高延迟，高带宽！
>
>**Slide 8**

事实上，如果你想发送一个非常大的消息（假设几十TB），通过互联网发送数据会很慢，实际上将数据写入一堆硬盘驱动器，用一辆货车装载，并将它们送往目的地。但从分布式系统的角度来看，传递消息的方法并不重要：我们只看到一个抽象的通信通道，它具有一定的延迟（从消息发送到接收的延迟，latency）和带宽（单位时间内可以传输的数据量，bandwidth）。

计算机网络课程（Part IB Lent term）侧重于使消息能够到达目的地的网络协议。分布式系统建立在此之上，但是关注的是多个节点应如何协调以实现某些共享任务。分布式算法的设计是关于决定发送什么消息，以及在收到消息时如何处理这些消息。

>#### 延迟和带宽
>
>**延迟**：消息到达之前的时间
>* 在同一建筑物/数据中心：≈1毫秒
>* 一个大陆到另一个大陆：≈100毫秒
>* 货车中的硬盘驱动器：≈1天
>
>**带宽**：单位时间内的数据量
>* 3G蜂窝数据：≈1Mbit/s
>* 家庭宽带：≈10Mbit/s
>* 货车中的硬盘驱动器：50TB/箱≈1Gbit/s
>（非常粗略的数字，在实践中差异很大！）
>
>**Slide 9**

Web是每天使用的分布式系统的一个示例。

><img src="../../../../assets/images/distributed-system/slide10.png" style="zoom: 33%;" />
>
>**Slide 10**

>客户端—服务端实例：Web
>时间从上到下流动。
>
><img src="../../../../assets/images/distributed-system/slide11.png" style="zoom: 50%;" />
>
>**Slide 11**

在网络中有两种主要类型的节点：服务器托管网站，显示它们的客户端（网络浏览器）。当您加载网页时，您的网络浏览器会向相应的服务器发送HTTP请求消息。收到该请求后，Web服务器将包含页面内容的响应消息发送给请求它的客户端。这些消息通常是不可见的，但我们可以使用Charles(https://www.charlesproxy.com/)等工具捕获和可视化网络流量，如Slide 12所示。讲座视频包括该软件的实际演示。

><img src="../../../../assets/images/distributed-system/slide12.png" style="zoom:50%;" />
>
>**Slide 12**

在URL中，//和后面的/之间的部分是客户端要向其发送请求的服务器的主机名（例如www.cst.cam.ac.uk） ，其余部分（例如/teaching/2122/ConcDisSys)是客户端在其请求消息中要求的路径。除了路径之外，请求还包含一些额外的信息，例如HTTP方法（例如GET加载页面，或POST提交表单），客户端软件（用户代理）的版本，以及客户端理解的文件格式列表（the accept header）。响应消息包含请求的文件及其文件格式（the content-type）的指示符；在网页的情况下，这可能是HTML文档、图像、视频、PDF文档或任何其他类型的文件。

由于请求和响应可能大于单个网络数据包的容量，因此HTTP协议在TCP之上运行，它将大块数据分解为小网络数据包流（参见Slide13），并将它们在收件人处汇合到一起。（TCP将在计算机网络课程中详细讨论。）HTTP还允许通过单个TCP连接发送多个请求和多个响应。然而，从分布式系统的角度来看这个协议时，这些细节并不重要：我们将请求视为一条消息，将响应视为另一条消息，而不管传输它们所涉及的物理网络数据包的数量。这使事物独立于底层网络技术。

**练习1.** *TCP连接允许两个节点相互发送任意长的字节序列。您决定要通过同一个TCP连接发送多个请求和响应。为了使用TCP实现这样的请求-响应协议，您需要做什么？*

><img src="../../../../assets/images/distributed-system/slide13.png" style="zoom: 67%;" />
>
>**Slide 13**

### 1.3 例子：远程过程调用（RPC）

>#### 客户端-服务器实例：在线支付
>
><img src="../../../../assets/images/distributed-system/slide14.png" style="zoom:50%;" />
>
>**Slide 14**

日常分布式系统的另一个例子是当你使用信用卡/借记卡在线购买东西时。当您在某个在线商店输入您的卡号时，该商店将通过Internet向专门处理卡支付的服务发送支付请求。

支付服务又与Visa或Master Card等卡进行网络通信，后者与发卡银行通信以接受付款。

对于实现在线商店的程序员来说，处理支付的代码可能类似于Slide 15中的代码。

>#### 远程过程调用（RPC）示例
>
>```java
>// 在线商店处理客户卡的详细信息
>Card card = new Card();
>card.setCardNumber("1234 5678 8765 4321");
>card.setExpiryDate("10/2024");
>card.setCVC("123");
>
>Result result = paymentsService.processPayment(card, 3.99, Currency.GBP); // 本函数实现在其他节点上！
>
>if (result.isSuccess()) {
>    fulfilOrder();
>}
>```
>
>**Slide 15**

调用process Payment函数看起来就像调用任何其他函数，但实际上，在幕后发生的事情是商店向支付服务发送请求，等待响应，然后返回收到的响应。Process Payment的实际实现——与卡网络和银行通信的逻辑——并不存在于商店的代码中：它是支付服务的一部分，是在属于不同公司的另一个节点上运行的另一个程序。

这种类型的交互（其中一个节点上的代码似乎调用另一个节点上的函数），称为远程过程调用(RPC)。在Java中，它称为远程方法调用(RMI)。实现RPC的软件称为RPC框架或中间件。（并非所有中间件都基于RPC；还有使用不同通信模型的中间件。）

当应用程序希望调用另一个节点上的函数时，RPC框架会在其位置提供stub。stub与实际函数具有相同的类型签名，但它不执行实际函数，而是将函数参数编码为消息并将该消息发送到远程节点，请求调用该函数。对函数参数进行编码的过程称为编组（marshalling）。在Slide16的示例中，JSON编码用于编组，但在实践中也使用了各种其他格式。

><img src="../../../../assets/images/distributed-system/slide16.png" style="zoom: 67%;" />
>
>**Slide 16**

从RPC客户端到RPC服务器的消息发送可以通过HTTP进行（在这种情况下也称为Web服务），或者可以使用一系列不同的网络协议之一。在服务器端，RPC框架解组（解码）消息并使用提供的参数调用所需的函数。当函数返回时，同样的事情反过来发生：函数的返回值被编组，作为消息发送回客户端，由客户端解组，并由stub返回。因此，对于stub的调用者来说，函数看起来好像是在本地执行的。

>#### 远程过程调用（RPC）
>
>理想情况下，RPC使对远程函数的调用看起来与本地函数调用相同。
>
>**“位置透明，Location transparency”：**
>系统隐藏了资源所在的位置。
>
>在实践中：
>* 如果服务在函数调用期间崩溃怎么办？
>* 如果消息丢失怎么办？
>* 如果消息延迟怎么办？
>* 如果出现问题，重试是否安全？
>
>**Slide 17**

RPC的困难在于很多事情都可能出错，因为网络和节点可能会崩溃。如果客户端发送RPC请求但没有收到响应，则不知道服务器是否收到并处理了该请求。如果它在一段时间内没有收到回复，它可以重新发送请求，但这可能会导致请求被执行多次（例如，对信用卡收费两次）。即使我们重试，也不能保证重试的消息会发送成功。永远等待不是一个好方法，因此在实践中，客户端将不得不在超时后放弃。

**练习2.** *RPC与本地函数调用有何不同？是否可以实现位置透明？*

几十年来，已经开发出许多RPC变体，其目标是使分布式系统的编程变得更容易。这包括面向对象的中间件，例如1990年代的CORBA。然而，底层的分布式系统挑战仍然没有改变[Waldo等人,1994]。

>#### RPC历史
>
>* SunRPC/ONCRPC（1980年代，NFS的基础）
>* CORBA：面向对象中间件，20世纪90年代火爆
>* Microsoft的DCOM和Java RMI（类似于CORBA）
>* SOAP/XML-RPC：使用XML和HTTP的RPC（1998）
>* Thrift(Facebook,2007)
>* gRPC（谷歌，2015年）
>* REST（通常使用JSON）
>* Web浏览器中的Ajax
>
>**Slide 18**

今天，最常见的RPC形式是使用通过HTTP发送JSON数据实现的。此类基于HTTP的API的设计原则称为代表性状态传输（representational state transfer）或REST[Fielding，2000]，遵守这些原则的API称为RESTful。这些原则包括：

* 通信是无状态的（每个请求都是自包含的并且独立于其他请求），
* 资源（可以检查和操作的对象）由URL表示，并且
* 通过使用标准方法类型（例如POST或PUT）向对应的的URL发出HTTP请求来更新资源的状态。

REST的流行是因为在Web浏览器中运行的JavaScript代码可以很容易地发出这种类型的HTTP请求，如Slide19所示。在现代网站中，在不重新加载整个页面的情况下使用JavaScript向服务器发出HTTP请求是很常见的。这种技术有时称为Ajax。

>#### JavaScript中的RPC/REST
>
>```javascript
>let args = {amount: 3.99, currency: 'GBP', /*...*/ };
>let request = {
>	method:'POST',
>	body:JSON.stringify(args),
>	headers: {'Content-Type': 'application/json'}
>};
>
>fetch('https://example.com/payments', request)
>	.then((response) => {
>		if (response.ok) success(response.json());
>		else failure(response.status); // server error
>	})
>	.catch((error) => {
>		failure(error); // network error
>	});
>```
>
>**Slide 19**

Slide 19上的代码将参数args使用JSON.stringify()将它们编组为JSON，然后使用HTTP POST请求将它们发送到https://example.com/payments 。这会有三种可能的结果：服务器返回一个指示成功的状态码（在这种情况下，我们使用response.json()解组响应），或者服务器返回一个指示错误的状态码，或者请求失败，因为没有从服务器收到响应（很可能是由于网络中断）。在上述情况下，代码都会调用success()或failure()函数。

尽管RESTful API和基于HTTP的RPC起源于网络（客户端是在网络浏览器中运行的JavaScript），但它们现在也常用于其他类型的客户端（例如移动应用程序），或用于服务器到服务器通信。

>#### 企业系统中的RPC
>
>“面向服务的架构”（Service-oriented architecture，SOA）/“微服务”：
>
>将大型软件应用程序拆分为多个服务（在多个节点上）通过RPC进行通信。
>
>不同语言实现的不同服务：
>
>* 互操作性（interoperability）：数据类型转换
>* 接口定义语言（Interface Definition Language，IDL）：独立于语言的API规范
>
>**Slide 20**

这种服务器到服务器的RPC在大型企业中尤为常见，其软件系统过于庞大和复杂，无法在单台机器上以单个进程运行。为了管理这种复杂系统，它被分解为多个服务，这些服务由不同的团队开发和管理，甚至可以用不同的编程语言实现。RPC框架促进了这些服务之间的通信。

当使用不同的编程语言时，RPC框架需要转换数据类型，以便被调用的代码能够理解调用者的参数，函数的返回值也是如此。一个典型的解决方案是使用接口定义语言(IDL)来提供与语言无关的函数类型签名，这些函数可通过RPC使用。通过IDL，软件开发人员可以为每个服务及其客户端的相应编程语言自动生成编组/解组代码和RPC stubs。Slide 21演示了gRPC使用的IDL示例，称为Protocol Buffers。语言的细节对于本课程并不重要。

> #### gRPC IDL示例
>
> ```idl
> message PaymentRequest {
> 	message Card {
>         required string cardNumber= 1;
>         optional int32expiryMonth = 2;
>         optional int32expiryYear= 3;
>         optional int32CVC= 4;
> 	}
> 	enum Currency { GBP = 1; USD = 2; }
> 		
>     required Cardcard= 1;
>     required int64amount= 2;
>     required Currency currency = 3;
> }
> 
> message PaymentStatus {
>     required boolsuccess= 1;
>     optional string errorMessage = 2;
> }
> 
> service PaymentService {
> 	rpc ProcessPayment(PaymentRequest) returns (PaymentStatus) {}
> }
> ```
>
> **Slide 21**
