# Is it a chori or not? Find out with CNNs and PyTorch /// README IN PROGRESS
This question has dazzled humanity for so long that we had to come up with an answer. 

![chori](https://brasasysabores.com/wp-content/uploads/2016/09/choripan-1024x577.jpg "Chori Detector")

## Gather training data
Use the [google-images-download](https://github.com/hardikvasa/google-images-download) library or look manually for images in Google Images or Flickr (filter by the photos allowed for 'commercial and other mod uses'). I downloaded around 500 images for the 'Choripan' class and extracted another 500 images for the 'Not Choripan' class from the ['Hot Dog - Not Hot Dog'](https://www.kaggle.com/dansbecker/hot-dog-not-hot-dog) dataset in Kaggle.

## Installation
This script supports Python 2.7 and 3.7, although if you run into problems with either PyTorch, Fast.ai, or Python 3.7, it might be easier to just run everything from the included Google Colaboratory notebook.

## Clone this repository
````
$ git clone https://github.com/nicolasmetallo/chori-notchori-classifier.git
````

## Install pre-requisites
```
$ pip install -r requirements.txt
```

## Split dataset into train, val, test
Run the 'build_dataset.py' script to split the 'images' folder into train, val, test folders.
```
$ python3 build_dataset.py --data_dir='images' --output_dir='dataset'
```

## Annotate images
There's no standard way to annotate images, but the [VIA tool](http://www.robots.ox.ac.uk/~vgg/software/via/via_demo.html) is pretty straightforward. It saves the annotation in a JSON file, and each mask is a set of polygon points. Here's a [demo](http://www.robots.ox.ac.uk/~vgg/software/via/via_demo.html) to get used to the UI and the parameters to set. Annotate the images in the 'train' and 'val' folders separately. Once you are done, save the exported 'via_region_data.json' to each folder.

![VIA annotation](via-annotation-ui.png)

## Train your model
We download the 'coco' weights and start training from that point with our custom images (transfer learning). It will download the weights automatically if it can't find them. Training takes around 4 mins per epoch with 10 epochs.
```
$ python3 custom.py --dataset='dataset' --weights=coco # it will download coco weights if you don't have them
```

## Google Colaboratory
### Mount Google Drive
```
# Install the PyDrive wrapper & import libraries.
# This only needs to be done once in a notebook.
!pip install -U -q PyDrive
from pydrive.auth import GoogleAuth
from pydrive.drive import GoogleDrive
from google.colab import auth
from oauth2client.client import GoogleCredentials

# Authenticate and create the PyDrive client.
# This only needs to be done once in a notebook.
auth.authenticate_user()
gauth = GoogleAuth()
gauth.credentials = GoogleCredentials.get_application_default()
drive = GoogleDrive(gauth)
```

### Save trained model to Google Drive
As the weights file is around 250 mb, you are not able to download it directly from Colab. You need to save it first to your Google Drive, and then download it you your local drive. Modify with your own variable names and paths.
```
# Create & upload a file.
uploaded = drive.CreateFile({'title': path/to/weights/file.h5})
uploaded.SetContentFile('file.h5')
uploaded.Upload()
print('Uploaded file with ID {}'.format(uploaded.get('id')))
```

### Load weights file from Google Drive
```
# Mount Drive folder
from google.colab import drive
drive.mount('/content/drive')
# Look for 'mask_rcnn_damage_0010.h5' and copy to the working directly
!cp 'drive/My Drive/mask_rcnn_damage_0010.h5' 'car-damage-detector'
```

# Apply color splash to an image
```
$ python3 custom.py splash --weights=logs/damage20181007T0431/mask_rcnn_damage_0010.h5 --image=dataset/test/67.jpg
```
or run ```inspect_custom_model.ipynb```
