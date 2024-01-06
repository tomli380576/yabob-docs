---
icon: cloud
order: 8
---

# How to run YABOB on a Cloud Server

For this guide, we are going to be using AWS cloud servers. If for some reason you choose not to use AWS, the steps should be the same for any other cloud server apart from how to instantiate a server

## Part 0: Set up

Pre-req: [Development Setup Guide](/dev-docs/dev-setup.md)

This guide assumes you have a decent grasp of the linux command line and file system. All commands are for linux based systems, i.e. probably won't work for windows cmd.

We highly recommend you to create a parent directory (aka folder) for the YABOB repo. We will be dealing with permission keys and ip addresses so it's safer to store them outside the git repo so that you never accidently commit sensitive info to the github repo.

The rest of the guide assumes you have set up a parent directory. For the rest of this guide, lets say the `YABOB` directory (which is connected to the github repo) is inside of another directory called `YABOB-super`

## Part 1: Creating an AWS EC2 Server

### Creating an AWS Account

TODO

### Navigating the AWS Dashboard

TODO

### Instantiating a new EC2 server

TODO

For now reference the offical documentation: https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-up-node-on-ec2-instance.html to create a new EC2 instance. Create an Ubuntu server instance, and if possible use the LTS. Make sure to use the free tier

Keep the private key in the `YABOB-super` directory and rename it to something of your choosing. For this guide we named the private key permission file as `YABOB-AWS-PRIVATE-KEY.pem`.

Also note down the IPv4 address of your EC2 instance. We will be refering to the address as `A.B.C.D` typically as `ubuntu@A.B.C.D`

If it's the account's EC2 instance you will get 1 year free trial. After that money will be automatically deducted from your account unless you set up prevention measures before. If you have disable billing, at the end of your free trial, you will lose access to the EC2 server instance you've created.

## Part 2: Connecting to an AWS EC2 Server

The following ssh command will give you remote access to the EC2 server instance you created

```sh
~/.../YABOB-super$ ssh -i YABOB-AWS-PRIVATE-KEY.pem ubuntu@A.B.C.D
```

After executing this command, you should now be inside the server. Try out some basic commands like `mkdir` and `cd` to get a feel of the system structure.

To return back to your local computer on the terminal, type the following command while in the EC2 server:

```sh
ubuntu@blahblahblah:~$ exit
```

## Part 3: How to transfer files to (and from) your EC2 instance

### Preface: The `scp` command (skip if you know what this does)

To copy a file from your computer to a server through ssh, the command is:

```sh
~/.../YABOB-super$ scp -i YABOB-AWS-PRIVATE-KEY.pem <path to local file> ubuntu@A.B.C.D:<path to destination directory>
```

e.g. lets say we are in the directory where the file `abc.xyz` is located and we want to transfer this file to the EC2 at the directory `~/qwerty`. The command would be as follows

```sh
~/.../YABOB-super$ scp -i YABOB-AWS-PRIVATE-KEY.pem ./abc.xyz ubuntu@A.B.C.D:~/qwerty
```

Try this out yourself!

The process for copying a file from the server to your local computer is similar and simply requires you to specify the server path first and then the local path

```sh
~/.../YABOB-super$ scp -i YABOB-AWS-PRIVATE-KEY.pem ubuntu@A.B.C.D:<path to file> <path to local destination directory>
```

e.g. Lets do the opposite of the previous example. Let's copy the file in the server whose path is `~/qwerty/abc.xyz` to the current directory

```sh
~/.../YABOB-super$ scp -i YABOB-AWS-PRIVATE-KEY.pem ubuntu@A.B.C.D:~/qwerty/abc.xyz ./
```

Try this out yourself!

If you try to copy over an entire directory using the either of the previous formats, you'll notice that the command line gives you an error saying you can't copy a directory. To resolve this issue, we need to specify the `-r` option so that the kernel knows to recursively call the copy command on any children of files that you specify.

e.g.:

```sh
~/.../YABOB-super$ scp -i YABOB-AWS-PRIVATE-KEY.pem -r ./qwerty-src ubuntu@A.B.C.D:~/qwerty-des
```

### Copying the YABOB repo to EC2

In theory, with the above knowledge, all we need to do to run YABOB on the EC2 server is to copy over the entire directory and then run the `npm run prod` command there. If you try to simply run:

```sh
~/.../YABOB-super$ scp -i YABOB-AWS-PRIVATE-KEY.pem -r ./YABOB ubuntu@A.B.C.D:~/YABOB
```

it will take a very long time. This is because you are also copying the `node_modules` folder which is very large in both size and number of files. It will also be trying to copy over git files which is unnecessary.

Instead, lets just copy over the necessary files. We chose the following since these are the only files required to compile and run the bot:

```
YABOB/src
YABOB/help-channel-messages
YABOB/package.json
YABOB/package-lock.json
YABOB/tsconfig.json
```
---
*NOTE: if in the future, you are unable to compile the bot on the EC2 instance due to RAM limitations do the following:*

1. Compile the node project locally
2. Copy over the `YABOB/dist` folder instead of `YABOB/src` and `YABOB/help-channel-messages`
(The following steps are in reference to the next section)
3. In the EC2 instance run `npm install`
4. Use `npm run dev_precompiled` or `npm run prod_precompiled` to run the project instead of `npm run dev` or `npm run prod`

---

Use the following template to create a file called `copy_files.sh` in `YABOB-super`. Replace `A.B.C.D` with your server ip address and modify the name of the permission file if you've saved it as something else

```sh
permission_file="YABOB-AWS-PRIVATE-KEY.pem"
server="ubuntu@A.B.C.D"

scp -i ${permission_file} -r YABOB/src YABOB/help-channel-messages YABOB/package.json YABOB/package-lock.json YABOB/tsconfig.json ${server}:~/YABOB/
```

**AVOID STORING THIS FILE INSIDE THE YABOB FOLDER SINCE YOU MAY ACCIDENTLY COMMIT SENSITIVE INFO TO THE REPO**

To run the shell file:
```sh
~/.../YABOB-super$ ./copy_files.sh
```

---
*NOTE: the kernel may tell you that you don't have permission to execute the `copy_files.sh` file. This is because all .sh files are usually unexecutable by default. To enable the file to be executable, run:*

```sh
~/.../YABOB-super$ chmod a+x ./copy_files.sh
```
---

## Part 4: Running a node.js app on EC2

### Installing and using the correct version of node.js

First check if `node.js` is alreayd installed in the server by running:
```sh
ubuntu@blahblahblah:~$ node -v
```

If present and it's a version less than 18.12.0, then use nvm to install and change to the latest version:

```sh
ubuntu@blahblahblah:~$ nvm install --lts
```

If node and/or nvm is not present, refer to [the offical AWS documentation to install node.js and nvm on EC2](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-up-node-on-ec2-instance.html)

### Running the project

Once you have the right node version installed, enter into the YABOB folder
```sh
ubuntu@blahblahblah:~$ cd YABOB
```

Then do npm install to install all the dependancies
```sh
ubuntu@blahblahblah:~/YABOB$ npm install
```

---

*NOTE: If `npm install` tells you there are vulnerabilities, run `npm audit fix` like it tells you to do fix the issue. If `npm audit fix` doesn't resolve all the vulnerabilities after multiple attempts, it is recommended that you try to resolve them manually, espeically if they're severe vulnerabilities*

---

Finally to run the project, do
```sh
ubuntu@blahblahblah:~/YABOB$ npm run dev
```

## Part 5: Using PM2 to run a process indefinetly on EC2 even when not remote accessing it

If you exit the server connection after doing `npm run dev`, even if you move the process to background, it's most likely going to stop running. To resolve this issue we will use `pm2` which allows us to run a process on a server even when we aren't remote accessing it. Another useful feature of pm2 is that it will automatically restart the process if the process were to crash

Install pm2 globally:
```sh
ubuntu@blahblahblah:~/YABOB$ npm install pm2 -g
```

The general template to run a process in pm2 is as follows:

```
ubuntu@blahblahblah:~/YABOB$ pm2 start <path to main file> --name <a name you choose>
```

for YABOB, we have the additional issue of having the process depend on environment variables. We solve this issue by defining the environmental variables before calling pm2:

```sh
ubuntu@blahblahblah:~/YABOB$ NODE_ENV=production pm2 start dist/src/app.js --name YABOB_PROD
```

Luckily for you, we consolidated the compilation and using pm2 to run the bot in the `npm run prod` command! So all you need to do is:
```sh
ubuntu@blahblahblah:~/YABOB$ npm run prod
```

*NOTE: It is not necessary to run `pm2 start` in the folder of the project, but we are doing so since we have environment variables. Also so that we can simply run `npm run prod`*

And now you're done! YABOB is now running on pm2! Exit the ssh session and double check that YABOB is still running

#### Some useful commands for pm2:

Viewing processes that pm2 is running:

```sh
ubuntu@blahblahblah:~/$ pm2 list
```

[Insert example image later]

To stop a process with id 'X' (which will be a number):

```sh
ubuntu@blahblahblah:~/$ pm2 stop X
```

To remove a process from pm2:

```sh
ubuntu@blahblahblah:~/$ pm2 delete X
```

## Part 6: Accessing Logs

Since you're running YABOB on pm2, the logs are not being printed out to the terminal. To access the logs,

Use this to see stdout (normal) logs:
```sh
ubuntu@blahblahblah:~/YABOB$ ~/.pm2/logs/app-out.log
```

Use this to see stderr (error) logs:
```sh
ubuntu@blahblahblah:~/YABOB$ ~/.pm2/logs/app-error.log
```

If you don't want to connect to the server to view the logs, use scp to copy over the log files to your local computer:

```sh
~/.../YABOB-super$ scp -i YABOB-AWS-PRIVATE-KEY.pem -r ubuntu@A.B.C.D:~/.pm2/logs/ ./
```

This will create a new folder in `YABOB-super` with the name `logs` which will contain the log files

*NOTE: if you are running multiple processes on pm2, all of the logs are combined into the same file, each for stdout and stderr. I.e., you will view a mix of logs from different processes when viewing pm2 logs*