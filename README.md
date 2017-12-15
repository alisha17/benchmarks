## Performance Comparison between NVIDIA’s GeForce GTX 1080 and Tesla P100 for Deep Learning


### Introduction

This whitepaper aims at comparing two different pieces of hardware that are often used for Deep Learning tasks. The first is a **GTX 1080 Ti GPU**, a gaming device. The second is a **Tesla P100 GPU**, a high-end device devised for data centers which provides high-performance computing for Deep Learning.


### Hardware

The hardware specifications of both the devices are:

|                                         | GeForce GTX® 1080 Ti | Tesla® P100-SMX2 |
| :-------------------------------------: | :------------------: | :--------------: |
|             **CUDA Cores**              |        3,584         |      3,584       |
| **Floating-point Performance (TFLOPS)** |         11.3         |       10.6       |
|               **Memory**                |    11 GB (GDDR5X)    |   16 GB (HBM2)   |
|        **Max Power Consumption**        |        250 W         |      300 W       |
|         **Launch price (USD)**          |         $699         |      $9,428      |



Tesla P100 has more memory than GTX 1080 Ti. Also, high-bandwidth HBM2 memory is significantly faster than GDDR5X. The direct CPU-to-GPU NVLink connectivity on P100 enables 5X faster transfers than standard PCI-E.
However, the cost of P100 is almost 15X more than the 1080 Ti.


### Software

We have used the following components for the software:
- **NVIDIA CUDA Toolkit:** 8.0
- **NVIDIA cuDNN:** 5.1
- **Python:** v3.6.3
- **NumPy:** v1.13.3
- **TensorFlow:** v1.4
- **NVIDIA graphics driver:** v384.81


### Dataset

The [Cars Overhead with Context (COWC)](gdo-datasci.ucllnl.org/cowc/) dataset is a large, high quality set of annotated cars from overhead imagery. The data consists of ~33,000 unique cars from six different image locales: Toronto Canada, Selwyn New Zealand, Potsdam and Vaihingen Germany, Columbus and Utah United States. The dataset is described in detail by [Mundhenk et al, 2016](https://gdo-datasci.ucllnl.org/cowc/mundhenk_et_al_eccv_2016.pdf). The Columbus and Vaihingen datasets are in grayscale. The remaining datasets are 3-band RGB images. Data is collected via aerial platforms, but at a view angle such that it resembles satellite imagery.


### Model

The Inception v4 model is the best choice when selecting a model which has 95.2% Top-5 accuracy. However, this network performs very well on the ImageNet challenge, but not on the classes of interest here. So we fine-tuned this model to adapt for our dataset and named it as 'Pondception'.


### Benchmarks

In order to compare the performance of both the hardware architectures, we used four benchmarks:

- We trained the Pondception classification network model till 2000 steps for the dataset on the CPU and both the hardware configurations.
- We used the maximum batch size that the memory of both the hardware configurations can fit and train the model.
- We monitored the amount of energy consumed and the temperature of both the hardware configurations during the training of the model.
- We ran some popular convolutional neural network models on both the hardware configurations.


### Results

#### Time taken during training

For a batch size of 32 and 2000 training steps, the total time taken to train the model on the CPU and the two GPU’s is:

|                                         | CPU | GTX 1080 Ti | Tesla P100 |
| :-------------------------------------: | :-----------| :------------------: | :--------------: |
|             **Time Taken (in mins)**             |        240         | 67 |     51     |



We can see that the GPU’s are significantly faster than the CPU, with about 4X speedup. Regarding the comparison between both the GPU’s, the P100 outperforms the 1080 Ti, though there is only 1.3X speedup, i.e. the time taken for training is reduced by approximately 20%.

#### Evaluation Metrics and Validation Loss comparison

Since the P100 has a larger memory size (i.e. 5 GB more than the 1080 Ti), it is able to fit a larger batch size for the given dataset. While the largest batch size for 1080 Ti is 32, the largest batch size for the P100 is 85. Training with these batch sizes and taking 2000 training steps gives the following results for the evaluation metrics and the loss in validation:

![](https://s3-eu-west-1.amazonaws.com/satellite-data/Screenshot+from+2017-12-15+09-55-28.png)

As we can visualize from the bar chart, there is not much difference in the metrics even though we had taken a larger batch size for the P100. In fact, the accuracy and the other metrics are around 2% less in the case of P100. So, we can’t conclude that if we have a larger memory in a GPU and take a larger batch size, it will give better validation accuracy.

![](https://s3-eu-west-1.amazonaws.com/satellite-data/Screenshot+from+2017-12-15+10-00-00.png)

If we visualize the plot for validation loss in the model for both the GPU’s for 2000 steps, we can see that the validation loss is decreasing more at every subsequent checkpoint in the case of P100. It might have decreased more and converged earlier in case of P100, had we trained for more number of steps.

#### Power Consumption and Temperature analysis
Following is the plot for power consumption during training of the model for both P100 and 1080 Ti:

![]( https://s3-eu-west-1.amazonaws.com/satellite-data/Screenshot+from+2017-12-15+09-49-28.png )

And following is the plot for temperature during training of the model for both P100 and 1080 Ti:

![](https://s3-eu-west-1.amazonaws.com/satellite-data/Screenshot+from+2017-12-15+09-49-37.png)

As we can infer from the plots, P100 definitely beats 1080 Ti in terms of power consumption and the temperature. For the temperature, it is natural since 1080 Ti GPU’s are installed in our office at room temperature and do not have any special cooling system besides the fans located in the device.

#### Image classification models performance

In this section, we use [InceptionV3](https://arxiv.org/abs/1512.00567),  [ResNet-50](https://arxiv.org/abs/1512.03385),  [VGG16](https://arxiv.org/abs/1409.1556), and [ResNet-152](https://arxiv.org/abs/1512.03385) models. They are tested using the [ImageNet](http://www.image-net.org/) data set.

We started with synthetic data to remove disk I/O as a variable and to set a baseline.

##### Speedup over CPU

Compared to CPUs, GPUs can provide huge performance speedups during Deep Learning training and inference.

The comparison is made on ResNet-50, using batch size at 64.

![](https://ws2.sinaimg.cn/large/006tKfTcgy1fm4n14je2jj30xo0mw401.jpg)

##### Speedup with multi-GPUs

![](https://ws2.sinaimg.cn/large/006tKfTcgy1fm82h35wtqj30lj0bzmy5.jpg)

#### Half-Precision (FP16) Performance

- According to the official introduction, P100 is designed for high-performance double precision (FP64, used in many HPC applications such as quantum chemistry and numerical simulation) as there are 1792 FP64 (double precision) CUDA Cores,  which is one half the number of FP32 (single precision) CUDA Cores.
- With GPUs like GTX 1080 or Titan X, you can compute with half-precision floats (FP16), but actually the performance will be slower than single-precision (FP32). However, P100 supports for FP16 arithmetic speeds up Deep Learning.

![](https://ws2.sinaimg.cn/large/006tKfTcgy1fm4ntaizjfj312q0oeq4n.jpg)

#### Memory Bottleneck

- GPU memory will never be too much for large-scale deep learning training. Sometimes the memory will be a bottleneck for training efficiency. Larger mini-batches are more efficient to compute and lead to better convergence using in fewer epochs , but at the same time they also require more GPU memory.
- Tesla P100 has 5GB more than GTX 1080 Ti, which enables to use larger batch sizes in deep learning. For example, when we use batch size 64, our GTX 1080 Ti will run out of memory, however the Tesla P100 still works.


![](https://ws3.sinaimg.cn/large/006tKfTcgy1fm82ytt7mrj30n30c1js3.jpg)


- One possilbe approach to avoid this is by applying half-precision computing because storing FP16 data compared to higher FP32 or FP64 reduces memory usage, which enable us to use larger batch size or train larger networks. For example, in the previous experiment, if we change to FP16 then the out-of-memory problem will be solved.

### Conclusions


We compared two different GPUs by running a couple of Deep Learning benchmarks. These devices are GeForce GTX 1080 and Tesla P100.

After looking at the results, we can argue that P100 is probably not worth the cost (which is 15X more than the 1080 Ti), while the performance is generally around 15% better.

However, as already discussed in previous section, the larger memory size of P100 would enable to either work with larger networks or with larger batches. Larger batches could lead to better convergence of the gradient descent process, enabling us to train a successful model in a smaller number of epochs. Also, P100 was stabler than 1080 Ti with a much smaller jitter variance during the model training process. Moreover, Tesla P100 might last longer given that it is a high-end device specially devised for datacenters.


### Acknowledgements

We would like to thank BIOS-IT for letting us test the performance of Tesla P100 GPUs as a part of whitepaper discussion on the performance comparison between P100 and GTX 1080 Ti GPUs.

