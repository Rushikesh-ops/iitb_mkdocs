# Best Practices for HPC

- Do NOT run any job which is longer than a few minutes on the login nodes. Login node is for compilation of jobs. It is best to run the job on compute nodes.
- It is recommended to go through the beginner’s guide in /home/apps/Docs/samples this should serve as a good starting point for the new users.
- Use the same compiler to compile different parts/modules/library-dependencies of an application. Using different compilers (e.g. pgcc + icc) to compile different parts of an application may cause linking or execution issues.
- Choosing appropriate compiler switches/flags/options (e.g. –O3) may increase the performance of the application substantially (accuracy of output must be verified). Please refer to documentation of compilers (online / docs present inside compiler installation path / man pages etc.)
- Modules/libraries used for execution should be the same as that used for compilations. This can be specified in the Job submission script.
- Be aware of the amount of disk space utilized by your job(s). Do an estimate before submitting multiple jobs.
- Please submit jobs preferably in $SCRATCH. You can back up your results/summaries in your $HOME
- $SCRATCH is NOT backed up! Please download all your data to your Desktop/ Laptop. 
- Before installing any software in your home, ensure that it is from a reliable and safe source. Ransomware is on the rise!
- Please do not use spaces while creating the directories and files.
- Please inform PARAM Rudra support when you notice something strange - e.g. unexpected slowdowns, files missing/corrupted etc.
