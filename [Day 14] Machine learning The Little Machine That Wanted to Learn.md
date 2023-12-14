The CTO has made our toy pipeline go wrong. By infecting elves at key positions in the toy-making process, he has poisoned the pipeline and caused the elves to make defective toys!

McSkidy has started to combat the problem by placing control elves in the pipeline. These elves take measurements of the toys to try and narrow down the exact location of problematic elves in the pipeline by comparing the measurements of defective and perfect toys. However, this is an incredibly tedious and lengthy process, so he's looking to use machine learning to optimise it.

**Learning Objectives**

- What is machine learning?
- Basic machine learning structures and algorithms
- Using neural networks to predict defective toys

Introduction

Over the last decade, there has been a massive boom in artificial intelligence (AI) and machine learning (ML) systems. Just in the last couple of years, the release of ChatGPT has taken the world by storm. However, how these systems actually work is often shrouded in mystery, leading to a lot of snake oil sales tactics.

In this task, we will provide you with a glimpse into the world of ML to help demystify this incredibly interesting topic. We will create our very own neural network that can be used to detect defective toys!

Zero to Hero on Artificial Intelligence

Before we can create our own AI, we need to learn some of the basics. First of all, let's discuss the two terms.

The term AI is used in broad strokes out there in the world – often incorrectly. We have to be honest with ourselves – AI can't just be a bunch of "if" statements. A better term to use is machine learning. ML refers to the process used to create a system that can mimic the behaviour we see in real life. This is because there is intelligence in real life and its structures. The field is incredibly broad, but here are a couple of popular examples:

Genetic algorithm: This ML structure aims to mimic the process of natural selection and evolution. By using rounds of offspring and mutations based on the criteria provided, the structure aims to create the "strongest children" through "survival of the fittest".
Particle swarm: This ML structure aims to mimic the process of how birds flock and group together at specific points. By creating a swarm of particles, the structure aims to move all the particles to the optimal answer's grouping point.
Neural networks: This ML structure is by far the most popular and aims to mimic the process of how neurons work in the brain. These neurons receive various inputs that are then transformed before being sent to the next neuron. These neurons can then be "trained" to perform the correct transformations to provide the correct final answer.
There are many more ML structures, but we'll stick to neural networks for this task, as they are the most popular. And, while there's a significant amount of maths involved in implementing an ML structure, we'll be abstracting this information. If you want to learn more, you can start here (this is where I started) and then work your way up!

Learning Styles

First on our list of ML basics to cover is the neural network's learning style. In order to train our neural network, we need to decide how we'll teach it. While there are many different styles and subsets of styles, we will only focus on the two main styles for now:

Supervised learning: In this learning style, we guide the neural network to the answers we want it to provide. We ask the neural network to give us an answer and then provide it with feedback on how close it was to the correct answer. In this way, we are supervising the neural network as it learns. However, to use this learning style, we need a dataset where we know the correct answers. This is called a labelled dataset, as we have a label for what the correct answer should be, given the input.
Unsupervised learning: In this learning style, we take a bit more of a hands-off approach and let the neural network do its own thing. While this sounds very strange, the main goal is to have the neural network identify "interesting things". Humans are quite good at most classification tasks – for example, simply looking at an image and being able to tell what colour it is. But if someone were to ask you, "Why is it that colour?" you would have a hard time explaining the reason. Humans can see up to three dimensions, whereas neural networks have the ability to work in far greater dimensions to see patterns. Unsupervised learning is often used to allow neural networks to learn interesting features that humans can't comprehend that can be used for classification. A very popular example of this is the restricted Boltzmann machine. Have a look here at the weird features the neural network learned to classify different digits.
For this task, we will focus on supervised learning. It's an easier learning style for learning the basics, including the basic network structure.

Basic Structure

Next on our list of ML basics to learn is the basic structure of a neural network. Sticking to the very basics of ML, a neural network consists of various different nodes (neurons) that are connected as shown in the animation below:

![ddd](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/7817ffe0-f86c-4d3a-9836-75e20330cd1a)

As shown in the animation, the neural network has three main layers:

Input layer: This is the first layer of nodes in the neural network. These nodes each receive a single data input that is then passed on to the hidden layer. This means that the number of nodes in this layer always matches the network's number of inputs (or data parameters). For example, if our network takes the toy's length, width, and height, there will be three nodes in the input layer.

Output layer: This is the last layer of nodes in the neural network. These nodes send the output from the network once it has been received from the hidden layer. Therefore, the number of nodes in this layer will always be the same as the network's number of outputs. For example, if our network outputs whether or not the toy is defective, we will have one node in the output layer for either defective or not defective (we could also do it with two nodes, but we won't go into that here).

Hidden layer: This is the layer of nodes between the neural network's input and output layers. With a simple neural network, this will only be one layer of nodes. However, for additional learning opportunities, we could add more layers to create a deep neural network. This layer is where the neural network's main action takes place. Each node within the neural network's hidden layer receives multiple inputs from the nodes in the previous layer and will then transmit their answers to multiple nodes in the next layer.
Now that we understand the basic layout of the neural network, let's zoom in on one of the nodes in the hidden layer to see what it's actually doing:

![inpuit](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/76628f78-986e-47df-997a-87b53f5f81e7)

As mentioned before, we will simplify the maths quite a bit here! In essence, the node is receiving inputs from nodes in the previous layer, adding them together and then sending the output on to the next layer of nodes. There is, however, a little bit more detail in this step that's important to note:

Inputs are not directly added. Instead, they are multiplied by a weight value first. This helps the neural network decide which inputs should contribute more to the output than others.
The addition's output is not directly transmitted out. Instead, the output is first entered into what is called an activation function. In essence, this decides if the neuron (node) will be active or not. It does this by ensuring that the output, no matter the input, will always be a decimal between 0 and 1 (or between −1 and 1).
Now that we understand the neural network's structure and how the layers and nodes within it work, let's dive into how the network is trained. There are two steps to training the network: the feed-forward step and the back-propagation step.

Feed-Forward Loop

The feed-forward loop is how we send data through the network and get an answer on the other side. Once our network has been trained, this is the only step we perform. At this point, we stop training and simply want an answer from the network. To complete one round of the feed-forward step, we have to perform the following:

![normalization](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/fd1055b7-397e-44ff-b9db-12c6cacddf7b)

Normalise all of the inputs: To allow our neural network to decide which inputs are most important in helping it to decide the answer, we need to normalise them. As mentioned before, each node in the network tries to keep its answer between 0 and 1. If we have one input with a range of 0 to 50 and another with a range of 0 to 2, our network won't be able to properly consume the input. Therefore, we normalise the inputs first by adjusting them so that their ranges are all the same. In our example here, we would take the inputs with a 0 to 50 range and divide all of them by 25 to change their ranges to 0 to 2.

Feed the inputs to our nodes in the input layer: Once normalised, we can provide one data entry for each input node in our network.

Propagate the data through the network: At each node, we add all the inputs and run them through the activation function to get the node's output. This output then becomes the input for the next layer of nodes. We repeat this process until we get to our network's output layer.

Read the output from the network: At the output layer of the network, we receive the output from our nodes. The answer will be a decimal between 0 and 1, but, for decision-making, we'll round it to get a binary answer from each output node.

Back-Propagation

When we are training our network, the feed-forward loop is only half of the process. Once we receive the answers from our network, we need to tell it how close it was to the correct answer. This is the back-propagation step. Here, we perform the following steps:

![wight](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/f72fd517-e586-40a2-928e-a5c5420d91bc)

Calculate the difference in received outputs vs expected outputs: As mentioned before, the activation function will provide a decimal answer between 0 and 1. Since we know that the answer has to be either 0 or 1, we can calculate the difference in the answer. This difference tells us how close the neural network was to the correct answer.

Update the weights of the nodes: Using the difference calculated in the previous step, we can start to update the weights of each input to the nodes in the output layer. We won't dive too deep into this update process, as it often involves a bit of complex maths to decide what update should be made.

Propagate the difference back to the other layers: This is where the term back-propagation comes from. Once the weights of the nodes in the output layer have been updated, we can calculate what the difference would be for the previous nodes. Once again, this difference is then used to update the weights of the nodes in that layer before being propagated backwards even more. We continue this process of back-propagation until the weights for the input layer have been updated.

Once all the weights have been updated, we can run another sample of data through our network. We repeat this process with all our samples in order to train our network.

Dataset Splits

The last topic to cover before we can build our network is dataset splits. Let's use an analogy to explain this. Let's say your teacher constantly tells you that 1+1 = 2 and 2+2 = 4. But, in the exam, your teacher asks you to calculate 3+3. The question here is:

Have you just learned what the answer is, or did you learn the fundamental principle required to get to the answer?

In short, you can overtrain yourself by learning the answers instead of learning the required principle itself. The same thing can happen with neural networks!

Overtraining is a big problem with neural networks. We are training them with data where we know the answers, so it's possible for the network to simply learn the answers, not how to calculate the answer. To combat this, we need to validate that our neural network is learning the process and not the answers. This validation also tells us when we need to stop our learning process. To perform this validation, we have to split our dataset into the three datasets below:

Training data: This is our largest dataset. We use it to train the network. Usually, this is about 70–80% of the original dataset.

Validation data: This dataset is used to validate the network's training. After each training round, we send this data through our network to determine its performance. If the performance starts to decline, we know we're starting to overtrain and should stop the process. Usually, this is about 10–15% of the original dataset.

Testing data: This dataset is used to calculate the final performance of the network. The network won't see this data at all until we are done with the training process. Once training is complete, we send through the testing dataset to determine the performance of our network. Usually, this is about 10–15% of the original dataset.

Now you know how a basic neural network works, so it's time to build our own!

Putting it All Together

Now that we've covered the basics, we are ready to build our very own neural network! Start the machine in the top right corner. It will show in split screen after two minutes. You can find the files that you will be working with on the Desktop in the NeuralNetwork folder. You are provided with the following files:

detector.py - This is the script where we will build our neural network. Some of the sections have already been completed for you.

dataset_train.csv - This is your training dataset. In this dataset, the elves have not only captured the measurements of the toys for you but also whether the toy was defective or not. We will use this dataset to train, validate, and test our neural network model.

dataest_test.csv - This is your testing dataset. In this dataset, the elves have only captured the measurements of the toys. Due to the sheer volume of the toy pipeline, they were unable to determine if the toy was defective or not. Once we have trained our neural network, we will predict which of the entries in the file are defective toys for McSkidy to remove from the pipeline.

Our first step is to complete the detector.py script. Let's work through the initial code (it has already been added for you in the script, as shown in the snippet below):

```
#These are the imports that we need for our Neural Network
#Numpy is a powerful array and matrix library used to format our data
import numpy as np
#Pandas is a data processing library that also allows for reading and formatting data structures
import pandas as pd
#This will be used to split our data
from sklearn.model_selection import train_test_split
#This is used to normalize our data
from sklearn.preprocessing import StandardScaler
#This is used to encode our text data to integers
from sklearn.preprocessing import LabelEncoder
#This is our Multi-Layer Perceptron Neural Network
from sklearn.neural_network import MLPClassifier

#These are the colour labels that we will convert to int
colours = ['Red', 'Blue', 'Green', 'Yellow', 'Pink', 'Purple', 'Orange']


#Read the training and testing data files
training_data = pd.read_csv('training_dataset.csv')
training_data.head()

testing_data = pd.read_csv('testing_dataset.csv')
testing_data.head()

#The Neural Network cannot take Strings as input, therefore we will encode the strings as integers
encoder = LabelEncoder()
encoder.fit(training_data["Colour Scheme"])
training_data['Colour Scheme'] = encoder.transform(training_data['Colour Scheme'])
testing_data['Colour Scheme'] = encoder.transform(testing_data['Colour Scheme'])



#Read our training data from the CSV file.
#First we read the data we will train on
X = np.asanyarray(training_data[['Height','Width','Length','Colour Scheme','Maker Elf ID','Checker Elf ID']])
#Now we read the labels of our training data
y = np.asanyarray(training_data['Defective'].astype('int'))

#Read our testing data
test_X = np.asanyarray(testing_data[['Height','Width','Length','Colour Scheme','Maker Elf ID','Checker Elf ID']])
```

Let's work through what this code does:

The first few lines are all the library imports that we need for our neural network. We will make use of pandas to read our datasets and scikit-learn for building our neural network.

Next, we load the datasets. In our case, there is a training and testing dataset. While we have the labels for the training dataset, we don't have them for the testing dataset. So, while we can perform supervised learning, we will only know our neural network's true performance once we have uploaded our predictions for review.

Once the data is loaded, we need to make sure that all the inputs are numerical values. One of our data types is the toy's colour scheme. In order to provide this data to our network, we will encode the colours to numbers.

Lastly, we load the data. Variable X is used to store our training dataset together with its labels stored in variable y. Lastly, test_X stores the testing dataset that we will use to perform the predictions on.

We'll now start to add the code required to build and train our neural network. We will do this in steps to perform the actions mentioned above.

Creating the Datasets

First, we need to create the datasets. In our case, we will use an 80/20 split. We will combine our validation and testing datasets as we will use the completely new data for our testing dataset. To do this, we have to add the following line in our code after the ###### INSERT DATASET SPLIT CODE HERE ###### line:

```train_X, validate_X, train_y, validate_y = train_test_split(X, y, test_size=0.2)```

This will split our training dataset into two. X_train contains the training data, and X_validate our validation data. y_train contains the labels for our training data, and y_validate our labels for validation data.
Normalising the Data

Next, we need to normalise our data. We can do this by adding the following line in our code after the ###### INSERT NORMALISATION CODE HERE ###### line:

```
scaler = StandardScaler()
scaler.fit(train_X)
 
train_X = scaler.transform(train_X)
validate_X = scaler.transform(validate_X)
test_X = scaler.transform(test_X)
```

As you can see from the lines of code, we cannot cheat by determining the normalisation on either the validation or test datasets. The normalisation vector is only created from the train data, which is then applied to all three datasets.
Training the Neural Network

Finally, we can train our neural network. First, we will create our classifier with the following code after the ##### INSERT CLASSIFIER CODE HERE ###### line:

```
clf = MLPClassifier(solver='lbfgs', alpha=1e-5,hidden_layer_sizes=(15, 2), max_iter=10000)
```

You can find more information on this specific classifier here. Here's a bit of an explanation of what the values are doing:

solver='' - This is the algorithm used to update the weights. This is a classic back-propagation algorithm, but others can be used as well.

alpha='' - The alpha value is used for the regularisation of the neural network. We won't dive too deep into the maths here, but we have selected a fairly default value.

hidden_layer_sizes='' - This tells us the structure of the hidden layers in our neural network. Based on the provided configuration, we will have 2 hidden layers with 15 nodes in each.

max_iter='' - This sets a cap on the number of iterations we can train our neural network before it is forcibly stopped.

Next, we can train our classifier with the following code after the ###### INSERT CLASSIFIER TRAINING CODE HERE ###### line:

```clf.fit(train_X, train_y)```

When this step is complete, we have successfully trained our neural network!

Validate our Neural Network

The next step is to validate our neural network. To do this, we can ask the network to predict the values based on the validation dataset with the following code added after the ###### INSERT CLASSIFIER VALIDATION PREDICTION CODE HERE ####### line:

```
y_predicted = clf.predict(validate_X)
```

As you will see, when you execute the script, predictions are significantly faster than training the network. This is why neural networks can actually be used in real-life applications, as the prediction rate is incredibly fast once the network has been trained. We can determine the accuracy of our classifier by comparing the two arrays with the following code:

```
#This function tests how well your Neural Network performs with the validation dataset
count_correct = 0
count_incorrect = 0
for x in range(len(y_predicted)):

    if (y_predicted[x] == validate_y[x]):
        count_correct += 1
    else:
        count_incorrect += 1

print ("Training has been completed, validating neural network now....")
print ("Total Correct:\t\t" + str(count_correct))
print ("Total Incorrect:\t" + str(count_incorrect))

accuracy =  ((count_correct * 1.0) / (1.0 * (count_correct + count_incorrect)))

print ("Network Accuracy:\t" + str(accuracy * 100) + "%")
```

As you will see when we run the code, the neural network is pretty accurate!

Saving the Poisoned Toy Pipeline

Finally, as a last step, we can now ask our neural network to make predictions on the testing data that was not labelled by the elves with the following code after the ###### INSERT CLASSIFIER TESTING PREDICTION CODE HERE ######  line:

```y_test_predictions = clf.predict(test_X)```

This is it! We are finally ready to train and run our network. From the terminal, run the application:

```
thm@thm:$python3 detector.py 
Sample of our data:
Features:
[[ 7.07  2.45  8.7   3.    3.   14.  ]
 [ 6.3   1.36 12.9   0.   13.    2.  ]
 [ 3.72  3.19 13.15  0.    5.    4.  ]]
Defective?:
[0 0 0]
Sampe of our data after normalization:
Features:
[[ 3.35493255e-01 -1.75013931e-01 -1.17236403e+00 -9.06084744e-04
  -1.19556010e+00  1.23756133e+00]
 [ 2.09925638e-02 -1.27580511e+00  4.63498054e-01 -1.50063256e+00
   1.02132923e+00 -1.41227971e+00]
 [-1.03278897e+00  5.72312189e-01  5.60870797e-01 -1.50063256e+00
  -7.52182236e-01 -9.70639533e-01]]
Defective?:
[0 0 0]
Starting to train our Neural Network
Training has been completed, validating neural network now....
Total Correct:		18314
Total Incorrect:	1686
Network Accuracy:	91.57%
Now we will predict the testing dataset for which we don't have the answers for...
Saving predictions to a file
Predictions are saved, this file can now be uploaded to verify your Neural Network
```

These predictions will be saved to a file. Upload your predictions here: http://websiteforpredictions.thm:8000/ to see how well your neural network performed. If your accuracy is above 90%, you will be awarded the flag, and McSkidy's toy pipeline will be saved!

Neural Network Accuracy

If your neural network is not able to reach 90% accuracy, run the script again to retrain the network and submit the new predictions. Usually, within two training rounds, you will be able to reach 90% accuracy on the testing data.

This does, however, raise the question of why the neural network's accuracy fluctuates.

The reason for the fluctuation is that neural networks have randomness built into them. The weights for each of the inputs to the nodes are randomised at the start, meaning that two neural networks are never exactly the same – similar to how different brains might learn the same data differently. To truly determine the accuracy of your neural network, you would have to train it several times and calculate the average accuracy across all networks.

Several other factors might also influence the accuracy of the network – for example, the quality of the dataset. In ML, there is a term called GIGO: garbage in, garbage out. This term is meant to illustrate that AI isn't this magical thing that can fix every single problem. An ML structure is only as good as the quality of the data used to train it. Without good data, we wouldn't be able to receive any accurate output.

CyberSec Applications for Machine Learning

Machine learning, or AI as it is often called out there in the world, has real-life applications for CyberSec. Here are just some of them:

As shown in the example today, ML structures are incredible at finding complex patterns in data and performing predictions on large datasets with incredible accuracy. While humans can often do the same, the sheer amount of data and predictions required can be overwhelming. Furthermore, the intricate connections between different inputs cannot often be determined by a human, whereas ML structures can learn these decision boundaries in hyperspace, allowing for features to be connected in more than three dimensions. This can be used for classifications that are complex, such as whether network traffic is malicious or not.

ML structures are incredibly good at anomaly detection. If you provide a well-trained ML structure with thousands of data points, it will be able to discern the outliers for you. This can be used in security to detect anomalies such as unauthorised account logins.

As ML structures have the ability to learn complex patterns, they can be used for authentication applications such as biometric authentication. ML structures can be used to predict whether a person's fingerprint or iris matches the template that has been stored to provide access to buildings or devices.

CyberSec Cautions for Machine Learning

While there are many benefits of ML in CyberSec, caution should be observed for the following two reasons:

Machine learning, just like humans, is inherently imperfect. There's a very good reason why the answer provided by the neural network is called a "prediction". It's just that: a prediction. As you saw in today's example, while we can get incredibly accurate predictions from our network, it's impossible for 100% of the predictions to be correct. For this reason, we should remember that AI isn't the silver bullet for all problems. It will never be perfect. But, it should be used in conjunction with humans to play to each of their strengths.

The same power that allows machine learning to be used for defence means that it can also be used for offence. As we will show you in tomorrow's task, ML structures and AI can also be used to attack systems. We should, therefore, always consider this a potential threat to the systems we create.

**Q/A**

