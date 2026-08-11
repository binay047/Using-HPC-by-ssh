# 0.D. Using HPC by SSH

## Theory

`SSH (Secure Shell)` is used to remotely access an HPC or another Linux computer from a local PC. After connecting to the HPC, Quantum ESPRESSO calculations can be run remotely, calculation progress can be monitored, and files can be transferred between the local PC and the HPC using `scp`.

In this guide, the HPC IP address is `100.104.182.30` and the username is `dell`. Replace these with the appropriate username and IP address of your HPC.

## Procedure

### 1. Connect to the HPC using SSH

Open a terminal on the local PC and type:

    ssh dell@100.104.182.30

Enter the password when prompted.

After successful login, you are now inside the HPC terminal.

Check the current directory:

    pwd

List files and directories:

    ls

### 2. Moving Between Directories

Go into a directory:

    cd directory_name

Go to a specific directory:

    cd /path/to/directory

Go one directory back:

    cd ..

Return to the home directory:

    cd 

### 3. Creating Directories

Create a directory:

    mkdir directory_name
    
Remove a directory and its contents:

    rm -r directory_name

### 4. Copying and Moving Files Inside HPC

Copy a file:

    cp filename1 filename2

Copy a file to another directory:

    cp filename /path/to/destination/

Copy an entire directory:

    cp -r directory_name /path/to/destination/

Move a file:

    mv filename /path/to/destination/

Rename a file:

    mv old_name new_name

### 5. Copying Files from PC to HPC

To copy files or folders from the local PC to the HPC, run `scp` from the **local PC terminal**.

#### Copy a Single File

General format:

    scp /path/to/file/filename username@HPC_IP:/path/to/destination/

Example:

    scp /home/binay/Desktop/scf.in dell@100.104.182.30:/home/dell/DFT/

#### Copy an Entire Folder

Use `-r` to copy a directory and all its contents:

    scp -r /path/to/folder username@HPC_IP:/path/to/destination/

Example:

    scp -r /home/binay/Desktop/Tc2C dell@100.104.182.30:/home/dell/DFT/

Here:

- `scp` = secure copy
- `-r` = recursively copy the entire directory
- `/home/binay/Desktop/Tc2C` = folder on the local PC
- `dell@100.104.182.30` = username and IP address of the HPC
- `/home/dell/DFT/` = destination directory inside the HPC

### 6. Downloading Files from HPC to PC

To download files or folders from the HPC, open a terminal on the **local PC** in the directory where you want the files to be downloaded.

#### Download a Single File

General format:

    scp username@HPC_IP:/path/to/file/filename /path/to/local/destination/

Example:

    scp dell@100.104.182.30:/home/dell/DFT/scf.out /home/binay/Desktop/

#### Download an Entire Folder

Use `-r`:

    scp -r username@HPC_IP:/path/to/folder /path/to/local/destination/

Example:

    scp -r dell@100.104.182.30:/home/dell/DFT/Tc2C /home/binay/Desktop/

If you are already inside the directory on your PC where you want the file, use `.`:

    scp dell@100.104.182.30:/home/dell/DFT/scf.out 

For a folder:

    scp -r dell@100.104.182.30:/home/dell/DFT/Tc2C 

### 7. Monitoring the HPC

To see overall CPU and memory usage:

    top

Press `q` to exit `top`.

Check the total number of available CPU cores:

    nproc

Detailed CPU information:

    lscpu

### 8. Monitoring Quantum ESPRESSO Calculations

Check whether `pw.x` is running:

    pgrep -a pw.x

Count the number of `pw.x` processes:

    pgrep -c pw.x

Check whether `ph.x` is running:

    pgrep -a ph.x

Count the number of `ph.x` processes:

    pgrep -c ph.x

Check other Quantum ESPRESSO programs:

    pgrep -a q2r.x
    pgrep -a matdyn.x
    pgrep -a bands.x
    pgrep -a dos.x

**Note:** `pgrep -c ph.x` gives the number of `ph.x` processes. It should not automatically be interpreted as the exact number of CPU cores being used because MPI and OpenMP calculations can use different process/thread configurations.


### 9. Monitoring Calculation Output

Continuously monitor an output file:

    tail -f scf. out

For a phonon calculation:

    tail -f ph. out

Press `Ctrl+C` to stop monitoring the file. This does not stop the calculation itself.

Show the last 20 lines:

    tail -20 scf. out

Show the first 20 lines:

    head -20 scf. out

Search for total energy:

    grep "!" scf. out

Search for Fermi energy:

    grep "the Fermi energy" scf. out

Search for errors:

    grep -i "error" scf. out

Search for warnings:

    grep -i "warning" scf. out

### 10. Checking Whether a Calculation Has Finished

For most Quantum ESPRESSO calculations:

    grep "JOB DONE" *.out

For an SCF calculation:

    grep "JOB DONE" scf. out

For a phonon calculation:

    grep "JOB DONE" ph. out

For a relaxation calculation:

    grep "JOB DONE" relax. out

For a relaxation, you can also check:

    grep "End final coordinates" relax. out

### 11. Running Long Calculations with `nohup`

If you want to run a calculation in the background:

    nohup pw.x < scf.in > scf.out &

For a phonon calculation:

    nohup ph.x < ph.in > ph.out &

Check whether the calculation is running:

    pgrep -a pw.x

Monitor the output:

    tail -f scf. out

### 12. Using `screen`

`screen` is useful for keeping a terminal session alive during long calculations.
* screen --version
* sudo apt update
* sudo apt install screen

Start a screen session:

    screen -S qe

Run the calculation:

    pw.x < scf.in > scf.out

Detach from the screen session:

    Ctrl + A
    D

You can now disconnect from SSH.

List screen sessions:

    screen -ls

Reconnect to the session:

    screen -r qe
Remove the session:

     screen -S qe -X quit

### 13. Using `tmux`
* tmux --version
* sudo apt update
* sudo apt install tmux

Start a tmux session:

    tmux new -s qe

Run the calculation:

    pw.x < scf.in > scf.out

Detach from tmux:

    Ctrl + B
    D

List tmux sessions:

    tmux ls

Reconnect:

    tmux attach -t qe

 Remove:
 tmux kill-session -t qe


### 14. Checking User and System Information

Check the current user:

    whoami

Check currently logged-in users:

    who

Check the current date and time:

    date

### 23. Checking Quantum ESPRESSO Installation

Check the location of `pw.x`:

    which pw.x

Check `ph.x`:

    which ph.x

Check other Quantum ESPRESSO executables:

    which q2r.x
    which matdyn.x
    which bands.x
    which dos.x

### 15. Running Shell Scripts

Create a script:

    nano run.sh

Example:

    #!/bin/bash

    pw.x < scf.in > scf.out

Make it executable:

    chmod +x run.sh

Run it:

    ./run.sh


**Congratulations! You can now connect to the HPC, transfer files between your PC and HPC, run Quantum ESPRESSO calculations, monitor calculations, and download results for further analysis.**
