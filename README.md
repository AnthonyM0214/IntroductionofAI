## 要求：
在数据集上分别实现对应的算法（当然也可以在自己找的数据集上）：
1、回归（Air quality dataset）：逻辑回归；
2、分类（BLE&RSSI dataset）：SVM、决策树、随机森林；
3、聚类（BLE&RSSI dataset）：DBScan、kmeans、GMM、层次聚类算法
其中聚类算法要求以t-SNE实现结果可视化
关于聚类可视化的问题，目的是将聚类结果以图的形式展现出来，便于对聚类结果有个直观的感受。

## 数据集简介
### 1.空气质量（逻辑回归）
数据集地址：https://archive.ics.uci.edu/ml/datasets/Air+quality
简介：数据集特征包括：
1. 日期（日/月/年）
2. 时间（小时.分钟.秒）
3. 平均每小时CO的浓度（单位为微克每立方米）
4. 平均每小时PT08 S1（tin oxide）
5. 平均每小时Benzene浓度（单位为微克每立方米）
6. 平均每小时PT08 S2（titania）
7. 平均每小时NOx浓度（单位为ppb）
8. 平均每小时PT08 S3（titania）
9. 平均每小时NO2浓度（单位为微克每立方米）
10. 平均每小时PT08 S4（tungsten oxide）
11. 平均每小时PT08 S5（indium oxide）
12. 温度（℃）
13. 空气中相对湿度（%）
14. 绝对湿度

说明：数据集中PT08 S1-S5是传感器测得五种不同金属氧化物，至于到底是什么值我也不清楚

### 目的：
助教给了两个分析方向
1. 根据氧化物的传感器的值结合环境其他数据估计对应环境真实值
2. 根据环境真实值和和其他环境数据估计氧化物传感器的值。
算法：线性回归

### 数据预处理
由于根据氧化物的传感器值和其他环境数据，那么就删除数据集中日期和时间两列，建立新的数据集。
该数据集没有非法数据，因此不许做过多预处理，归一化将在sklearn中完成。

### 准备运行算法的数据
以氧化物的传感器值和其他环境数据作为样本特征X，环境真实值作为样本输出Y

### 划分训练集和测试集
利用sklearn中的train_test_split把X和Y的样本组合划分成两部分
可以看到75%的样本数据被作为训练集，25%的样本被作为测试集。

### 运行scikit-learn的线性模型
输出结果为

```
[34.59627334 98.26031991 25.50906073]
[[ 1.60883325e-04 -1.70368203e-02 -9.59465523e-03  1.20805262e+00
  -1.99628336e-02 -1.53105713e-02 -1.01831587e-02  2.43116254e-02
   1.98259797e-02 -1.14616203e-02]
 [ 6.81196912e-03  3.67602500e-02  2.51158721e-03  1.61071490e+00
  -1.57669562e-01  4.58338773e-02 -2.04653125e-02 -7.79855275e-02
   2.37921474e-02  2.17942203e-02]
 [ 6.23057434e-04 -1.87020855e-03 -2.12318093e-04  1.16319135e+00
  -3.34871543e-02 -3.47560627e-03 -4.74652706e-03  5.04164142e-03
   1.15069289e-03 -1.52383837e-04]]
```

### 模型评价
MSE: 71.60629521737884
RMSE: 8.46205029631583

### 可视化

### 2.BLE RSSI 室内位置和导航数据集 
数据集地址：https://archive.ics.uci.edu/ml/datasets/BLE+RSSI+Dataset+for+Indoor+localization+and+Navigation
简介：数据特征包括：
1. 地点: 接受RSSIs的地点从ibeacons b3001 to b3013
2. 日期: 格式为d-m-yyyy hh:mm:ss
3. b3001 - b3013: RSSI 数值，只有整型

### 目的：
1. 分类根据Ibeacon的值来推测location：SVM、决策树、随机森林
2. 聚类：DBScan、kmeans、GMM、层次聚类算法

### 数据预处理
1.分类:为了简化模型不考虑时间，所以去掉Date列
2.聚类:原数据集Location为？，删除该列或将输出结果填写在该列中。

### 可视化
用TSNE进行数据降维并展示聚类结果

---
## 2.CIFAR-10数据集图像分类
### 数据集简介
该数据集共有60000张彩色图像，图像为32*32，供10类，每类6000张图，50000张用于训练，构成5个训练批，每一批10000张图；10000张用于测试。测试批的数据取自10类中的每一类，每一类随机取1000张。训练批为剩下的随机抽取。

### 数据预处理
对图像进行归一化，将标签变为one hot向量
```
x_img_train_normalize = x_img_train.astype('float32') / 255.0
x_img_test_normalize = x_img_test.astype('float32') / 255.0
from keras.utils import np_utils
y_label_train_OneHot = np_utils.to_categorical(y_label_train)
y_label_test_OneHot = np_utils.to_categorical(y_label_test)
```
### 搭建模型
同样使用序贯模型，CNN为两层卷积层和池化层
```
model = Sequential()
#卷积层和池化层
model.add(Conv2D(filters=32,kernel_size=(3,3),input_shape=(32, 32,3), activation='relu', padding='same'))
model.add(Dropout(rate=0.25))
model.add(MaxPooling2D(pool_size=(2, 2)))
#卷积层和池化层
model.add(Conv2D(filters=64, kernel_size=(3, 3), activation='relu', padding='same'))
model.add(Dropout(0.25))
model.add(MaxPooling2D(pool_size=(2, 2))) 
#建立神经网络
model.add(Flatten()) 
model.add(Dropout(rate=0.25))
model.add(Dense(1024, activation='relu')) 
model.add(Dropout(rate=0.25))
model.add(Dense(10, activation='softmax')) 
```
### 训练模型
10批，每批大小为128
```
model.compile(loss='categorical_crossentropy',optimizer='adam', metrics=['accuracy'])
training=model.fit(x_img_train_normalize, y_label_train_OneHot,validation_split=0.2,epochs=10, batch_size=128, verbose=1) 
```
### 模型评估
```
scores = model.evaluate(x_img_test_normalize, y_label_test_OneHot, verbose=0)
```
结果为0.8069150501251221
