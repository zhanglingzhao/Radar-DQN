Ersin - On the Use of MDPs in Cognitive Radar: An Application to Target Tracking  
Date: 7/27/2018  
Author: Jianyuan (Jet) Yu  
Contact: jianyuan@vt.edu   
Affiliate: Wireless, ECE, Virginia Tech



Table of Contents
=================

   * [Setup](#setup)
   * [How to run](#how-to-run)
      * [Mac OS](#mac-os)
      * [Ubuntu](#ubuntu)
   * [Running on ARC](#running-on-arc)
      * [single mode](#single-mode)
      * [batch mode](#batch-mode)
   * [Run Maltab  in terminal without GUI](#run-maltab--in-terminal-without-gui)
   * [Tricks to acccelerate the training by running codes at the same time](#tricks-to-acccelerate-the-training-by-running-codes-at-the-same-time)



# Setup
1. version: matlab 2018a, python 2.7, tensorflow 1.6.0
2. add in python & tensorflow into matlab path:  
in matlab terminal 
``` 
py.sys.path
insert(py.sys.path,int32(0),'/PATH_of_Tensorflow')
```  
the ```PATH_of_Tensorflow``` depend on where you install them, e.g. in my mac OS, they look like
```
insert(py.sys.path,int32(0),'/anaconda2/lib/python2.7/')
insert(py.sys.path,int32(0),'/anaconda2/lib/python2.7/site-packages')
```
after that, type in
```
py.importlib.import_module('dqn')
```
it will work if no error pop up.  


# How to run
## Mac OS 
**first setup**  refer to setup step 1-2
1. start matlab from the terminal(by the [guide](https://stackoverflow.com/questions/45733111/importing-tensorflow-in-matlab-via-python-interface)) from the PATH where /bin of Matlab is, in my Mac OS, it is like the change to path ```/Application/MATLAB_R2018a.app/bin```
2. after Matlab launch, change the path to where ```dqn.py``` file is located, type in 
    ```
    py.importlib.import_module('dqn')
    ``` 
to load the python module.  
3. execute ```RunSimulations.m``` file, notice replace the 'dqn' as 'mdp' solver(last second option) recurrent to Ersin mdp settings.
It seems Matlab2018a easily crash down. Thus we save the figure asap. To further save the command windows text(like running time or errors) by command [diary](https://www.mathworks.com/help/matlab/ref/diary.html), by doing so we can look up the logs of the terminal by the ```diary``` file.

---
## Ubuntu 
(Matlab version 2018a) User  **first setup** refer to setup step 1-2 as well, and then follow below  
1. downgrade tensorflow to version 1.5.0 to avoid kernel dead error in ubuntu, in terminal  
    ```
    conda install -c conda-forge tensorflow=1.5.0
    ```
2. replace(actually work as update) the ```libstdc++.so.6```,```libstdc++.so.6.0.22``` of matlab as the those ```libstdc++.so.6```,```libstdc++.so.6.0.22``` of anaconda.  
in terminal, type in command as below. The second command just archived the old lib, and third copy the new one from anaconda, similiar to the other lib.   
    ```
    cd /usr/local/MATLAB/R2018a/sys/os/glnxa64/
    sudo mv libstdc++.so.6 libstdc++.so.6.old
    sudo cp ~/anaconda2/lib/libstdc++.so.6 .
    sudo mv libstdc++.so.6.0.22 libstdc++.so.6.0.22.old
    sudo cp ~/anaconda2/lib/libstdc++.so.6.24 .
    ```

3. start matlab from terminal(same as Mac OS step 1)  
4. after Matlab launch, change path to where ```dqn.py``` file is locate(same as Mac OS step 2)
5. in Matlab, type in command
    ```
    py.sys.setdlopenflags(int32(10))
    ```
6. load the python module (same as Mac OS step 2)
7. execute ```RunSimulations.m``` file.  
After the first setup pass, next time we start the matlab from terminal, change path to where```dqn.py```  is, and run ```RunSimulations.m```.  
~~1. change directory to where ```dqn.py```  is~~  
~~2. for ubuntu in matlab type in  ```py.sys.setdlopenflags(int32(10))```, for Mac OS skip this step~~  
~~3. then in matlab type in  ``` py.importlib.import_module('dqn')```~~
-------------------------------------------------------------------------



# Running on ARC
## single mode
Recommend to run on ARC when large computation, we can launch MATLAB in ARC as below. Notice matlab(free with campus license) is installed on ```cascade``` while not on ```huckleberry```, and we running codes on GUI (meaning remote GUI helper app like ```XQuartz``` is needed).
1. If you are out of campus, login in *VT VPN*.
2. login in cascades like ```ssh -Y -C yourname@cascades2.arc.vt.edu```, where ```-Y``` for macOS and ```-X```
for Windows/Linux,  
3. after login, find where matlab is by ```module spider matlab```,  
4. select right version by ```module load matlab/R2018a```,  
5. load exact python 2.7 and tensorflow 1.5.0 version by creating**conda virtual environment**.    
    ```
        module purge
        module load Anaconda/2.3.0
        module load gcc
        conda create --name RadarDQN python=2.7
        source activate RadarDQN
        pip install --ignore-installed --upgrade  https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-1.5.1-cp27-none-linux_x86_64.whl

    ```
    where *RadarDQN* is self-defined name. For other tensorflow version, refer to [tensorflow build .whl](https://github.com/lakshayg/tensorflow-build).

6. then type ```matlab``` to start, after that ```XQuartz``` is launched and then Matlab is launched.
7. after that, remember to add in the PATH of tensorflow by
    ``` insert(py.sys.path,int32(0),'~/.conda/envs/RadarDQN/lib/python2.7/site-packages/') ```, and it would work if no error pop after the test ```py.importlib.import_module('dqn')```.   
8. excute local codes, or get the updated codes by ```scp``` or ```git pull```.  
I will update the guidance once *mcc* compiler goes through and *pbs* on ARC works.  

Notice:
1. Before ```git pull```, make ``` git clone ``` first. An easier way is clone in the **https** way rather than ssh, while for ssh way, please [set up public key](https://help.github.com/articles/error-permission-denied-publickey/#platform-linux) ahead. Moreover, every time you do ```git pull```, you may need to remove all ```.pyc``` files generated late time.
2. For ```scp``` command, double check the PATH of the remote server. e.g. ``` scp note.txt jianyuan@cascades1.arc.vt.edu:/home/jianyuan```.

## batch mode
*__Run the single mode with tiny iterations to ensure everything works well.__*  
As we config virtual env setting in job script file ```pbs.bash``` and config the tensorflow path in ```RunSimulations.m``` already, hence we could *skip step 3-5 and step 6-7*. In terminal type 
 ```
    qsub pbs.bash
```
It is under the folder, you could make some modification on it if necessary, and refer to [job script example](https://www.arc.vt.edu/userguide/v100_normal_q/).   
As for the resource line, the recommended setting is  
``` 
    #PBS -l nodes=4:ppn=16:gpus=2 
```
higher request would result in submit error or too long queueing.  

Notice:
1. Our group's identical allocation id is ```RadarDQN```, contact Dr Buehrer if your account is not on the group list. 
2. some basic commands to manage jobs status:
* ```qstat -u username``` check the status of the job, where *Q* for in queue waiting, *R* for running.
* ```showq -u jianyuan``` for some other info,
* ```qdel jobID```, delete some stuck job that queue too long, where *jobID* is *6-digit number* you can find by qstat,
* ```checkjob -v jobID``` to check more detailed info about job status, especial for some wrong setting in the *pbs.bash* file.
3. log files could be read by look into the output file ```pbs.bash.ojobID``` and error file ```pbs.bash.ejobID```, an easier way could be ```cat pbs.bash.ejobID```. By default, there should be an email telling you when the job starts off, and another email tells you when the job ends.
4. do remember to increase the *wall time* in ```pbs.bash``` file if you need more computation, while the estimation of wall time come from tiny test.
5. refer to [vim cheatsheet](https://vim.rtorr.com/) or Jet's [brief note of vim](https://github.com/yujianyuanhaha/blog/blob/master/source/_posts/tutorial/vim.md) to getting used to vim.



# Run Maltab  in terminal without GUI
Matlab support running in the terminal without GUI both for Mac and Ubuntu.
For Mac, excute ```/MATLAB_PATH/matlab -nodesktop -nosplash -r 'FILE_NAME' ```  
e.g ```/Applications/MATLAB_R2018a.app/bin/matlab -nodesktop -nosplash -r 'RunSimulations' ```.  
While for Ubuntu, after load matlab by ```module load matlab/R2018a```, execute like ```matlab -nodesktop -nosplash -r 'FILE_NAME' ```, where only difference is path name is not necessary needed.  
By default, plots will pop up when codes run to the end, and type ``` edit FILE_NAME.m``` can edit related files.



# Edit .gitignore to ignore non-programming file
This programmes automatically save down files, thus by default the Github would notice you about these non-programming file changes. Consider we only trace codes, a smart way is edit ```.gitignore``` file to rule out these files. e.g. in this case, edit  the .gitignore file (which is blanket by default) like 
``` shell
    **/*-Result
``` 
to ignore folders ending with name ```...-Result``` and files in it. In this way, folder naming like ```2018-Sep-02-173544-CONST-10000-1MEMSTATE-Results``` would not be traced.   
Refer to [bitbucks git ignore tutorial](https://www.atlassian.com/git/tutorials/saving-changes/gitignore) for more tricks.  
Notice if you have not edit `.gitignore` when init the repository, you need to remove all targeted files.


# Tricks to accelerate the training by running codes at the same time  
1. For GUI matlab, "open another instance" to run another case with diff parameter simultaneously.
2. For ARC, submit several jobs with different parameter codes.  


# Notice
1. ~~__"Undefined function or variable 'RunSimulations'."__, weired bug, just move the `RunSimulations.m` to somewhere else, and move it back. Once this file could be called by `Tab`, it could be run.~~ -> Do enable __Avaiable Offline__ of Google Drive.