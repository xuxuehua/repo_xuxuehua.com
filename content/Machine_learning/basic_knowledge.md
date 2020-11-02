---
title: "basic_knowledge"
date: 2020-10-21 09:18
---
[toc]



# 机器学习

机器学习基本就是在已知的样本数据中寻找数据的规律，在未知的数据中找数据的关系。所以，这就需要一定的数学知识了，但对于入门的人来说，学好高数、线性代数、概率论、数据建模等大学本科的数学知识应该就够用了。



## 监督式学习（Supervised Learning）

需要提供一组学习样本，包括相关的特征数据以及相应的标签。程序可以通过这组样本来学习相关的规律或是模式，然后通过得到的规律或模式来判断没有被打过标签的数据是什么样的数据。



一种比较常见的监督式学习，就是从历史数据中获得数据的走向趋势，来预测未来的走向。比如，我们使用历史上的股票走势数据来预测接下来的股价涨跌，或者通过历史上的一些垃圾邮件的样本来识别新的垃圾邮件。



在监督式学习下，需要有样本数据或是历史数据来进行学习，这种方式会有一些问题。

- 如果一个事物没有历史数据，那么就不好做了。变通的解决方式是通过一个和其类似的事物的历史数据。我以前做过的需求预测，就属于这种情况。对于新上市的商品来说，完全没有历史数据，比如，iPhone X，那么就需要从其类似的商品上找历史数据，如 iPhone 7 或是别的智能手机。

- 历史数据中可能会有一些是噪音数据，需要把这些噪音数据给过滤掉。一般这样的过滤方式要通过人工判断和标注。举两个例子。某名人在其微博或是演讲上推荐了一本书，于是这本书的销量就上升了。这段时间的历史数据不是规律性的，所以就不能成为样本数据，需要去掉。同样，如果某名人（如 Michael Jackson）去世导致和其有关的商品销售量很好，那么，这个事件所产生的数据则不属于噪音数据。因为每年这个名人的忌日的时候出现销量上升的可能性非常高，所以，需要标注一下，这是有规律的样本，可以放入样本进行学习。





## 非监督式学习（Unsupervised Learning）

数据是没有被标注过的，所以相关的机器学习算法需要找到这些数据中的共性。因为大量的数据是没有被标识过的，所以这种学习方式可以让大量的未标识的数据能够更有价值。而且，非监督式的学习，可以为我们找到人类很难发现的数据里的规律或模型。所以，也有人将这种学习称为“特征点学习”。其可以让我们自动地为数据进行分类，并找到分类的模型。



一般来说，非监督式学习会应用在一些交易型的数据中。比如，有一堆堆的用户购买数据，但是对于人类来说，我们很难找到用户属性和购买商品类型之间的关系，而非监督式学习算法可以帮助我们找到之间的关系。比如，一个在某一个年龄段的女性购买了某种肥皂，有可能说明这个女生在怀孕期，或是某人购买儿童用品，有可能说明这个人的关系链中有孩子，等等。于是这些信息会被用作一些所谓的精准市场营销活动，从而可以增加商品销量。





# 资源推荐

学习机器学习有几个课是必需要上的。

- 吴恩达教授（Andrew Ng）在 [Coursera 上的机器学习课程](https://www.coursera.org/learn/machine-learning)非常棒。我强烈建议从此入手。对于任何拥有计算机科学学位的人，或是还能记住一点点数学的人来说，都非常容易入门。这个斯坦福大学的课程后面是有作业的，请尽量拿满分。另外，[网易公开课上也有该课程](http://open.163.com/special/opencourse/machinelearning.html)。
- 卡内基梅隆大学计算机科学学院汤姆·米切尔（Tom Mitchell）教授的机器学习课程 [英文原版视频和课件 PDF](http://www.cs.cmu.edu/~tom/10701_sp11/lectures.shtml) 。汤姆·米切尔是全球 AI 界顶尖大牛，在机器学习、人工智能、认知神经科学等领域卓有建树，撰写了机器学习方面最早的教科书之一[《机器学习》](http://item.jd.com/10131321.html)，被誉为入门必读图书。
- 加利福尼亚理工学院亚瑟·阿布·穆斯塔法（Yaser Abu-Mostafa）教授的 [Learning from Data 系列课程](http://work.caltech.edu/lectures.html) 。本课程涵盖机器学习的基本理论和算法，并将理论与实践相结合，更具实践指导意义，适合进阶。

除了上述的那些课程外，下面这些资源也很不错。

- YouTube 上的 Google Developers 的 [Machine Learning Recipes with Josh Gordon](https://www.youtube.com/playlist?list=PLOU2XLYxmsIIuiBfYad6rFYQU_jL2ryal) 。这 9 集视频，每集不到 10 分钟，从 Hello World 讲到如何使用 TensorFlow，非常值得一看。
- 还有 [Practical Machine Learning Tutorial with Python Introduction](https://pythonprogramming.net/machine-learning-tutorial-python-introduction/) 上面一系列的用 Python 带着你玩 Machine Learning 的教程。
- Medium 上的 [Machine Learning - 101](https://medium.com/machine-learning-101) 讲述了好多我们上面提到过的经典算法。
- 还有，Medium 上的 [Machine Learning for Humans](https://medium.com/machine-learning-for-humans)，不仅提供了入门指导，更介绍了各种优质的学习资源。
- [杰森·布朗利（Jason Brownlee）博士的博客](https://machinelearningmastery.com/blog/) 也是非常值得一读，其中好多的 “How-To”，会让你有很多的收获。
- [i am trask](http://iamtrask.github.io/) 也是一个很不错的博客。
- 关于 Deep Learning 中神经网络的学习，推荐 YouTube 介绍视频 [Neural Networks](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi)。
- 用 Python 做自然语言处理[Natural Language Processing with Python](http://www.nltk.org/book/)。
- 以及 GitHub 上的 [Machine Learning 和 Deep Learning](https://github.com/ujjwalkarn/Machine-Learning-Tutorials) 的相关教程列表。

此外，还有一些值得翻阅的图书。

- [《机器学习》](https://cs.nju.edu.cn/zhouzh/zhouzh.files/publication/MLbook2016.htm)，南京大学周志华教授著。本书是一本机器学习方面的入门级教科书，适合本科三年级以上的学生学习。为了照顾学习的进度，本书并不特别“厚”，让学生能在同时修多门课的情况下至多 2 学期时间内完成本书的学习。同时，这本书也非常方便其他对机器学习感兴趣的初学者快速学习入门知识。

本书如同一张地图一般，让读者能“观其大略”，了解机器学习的各个种类、各个学派，其覆盖面与同类英文书籍相较不遑多让。为了帮助读者尽可能多地了解机器学习，作者已试图尽可能少地使用数学知识。对于仅需对机器学习做一般了解的读者，阅读本书时也可以略过数学细节仅做概观，否则建议对相关基础知识稍作复习以收全功。

- [A Course In Machine Learning](http://ciml.info/)，马里兰大学哈尔·道姆（Hal Daumé III）副教授著。 本书讲述了几种经典机器学习算法，包括决策树、感知器神经元、kNN 算法、K-means 聚类算法、各种线性模型（包括对梯度下降、支持向量机等的介绍）、概率建模、神经网络、非监督学习等很多主题，还讲了各种算法使用时的经验技巧，适合初学者学习。此外，本书官网提供了免费电子版。
- [Deep Learning](http://www.deeplearningbook.org/)，麻省理工学院伊恩·古德费洛（Ian Goodfellow）、友华·本吉奥（Yoshua Benjio）和亚伦·考维尔（Aaron Courville）著。本书是深度学习专题的经典图书。它从历史的角度，将读者带进深度学习的世界。深度学习使用多层的（深度的）神经元网络，通过梯度下降算法来实现机器学习，对于监督式和非监督式学习都有大量应用。如果读者对该领域有兴趣，可以深入阅读本书。本书官网提供免费电子版，但不提供下载。实体书（英文原版或中文翻译版）可以在网上买到。
- [Reinforcement Learning](http://www.freetechbooks.com/reinforcement-learning-an-introduction-second-edition-draft-t1282.html)，安德鲁·巴托（Andrew G.Barto）和理查德·萨顿（Richard S. Sutton）著。本书是强化学习（Reinforcement Learning）方面的入门书。它覆盖了马尔可夫决策过程（MDP）、Q-Learning、Sarsa、TD-Lamda 等方面。本书作者是强化学习方面的创始人之一。强化学习（结合深度学习）在围棋程序 AlphaGo 和自动驾驶等方面都有着重要的应用。
- [Pattern Recognition and Machine Learning](https://www.amazon.com/Pattern-Recognition-Learning-Information-Statistics/dp/0387310738) ，微软剑桥研究院克里斯托夫·比肖普（Christoph M. Bishop）著。本书讲述模式识别的技术，包括机器学习在模式识别中的应用。模式识别在图像识别、自然语言处理、控制论等多个领域都有应用。日常生活中扫描仪的 OCR、平板或手机的手写输入等都属于该领域的研究。本书广受读者好评，是该领域一本不错的图书。



