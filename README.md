## Reproducible Jupyter Notebook using Singularity Container

I *love* singularity containers and am conviced that they will play an instrumental role in making data-heavy research more reproducible. 
There is, however, one thing that I had a hard time wrapping my head around that might be of general interest to the less container-savy users of singularity:
By default, singularity mounts the user home directory. 
While this is often convenient, it also was a source of great confusion to me since I felt that my containers where more isolated than they actually are.
With the default behavior, user configurations in `~/home` tend to get precedence over system-wide configurations when executing the container thus potentially changing the computing environment. 
From a reproducibility point of view this is very important to keep in mind when working with singularity containers!
A good example to illustrate the issue is the installation of jupyter notebook + R kernel in a singularity container.
By default, jupyter writes kernel files in the users `~/.jupyter` configuarion folder and gives it precedence for config look-up during execution as well.
Since the user home folder during execution of the container image will typically differ from the one during building the container (root!), this means that jupyter will complain about missing kernel files.

Following the helpful discussion in https://groups.google.com/a/lbl.gov/forum/#!topic/singularity/wyxST5kHmCg (thanks!), I implemented a minimal working example of how to set up a reproducible jupyter notebook using singularity in this repository.
In essence, I redirect the jupyter search paths to the system config locations manually.
During runtime, the home directory of the container is redirected to a temporary folder to avoid any dependencies on further configuration files.
First, build the container
```bash
$ sudo ./build-container
```
then start the notebook server 
```bash
$ ./start-notebook-server
```
Take a look at the scripts above to see how the build and start of the server works. Follow the link given in the command line to open the browser interface to the notebook server and open the `demo.ipynb` notebook which uses an IRkernel and just contains a single line of R code. Try saving, and see that it works! Note that this approach provides a reproducible notebook environment (with writable for your notebooks) but does not ensure that the notebook without the container is reproducible.
