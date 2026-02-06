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
<img width="583" height="570" alt="image" src="https://github.com/user-attachments/assets/b38fc0ae-4ac3-4acc-a9b3-9146069729fb" />

In this lab we delegate control to Phillip, type his name and click check names, click OK.

Next it will prompt you to select tasks to delegate.
<img width="531" height="414" alt="image" src="https://github.com/user-attachments/assets/e0aba5f9-091b-4ce0-902f-9f40319d8d33" />
Phillip however can't access AD like we did, they must use the following command in powershell:
```bash
\Users\phillip> Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```
> Note: Sofie is in the OU that we delegated control to Phillip.


