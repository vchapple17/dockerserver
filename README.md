# README #

This README would normally document whatever steps are necessary to get your application up and running.

### Rebuild dockerweb

  docker-compose down && docker-compose up --build -d

### composer and yarn build a project

transfer files then run something like...

  sudo docker exec -it profile composer --working-dir=/project_files/profile install

  docker exec -it profile yarn --cwd /project_files/profile install

  docker exec -it profile yarn --cwd /project_files/profile encore production --progress

### What is this repository for? ###

* Quick summary
* Version
* [Learn Markdown](https://bitbucket.org/tutorials/markdowndemo)

### How do I get set up? ###

* Summary of set up
* Configuration
* Dependencies
* Database configuration
* How to run tests
* Deployment instructions

### Contribution guidelines ###

* Writing tests
* Code review
* Other guidelines

### Who do I talk to? ###

* Repo owner or admin
* Other community or team contact
