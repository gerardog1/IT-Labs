# Hands On Active Directory
We RDP into the machine with the given IP and credentials provided by the room
## Active Directory Structure
Active Directory is accessed on this admin account, the structure is as shows:
<img width="550" height="451" alt="image" src="https://github.com/user-attachments/assets/c538bea6-e586-4ef1-a132-430a821d98fe" />
## Deleting extra OU
To delete extra OU's in AD, you must enable Advanced Features in the View tab, OUs are protected against accidental deletion by default. 
<img width="420" height="472" alt="image" src="https://github.com/user-attachments/assets/6eb3500d-9cca-4546-8222-50d3811b67dc" />

Unchecking this box lets you delete the specifc OU selected.
## Delegation 
In the case of needing to delegate permissions to a user, you do so by right clicking the department and click Delegate.
<img width="311" height="372" alt="image" src="https://github.com/user-attachments/assets/f45a52b6-8857-433e-9f89-e1e6029e63e9" />

This opens a new window where you're asked to enter the users whom you want to delegate control.
<img width="583" height="570" alt="image" src="https://github.com/user-attachments/assets/98199e6a-e054-451e-a34b-76758f689fdd" />

In this lab we delegate control to Phillip, type his name and click check names, click OK.

Next it will prompt you to select tasks to delegate.

<img width="531" height="414" alt="image" src="https://github.com/user-attachments/assets/e0aba5f9-091b-4ce0-902f-9f40319d8d33" />

Phillip however can't access AD like we did, they must use the following command in powershell:
```bash
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```
> Note: Sophie is in the department that we delegated control to Phillip.

In this lab, we changed Sophie's password as Phillp. Since we we wouldn't want Sophie to keep on using a password we know, we can force a password reset at the next logon for security best practice, with the following command:
```bash
Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose
```
## Managing Computers
By default, all the machines that join a domain (except for the DCs) will be put in the container called "Computers".
<img width="754" height="417" alt="image" src="https://github.com/user-attachments/assets/7423e833-7ddb-4c53-82ce-6bac28a4c1d3" />

There is not only machines here, but also servers. We would very likely want the machines used daily to have different policies from the servers. Let's do this by creating seperate OUs; Workstations, Servers, & Domain Controllers (auto created by Windows)
<img width="754" height="366" alt="image" src="https://github.com/user-attachments/assets/3d0748fb-ac5f-4253-b967-733f1fb5a81b" />

Now we move the corresponding machines to our newly made OUs.
## Group Policies
In Windows we use Group Policy Objects to push different configurations and security baselines to users depending on their department. GPOs are simply a collection of settings that can be applied to OUs. GPOs can contain policies aimed at either users or computers, allowing you to set a baseline on specific machines and identities.

To configure GPOs, you can use the Group Policy Management tool, available from the start menu.

Upon launching the tool, you will see your complete OU hierarchy. To configure Group Policies, you first create a GPO under Group Policy Objects and then link it to the OU where you want the policies to apply. 
<img width="954" height="577" alt="image" src="https://github.com/user-attachments/assets/0c4fae1d-441a-4666-b148-def3507be56d" />



