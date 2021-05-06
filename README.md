# Supervised learning using a quantum classifier

This machine learning modle utilizes qubits to help speed up training. It is based off of the paper ["Supervised learning with a quantum classifier using a multi-level system"](https://arxiv.org/pdf/1908.08385.pdf). This is done through the utilization of Hilbert space. Hilbert space is perfect for machine learning. It is a vector space that is ideal for vector calculus, and machine learning is fancy vector calculus.

## Installation

Download Anaconda and install [environment_droplet.yml](https://github.com/JohnCook17/Quantum_Machine_Learning/blob/dev/environment_droplet.yml) using the command:
```bash
conda env create -f environment.yml
```
 to install the environment.
 NOTE: you may want to change "environment" to your desired environment name.


## Basic Usage
In an anaconda enabled terminal navigate to the repo and type:
```bash
conda activate environment
jupyter notebook
```
This will launch the jupyter notebook. Jupyter notebooks are interactive development environments. If you need to install anaconda [this page](https://docs.anaconda.com/anaconda/install/) might help.

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
[MIT](https://choosealicense.com/licenses/mit/)

## Contributors
John Cook

## Data set
I used the MNIST Data set, as it is very simple and demonstrates that the project will work as a proof of concept. You can find the Mnist Data set [here](http://yann.lecun.com/exdb/mnist/) Once you have the data set you need to put it in the folder:
```bash
data/MNIST/MNIST_GZ/
```

## How it works
There are 4 primary sections of the jupyter notebook, the preprocessing, the optimizer, which I got from [here]( https://towardsdatascience.com/how-to-implement-an-adam-optimizer-from-scratch-76e7b217f1cc), the Forward_and_Backward class, and the execution section. 

The preprocessing is minimal. The images need to be sorted and this is take care of. Also you can change how many files are loaded and the data split. There is an image print out to verify that the first image is properly labled.

The Adam optimizer is slightly modified from the original as the model uses oneshot training, meaning all the values of a class are trained at once, this speeds up the training significantly.

The Forward_and_Backward class is the heart of this projcet it contains all the code that is relevent to the experiment. The class is initialized with the following variables:
```
self.num_of_it: number of iterations
self.target_N: the number of classes
self.data: the data to classify and train with
self.test_data: the test data
self.alpha: a learnable parameter simular to biases
self.total_error: The total error per class starting with class 0 and ending at class N. Is a dict containing list of the errors per epoch
self.set_lie_alge: If true gets a new set of lie algebras if flase saves the current set for calculations
self.cumulative_error: the cumulative error over iterations
self.predict_mode: when true predicts the label of the image
```

H is short for Hadamard gate it distributes the probabilites of the classes into equal probabilites. This is done by rotating the entangled qubits by the Z axis then the Y axis then the Z axis again. This along with some math applies equal probabilites to each class and allows the classes to be encoded into hilbert space.

H contains the sub method map_circuit, it entangles all the quantum bits.

quNit encodes the images into hilbert space and applies the weights to them.

A is part of the math used in equation 3 of the paper mentioned above. It was complicated enough to warent its own method.

SU_of_N is supposed to calculate the special unitary group of n but I could not figure this out for values above 3, and ended up using special orthogonal group n, which SU(N) is a subgroup of. Think of it as the saying "not all rectangles are squares but all squares are rectangles". This method helps manipulate the hilbert space.

lie_algebra calculates the lie algebra of the SU(N). This helps scale the alpha terms and other parts of the equations. This is also a source of numerical instability. I have done my best to ensure that there are no divisions by 0 but it does occasionaly happen. As lie algebra is a group of infintesimal values with special properties.

forward is the forward step of the model, it also calculates ket_x which is passed to the backwards step. Ket x is a vector representation of the images we started with with weights and biases applied.

backwards is the backwards step and also the cost function. It also is used to predict the most likely class of each image in predict mode. There are some tunable hyper parameters in this class but I would leave them alone unless you know what you are doing.

train is the training portion of the modle it applies the forward and backward steps in an ordly fashion and drives the other componets of the modle it returns the errors of the modle, lower is better.

predict is the prediction part of the code it goes through each class and compairs the image to that class variables. I will then compaire the vvariables of each class to the image and determine which is lowest it will then make a prediction bassed off of this.

The execution protion of the code executes the code and prints out relevant information. The first portion instanciates the class and passes the data to it. Next the training is executed. An image is printed out with a label to verify that everything is correctly labled. Next the training begins. It will print out a tuple of with the following form (number of images per class, shape 1 of the image, shape 2 of the image). It will then print out what class value it is working on. It will then print the iteration it is on. Next followes the probability distiribution, as long as the probabilites are all equal the results should be roughly accurate. Occasionaly they are not the same due to noise in the simulation, this can be corrected buy doing more iterations. However due to the nature of one shot or few shot training it is best to use few iterations. Next is printed out the errors per class for the first 5 iterations the class is labled as its class value after that only momentum will continue to lable it to avoid vanishing gradients. The error of each class is given per class to see if there is any confusion between classes. On my system it takes about 10 minutes to complete 10 epochs, an epoch in this model is each time it finishes itterating through a given class.

Next is the prediction execution, it is simular to the process described above. Admittly the prediction process is slow this is a trade off of my implementation of one shot or few shot traning.

Finaly at the end of the note book there will be graphs and data about the training and prediction, eventualy.
