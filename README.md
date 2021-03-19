# Set up a secure MongoDB Replica-set for development using Docker

This repository showcases how one can setup a secure MongoDB replica set for development using Docker.  
I have explained the details of the `docker-compose.yml` file in my blog post, link to which can be found [at this link](https://sntnupl.com/mongodb-replicaset-for-development-using-docker).

**NOTE**  
This replica set is for educational and demonstration purposes ONLY.  
Running multiple nodes of a Replica Set within a single machine is an anti-pattern, and MUST BE AVOIDED in Production.  
Using this setup for local development though, is perfectly fine.  

## Getting Started

Most of the stuff here will work out-of-the-box, and the containers will be up and running once you run `docker-compose up -d`.
However, there are a few configuration steps that you would need to incorporate - which are mentioned below.

### Configure environmental variables  

If you check the `docker-compose.yml` file, we are relying on `.env` file to populate the env variables for the compose.
A `sample.env` file is provided here, use it to create your own `.env` file.

### Create keyfiles for MongoDB nodes to authenticate themselves

For this we need to create a keyfile.  

In Linux, the following are the commands:  
```bash
openssl rand -base64 700 > file.key
chmod 400 file.key
sudo chown 999:999 file.key
```

I have kept a sample file in `.docker/mongodb` path, but you must follow the steps above to generate a new `file.key`.  

### Update hostnames   

Once the replica set is up, you will need to update hostnames in local `/etc/hosts` file.  

<details><summary>Sample entry</summary>
<p>

```bash
127.0.0.1 localhost mongo1 mongo2 mongo3
```

</p>
</details>  

This change is needed to be done in all client machine, from where you would like to connect to this Replica Set.  
So if you are running this replica set in your local development machine, and want to connect to it, you would have to update `/etc/hosts` file in your local dev machine ONLY.  
However, if you want to connect to this replica set from a different machine, you would need to update the `/etc/hosts` file in that other machine as well.  

<details><summary>Sample entry on a different machine</summary>
<p>

```
10.20.30.40 mongo1 mongo2 mongo3
```  

Here, `10.20.30.40` is the public-ip of the machine where this replica set is running.
</p>
</details>  


**NOTE:** In windows, the hosts file is located at `C:\Windows\System32\drivers\etc\hosts`


## Result
Once the MongoDB replica set is up and running, Once everything comes up, you can run `docker-compose status`, and see something like this:  

![replica set status](./images/final-result.jpg?raw=true "Replica set status")


To connect to the replica set, you can use mongo client.  
<details>
<summary>Connect to replica set running on local machine</summary>
<p>  

```bash
$ mongo "mongodb://localhost:30001,localhost:30002,localhost:30003/<MONGO_INITDB_DATABASE>?replicaSet=rs0" -u <MONGO_INITDB_USERNAME> --authenticationDatabase admin
```  

![Connect to local Replica set](./images/connect-to-replica-set-local.jpg?raw=true "Connect to Replica set running locally")

</p>
</details>  

</br>

<details>
<summary>Connect to replica set running on local or remote machine</summary>
<p>

```bash
$ mongo "mongodb://mongo1:30001,mongo2:30002,mongo3:30003/<MONGO_INITDB_DATABASE>?replicaSet=rs0" -u <MONGO_INITDB_USERNAME> --authenticationDatabase admin
```  

As mentioned above, you MUST update the local hosts file in the client machine (running this command), to point to the public IP address of the machine where the replica set is running.  

![Connect to remote Replica set](./images/connect-to-replica-set-remote.jpg?raw=true "Connect to Replica set running remotely")

</p>
</details>  
</br>

Remember to replace the `<MONGO_INITDB_DATABASE>` and `<MONGO_INITDB_USERNAME>` tags with appropriate values that you've put in the `.env` file.  

Once connected you can use the more succinct `db.isMaster()` command to debug details like hosts of the replicaset a client must connect to, if the node is primary, etc.  

<details>
<summary>db.isMaster()</summary>
<p>

![Connect to remote Replica set](./images/db-ismaster.jpg?raw=true "Connect to Replica set running remotely")

</p>
</details>


Full documentation of the command is available [at this link](https://docs.mongodb.com/manual/reference/command/isMaster/#dbcmd.isMaster).  


