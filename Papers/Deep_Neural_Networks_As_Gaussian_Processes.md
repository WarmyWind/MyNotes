# Deep Neural Networks As Gaussian Processes

人们很早就知道单层全连接神经网络在参数的先验是i.i.d时，在网络宽度趋于极限时等价于高斯过程（Gaussian Process，GP）。该文章得到了无限宽度**深度**神经网络与GP之间的确切等式关系。

## Introduction

深度神经网络是一种灵活的参数化模型，而GP则是一种非参数化模型。人们已经发现在网络宽度为无限时，两种方法存在对应关系。这种对应关系说明，如果我们选择无限宽度神经网络的假设空间，权重的i.i.d先验可以被对应的一个GP先验代替。

## Deep, Infinitely Wide Neural Networks Are Drawn From GPs
