# Project Report

## Milestone 3
Group: Adrian Mrozowski & Ludwig Kraft
Date:  October 13, 2020


Task 0
---------
### Clean up & catch up

As kindly provided by Arthur the last feedback sheet made us aware of a few points that weren't completed in their entirety within the setting of the last milestone. We want to solve the remains in this section. 

#### <u>PEP8 conformity
Ludwig noticed that there were indeed several names set incordantly with PEP8. I quickly changed names like folder names with upper case initial letter to lower case letters (e.g. "Modules" -> "modules").

More importantly there several script names and function names needed to be modified. Check several examples vicariously: 

<u>script names: 	
- setInitials &rarr; set_initials 
- shapeModel &rarr; shape_model 		

<u>fucntion names: 	
- addModel &rarr; add_model 
- saveCNN &rarr; save_cnn

A crucial detail to succeed with the renaming is to rename every single reference of a certain script-, function- or variable name. They do not only occur within the main script, but also within other functions and especially imports of functions from scripts. 

#### <u>Code functionality: Load and Test

Even though we thought our code would be running smoothly already, the code did not yet finish but got stuck at a certain level within the test function. To handle the problem more efficiently, we wrote a load_and_test script to quickly check whether the test file was running accordingly. Before we always had to first train a CNN in accordance to test it afterwards. After the creation of the load_and_test script as well as some outcommented (useless) lines of the test script. I had to look into the code in greater detail and check 
- firstly, where the code wasn't continuing until the end ( print-outputs help a lot) and 
- secondly, what was happening at the end of the code.

By leaving out a certain line at a time within the code and quickly running the load_and_test script again helped great deal in order to debug the code. 
I noticed that the code in the end used sample images that were not from cifar10. We do not have other images and therefore a testing with cifar10-test branches seemed to be legit. 

<u>In the Linux Command Line:</u> 
The code is now running for training, loading and testing. 
For loading and running the code use within the folder that contains all the code modules `python3 load_and_test.py` and for training, loading and testing the CNN use `python3 main.py`

We still need to dockerize the modularized code. But this intersects with task 1 of the current project, so...


Task 1
---------

### Dockerizing Our Code and Docker Compose


#### 1.) <u>Dockerizing the modularized code

We do not yet know how to exactly build the Docker Container. It is easy to do with example Containers from with Videos from Youtube or the Docker Docs. However, we don't know exactly which parameters we should use to fill the environment of the Dockerfile. Therefore we created an **issue on github**. Check it out [here](https://github.com/adrianmrozo/ds/issues/4).

After some research with a [playlist provided by our instructors](https://www.youtube.com/playlist?list=PLhW3qG5bs-L99pQsZ74f-LC-tOEsBp2rK) we learned a lot about building docker containers with videos numbered [8](https://www.youtube.com/watch?v=HqBMEmoAd1M&list=PLhW3qG5bs-L99pQsZ74f-LC-tOEsBp2rK&index=8),  [9](https://www.youtube.com/watch?v=QBOcKdh-fwQ&list=PLhW3qG5bs-L99pQsZ74f-LC-tOEsBp2rK&index=9), [10](https://www.youtube.com/watch?v=Rv3DAJbDrS0&list=PLhW3qG5bs-L99pQsZ74f-LC-tOEsBp2rK&index=10) and [12](https://www.youtube.com/watch?v=LQjaJINkQXY&list=PLhW3qG5bs-L99pQsZ74f-LC-tOEsBp2rK&index=12).

### Python Code to Docker Container




**Masterplan:** Build a Dockerfile and make to an image by using `docker build -t <image_name:tag_name> <location_of_Dockerfile>`. Images are listed at `docker images`.
Then run the image to create a container with `docker run <image_name>`. Containers are listed at `docker ps`
After that run the docker container via `docker run -it <container name>`. (If we also want to add a name for the container use  `docker run --name <my_container_name> -it <container name>`

#### <u>Step-by-step</u>:
First we need to create a fitting Dockerfile for dockerizing our python code. Adrian found great instructions [here](https://www.youtube.com/watch?v=8uaDoMuDK6E&feature=youtu.be).

Understand the Dockerfile: 

    # use python version 3 to get a docker image from dockerhub
    FROM python:3.8.5
    
    #wd in docker virtual environment
    WORKDIR /usr/src/app 
    
    # copy all required files there (that will be our modules and trained cnn
    # . to direct to current local wd. 
    # exists to copy files from current wd to virtual docker wd
    COPY all modules one-by-one .
    COPY load_and_test.py .
    COPY requirements.txt .
    
    #we don't know, which packages are installed in the base python image. 
    #Therefore install dependencies via requirements with fixed version number to insure 
    #that container will work in the future when new versions will be available
    RUN pip install --no-cache-dir -r requirements.txt
    
    # type this in the command line to execute the python file named pythonscript.py
    CMD ["python3", "./main.py"]

Now we can acutally build the docker image via `docker build -t <image_name:tag_name> <location_of_Dockerfile>`, which is here `docker build -t main:001 .`


<u>Problems encountered: </u>
Installing packages:
- cv2 cannot be installed with its current version 4.4.0 
- os could not be set fixed to a version &rarr; doesn't need to be fixed as "os" is a standard library and is orientated at the python version &rarr; we actually didn't need to install cv2. So we left it. Problem: When we want to tranform the docker image to a container, the missing installation of cv2 comes up again... So we definitely need to install it before creating the container. 
Installing cv2 works locally by opening python and then simply using `import cv2`. We even get the version of the package with `cv2.__version__`. However, we don't know how to set this command in the Dockerfile. I tried several combinations in the requirements file and the docker file. 




All without success. NOT SOLVED YET

Requirements File:
- all versions within the requirements file needed to be fixed. How could one get a version of an installed package without searching all installed packages with `pip3 list`?
&rarr; use `pip3 show <package_name>`

Status quo: 
We now have a docker image with the image_ID `67b2282b3d1b`, the tag `001` and the size of `2.08GB`
According to the master plan we now create the Docker Container via `docker run 67b2282b3d1b`

We get an importError that cv2 couldn't be imported. 
I tried and errored myself through two nights and hours of siting in from of my laptop and decided to continue as no relevant improvement was in sight. The error, that there was an import error or module error with cv2 appeared even when, we commented out ALL references of cv2 and opencv-python in ALL modules. Probably TBC as we need the dockerized code for Task 4.


<u>Adrian gave it a shot as well: </u>
We nearly managed to completely dockerize our code. However if we tried to let Docker run completely automatic our code, by adding the command:  
CMD [ "python", "./main.py" ]  
at the end of our Dockerfile, we received an error message regarding this command. We tried many variations, however it did not run. 

We had to ***manually*** execute our main.py file with the following command, which in our understanding made the code run within the container:`docker exec [container-id] python3 main.py` 
The model was trained, saved, and afterwards loaded again to be tested on some pictures, it was shown in the terminal the predicted picture category and the actual picture category.  

We were to able to confirm that it runs in our container with the command: `docker stats [container-id]`

We saw the the container used a significant amount of our memory and CPU for training the model.

Arthur gave as regarding the issue of running the main.py file by adding the command in the Dockerfile the following tip: " CMD ["python3", "./main.py"] strikes me to be a bit weird. Maybe CMD ["python3", "main.py"]?"

Together with a lot other helpful information, which we will try out again at Task 4.  

Even though Flask/Redis was not part of our milestone, we sometimes still used it, as it was used in the example [https://docs.docker.com/compose/gettingstarted/](https://docs.docker.com/compose/gettingstarted/) and we were using the given example as basis for our Dockerfile and docker-compose.yml file, since we had to start somewhere.


### Docker-Compose

***1.) Install docker compose***

 We already tackled this task in the last milestone, so please check out Task 6 in our [second project report](https://github.com/adrianmrozo/ds/blob/workon/reports/project2.md).


***2.) What services are being used in the example yml file?***
The example yml file is stored, discussed and discribed in the issue I submitted during this milestone. Check it out again [here](https://github.com/adrianmrozo/ds/issues/4).

Obviously the two used services are "web" and "redis". "Web" is being used to make the container available, i.e. binding the container and the host machine towards the port 5000 and "redis" is being used to import the publicly available image "alpine". 

They relate to the host names via the host and the port parameters within the Python file that is being run over the Dockerfile. 

***3.) What ports are being used?***

Within the appliation there is the port 6379 being used.
The container and host machine are both binded to the port 5000 in the manner of `<host port>:<machine port>`

***4.) How do host and container communicate? What ports are being exposed?***

The host machine communicates with the
application inside the Docker container over the container ID (to choose the respective container) and the ports (to locate the container). 
Ports mentioned in docker-compose.yml under the variable "EXPOSE" will be shared among different services started by the docker-compose.
-Ports will also be exposed to the host machine to a random port or a given port.


***5.) What is localhost and why is it useful in the domain of web applications??***

A localhost is the name of a computer, that a program is running on. The localhost is identified by the loopback address 127.0.0.1.

Another host could be a networkhost. We access a networkhost e.g. by typing a URL or website address in a web browser. That connects our machine to the networkhost. If we type "localhost" into the browser we connect to our own machine.

Speaking of advantages of using a localhost, it 
-   makes the development faster
-   We don't need to express half done work or undergoing development to the outside world. Data on the localhost does not leave the machine.
-   We can use the localhost as much time as we want - with free of cost. No external server needs to be paid for.
-   Fewer chances of losing your data as there is no data transfer.
-   Working offline and free of internet boundaries.
-   Not blocked by firewalls
-   Ability to test the own website while developing it






---
## Task2

### PostgreSQL and building a database

***1.) What is PostgreSQL? is it SQL or NoSQL?***

**PostgreSQL** is a powerful, open source object-relational database managment system (RDBMS) emphasizing extensibility and SQL compliance. It has a script schema and requires a primary key. 

However, it supports SQL. 
What is SQL though? **SQL** stands for *Structured Query Language*. It's a language that communicates with databases. It is designed for managing data held in a RDBMS.

**Non-SQL** is used for databases that are non-relational.

SQL databases are table-based ("structured") whereas NoSQL databases are document-based, graph databases, wide-column stores, or key-value pairs.

Notes:
- PostgreSQL and SQL Servers are two widely used relational databases. Although they share a number of core traits, there are major differences between them
- PostgreSQL is open source, while SQL Server is owned and licensed by Microsoft
- PostgreSQL is designed to handle a range of workloads, from single machines to data warehouses or Web services with many concurrent users.

*SQL or NoSQL?*

Per definition PostgreSQL is a RDBMS, stating relational and therefore should be SQL. 

However, PostgreSQL, like many other RDBMS, has added support for JSON data, which is the most popular format for semi-structured data commonly stored in NoSQL systems. Now you can say that PostgreSQL supports a certain feature commonly found in NoSQL databases.

Still, SQL is the only way to interact with a PostgreSQL database, so you couldn't call it a NoSQL database, neither.

***2.) Run a PostgreSQL Server***

We pull a docker image from the official dockerhub [PostgreSQL website](https://hub.docker.com/_/postgres) and notice two things: 
There are 1B+ downloads already, which means the image can be used without hesitation. And we find the pull command `docker pull postgres` to pull the image on to our machine. To get version 12.4 we edit the command to `docker pull postgres:12.4`

To now make an container out of the image we use:


	$ docker run --name postgres1 -q <localport>:<dockerport> -e POSTGRES_PASSWORD=mysecretpassword -d postgres:<version>

With `docker ps` we can see, that the container is now up and running. We can stop it again with `docker stop <container_ID>`.

However, we now want to make the container running in our browser. Therefore we need the .yml file. From the [documentation](https://hub.docker.com/_/postgres) we get a appropriate .yml file: 

 
    version: '3'
    services:

        postgres:
           image: postgres:12.4-alpine

    # Expose the default Postgres port on localhost
            ports:
               - '5432:5432'
        network_mode: 'bridge'
        container_name: 'postgres'

        environment:
              POSTGRES_USER: 'postgres'
              POSTGRES_PASSWORD: 'pgpass'
              POSTGRES_DB: 'ms3_jokes'

    # Copy files from dbinit into the image so that they will be run on boot
       volumes:
           - ./initdb:/docker-entrypoint-initdb.d



Notes: 
- We use version 3.2 as 3.1. is known to cause problems in form of "unsupported version"
- We name the file **postgres.yml**

Then we run `docker-compose -f stack.yml up`.
After that we check our localhost via 
`http://localhost:8080` or `127.0.0.1:8080` or `0.0.0.0:8080` (last one not save)

Success! PostgreSQL is running on our localhost server. Now how to login? We figured out the necessary inputs like seen above under environment. 

Now the container is running locally. To **run it on a server**, we do the following:

To build a server we use pgadmin4. Therefore we biuld another .yml file, called pgadmin.yml: 



    version: '3'
    services:

        pgadmin:
            image: dpage/pgadmin4

    # Expose the web UI on localhost port 8080
        ports:
            - '8081:80'
        network_mode: bridge

    # Link this container to the postgres container with hostname 'postgres'
       external_links:
          - postgres:postgres

       environment:
           PGADMIN_DEFAULT_EMAIL: 'admin@example.com'
           PGADMIN_DEFAULT_PASSWORD: 'admin'


So to run postgres on a docker container on a server, we type in the command line: 
`docker-up -f postgres.yml up` to start postgres and 
`docker-up -f pgadmin.yml up` to start pgadmin. Now we visit our localhost on port 8080 (pgadmin is running there) and insert the login data, we stored in the .yml file.

Then we add a server in the GUI using the data displayed in the postgres.yml file. Check the server and see, that there has sample database been build. This means: postgres on docker container on server at pgadmin is up and running Success!!

[Good guide](https://medium.com/better-programming/connect-from-local-machine-to-postgresql-docker-container-f785f00461a7) to start a database in PostgreSQL inside a Docker container and this [video](https://www.youtube.com/watch?v=sNXVP2suMA8), too. 


***3.) Find a python-to-postgres-adapter***

psycopg2 is the package of interest. Unfortunately `pip3 install psycopg2` gives a load of error messages. We therefore try `pip3 install psycopg2-binary`. According to several installation guides it it still possible to import psycopg in the python script.

To write the python code we need data like user name, database name and port to connect to. We build and get those by the following lines: 

I again started a database like this: 
`sudo apt-get update postgresql` and `sudo apt-get update postgresql postgresql-contrib`. Then `sudo -u postgres createuser --interactive` and enter a name for the user.  


``docker container run -d --name=pg -p 5433:5433 -e POSTGRES_PASSWORD=post_kasten -e PGDATA=/pgdata -v /pgdata:/pgdata postgres:12.4`` to build a docker container
 followed by 
 ``docker exec -it demo psql -U postgres`` to connect to postgres.
 Now can create an database by ``CREATE DATABASE pg_db1``
 and we connect to it via  ``\c pg_db1``
 Then we are inside the database and can create a table and add information with ``CREATE TABLE pg_t(something int);`` and ``INSERT INTO pg_t (something) VALUES (1);``
 Now this is more or less, what our pyhton program is supposed to do in a infrastructural level. 
 

***4.) Write a python program***

Doing a layover of the two videos [this](https://www.youtube.com/watch?v=sNXVP2suMA8) and [that](https://www.youtube.com/watch?v=2PDkXviEMD0) I came up with the follwoing code that should do the job for the requirements:


    host = "127.0.0.1"
    database = "postgres"
    port = "5432"
    user = input("Insert a name for your database:") or "postgres"
    password = input("Insert a password for your database:") or "pgpass"
    
    import psycopg2
    
    con = psycopg2.connect(dbname=database, user=user, password=password, host=host, port = port)
    
    cur = con.cursor()
    
    
    cur.execute("CREATE TABLE jokes (ID SERIAL PRIMARY KEY, joke TEXT);")
    
    cur.execute("insert into jokes (ID, joke) values (%s, %s)", (1, "Warum freut sich eine Blondine, wenn sie nach 6 Monaten ein Puzzle geschafft hat? - Wil auf der Verpackung steht: 2-4 Jahre.") )
    
    #execute query
    
    cur.execute("select joke from jokes;")
    print ("This is my favorite joke:")
    print(cur.fetchall())
    
    
    #commit my input data to db
    con.commit()
    
    
    con.close()


***5.) Run the python script with PGadmin in a docker container***

We not have the postgre docker container running with 

    docker-compose -f postgres.yml up 

Then we run the container pgadmin with `docker-compose -f pgadmin.yml up` Now we go to the browser and search `localhost:8081` (where pgadmin is running) and login with the data provided in the pgadmin.yml file.

Now we run inside the terminal the python code `python3 postgres_db.py` , put in the data provided in the postgres.yml file or simply by **pressing enter** and we get the joke displayed. Finally success!

<u>Problems encountered:</u>
- I had a huge problem managing the correct login. First we had the local host with the postgresql database running on the browser, but there was no login and password I could use. It cost me hours to try the new approach described above.
- Sometimes - without changes in the code - and after running and stopping a container and trying to start it again, the port was somehow blocked. I could not free the port, so I used another one. 
- Changing the name of the database or the user was often confused to with other parameters, which led to running closing down the containers and opening them up again and try-and-erroring myself further one step-at-a-time. 

Efforts put into the task until here LK: ~30h


## Task3

### Relational Databases

***1.) How do you need to represent/transform image data to save it to a relational database?***


Adrian: 
Datanamic.com gives a good overview of the options and their advantages and their disadvantages:
https://www.datanamic.com/support/storeimagesinthedatabase.html
"A database gives you the opportunity to store photos and other small images in a database table. You can create such a database table for example when you want to create an online photo album with descriptions of your photos.

Storing images in a database table is **not recommended**. There are too many disadvantages to this approach. Storing the image data in the table requires the database server to process and traffic huge amounts of data that could be better spent on processing it is best suited to. **A file server can process such image files much better.**
**Storing the image data inside a binary field:**
Storing the image data inside a binary field leaves that data only available to an application that streams raw image data to and from that field. You cannot view the image with an external standard image viewer anymore. The only advantage to this approach is that you can better secure your images because you can use the database security features.
**Store the images outside of the database:**
An alternative, and better method is to store the images outside of the database and store only a link to the image file. You only need a text field in your database table to store this information. The only problem to this approach is that you must synchronize the data in the link field with your file system. It is also very easy to use this method of storing images in combination with a programming language since most programming languages support some kind of LoadFromFile() function which can be used to display the image file.""

Regarding the binary file, we found the following related to PostgreSQL on the official PostgreSQL website:
https://www.postgresql.org/docs/7.4/jdbc-binary-data.html

"**31.7. Storing Binary Data**
PostgreSQL provides two distinct ways to store binary data. Binary data can be stored in a table using the data type bytea or by using the Large Object feature which stores the binary data in a separate table in a special format and refers to that table by storing a value of type oid in your table.

In order to determine which method is appropriate you need to understand the limitations of each method. The bytea data type is not well suited for storing very large amounts of binary data. While a column of type bytea can hold up to 1 GB of binary data, it would require a huge amount of memory to process such a large value. The Large Object method for storing binary data is better suited to storing very large values, but it has its own limitations. Specifically deleting a row that contains a Large Object reference does not delete the Large Object. Deleting the Large Object is a separate operation that needs to be performed. Large Objects also have some security issues since anyone connected to the database can view and/or modify any Large Object, even if they don't have permissions to view/update the row containing the Large Object reference."

*Remark added later: Even though the above is correct, We figured that none of the above really applies to our case, as a single CIFAR 10 image is stored as a numpy.ndarray, more about that later.*

**Look at your own data set: How is your data structured (you can download and load it from the source. Some of you may use the Keras function to download it).**

I found our dataset here:
https://keras.io/api/datasets/
https://www.cs.toronto.edu/~kriz/cifar.html
https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz (Size: 163 MB, md5sum: c58f30108f718f92721af3b95e74349a)

I downloaded it manually and took a look at it: It is a 163 MB big Tar.gz archive file, it consists of 8 files:
readme.html (same website as above, https://www.cs.toronto.edu/~kriz/cifar.html)

And the following 7 files which I was not able to open:
batches_meta (only 158 bytes)
and each of the following files was 31MB and also not possible to open:
test_batch
data_batch_1
data_batch_2
data_batch_3
data_batch_4
data_batch_5

On the dataset website (www.cs.toronto.edu/~kriz/cifar.html) the actual dataset is very well described:
I will describe the layout of the Python version of the dataset. The layout of the Matlab version is identical.

"The archive contains the files data_batch_1, data_batch_2, ..., data_batch_5, as well as test_batch. Each of these files is a Python "pickled" object produced with cPickle. Here is a python2 routine which will open such a file and return a dictionary:
```
numpy.ndarray
def unpickle(file):
    import cPickle
    with open(file, 'rb') as fo:
        dict = cPickle.load(fo)
    return dict
```
And a python3 version:
```
def unpickle(file):
    import pickle
    with open(file, 'rb') as fo:
        dict = pickle.load(fo, encoding='bytes')
    return dict
```
Loaded in this way, each of the batch files contains a dictionary with the following elements:

**data** -- a 10000x3072 numpy array of uint8s. Each row of the array stores a 32x32 colour image. The first 1024 entries contain the red channel values, the next 1024 the green, and the final 1024 the blue. The image is stored in row-major order, so that the first 32 entries of the array are the red channel values of the first row of the image.
**labels** -- a list of 10000 numbers in the range 0-9. The number at index i indicates the label of the ith image in the array data.

The dataset contains another file, called batches.meta. It too contains a Python dictionary object. It has the following entries:

label_names -- a 10-element list which gives meaningful names to the numeric labels in the labels array described above. For example, label_names[0] == "airplane", label_names[1] == "automobile", etc."

**Explain how you would define your relational database tables in terms of
their attributes to save your data. What kind of data types could you use
(https://www.postgresql.org/docs/12/datatype.html):**

Considering what we discovered before at, we would store the images itself as "bytea data type".
Other descriptive attributes the following data types seem to be the most straightforward:
integer for integers
decimal for decimal numbers
text for text/strings

**What additional relational database table attributes might make sense to
easily query your data (f.e. find all pictures of giraffes):**

We can think of the following table attributes that might be useful, but probably not all of them will be implemented in this release, in bold the important ones:
**Picture_ID**
CreationTime
**Picture**
**ActualCategory**
PredictedCategory
PredictedbyModelID
PredictionTime


**Repeat Task 2 using a sample from your own data set! In case you deal with images, you may want to draw that picture using an appropriate Python package, after you retrieved the image from the database. To make sure, you applied the
correct "reverse" transformation. Look here (Image.open from the Pillow
Package):
https://pillow.readthedocs.io/en/3.0.x/reference/Image.html#PIL.Image.open**

In a first attempt, as I assumed that we are handling actual image data, I was trying to just display in a test to and handle this image data. During this test run, it became pretty clear, that is not easy. So far in my life image data were .jpg, .png etc., now I saw for the first time images saved as **numpy.ndarray**. I tried the above "pickle" function, which was not possible to implement.

I implemented successfully the proposed PIL.Image.open function in python with an exemplary picture (not from our dataset, but a random picture from the internet!), so open .jpg or handling them in general is not a problem. As explained here:
https://pillow.readthedocs.io/en/3.0.x/reference/Image.html#PIL.Image.open

However we are not dealing with .jpgs in the CIFAR10 dataset directly.
As mentioned on the Tensorflow website: website
https://www.tensorflow.org/api_docs/python/tf/keras/datasets/cifar10/load_data
We are handling Tuple of Numpy arrays arrays (which I can confirm, as I tested the datatypes in terminal and they were mainly numpy.ndarray and tuple datatypes, actually exactly how they wrote it on Tensorflow: Tuple of Numpy arrays), see also the original text here:

tf.keras.datasets.cifar10.load_data()

Returns
Tuple of Numpy arrays: (x_train, y_train), (x_test, y_test).

x_train, x_test: uint8 arrays of RGB image data with shape (num_samples, 3, 32, 32) if tf.keras.backend.image_data_format() is 'channels_first', or (num_samples, 32, 32, 3) if the data format is 'channels_last'.

y_train, y_test: uint8 arrays of category labels (integers in range 0-9) each with shape (num_samples, 1). 

I nevertheless did not gave up at first, and tried to display the image files out of the CIFAR10 dataset, by trying those resources:
https://stackoverflow.com/questions/35995999/why-cifar-10-images-are-not-displayed-properly-using-matplotlib (did not print anything)
https://gist.github.com/juliensimon/273bef4c5b4490c687b2f92ee721b546 (turned out to be Python2, I even installed Python2, but it didn't work, I also tried to convert the Python2 code into Python3 code on https://www.pythonconverter.com/, which did not work, I did not pursue it further)

I also tested this first entry in this Quora response, which did not work for me:
https://www.quora.com/How-can-l-visualize-cifar-10-data-RGB-using-python-matplotlib

The second response in this Quora entry was quite elaborated. Somebody invested a lot of time to display the pictures out of the CIFAR10 dataset:
https://github.com/pranka02/nn_CIFAR10
By creating a Jupyter Notebook: dnn.ipynb 
I tested it, but it also did not work on my machine. I also uploaded it into Google Colab, and downloaded again it just as a .py file, which still did not work. I tested also the datatype of some of the variable, which should actually include the image, the datatype test resulted in "none", so I gave this also up. However the uploaded Jupyter Notebook still exists in Google Colab, and IF it is correctly made, it actually offers quite some insights into how the CIFAR 10 data can be converted again into pictures (however as it did not work for me, it is to be confirmed that the code is indeed correct):
https://colab.research.google.com/drive/1NhcaJ3EdpbmZhBch7xkHJg6tL6r0eR3Y?usp=sharing

So after this extensive research and effort, I decided that we should not try to display for now (and best not at any point of time) the pictures.

For a later point of time (the next milestones maybe) I prupose the database should be built as follows, one table for the 50000 train images, and one table for the 10000 test images.
It is not the most efficient way, as they have basically the same columns/attributes, but I strongly recommend to do it this way, so that we have another layer of security, that the train and test images are seperated. As the worst thing that could happen, is that we train our model on test images.

To be 100% sure, what the dataset looks like, I made experiments in python with the following code (the beginning is directly of out of the original cifar10_cnn.py main file):

```
#The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

trainimages=(x_train)
testimages=(x_test)
traincategorylabels=(y_train)
testcategorylabels=(y_test)

print(keras.backend.image_data_format())

(x_train, x_test) = cifar10.load_data()

allpictures =(x_train, x_test)

print(type(trainimages))
print(len(trainimages))
print(type(testimages))
print(len(testimages))
oneimage = testimages[1]
print(type(oneimage))
print(len(oneimage))
print(type(oneimage[1]))
notsurewhatthisis = oneimage[1]
print(type(notsurewhatthisis))
print(type(notsurewhatthisis[1]))
andnotsurewhatthisiscauseserrorifprinted = notsurewhatthisis[1]

print(type(traincategorylabels))
print(len(traincategorylabels))
print(type(traincategorylabels[1]))
print(len(traincategorylabels[1]))
print(traincategorylabels[1])
print(traincategorylabels[2])
print(traincategorylabels[3])
print(traincategorylabels[4])
print(traincategorylabels[5])
print(traincategorylabels[6])
print(traincategorylabels[7])
print(traincategorylabels[8])
print(traincategorylabels[9])
print(traincategorylabels[10])

print(type(oneimage))
print(len(oneimage))
```


Which resulted in the following console output:

```
x_train shape: (50000, 32, 32, 3)
50000 train samples
10000 test samples
channels_last
<class 'numpy.ndarray'>
50000
<class 'numpy.ndarray'>
10000
<class 'numpy.ndarray'>
32
<class 'numpy.ndarray'>
<class 'numpy.ndarray'>
<class 'numpy.ndarray'>
<class 'numpy.ndarray'>
50000
<class 'numpy.ndarray'>
1
[9]
[9]
[4]
[1]
[1]
[2]
[7]
[8]
[3]
[4]
<class 'numpy.ndarray'>
32
```

To complete task 3, I decided to save "oneimage" from the code example above, which should represent in my understanding one exemplary image, into the database, into a table called "input_data". And load it again.

However I did not find a solid way to save the image which is a numpy.ndarray into the table. As far as I understood the resources on the web, a numpy.ndarray can not be saved directly in a field of table. There were some suggestions which sounded rather experimental, and I decided after the direct saving of the oneimage (or testimages[1]) as numpy.ndarray into table caused not surprisingly an error message to just only save the length of this array by using len(testimages[1]). Once a solid convertion method will be found we will use it.

All the related files you find in the folder "milestones3task3", in case you executed before the files from task 2, in the postgres folder, make sure to stop and close first all containers, as the same ports might be used. As this task 3 is just an intermediate step with little value for itself, I expect that the folder milestones3task3 will be deleted in the next release.

***2.) How is our data structured?***

Some of you may use the Keras function to download it.

I found our dataset here:
https://keras.io/api/datasets/
https://www.cs.toronto.edu/~kriz/cifar.html
https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz (Size: 163 MB, md5sum: c58f30108f718f92721af3b95e74349a)

I downloaded it manually and took a look at it: It is 163 MB big Tar.gz archive file, it consists of 8 files:
readme.html (same website as above, https://www.cs.toronto.edu/~kriz/cifar.html)

And the following 7 files which I was not able to open:
batches_meta (only 158 bytes)
and each of the following files was 31MB and also not possible to open in the file browser:
- test_batch
- data_batch_1
- data_batch_2
- data_batch_3
- data_batch_4
- data_batch_5

On the dataset website (www.cs.toronto.edu/~kriz/cifar.html) the actual dataset is very well described:
Python / Matlab versions
I will describe the layout of the Python version of the dataset. The layout of the Matlab version is identical.

The archive contains the files data_batch_1, data_batch_2, ..., data_batch_5, as well as test_batch. Each of these files is a Python "pickled" object produced with cPickle. Here is a python2 routine which will open such a file and return a dictionary:

    def unpickle(file):
        import cPickle
        with open(file, 'rb') as fo:
            dict = cPickle.load(fo)
        return dict

And a python3 version:

    def unpickle(file):
        import pickle
        with open(file, 'rb') as fo:
            dict = pickle.load(fo, encoding='bytes')
        return dict

Loaded in this way, each of the batch files contains a dictionary with the following elements:

    data -- a 10000x3072 numpy array of uint8s. Each row of the array stores a 32x32 colour image. The first 1024 entries contain the red channel values, the next 1024 the green, and the final 1024 the blue. The image is stored in row-major order, so that the first 32 entries of the array are the red channel values of the first row of the image.
    labels -- a list of 10000 numbers in the range 0-9. The number at index i indicates the label of the ith image in the array data.

The dataset contains another file, called batches.meta. It too contains a Python dictionary object. It has the following entries:

    label_names -- a 10-element list which gives meaningful names to the numeric labels in the labels array described above. For example, label_names[0] == "airplane", label_names[1] == "automobile", etc.

• Explain how you would define your relational database tables in terms of
their attributes to save your data. What kind of data types could you use
(https://www.postgresql.org/docs/12/datatype.html):

Considering what we discovered before at, we would store the images itself as "bytea data type".
Other descriptive attributes the following data types seem to be the most straightforward:
integer for integers
decimal for decimal numbers
text for text/strings

• What additional relational database table attributes might make sense to
easily query your data (f.e. find all pictures of giraffes):

We can think of the following table attributes that might be useful:
- Picture_ID
- CreationTime
- Picture
- ActualCategory
- PredictedCategory
- PredictedbyModelID
- PredictionTime



## Task4

### Multidocker Dontainer Application

***1.) Testing just one image: python function test_one in test modlule***


For testing only one image I wrote a new test-function as our previous function was not suited for that. The new test function of course belongs to the module test.py. I really took an effort to understand the code even better to extract all necessary information: one test image "testData", the according test label "test_label" and a prediction label "pred_label". 

    def test_one(model):
     # initialize the ground-truth labels for the CIFAR-10 dataset
     gtLabels = ["airplane", "automobile", "bird", "cat", "deer", "dog", "frog", "horse", "ship", "truck"]
     
      # scale the data points into the range [0, 1]
     print("[INFO] sampling one image from CIFAR-10...")
     (testData, testLabels) = cifar10.load_data()[1]
     testData = testData.astype("float") / 255.0
     idxs = np.random.choice(testData.shape[0], size=(1,), replace=False)
     (testData, testLabels) = (testData[idxs], testLabels[idxs])
     testLabels = testLabels.flatten()
    
     # make predictions on the sample of testing data
     print("[INFO] predicting on testing data...")
     probs = model.predict(testData, batch_size=32)
     predictions = probs.argmax(axis=1)
     
     # loop over each of the testing data points
     for (i, prediction) in enumerate(predictions):
    	 print("[INFO] predicted: {}, actual: {}".format(gtLabels[prediction], gtLabels[testLabels[i]]))
    	
     for (i, prediction) in enumerate(predictions):
      pred_label=gtLabels[prediction]
      test_label=gtLabels[testLabels[i]] 
    	 
     return testData, test_label, pred_label 

<u>Problem encountered: </u> 
that took much time was figuring out how to use the data from "predictions". In the end I decided to use the setting from the given for-loop to fill the output parameters. 

***2.) Writing a python code***

<u>What does it?</u>
*This code is creating and training a cnn, testing it and then creating a postgresql database called milestone3 then testing the cnn with one single image again. Then it creates two tables  and insers the testData as label into one table and the prdeicted result as a label in the other one. Then all data is printed.*

I based much of the code on the work of Task 2. 

    host = "127.0.0.1"
    database = "milestone3"
    port = "5432"
    user = input("Insert a name for your database:") or "postgres" #For simplicity just press enter
    password = input("Insert a password for your database:") or "pgpass" #For simplicity just press enter
    
    import psycopg2
    import numpy as np
    
    con = psycopg2.connect(dbname=database, user=user, password=password, host=host, port = port)
    
    cur = con.cursor()
    
    # create input data table
    cur.execute("CREATE TABLE input_data (ID SERIAL PRIMARY KEY, input_label TEXT);")
    
    #create predictions table
    cur.execute("CREATE TABLE predictions (ID SERIAL PRIMARY KEY, prediction TEXT);")
    
    #train the model and store it
    #also make it available in this script
    import main
    model = main.model
    
    #store test data, test label, prediction label
    from test import test_one
    testData, test_label, pred_label = test_one(model)
    
    #load testdata into database input_data
    cur.execute("insert into input_data (ID, input_label) values (%s, %s)", (1, test_label) )
    
    #load prediction into database predictions
    cur.execute("insert into predictions (ID, prediction) values (%s, %s)", (1, pred_label) )
    
    
    #execute query
    cur.execute("select * from input_data;")
    print ("These are the inputs that have been tested so far:")
    print(cur.fetchall())
    
    
    cur.execute("select * from predictions;")
    print ("These are all tests we made to far:")
    print(cur.fetchall())
    
    
    #commit data to db
    con.commit()
    
    
    con.close()

<u>Problem encountered: </u> 
It was a huge problem loading an image into the table as input. I tried several versions with array, integer array, array[x] and so on, but finally decided that the image label would suffice as well.

***3.) Building the .yml files***
Check the work done in Task 2. We reused the files from there and soly changed the ports, whenever a port was already blocked from a previous try. 
In the postgres file we add a volume and write: 
		
	volumes:
		.saved_models:/var/lib/postgresql/data

Finally:

##  <u>How to make it all run:</u> 
Comparable to Task 2 we first start both docker files in this order: 

Build the Docker image and the respective container for running our code: 

- `docker build -t ms3:001 .` 
 - `docker run ms3:001` 
 - `docker run --name ms3_cont -it ms3:001`
 

To build our machine we start postgres.yml with 

      docker-compose -f postgres.yml up
      
and afterwards pgadmin.yml with

      docker-compose -f pgadmin.yml up

Then we open the port in the browser with

	localhost:8086

and login with the data provided in the yml file. Then we run the python script in the console via 

	python3 postgres_db.py

<u>Notes:</u> 
- After we ran the code for the first time, we needed to comment out the "create" command in the postgres_db script. In case we don't do that we get an error as the tables with the defined names already exist. 

- The submission was done with the lines commented out.

- After the tables were created and the data inserted in the browser, I disconnected the server via right click on server and connected again. The data was still available, which makes me aware, that the **volume is working**.  


## Riddles

### SQL Injection Attacks

***1.) What is an SQL Injection Attack?***

SQL injection, also known as SQLI, is a common attack vector that uses malicious SQL code for backend database manipulation to access information that was not intended to be displayed.
Websites are the most frequent targets of SQL injections. There are three types of SQL injections and they are classified according to the methods they use to access backend data and their damage potential:
- In-band SQLi (Classic)
- Inferential SQLi (Blind)
- Out-of-band SQLi.

Data can be deleted, extracted and manipulated. Therefore it can cause huge real-world problems.

Find further information on the classification [here](https://www.imperva.com/learn/application-security/sql-injection-sqli/).

***2.) How to protect against SQL injections?***

- **input validation (a.k.a. sanitization):**
 it is a best practice of writing code that can identify illegitimate user inputs.
- **web application firewall (WAF):** 
it is commonly employed to filter out SQLI, as well as other online threats
- **signature recognition, IP reputation:**
Whenever a web application firewall encounters a suspicious, but not outright malicious input may cross-verify it with IP data before deciding to block the request. It only blocks the input if the IP itself has a bad reputational history.


### Decompression Bomb

***1.) What is a Decompression Bomb?***

A decompression bomb is a malicious file that unpacks to an enormous amount of data - thus "flooding" the unpacking engine. That leads to crashing or rendering useless the program or system reading it. It is often employed to disable antivirus software, in order to create an opening for more traditional viruses.
It is also called ‘zip of death’ or a zip bomb.

-----
# THE END 


















----

### Notes: 

Commands used in the research for this task. Saved here for future use: 

-  Created a account on docker hub and login in terminal via: 
``docker login`` and credentials
- shorten command line standard line via `export PS1="\u$ "`

***docker images***
- `docker images` to view all images locally available 
&#8594; check versions  `-a` for checking all information there is and `-q` for requesting only the image IDs
- `docker rmi <image_ID>` for deleting a downloaded image
&#8594; check version  `docker rmi -f <image_ID>` for forcing an image to end and remove
In case image still doesn't stop use: 
try executing a shell in the docker container using the command  `docker exec -it imagebase bash` then issuing `kill 1` command.
- `docker ps` list all running containers 
- `docker run <container name>` run locally available container (before it downloads the image from dockerhub
- `docker run -it <container name>` to get the docker container running. Optional: 
- `docker start <container_ID)` to start container 
- `docker stop <container_ID)` to end container 
- `docker container rm [container_id]` to delete a docker container 
- `docker system prune` deletes all stopped containers
- `docker pull <image_name:version>` to download a docker image and if needed add version number as suggested on docker hub
- `docker run <image_name>` makes an container out of an image (containers as running instances of docker images) Container then gets listed at ``docker ps``
- `docker build -t <image_name:tag_name> <location_of_Dockerfile>` to build the image from the Dockerfile or more simple: `docker build .`


-----
---
