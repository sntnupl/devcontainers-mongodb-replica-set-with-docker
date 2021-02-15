# Set up a secure MongoDB Replica-set for development using Docker

This repository showcases how one can setup a secure MongoDB replica set for development using Docker.  
I have explained the details of the `docker-compose.yml` file in my blog post, link to which can be found [at this link]().

## Getting Started

Most of the stuff here will work out-of-the-box, and the containers will be up and running once you run `docker-compose up -d`.

However, if you check the `docker-compose.yml` file, we are relying on `.env` file to populate the env variables for the compose.
A `sample.env` file is provided here, use it to create your own `.env` file.

Also, we are using keyfiles for MongoDB nodes in the replica set to authenticate themselves.  
For that we need to initially create a keyfile.  

In Linux, the following are the commands:  
```bash
openssl rand -base64 700 > file.key
chmod 400 file.key
sudo chown 999:999 file.key
```

I have kept a sample file in `.docker/mongodb` path, but you must follow the steps above to generate a new `file.key`.  

Once the MongoDB replica set is up and running, Once everything comes up, you can run `docker-compose status`, and see something like this:  

![replica set status](./images/final-result.jpg?raw=true "Replica set status")


To connect to the replica set, you can use mongo client like so:   
```bash
$ mongo "mongodb://localhost:30001,localhost:30002,localhost:30003/<MONGO_INITDB_DATABASE>" -u <MONGO_INITDB_USERNAME>
```  
Remember to replace the `<MONGO_INITDB_DATABASE>` and `<MONGO_INITDB_USERNAME>` tags with appropriate values that you've put in the `.env` file.  

![Connect to Replica set](./images/connect-to-replica-set.jpg?raw=true "Connect to Replica set")

