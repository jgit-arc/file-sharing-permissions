<p align="center">
<img src="https://i.imgur.com/M3rj4Ua.png" />
</p>

# Understanding File Sharing and Permission Controls

This walkthrough will cover moving resources across the network and allowing read, write, or deny access to users and groups. The goal is to understand the varying levels of access a user can (or cannot) have as designated by permissions and policies in Active Directory. This walkthrough references the previous Active Directory configuration and virtual machines found <a href="https://github.com/jgit-arc/ad-virtual-machines">here</a>. 

---

## 1. What is a file share?


<p>
<img src="hhttps://i.imgur.com/3S5vkok.png" />
<img src="https://i.imgur.com/MWFzM8s.png" />
</p>

A file share is essentially a folder located on a computer within the network that can be accessed by users. Access levels may vary, with some users having more permissions than others. For example, certain users might be allowed to edit or delete files. These access levels, known as "permissions," are typically assigned by the network administrator or based on the user's role or group within the network.

## 2. Creating sample file shares with various permissions

Log in to DC-1 as your domain admin account (mydomain.com\jane_admin). Log in to client-1 as a normal user (as usual, I recommend sticking with the same previously used account for consistency).

On DC-1, click the File Explorer icon. Select the Local Disk (C:) drive. In this directory, create four folders named:

  - read-access
  - write-access
  - no-access
  - accounting

The names of the folders are cosmetic. Until they are given permissions, they are just names and could be named anything. 



<p>
<img src="https://i.imgur.com/Hp0dP2z.png" />
</p>

Right-click the read-access folder. Click Properties. On the Sharing tab, click Share. Enter Domain Users and click Add. Under Permission Level, select Read and click Share.

Right-click the write-access folder. Click Properties. On the Sharing tab, click Share. Enter Domain Users and click Add. Under Permission Level, select Read/Write and click Share.

Right-click the no-access folder. Click Properties. On the Sharing tab, click Share. Enter **Domain Admins** and click Add. Under Permission Level, select Read/Write and click Share.

We will come back to the accounting folder later.

## 3. Access file shares as a normal user



<p>
<img src="https://i.imgur.com/GQGcGUK.png" />
<img src="https://i.imgur.com/qEPU8Me.png" />
</p>

We will verify that the permissions we just set are working. In client-1, click the File Explorer icon. In the address window, enter `\\dc-1` (note the backslashes). This is the same directory we just created but with two additional folders - NETLOGON and SYSVOL. These are automatically created with domain controllers and can be ignored. Also, notice how the accounting folder does not appear at all, as we did not assign it read permissions.



<p>
  <img src="https://i.imgur.com/LS4Rbqf.png" />
</p>

Open the read-access folder. If you attempt to create a new file, you will receive an error. This user (as part of Domain Users) only has read access.

Open the write-access folder. Here, you can create a new file without issue.


## 4. Create the ACCOUNTANTS Security Group and Assign Permissions



<p>
<img src="https://i.imgur.com/yoBS52u.png" />
<img src="https://i.imgur.com/SsP4kZj.png" />
</p>

In DC-1, if it's not already open, open Active Directory Users and Computers. Right-click mydomain.com, select New, and click Organizational Unit. Enter _GROUPS and click OK. Right-click mydomain.com and click Refresh. You will see the newly created OU _GROUPS.

In the _GROUPS window, right-click, select New, and click Group. In Group Name, enter ACCOUNTANTS, leave the other options on their default settings, and click OK.



<p>
<img src="https://i.imgur.com/AInlP4o.png" />
<img src="https://i.imgur.com/g7Gm2tn.png" />
</p>

Now, we will update the accounting folder we created earlier. Right-click the accounting folder. Click Properties. On the Sharing tab, click Share. Enter ACCOUNTANTS and click Add. Under Permission Level, select Read/Write and click Share.

Let's try to access the accounting folder. In client-1, return to the `\\dc-1` directory and refresh. Because we have updated the permissions, the accounting folder will appear. However, you will not be able to open it because this user account is not a member of the ACCOUNTANTS group we just created.

We will make this user a member of the ACCOUNTANTS security group. Log out of client-1.



<p>
<img src="https://i.imgur.com/Ou7xyfY.png" />
<img src="https://i.imgur.com/Kzsxn7m.png" />
<img src="https://i.imgur.com/meB6HBk.png" />
</p>

In DC-1, return to the ACCOUNTANTS security group in Active Directory Users and Computers. Click ACCOUNTANTS. In the Members tab, click Add. Enter the user you were logged into as on client-1, click Check Names, and click OK. Click Apply and OK.

We will try to access the accounting folder again. Sign back into client-1 as this user. Open File Explorer and enter `\\dc-1` into the address window. You will now be able to open the accounting folder with full read/write access.
