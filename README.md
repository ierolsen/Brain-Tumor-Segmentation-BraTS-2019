# INTRODUCTION 

In this project, I aim to work with 3D images and UNET models. Before I couldn’t have any chance to work with them thus I don’t have any idea what they are. But this project will be so educational for me. As well I aim to make practice in algorithms.

# Download Dataset:

Dataset Link: https://ipp.cbica.upenn.edu/categories/brats2020

In order to download the dataset, first, you must sign up. After sign up, your account will be reviewed within 3 days. Then you can send a request to download data, after this step you will get an email (if I am not wrong) that contains **REGISTRATION_STATUS.txt**. In this txt file, you can find train and validation dataset links.


---
# READ and OPEN IMAGES

First I started with uploading data using by **glob**, glob is one of the awesome libraries of Python. Using that I can get paths. But defining **glob**, I used **recursive=True** which means reach subfiles.

![read_data](https://user-images.githubusercontent.com/30235603/107290033-2f6fda00-6a66-11eb-9a13-e611546d1639.png)


After reading the data, now I need to convert to **array**. In order to do that, I will use **skimage.io** but first, it must be installed, thus:

```sh
pip install skimage
```
Now, I can read the data but first I will go on step-by-step. I meant I will show pic step-by-step, after all I will define a function to read data. I import **skimage.io** but I will use it as **io** whatever, using by **io.imread()** I can easily read the pic. But this function contains a really important parameter called **plugin** that means which kind of pics read, I’d rather choose **simpleitk** due to work on medical images. This **simpleitk** is used for medical images. But first I need to install that using this short magical word :

```sh
pip install SimpleITK
```
After installation, I am reading the pic :

![convert_to_array](https://user-images.githubusercontent.com/30235603/107290030-2ed74380-6a66-11eb-8165-1610c13ac107.png)


As you can see the image is 3D, that’s great because I am working on 3D images for the first time. 

Now I will visualize this 3D image axes-by-axes using by the traditional way (using matplotlib)

![1](https://user-images.githubusercontent.com/30235603/107290040-326aca80-6a66-11eb-882e-e9cd3d3411b9.png)


The image that was reviewed is not segmented. This time I will read and visualize the segmented image. For that, I will use **skimage.io** again.

![2](https://user-images.githubusercontent.com/30235603/107290041-326aca80-6a66-11eb-9424-ce5c5d514d61.png)


As I read my data, now I will visualize this segmented img as 3 axes:

![3](https://user-images.githubusercontent.com/30235603/107290043-33036100-6a66-11eb-82d7-a56e9bd8f3ba.png)


----

# To Array

I need to convert all images to the array. To do that, I will define a function and then I will explain what it is.

![4](https://user-images.githubusercontent.com/30235603/107290044-33036100-6a66-11eb-9804-83764752551a.png)


This function makes you think. But no worries, I will explain now. Probably the second loop makes you think, so I will start there. 

![5](https://user-images.githubusercontent.com/30235603/107290045-339bf780-6a66-11eb-8d14-daf29ce17f8b.png)


**range(60, 130):**
In data, there are no significant images before 60 and after 130. All significant images are between 60 and 130, so that’s the reason why I only make them contain 60 and 130. Also, you can find some images that are before 60 and after 130:

![6](https://user-images.githubusercontent.com/30235603/107290047-339bf780-6a66-11eb-8e0a-fff8db0c4b1b.png)


As you can see above, the tumor is not certain. Thus there is no meaning to contain all data. Also if all images are contained, it makes my pc force. Unfortunately, my pc doesn’t have enough space. Anyway...

Also, I want to explain what **np.expand_dims()** works.
I am working on 3D images, therefore I need to work with U-Net. If you work with U-Net I need 3 input dimensions that are width, height, and channel. But the images are 3D and also there is no channel.

![7](https://user-images.githubusercontent.com/30235603/107290052-3565bb00-6a66-11eb-9969-d5687ffdbd99.png)


But using by **np.expand_dims()** I can add one more size to axis=0

![8](https://user-images.githubusercontent.com/30235603/107290054-35fe5180-6a66-11eb-9e2b-da919cfac317.png)


As you can see, now the image has a channel.

If I give a short summary, 
 * First I define a function
 * I define an empty list called img_list that will be used for appending images in the end.
 * Using by **glob** I reach the locations
 * Then using by random library, I shuffled them
 * I define a loop due to open images.
 * After opening images made standardization and convert them float32
 * Then I created another for loop due to work on limited images.
 * In **Axial (transverse) plane** totally of 155 images are available. But I only wanted to work them in between 60 and 130. Now there are 70 images.
 * I applied **np.expand_dims()** deu to add one more size that is called a channel.
 * And finally I appended them to the image list that was defined before, and then it is returned as a **np.array()**!

That’s all...

---


# To Array for Segmented Images

1 - Non-enhancing Tumor

2 - Edema

4 - Enhancing Tumor

To understand clearly, I will visualize step-by-step tumor areas. But first I will show you up the segmented image again.

![9](https://user-images.githubusercontent.com/30235603/107290055-3696e800-6a66-11eb-94e6-13de13ca6673.png)


In the image above, you can see the layer by layer of Non-enhancing Tumor, Edema, and Enhancing Tumor.

First I will start with all area tumors, for that, I will equal all values 1 that do not equal 0. Thus only all areas of the tumor will appear clearly.

![10](https://user-images.githubusercontent.com/30235603/107290056-3696e800-6a66-11eb-9a81-2ace980ffe7f.png)


After that, I will go on with a Non-enhancing Tumor that is marked by 1. To make it show up, I will equal all area 0, except for those that equal 1.

![11](https://user-images.githubusercontent.com/30235603/107290059-3696e800-6a66-11eb-9ce1-1dc23da752ba.png)


And now I can visualize Edema, in order to do that, I need to cover (equal zero) Non-enhancing Tumor (1) and Enhancing Tumor (4). For that, if values equal 1 and 4, I will equal them 0. But if other values do not equal zero, I will equal them 1. These are what I’m expecting.

![12](https://user-images.githubusercontent.com/30235603/107290060-372f7e80-6a66-11eb-8fe5-15616cf4e7ab.png)



Then lastly, I can visualize Enhancing Tumor for that, Only I will cover those that do not equal 4. I meant I will only show up values that do not equal 4.

![13](https://user-images.githubusercontent.com/30235603/107290062-372f7e80-6a66-11eb-865e-7935a80c5059.png)



Now it is time to define a function that turns images into array.

![14](https://user-images.githubusercontent.com/30235603/107290064-372f7e80-6a66-11eb-8a0a-f1dfa1023c70.png)


![15](https://user-images.githubusercontent.com/30235603/107290065-37c81500-6a66-11eb-88b3-c0be16e2f46f.png)


Also, we need the original segmented image and I labeled it as 0. This original segmented image will be used for **comparative**.

**But also, I can not use these functions because of the empty size of my computer. When I try I got Memory Error. Maybe next day, I can figure out that. I hope so.**

---

## GREAT NEWS:

Finally, I figured out that! Instead of jupyter notebook, I am using Google Colab. That’s the best way actually, even probably I will use it in my all projects :) 

Anyway, I want to talk about how to upload data to colab, etc.

First, you must connect your Gmail account and go to the drive section. After this step, you must upload your data into drive. That’s all! Then open a colab notebook and start to code! I don’t know if there is another way, actually I didn’t searched for that because I was annoyed by my pc.


But this time, I face another problem. When I run the code that converts to array train images, google colab file gives a run time error. But when I run the code that converts to array for segmentation dataset, there is no problem, it works and I don’t face any error.

Also, I fixed up these errors :) what I did is **resize** all images. Their sizes were 240,240, after resize they have 120,120 sizes.

![16](https://user-images.githubusercontent.com/30235603/107290067-37c81500-6a66-11eb-9ad4-9dc07e05c0d7.png)

# U-NET MODEL

Before defining the model, I will define **Flair**, **T2**, and **seg** images. Also during training, I will use **Flair + T2**. The point is **Flair** and **T2** will be added each self. The reason why I will add two images called **Flair** and **T2** is that these two images complete their deficiency. For example, we can see clearly Edema in Flair but in T2, we can see the center of the Tumor clearly.


![flair_t2_explanation](https://user-images.githubusercontent.com/30235603/107617700-ad8fd480-6c50-11eb-88e9-0ddf48860f8d.png)


![17](https://user-images.githubusercontent.com/30235603/107290068-3860ab80-6a66-11eb-8a41-e98ba819bc15.png)


### Explanations

```python
K.set_image_data_format('channels_first')
```
The shape of images are like that : (2, 120, 120), 

if images are like that: (120, 120, 2) then I would use this code

```python
K.set_image_data_format('channels_last')
```

### Dice Coefficient

When we use accuracy in U-NET models, we can not realize that if the model was trained or not. Thus we use Dice Coefficient. When we put images on top of each other, Dice Coefficient gives differences of pixels. In U-NET Models, input and output are **images !!** so therefore we should analyze the difference of pixels.

![unet](https://user-images.githubusercontent.com/30235603/107290036-30087080-6a66-11eb-9bcb-7c0ecdfc2a92.png)


![dice](https://user-images.githubusercontent.com/30235603/107290031-2ed74380-6a66-11eb-8fe8-22529312eeba.jpg)


## NEWS:

Unfortunately for 2 days, I have been trying to solve some UNET errors.

The first of them was about the TensorFlow version. In my environment, I’ve been using 1.13.1 tf version since 2017. Then my solution was, using **Colab** again. Because **Colab** uses 2.4.1 tf version.

Another and last error was that: 

ValueError: A `Concatenate` layer requires inputs with matching shapes except for the concat axis. Got inputs shapes: [(None, 512, 14, 14), (None, 512, 15, 15)]

**When I googled that, what I found was related by the size of images. I’ve learned that when you work with UNET, the sizes of your images must be divided by 32. Then I changed the sizes as (128,128)**

![18](https://user-images.githubusercontent.com/30235603/107290069-3860ab80-6a66-11eb-8ec2-d00966a53f8b.png)


Then using GPU of **Colab** I’ve trained my model in the 15th epochs.

![19](https://user-images.githubusercontent.com/30235603/107290070-38f94200-6a66-11eb-8235-ef904b2bc949.png)



# PREDICTION

In order to test my model, I will randomly choose an image and then I will make it predicted by the model. 
**BUT** first I need to expand the size of my image. Because, during training, the model worked with 4 dimensions, thus I will use **np.expand_dims()** again. 
First, its size was (2, 128, 128) after expand, its size is (1, 2, 128, 128)

![20](https://user-images.githubusercontent.com/30235603/107290071-38f94200-6a66-11eb-999c-e6a28eab217d.png)



Actually, all these parts were the easy part. Now it’s time to switch to the hard part!
Now I will find out a solution for segmenting separately Enhancing Tumor and center of Tumor.


![21](https://user-images.githubusercontent.com/30235603/107290072-38f94200-6a66-11eb-841d-10ea15a3a494.png)


In the above images, as you can see Non-enhancing Tumor and Enhancing Tumor images seem really less in the pictures. So what my aim is to crop useless areas of these images.

For that I will apply these steps:

* I will get coordinates of **Tumor Centers** of Non-enhancing Tumor and Enhancing Tumor using by **Numpy**

* Then I will be cropping images using these coordinates as the center point.

* These cropping steps, all cropped images will be input for the UNET model for training.

* After all, I will add the new output to the original image using the coordinate of the cropped image. 

![22](https://user-images.githubusercontent.com/30235603/107290073-3991d880-6a66-11eb-8a62-82a6ae521968.png)


So how can I find the center point of Tumors? The answer is that using by **np.where()** 
np.where gives coordinates as a list for 2 axes (x,y) 

![23](https://user-images.githubusercontent.com/30235603/107290079-3a2a6f00-6a66-11eb-8245-42f9f9181abd.png)


According to these results, when I calculate center point, what I need to do is that (min_value + max_value) / 2 gives me center point for 2 axes.

![24](https://user-images.githubusercontent.com/30235603/107290080-3a2a6f00-6a66-11eb-9e2f-9afea213f377.png)

# CROP

Now I can crop it but first I will create a matrix that has (64,64) sizes. After this matrix, which has only zeros, I will add **T1ce** image. But an important point is when I add them, I have to add **64/2** and also I have to subtract **64/2** these operations provide that the image will be middle of the matrix.

![25](https://user-images.githubusercontent.com/30235603/107290025-2da61680-6a66-11eb-88fb-85038ba2a713.png)


After **T1ce** image, I will do it same steps for the segmentation image.

![26](https://user-images.githubusercontent.com/30235603/107290027-2e3ead00-6a66-11eb-8740-4687bcfcc6b8.png)


In order to explain my steps, I only happened to all these steps just one image that is 280 images of the dataset. 

It’s time to define a function that applies all these steps for all images! 

# FUNCTION FOR CROPPING

![27](https://user-images.githubusercontent.com/30235603/107290028-2e3ead00-6a66-11eb-9b45-2e1dc15f4a46.png)


As you can see in the above image, I defined my function. Also, I don’t think that I need to explain what they are because I believe that I mentioned them in a nice way!

![28](https://user-images.githubusercontent.com/30235603/107290029-2e3ead00-6a66-11eb-9d16-8e5759de5648.png)


After applying these functions, you can realize that all images are got bigger and they are more clear.

In the next step, all images will be turned into **np.array** by for loop.


![29](https://user-images.githubusercontent.com/30235603/107564901-412abc00-6be3-11eb-9f01-2d6eba6356e5.png)


**BUT** unfortunately, when I use this loop for all images, I get an error again, I checked it meticulously but then I realized that there is no error in code. Because, in order to check that, I’ve created a subset that has about 217 images (36 files of HGG), and when I use them, the code works! That means there is no error in code, the problem is on images. But I don’t know why. Next few days, probably I can work on that why.

The error is:


![30](https://user-images.githubusercontent.com/30235603/107564903-412abc00-6be3-11eb-86f8-01636e394c24.png)


After using subset (a part of all dataset of HGG images)

As you can see, there is no change in code but it works for images that are in subset.


![31](https://user-images.githubusercontent.com/30235603/107564891-3ff98f00-6be3-11eb-86b0-751daa6f112b.png)


After this loop, all segmentation images will be in the center of the frame and also they look bigger, more clear!


![32](https://user-images.githubusercontent.com/30235603/107564896-40922580-6be3-11eb-9c31-ddbe8b6b021d.png)


# UNET MODEL FOR CROPPED IMAGES (SEGMENTATION)

Now, I will train a model again for cropped images


![33](https://user-images.githubusercontent.com/30235603/107618078-43c3fa80-6c51-11eb-8a5e-a93b496ab786.png)


The model doesn’t obtain well results because of that I worked with a subset (less dataset) but next days, if I solve the error, I will upload that again.

# CONCLUSION

In this great project, I’ve learned tons of useful knowledge about algorithms, computer vision, and above all, 3D images and UNET Models. The other important thing that I learned is absolutely fixing error :) they took a few days but I solved all of them! These projects are the best way to improve myself, facing new errors and solving them.

But actually, I can not say this project ends here because I have some idea about that I have to update this project but I can not because of my exams. Unfortunately, my exams are approaching so I need to study.

Some ideas that I have to add to this project:

 * Improving **crop** function. I determined the size as (64, 64) but if the tumor is bigger than this size what will happen? The tumor can not fit in the frame. So I have to define a new function that contains some simple conditions according to tumor size. 

Anyways maybe I can improve this function too.