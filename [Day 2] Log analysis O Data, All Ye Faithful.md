After yesterday’s resounding success, McHoneyBell walks into AntarctiCrafts’ office with a gleaming smile. She takes out her company-issued laptop from her knapsack and decides to check the news. “Traffic on the North-15 Highway? Glad I skied into work today,” she boasts. A notification from the Best Festival Company’s internal communication tool (HollyChat) pings.

It’s another task. It reads, “The B-Team has been tasked with understanding the network of AntarctiCrafts’ South Pole site”. Taking a minute to think about the task ahead, McHoneyBell realises that AntarctiCrafts has no fancy technology that captures events on the network. “No tech? No problem!” exclaims McHoneyBell.

She decides to open up her Python terminal…

Learning Objectives

In today’s task, you will:

- Get an introduction to what data science involves and how it can be applied in Cybersecurity
- Get a gentle (We promise) introduction to Python
- Get to work with some popular Python libraries such as Pandas and Matplotlib to crunch data
- Help McHoneyBell establish an understanding of AntarctiCrafts’ network

**Data Science 101**

The core element of data science is interpreting data to answer questions. Data science often involves programming, statistics, and, recently, the use of Artificial Intelligence (AI) to examine large amounts of data to understand trends and patterns and help businesses make predictions that lead to informed decisions. The roles and responsibilities of a data scientist include:

|Role    |Description   |
|---|---|
|Data Collection	   |This phase involves collecting the raw data. This could be a list of recent transactions, for example.   |
|Data Processing   | This phase involves turning the raw data that was previously collected into a standard format the analyst can work with. This phase can be quite the time-sink!  |
|Data Mining (Clustering/Classification)   | This phase involves creating relationships between the data, finding patterns and correlations that can start to provide some insight. Think of it like chipping away at a big stone, discovering more and more as you chip away.  |
|Analysis (Exploratory/Confirmatory)	   | This phase is where the bulk of the analysis takes place. Here, the data is explored to provide answers to questions and some future projections. For example, an e-commerce store can use data science to understand the latest and most popular products to sell, as well as create a prediction for the busiest times of the year.  |
|Communication (Visualisation)	   |This phase is extremely important. Even if you have the answers to the Universe, no one will understand you if you can't present them clearly. Data can be visualised as charts, tables, maps, etc.   |

**Data Science in Cybersecurity**

The use of data science is quickly becoming more frequent in Cybersecurity because of its ability to offer insights. Analysing data, such as log events, leads to an intelligent understanding of ongoing events within an organisation. Using data science for anomaly detection is an example. Other uses of data science in Cybersecurity include:

- SIEM: SIEMs collect and correlate large amounts of data to give a wider understanding of the organisation’s landscape.
- Threat trend analysis: Emerging threats can be tracked and understood.
- Predictive analysis: By analysing historical events, you can create a potential picture of what the threat landscape may look like in the future. This can aid in the prevention of incidents.

**Introducing Jupyter Notebooks**

Jupyter Notebooks are open-source documents containing code, text, and terminal functionality. They are popular in the data science and education communities because they can be easily shared and executed across systems. Additionally, Jupyter Notebooks are a great way to demonstrate and explain proof of concepts in Cybersecurity.

Jupyter Notebooks could be considered as instruction manuals. As you will come to discover, a Notebook consists of “cells” that can be executed one at a time, step by step. You’ll see an example of a Jupyter Notebook in the screenshot below. Note how there are both formatted text and Python code being processed:

![cells](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/d674fbd9-fb48-40c1-8b46-b9ce96835462)

Before we begin working with Jupyter Notebooks for today’s practicals, we must become familiar with the interface. Let’s return to the machine we deployed at the start of the task (pane on the right of the screen).

You will be presented with two main panes. On the left is the “File Explorer”, and on the right is your “workspace”. This pane is where the Notebooks will open. Initially, we are presented with a “Launcher” screen. You can see the types of Notebooks that the machine supports. For now, let’s left-click on the “Python 3 (ipykernel)” icon under the “Notebook” heading to create our first Notebook.

![jupy1](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/6c3a31c9-f64a-4db4-9613-bafebbcbc3af)

You can double-click the "Folder" icon in the file explorer to open and close the file explorer. This may be helpful on smaller resolutions. The Notebook’s interface is illustrated below:

![jupy2](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/6d26fec5-4536-4bd3-8ed1-d567b72adca5)

The notable buttons for today’s task include: 

|Action   |Icon   |Keyboard Shortcut   |
|---|---|---|
|Save   |A floppy disk	   |CTRL + S   |
|Run Cell   |   A play button	|SHIFT + ENTER   |
|Run All Cells   |Two play buttons alongside each other	   |None   |
|Insert Cell Below	   |Rectangle with an arrow pointing down	   |B   |
|Delete Cell   | A trash can	  | D  |

For now, don’t worry about the toolbar at the very top of the screen. For brevity, everything has already been configured for you. Finally, note that you can move cells by clicking and dragging the area to their left:

![gif1](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/30e22cca-9075-429e-9073-cbb4d6cdd306)

**Practical**

For the best learning experience, it is strongly recommended that you follow along using the Jupyter Notebooks stored on the VM. I will recommend what Jupyter Notebook to use in each section below. The Notebooks break down each step of the content below in much more detail.

**Python3 Crash Course**

The Notebook for this section can be found in ```1_IntroToPython -> Python3CrashCourse.ipynb```. Remember to press the “Run Cell” button (Shift + Enter) as you progress through the Notebook. Note that if you are already familiar with Python, you can skip this section of the task.

Python is an extremely versatile, high-level programming language. It is often highly regarded as easy to learn. Here are some examples of how it can be used:

- Web development
- Game development
- Exploit development in Cybersecurity
- Desktop application development
- Artificial intelligence
- Data Science

One of the first things you learn when learning a programming language is how to print text. Python makes this extremely simple by using ```print(“your text here”)```.

Note the terminal snippet below is for demonstration only.

```
C:\Users\CMNatic>python
Python 3.10.10 (tags/v3.10.10:aad5f6a, Feb  7 2023, 17:20:36) [MSC v.1929 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> print("Hello World")
Hello World
```

**Variables**

A good way of describing variables is to think of them as a storage box with a label on it. If you were moving house, you would put items into a box and label them. You’d probably put all the items from your kitchen into the same box. It’s very similar in programming; variables are used to store our data, given a name, and accessed later. The structure of a variable looks like this: ```label = data```.

```
# age is our label (variable name).
# 23 is our data. In this case, the data type is an integer.
age = 23

# We will now create another variable named "name" and store the string data type.
name = "Ben" # note how this data type requires double quotations.
```

The thing to note with variables is that we can change what is stored within them at a later date. For example, the "name" can change from "Ben" to "Adam". The contents of a variable can be used by referring to the name of the variable. For example, to print a variable, we can just parse it in our ```print()``` statement.

Note the terminal snippet below is for demonstration only.

```
C:\Users\CMNatic>python
Python 3.10.10 (tags/v3.10.10:aad5f6a, Feb  7 2023, 17:20:36) [MSC v.1929 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> name = "Ben"
>>> print(name)
Ben
```

Lists

Lists are an example of a data structure in Python. Lists are used to store a collection of values as a variable. For example: ```transport = ["Car", "Plane", "Train"] age = ["22", "19", "35"]```

Note the terminal snippet below is for demonstration only.

```
Creating and printing a list in Python

C:\Users\CMNatic>python
Python 3.10.10 (tags/v3.10.10:aad5f6a, Feb  7 2023, 17:20:36) [MSC v.1929 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> transport = ["Car", "Plane", "Train"]
>>> print(transport)
['Car', 'Plane', 'Train']
```

**Python: Pandas**

The Notebook for this section can be found in ```2_IntroToPandas ->; IntroToPandas.ipynb```. Remember to press the “Run Cell” button (Shift + Enter) as you progress through the Notebook.

Pandas is a Python library that allows us to manipulate, process, and structure data. It can be imported using ```import pandas```. In today’s task, we are going to import Pandas as the alias "pd" to make it easier to refer to within our program. This can be done via  ```import as pd```.

There are a few fundamental data structures that we first need to understand.

**Series**

In pandas, a series is similar to a singular column in a table. It uses a key-value pair. The key is the index number, and the value is the data we wish to store. To create a series, we can use Panda's Series function. First, let's:

1. Create a list: ```transportation = ['Train', 'Plane', 'Car']```
2. Create a new variable to store the series by providing the list from above: ```transportation_series = pd.Series(transportation)```
3. Now, let's print the series: ```print(transportation_series)```

|Key Index   | Value  |
|---|---|
| 0  | Train  |
|  1 | Plane  |
|   2| Car  |

**DataFrame**

DataFrames extend a series because they are a grouping of series. In this case, they can be compared to a spreadsheet or database because they can be thought of as a table with rows and columns. To illustrate this concept, we will load the following data into a DataFrame:

- Name
- Age
- Country of residence

| Name  |Age   | Country of Residence  |
|---|---|---|
|Ben   |24   | United Kingdom  |
|   Jacob| 32  | USA  |
|  Alice | 19  | Germany  |

For this, we will create a two-dimensional list. Remember, a DataFrame has rows and columns, so we’ll need to provide each row with data in the respective column.

```
For this, we will create a two-dimensional list. Remember, a DataFrame has rows and columns, so we will need to provide each row with data in the respective column.

data = [['Ben', 24, 'United Kingdom'], ['Jacob', 32, 'United States of America'], ['Alice', 19, 'Germany']]
Now we create a new variable (df) to store the DataFrame using the list from above. We will need to specify the columns in the order of the list. For example:

Ben (Name)
24 (Age)
United Kingdom (Country of Residence)
df = pd.DataFrame(data, columns=['Name', 'Age', 'Country of Residence'])
Now let's print the DataFrame (df)

df
```

**Python: Matplotlib**

The Notebook for this section can be found in ```3_IntroToMatplotib -> IntroToMatplotlib.ipynb```. Remember to press the “Run Cell” button (Shift + Enter) as you progress through the Notebook.

Matplotlib allows us to quickly create a large variety of plots. For example, bar charts, histograms, pie charts, waterfalls, and all sorts!

**Creating Our First Plot**

After importing the Matplotlib library, we will use ```pyplot``` (plt) to create our first line chart to show the number of orders fulfilled during the months of January, February, March, and April.

```
Simply, we can use the plot function to create our very first chart, and provide some values.

Remember that adage from school? Along the corridor, up the stairs? It applies here! The values will be placed on the X-axis first and then on the Y-axis.

Let's call pyplot (plt)'s plot function. plt.plot()

Now, we will need to provide the data. In this scenario, we are manually providing the values.

Remember, X-axis first, Y-axis second!
plt.plot(['January', 'February', 'March', 'April' ],[8,14,23,40])
Ta-dah! Our first line chart.
```

![pyplot](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/c2b555b0-e432-4c41-8916-ad522299dba6)

**Capstone**

Okay, great! We've learned how to process data using Pandas and Matplotlib. Continue onto the "Workbook.ipynb" Notebook located at ```4_Capstone``` on the VM. Remember, everything you need to answer the questions below has been provided in the Notebooks on the VM. You will just need to account for the new dataset "network_traffic.csv".

