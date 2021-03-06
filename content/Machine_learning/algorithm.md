---
title: "algorithm"
date: 2020-10-21 09:26
---
[toc]





# 监督式学习

有如下经典算法。

1. 决策树（Decision Tree）。比如自动化放贷、风控。
2. 朴素贝叶斯分类（Naive Bayesian classification）。可以用于判断垃圾邮件，对新闻的类别进行分类，比如科技、政治、运动，判断文本表达的感情是积极的还是消极的、人脸识别等。
3. 最小二乘法（Ordinary Least Squares Regression）。算是一种线性回归。
4. 逻辑回归（Logisitic Regression）。一种强大的统计学方法，可以用一个或多个变量来表示一个二项式结果。可以用于信用评分、计算营销活动的成功率、预测某个产品的收入等。
5. 支持向量机（Support Vector Machine，SVM）。可以用于基于图像的性别检测，图像分类等。
6. 集成方法（Ensemble methods）。通过构建一组分类器，然后根据它们的预测结果进行加权投票来对新的数据点进行分类。原始的集成方法是贝叶斯平均，但是最近的算法包括纠错输出编码、Bagging 和 Boosting。



# 非监督式的学习

有如下经典算法。

1. 聚类算法（Clustering Algorithms）。聚类算法有很多，目标是给数据分类。
2. 主成分分析（Principal Component Analysis，PCA）。PCA 的一些应用包括压缩、简化数据，便于学习和可视化等。
3. 奇异值分解（Singular Value Decomposition，SVD）。实际上，PCA 是 SVD 的一个简单应用。在计算机视觉中，第一个人脸识别算法使用 PCA 和 SVD 来将面部表示为“特征面”的线性组合，进行降维，然后通过简单的方法将面部匹配到身份。虽然现代方法更复杂，但很多方面仍然依赖于类似的技术。
4. 独立成分分析（Independent Component Analysis，ICA）。ICA 是一种统计技术，主要用于揭示随机变量、测量值或信号集中的隐藏因素。



