Having done all the steps on the employee on-boarding, I  I want to suggest some improvements on the documentation, to optimize the setup of the repository in the future, if possible:

- ## The link to the  whitebox-core repository must be updated

  ![[Pasted image 20230607195502.png]]
  
  Because after spending a long time trying to do the build without success, I realized thanks to Angela that <span style='color:#eb3b5a'>this was not the current repository</span>. 
  
![[Pasted image 20230607200008.png]]

That simple change could improve this little but important process.

- ## More details to help with the login on ghcr.io step on Linux systems 
  (Ubuntu at least, that was my case)

![[Pasted image 20230607200841.png]]

This step took me much longer than expected, due to lack of information and ambiguity in the official *<span style='color:#3867d6'>pass</span>* documentation (one of the secure options for login to ghcr.io through docker).

I had been trying to log in without success for at least 1 hour. Going through the docker documentation, then the pass documentation, then googling... 
And finally I succeeded in the following way:

Requirements:
- install pass
- Docker (for obvious reasons).
- install docker-credential-pass (if you don't have it already)
  
These was the steps I follow:

1. **Install pass, create a GPG key and initialize a password storage**

   ```bash
   sudo apt-get install pass
   ```

```bash
   gpg --gen-key
```

```bash
   pass init "Your GPG ID recently created"
```


2. **Install docker-credential-pass (if you don't have it already)**

```bash
   wget https://github.com/docker/docker-credential-helpers/releases/download/v0.6.3/docker-credential-pass-v0.6.3-amd64.tar.gz
   tar -xf docker-credential-pass-v0.6.3-amd64.tar.gz
   chmod +x docker-credential-pass
   sudo mv docker-credential-pass /usr/local/bin/
```


3. **Add the binaries to the $PATH**

```bash
   echo 'export PATH=$PATH:/usr/local/bin/' >> ~/.bashrc
   source ~/.bashrc
```

you have to change `~/.bashrc` with `~/.zshrc` if you are using Zsh.

4. **Configure docker to use pass as the credentials store**

	 1. Open the docker configuration file with your preferred text editor with `sudo` privileges, and then add this configuration:

```bash
   sudo nano ~/.docker/config.json
```

```json                         
{
	"credsStore": "pass"
}
```


5. **Now you can login into ghcr.io easily** :) 👍

```bash
	╰─ docker login ghcr.io
Username: YourGhUsername
Password: YourPersonalGhToken
Login Succeeded
```


- ## Add information that it is essential to have the git submodules configured correctly before `make install`

It is important to point out that it is necessary to initialize the <span style='color:#eb3b5a'>git submodules</span> in order to have all the core dependencies in the local repository, so we can run make install successfully.

[Git submodules in Notion](https://www.notion.so/whtbx/Project-development-guide-584011be121f4ac3a98b3d603d571afa#98e584554f3d4f6fbe46b27888eff990)

That made me waste some time as well 