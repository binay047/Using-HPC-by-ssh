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

List files with detailed information:

    ls -lh

List hidden files:

    ls -la

### 2. Moving Between Directories

Go into a directory:

    cd directory_name

Go to a specific directory:

    cd /path/to/directory

Go one directory back:

    cd ..

Return to the home directory:

    cd 

Show the current directory:

    pwd

### 3. Creating Directories

Create a directory:

    mkdir directory_name
    
Remove a directory and its contents:

    rm -r directory_name

### 4. Copying and Moving Files Inside HPC

Copy a file:

    cp file1 file2

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

    scp dell@100.104.182.30:/home/dell/DFT/scf.out .

For a folder:

    scp -r dell@100.104.182.30:/home/dell/DFT/Tc2C .

### 7. Monitoring the HPC

To see overall CPU and memory usage:

    top

Press `q` to exit `top`.

If `htop` is available:

    htop

Check the total number of available CPU cores:

    nproc

Detailed CPU information:

    lscpu

Check memory usage:

    free -h

Check disk space:

    df -h

Check the size of the current directory:

    du -sh .

Check the size of a specific directory:

    du -sh directory_name

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

### 9. Running Quantum ESPRESSO

Run an SCF calculation:

    pw.x < scf.in > scf.out

Run a relaxation:

    pw.x < relax.in > relax.out

Run a phonon calculation:

    ph.x < ph.in > ph.out

Run `q2r.x`:

    q2r.x < q2r.in > q2r.out

Run `matdyn.x`:

    matdyn.x < matdyn.in > matdyn.out

### 10. Running Quantum ESPRESSO with MPI

Run `pw.x` using 4 MPI processes:

    mpirun -np 4 pw.x < scf.in > scf.out

Run `ph.x` using 4 MPI processes:

    mpirun -np 4 ph.x < ph.in > ph.out

Run `q2r.x` using 4 MPI processes:

    mpirun -np 4 q2r.x < q2r.in > q2r.out

Run `matdyn.x` using 4 MPI processes:

    mpirun -np 4 matdyn.x < matdyn.in > matdyn.out

### 11. Monitoring Calculation Output

Continuously monitor an output file:

    tail -f scf.out

For a phonon calculation:

    tail -f ph.out

Press `Ctrl+C` to stop monitoring the file. This does not stop the calculation itself.

Show the last 20 lines:

    tail -20 scf.out

Show the first 20 lines:

    head -20 scf.out

Search for convergence:

    grep "convergence has been achieved" scf.out

Search for total energy:

    grep "!" scf.out

Search for Fermi energy:

    grep "the Fermi energy" scf.out

Search for errors:

    grep -i "error" scf.out

Search for warnings:

    grep -i "warning" scf.out

### 12. Checking Whether a Calculation Has Finished

For most Quantum ESPRESSO calculations:

    grep "JOB DONE" *.out

For an SCF calculation:

    grep "JOB DONE" scf.out

For a phonon calculation:

    grep "JOB DONE" ph.out

For a relaxation calculation:

    grep "JOB DONE" relax.out

For a relaxation, you can also check:

    grep "End final coordinates" relax.out

### 13. Running Long Calculations with `nohup`

If you want to run a calculation in the background:

    nohup pw.x < scf.in > scf.out &

For a phonon calculation:

    nohup ph.x < ph.in > ph.out &

Check whether the calculation is running:

    pgrep -a pw.x

Monitor the output:

    tail -f scf.out

### 14. Using `screen`

`screen` is useful for keeping a terminal session alive during long calculations.

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

### 15. Using `tmux`

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

### 16. Finding Files

Find a specific file:

    find . -name "scf.in"

Find all Quantum ESPRESSO output files:

    find . -name "*.out"

Find directories:

    find . -type d -name "out"

### 17. Searching Inside Files

Search for a word:

    grep "Fermi" scf.out

Case-insensitive search:

    grep -i "fermi" scf.out

Search recursively:

    grep -R "JOB DONE" .

### 18. Compressing Calculation Files

Compress an entire calculation directory:

    tar -czvf calculation.tar.gz calculation/

Extract a `.tar.gz` file:

    tar -xzvf calculation.tar.gz

View the contents without extracting:

    tar -tzvf calculation.tar.gz

Compressing a large calculation directory before using `scp` can make file transfer easier and reduce the number of individual files that need to be transferred.

### 19. Checking Disk Space

Check available disk space:

    df -h

Check the size of a calculation directory:

    du -sh calculation/

Check the size of directories in the current location:

    du -h --max-depth=1 | sort -h

### 20. Checking Memory

Check memory usage:

    free -h

Detailed memory information:

    cat /proc/meminfo

### 21. Checking CPU Information

Show CPU information:

    lscpu

Show the number of available CPU cores:

    nproc

### 22. Checking User and System Information

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

Check the Quantum ESPRESSO executable:

    pw.x -h

### 24. File Permissions

Make a shell script executable:

    chmod +x script.sh

Run the script:

    ./script.sh

Give the owner read, write, and execute permission:

    chmod 700 script.sh

### 25. Running Shell Scripts

Create a script:

    nano run.sh

Example:

    #!/bin/bash

    pw.x < scf.in > scf.out

Make it executable:

    chmod +x run.sh

Run it:

    ./run.sh

### 26. Basic HPC Workflow

The typical workflow is:

    Local PC
       |
       | ssh
       ↓
    HPC
       |
       | Prepare input files
       ↓
    Run Quantum ESPRESSO
       |
       | Monitor using top / pgrep / tail
       ↓
    Calculation completed
       |
       | scp
       ↓
    Local PC
       |
       | Analyze results
       ↓
    XCrySDen / Xmgrace / Python / MATLAB / other tools

### 27. Quick Reference

#### SSH Login

    ssh dell@100.104.182.30

#### PC → HPC: Single File

    scp /path/to/file/filename dell@100.104.182.30:/path/to/destination/

#### PC → HPC: Folder

    scp -r /path/to/folder dell@100.104.182.30:/path/to/destination/

#### HPC → PC: Single File

    scp dell@100.104.182.30:/path/to/file/filename /path/to/local/destination/

#### HPC → PC: Folder

    scp -r dell@100.104.182.30:/path/to/folder /path/to/local/destination/

#### Monitor HPC

    top

#### Monitor `pw.x`

    pgrep -a pw.x
    pgrep -c pw.x

#### Monitor `ph.x`

    pgrep -a ph.x
    pgrep -c ph.x

#### Monitor Output

    tail -f scf.out

#### Check Calculation Completion

    grep "JOB DONE" scf.out

#### CPU Information

    nproc
    lscpu

#### Memory

    free -h

#### Disk Space

    df -h

#### Run QE with MPI

    mpirun -np 4 pw.x < scf.in > scf.out

#### Run Long Calculation

    screen -S qe

or:

    tmux new -s qe

### 28. Disconnecting from HPC

When all work is finished, disconnect from the HPC using:

    exit

Alternatively:

    Ctrl + D

You will return to the terminal of your local PC.

## Important Notes

- **Note:** `scp` commands for transferring files are normally executed from the **local PC terminal**. You do not need to SSH into the HPC first.

- **Note:** Replace `dell@100.104.182.30` with the correct username and IP address of your HPC.

- **Note:** `pgrep -c ph.x` counts the number of `ph.x` processes. It does not necessarily represent the exact number of CPU cores being used by the calculation.

- **Note:** For long calculations, use `screen`, `tmux`, `nohup`, or the HPC's job scheduler instead of relying on a normal SSH terminal.

- **Note:** If the HPC uses a scheduler such as SLURM or PBS, large production calculations should normally be submitted through the scheduler rather than run directly on the login node.

- **Note:** Avoid using `rm -rf` unless you are completely certain about the path and files being removed.

- **Note:** Keep important calculation results backed up on another system.

- **Note:** For large calculations, it is often convenient to compress the calculation directory using `tar` before transferring it with `scp`.

**Congratulations! You can now connect to the HPC, transfer files between your PC and HPC, run Quantum ESPRESSO calculations, monitor calculations, and download results for further analysis.**
