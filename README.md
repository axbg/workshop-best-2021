# Closing the Gap: *From Development to DevOps* - Accenture Webinar@BEST 2021

#### The POC  developed for Accenture's webinar held at an [online event](https://www.facebook.com/events/947557562778278) organized by [ASMI](https://bestbucuresti.ro/)
#### The project consists of a CI/CD GitHub Actions Pipeline which builds and deploys a Java application using [AWS S3](https://aws.amazon.com/s3/) and [AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/).
#### The application is written using Spring Boot and leverages [AWS Rekognition](https://aws.amazon.com/rekognition/) and [TheCocktailDB API](https://www.thecocktaildb.com/api.php) to help students find out what cocktails can be made with the ingredients they already have. :)

# Content
1. [Requirements](#1-requirements)
2. [The application](#2-the-application)
3. [Configurations for CI/CD](#3-configurations-for-cicd)
4. [Contribution](#4-did-it-work-)


## 1. Requirements
- Tools
    - Java 11+
    - Maven 3.6+
    - Git
    
- Accounts
    - GitHub free account
    - AWS account (mostly free)


## 2. The application
The application is, actually, an API that exposes 3 endpoints.

- POST - /api/drinks - Multipart request body
    - receives multiple pictures which contain common cocktail ingredients and make use of Rekognition to extract
      their names, which are then sent to TheCocktailDB API to retrieve the cocktails that can be made with all the
      possible combinations

- POST - /api/drinks - JSON request body
    - the same as the previous endpoint, but instead of receiving pictures, it receives ingredients in a JSON format -
      it's helpful when ingredients without obvious appearance are used with which Rekognition has a hard time

- GET - /api/drinks/:id
    - sends the ID of a drink obtained from one of the previous results to obtain full information about it, including
      instructions

The [Insomnia project](./api.json) included in the repository shows exactly how these endpoints have to be called.
![Insomnia project snippet](.screenshots/0.jpg)


## 3. Configurations for CI/CD
The GitHub Action responsible for building, testing, and deploying the application can be
found [here](.github/workflows/ci-cd.yml) and it contains useful explanations on the most interesting steps.

To make the pipeline work, you have to create and configure some AWS resources first.  

It's a UI driven process, so it should be pretty easy.

### a. Creating a new AWS user
- From your AWS dashboard, search for the **IAM** service and navigate to it.
  ![IAM search](.screenshots/1.jpg)

- Navigate to the *Users* interface.
  ![IAM dashboard](.screenshots/2.jpg)

- Open the *Add users* form.
  ![IAM add user](.screenshots/3.jpg)

- Insert a name and select *Access key - Programmatic access*.
  ![IAM insert name](.screenshots/4.jpg)

- Open the *Attach existing policies* panel and select the same permissions that can be observed in the attached picture.
  ![IAM select permissions](.screenshots/5.jpg)

- You can attach tags to the newly created user to help you identify it faster in future searches.
  ![IAM add tags](.screenshots/6.jpg)

- Review the selected permissions and confirm the creation of the new user.
  ![IAM confirm creation](.screenshots/7.jpg)

- Copy the *Access key ID* and *Secret access key* in a safe place - we will need them soon.
- You can also download the generated CSV file containing both secrets.
  ![IAM user credentials](.screenshots/8.jpg)


### b. Creating an S3 bucket
With the user created, we can now move on and create the required resources.

- The first one would be the S3 bucket.
  ![S3 search](.screenshots/9.jpg)

- Open the *Create bucket* form.
  ![S3 create bucket](.screenshots/10.jpg)

- Insert a name for the bucket and select a region - be aware that the name should be unique in the selected region.
- *It's a good practice to choose the region closest to your final users*.
  ![S3 insert name](.screenshots/11.jpg)

- After you insert the name and select a region, everything else should be left as default - just confirm the bucket creation.
![S3 confirm creation](.screenshots/12.jpg)


### c. Creating & configuring an Elastic Beanstalk instance
- After the S3 bucket creation, we can start to configure the Elastic Beanstalk instance that will run our application.
  ![EBS search](.screenshots/13.jpg)

- Open the *Create Application* form.
  ![EBS create app](.screenshots/14.jpg)

- Insert an application name and configure the platform selection as shown in the picture. 
- *Because the application is written in Java 11, we will choose the Corretto 11 platform.*
  ![EBS insert name](.screenshots/15.jpg)

- Make sure that the *Sample application* radio button is selected, and confirm the application selection.
  ![EBS confirm creation](.screenshots/16.jpg)

- It will take a few minutes for AWS to prepare our application and its environment.  
  After the initialization finishes, we should modify the default configuration a bit. 
- Open the *Configuration* panel for the environment that was attached to the newly created application, 
  and click on the *Edit* button in the Software row.
  ![EBS open configuration](.screenshots/17.jpg)

- Scroll down until you find the *Environment properties* section where you should change the default port from *5000*
  to *8080* and add 2 new properties: *AWS_ACCESS_KEY_ID* and *AWS_SECRET_ACCESS_KEY* and fill their values with the keys
  of the user that was previously created.
- After that, click on apply and the environment will be restarted with the new configurations.
  ![EBS configure env](.screenshots/18.jpg)


### d. Configuring GitHub
The last platform we have to configure is GitHub.

First, you'll need a repository where the application code has to be uploaded.

- The fastest way to do it is to use the fork method - this way a copy of this repository will be created with you as the
  owner!
  ![GitHub fork](.screenshots/19.jpg)

- Navigate to the *Settings* section of the forked repository or the repository where you uploaded the application.
  ![GitHub open settings](.screenshots/20.jpg)

- Click on the *New repository secret*.
  ![GitHub new repository secret](.screenshots/21.jpg)

- In the form that popped up, you have to insert the *AWS_ACCESS_KEY_ID* and *AWS_SECRET_ACCESS_KEY* values that you also
  user for the Elastic Beanstalk configuration.

- *Make sure to add both of them!*

- In the end, the *Repository secrets* section should look just like this.
  ![GitHub repository secrets](.screenshots/22.jpg)

- With the secrets in place, the last step is to enable GitHub Actions for your repository.
  ![GitHub enable actions](.screenshots/23.jpg)

Now, to trigger the pipeline, you have to make a push or a pull request into the main branch.

*The fastest and most harmless way would be to change something in the README.md file.* 

Come on, give it a try!

*If the pipeline succeeded, the URL where the application is published can be found in the AWS Elastic Beanstalk dashboard.*
![Elastic Beanstalk published URL](.screenshots/24.jpg)


## 4. Did it work? :)
If not, please feel free to contribute by creating a pull request into the 
[original repository](https://github.com/axbg/workshop-best-2021).

Thank you!
