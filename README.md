# License-Plate-Detection

In this project, I have trained a [YoloV5-small](https://github.com/ultralytics/yolov5) on [License-Plate-Detection Data](https://drive.google.com/file/d/11uZ5Y0i5SMvjdj3Un5mScDY-7lZeTuYp/view).

Evaluation on Validation Dataset:
![YoloV5_valid_performance](https://user-images.githubusercontent.com/47216475/143021916-dc9210ca-062f-4988-b61d-f78ba40f584f.png)

Evaluation on Test Dataset:
![YoloV5_test_performance](https://user-images.githubusercontent.com/47216475/143022527-e22a9617-58fb-484b-a5de-f74058d9e4cc.png)

The model has an average inference time of ~0.160s, which gives more than +5 FPS on intel i5 machine(my local machine) while performing real-time object detection.

For a detailed inference notebook, look here - [inference notebook](https://github.com/prikmm/License-Plate-Detection/blob/main/inference.ipynb). If run on local machine, then even real-time license plate detection can be performed. Use google on your smartphone to get new license plate images 😆.

***All the data - 3 Datasets (YoloV5, PascalVoc & COCO) and My YoloV5 models are present here: https://drive.google.com/file/d/1ALTC356iZxLafkiRqG1AfvnM9UyDqYpe/view?usp=sharing***

`Plotted Images` folder contains the plotted test set images (53 images) along with labels in `txt` files

### How to run:
* The project is divided into two notebooks - Training-n-Model-Conversion and Inference.
* Use [training notebook](https://github.com/prikmm/License-Plate-Detection/blob/main/Train_Model_Conversion_Notebook.ipynb) to train the model. All that is required is to to configure the dataset paths in the initial ```yaml``` file.
* Once, the model is trained, download the model and use [inference notebook](https://github.com/prikmm/License-Plate-Detection/blob/main/inference.ipynb) to test the ```TFLite``` model out. The only thing required is to configure the model path correctly. That's all!

For a complete 300 epoch run: check out this [kaggle notebook](https://www.kaggle.com/priyammehta/license-plate-detection-using-yolov5/notebook)

### My Approach to the Problem:

#### Dataset Preparation Steps:
* Create a RoboFlow account 😃.
* Upload the images and labels. Viola!
 Now, you are free to choose any combination present for Preprocessing and Data-Augmentation steps.
 
##### 1. Preprocessing:
* Resize: 448x448 (Explanation below)
* Auto-Orient: RoboFlow recommends it.
* Auto-Contrast: Again recommended.
 
For preprocessing, I resized all the images to 448x448. This specific size was selected to reduce trouble when I perform inference on my TFlite YoloV5 model. YoloV5 has a stride-multple constraint on the input, i.e the image dimensions should be a multiple of 64 (max stride of YoloV5). When training, this gets handled internally, but when the model is converted to TFLite, then, we neeed to perform this manually for every image input during inference. So, to reduce some burden, I resized all the images to 448x448 from the get go.

##### 2. Data-Augmentation:
I played with a couple of combinations for Data-Augmentation and saw some interesting results on model performance.
* Random Saturation: -25% to 25%
* Random Exposure: -25% to 25%
* Random Noise: upto 1% pixel
* Mosaic: very interesting and breakthrough results because of this step. Read below for more.

I included Saturation, Exposure, and Noise in Augmentation Pipeline to solely generate more real-world data and make the model better. But, I was only able to achieve ***~0.91 mAP@0.5*** and ***~0.5 mAP@[.5:.95]***. When I added Mosaic into the pipeline, these metrics shot up like crazy. Now, my best model is getting ***~0.966mAP@0.5*** and ***~0.847@mAP[.5:.95]***. I presume adding mosaic into the pipeline generated images containing license plates of varied sizes and numbers. This combined with the rest 3 augmentation steps, created a very robust dataset. 

The dataset has been exported into 3 formats - YoloV5, PascalVoc & COCO and already uploaded on my google drive. The notebooks have been configured to download them. Each dataset contains ~1200 train images, 53 valid images and 53 test images each.

#### Model Training:

Before settling with YoloV5, I tried out different models from [TensorFlow Object Detection Zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md), but didn't get good results even when I performed robust data-augmentation using [Albumentation](https://albumentations.ai/). 

Hence, settled with YoloV5 and was impressed. The model is trained for 300 epochs and with default hyperparamters. SGD is used as the optimizer.
 
 
 
 
