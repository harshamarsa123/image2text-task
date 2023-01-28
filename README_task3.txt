# Image2text

1.How you split the data into training, validation and test set
 
The data set contains images and corresponding labels<D1><images><lables>......to <D1><all_date>
 here in the all_data combination of images and corrsponding labels are present .
tarining_set contains =500 
evaluation_set conatons =100
total_set in the all_data =600

2.Details of model architecture chosen and why you chose the model?
 
The model architecture contains 3 stages they are 1)CNN - this is used to extract features from the images and the output of the cnn is feature_vector.
                                                  2)Lstm - in this we will pass the text code to get the token based vector as vector.
                                                  3)Lstm- the combination of the cnn output and lstm outptu are concetenate and again pass through Lstm where we get code and if we do compilation we can get the exact code.
There is a paper called pix2code  in this the model architecture building blocks are present.

3.Complete details on how to set up the training environment and all package dependencies.
   conda create -n myenv python==3.6.3
   pip install --ignore-installed --upgrade tensorflow==1.14.0
   pip install -r requirements.txt
 this complets the trainig enviroment setup
4.Steps to train the model.
  
cd ../model

# split training set and evaluation set while ensuring no training example in the evaluation set
# usage: build_datasets.py <input path> <distribution (default: 6)>
./build_datasets.py ../datasets/D1/all_data
./build_datasets.py ../datasets/D2/all_data
./build_datasets.py ../datasets/D3/all_data

# transform images (normalized pixel values and resized pictures) in training dataset to numpy arrays.
# usage: convert_imgs_to_arrays.py <input path> <output path>
./convert_imgs_to_arrays.py ../datasets/ios/training_set ../datasets/D1/training_features
./convert_imgs_to_arrays.py ../datasets/android/training_set ../datasets/D2/training_features
./convert_imgs_to_arrays.py ../datasets/web/training_set ../datasets/D3/training_features
```

Train the model:
```sh
mkdir bin
cd model

# provide input path to training data and output path to save trained model and metadata
# usage: train.py <input path> <output path> <is memory intensive (default: 0)> <pretrained weights (optional)>
./train.py ../datasets/D1/training_set ../bin

# train on images pre-processed as arrays
./train.py ../datasets/D1/training_features ../bin

# train with generator to avoid having to fit all the data in memory (RECOMMENDED)
./train.py ../datasets/D1/training_features ../bin 1


After training the model please keep weight files in the name image2text.h5

5.Steps to evaluate the model on the test set and get accuracy
   

evaluation steps are 
  Generate code for batch of GUIs:
```sh
mkdir code
cd model

# generate DSL code (.gui file), the default search method is greedy
# usage: generate.py <trained weights path> <trained model name> <input image> <output path> <search method (default: greedy)>
./generate.py ../bin image2text ../gui_screenshots ../code

# equivalent to command above
./generate.py ../bin image2text ../gui_screenshots ../code greedy

# generate DSL code with beam search and a beam width of size 3
./generate.py ../bin image2text ../gui_screenshots ../code 3
```

Generate code for a single GUI image:
```sh
mkdir code
cd model

# generate DSL code (.gui file), the default search method is greedy
# usage: sample.py <trained weights path> <trained model name> <input image> <output path> <search method (default: greedy)>
./sample.py ../bin image2text ../test_gui.png ../code

# equivalent to command above
./sample.py ../bin image2text ../test_gui.png ../code greedy

# generate DSL code with beam search and a beam width of size 3
./sample.py ../bin image2text ../test_gui.png ../code 3
```

Compile generated code to target language:
```sh
cd compiler

# compile .gui file to Android XML UI
./D2-compiler.py <input file path>.gui

# compile .gui file to iOS Storyboard
./D1-compiler.py <input file path>.gui

# compile .gui file to HTML/CSS (Bootstrap style)
./D3-compiler.py <input file path>.gui


To get accuracy : The accuracy/error is measured at the code level by comparing each generated token with each expected token.
                  Any difference in length between the generated token sequence and the expected token sequence is also counted as error.





Reference:The paper is available at [https://arxiv.org/abs/1705.07962](https://arxiv.org/abs/1705.07962)


Ideas to improve the model:
1. We can use autoencoders i,e images can pass thorugh auto encoders wiht cnn as encoder and deconvolution or transposedconv as decoding
Text files can pass through lstm to get token vector.The ouptut of cnn and lstm can concetenate and pass through to lstm 

2.we can use GANs for these purpose image2text block as generator and text2image block as decorator.