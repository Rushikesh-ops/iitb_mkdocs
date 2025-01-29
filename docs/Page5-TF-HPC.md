# Transferring files between local machine and HPC cluster

Users need to have the their data and applications related to their project or research work on PARAM Rudra. To store the data, special directories named “home” have been made available to the users. While these directories are common to all the users, each user will have their own directory with their username in the “/home/” directory, where they can store their data.

/home/`<username>`/ : This directory is generally used by the user to store their data and if need install their own applications.

However, there is a limit to the storage provided to users. The limits have been defined according to quota over these directories, and all users will be allotted the same quota by default. When a user wishes to transfer data from their local system (laptop/desktop) to the HPC system, they can use various methods and tools.

A user using the ‘Windows’ operating system will have access to methods and tools native to Microsoft Windows, as well as tools that can be installed on their Windows machine. Linux operating system users, however, do not require any tool. They can simply use the “scp” command on their terminal. Here’s how:

```
scp -r -P 22 <path to the local data directory> 
<username>@paramrudra.iuac.res.in:<path to directory on HPC where to save 
the data>
```

<!-- <span style="color:#259AA4;">Note: use port 22 within IUAC Institute -->

<div class="admonition note"> <p class=admonition-title>Note</p>
 <p>Note: use port 22 within IUAC Institute</p>
</div>

<!-- <span style="color:#259AA4;">
<marquee> This is a test announcement </marquee>
</span> -->

Example:

Same Command could be used to transfer data from the HPC system to other HPC system, or your own system.

```
scp -r -P 4422 <file path> <username@<cluster IP/hostname>:/home/user/<path>
```

<div class="admonition note"> <p class=admonition-title>Note</p>
 <p>Use port 22 within IUAC Institute</p>
<p> The local system (laptop/desktop) must be connected to a network that allows access to the HPC system. Additionally, please ensure that the firewall settings on your laptop are configured to allow access from the HPC system. </p>
</div>

Users are advised to keep a copy of their data once their project or research work is completed by transferring the data from PARAM Rudra to their local system (laptop/desktop). The command below can be used for file transfers in all the tools.

##### Tools

`<b style="color:#259AA4">`WinSCP (Windows installable application)`</b>`

This popular tool is freely available and is used very often to transfer data from Windows machine to Linux machine. This tool is GUI based which makes it very user-friendly.

Link for this tool is: [https://winscp.net/eng/download.php](https://winscp.net/eng/download.php)

<img src="/img/img6.png">

<p style="text-align: center;">Figure 6 - A snapshot of the "WinSCP" tool to transfer file to and from remote computer.</p>

<img src="/img/img7.png">

<p style="text-align: center;">Figure 7–Enter Captcha/String</p>

<div class="admonition note"> <p class=admonition-title>Note</p>
<p> Port Used for SFTP connection is 4422 and not 22. Please change it to 4422 </p>
</div>
