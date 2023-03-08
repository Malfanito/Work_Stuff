# Name Changes
Not too common for us at our current company, but this is a simple enough problem with a simple enough solution. This most commonly arises with newly weds (or newly divorced), but can happen for all sorts of reasons! Don't want to be displaying incorrect names for users in Microsoft Office, Teams, etc.

## Doing this manually
As all things are, its imporant to know how they are done through the GUI before you begin automation.

Thankfully, this one is simple.

Right click the user in AD -> Rename -> Fill out the forms with their new name -> Hit OK

That mostly covers it, but forgets a crucial part of the process - their email! The renaming function does just that - renaming. We will still have to go into the user account and edit the user's email AND (super importantly) label their old email as an email alias so that email to the old email address still comes through.

Open user account in AD -> Attribute Editor -> (I forget what this is called, **UPDATE**) -> enter old email -> Apply, OK

This process will take some time to replicate throughout, so let it cook for awhile and confirm the change through Microsoft Outlooks address book, Teams, or any other programs you may have synced with AD.

## Automation
Churn up Powershell (or Visual Studio Code, I'll join you there someday!), and let's get to it!

First off - the cmdlet for renaming users is `Set-ADUser` and the syntax will begin similar to this:

```
Set-ADUser -Identity $SAMName
```

**NOTE:** `Set-ADUser` can do ***A LOT*** including this, so please becareful and use the `-WhatIf` switch if you're doing testing in prod...

A good first step to seeing what all `Set-ADUser` will need to do is to observe what all changes when we manually rename a user through the GUI. I'll spare the details and list them for you here: userPrincipalName, displayName, distinguishedName, and sAMAccountName.

Our code is shaping up to be something like:
```
Set-ADUser -Identity $SAMName -UserPrincipalName $UserLogonName -DisplayName $DisplayName -GivenName $NewFirstName -SurName $NewLastName -SamAccountName $NewSAMName
```

Whew! Going to be a **LOT** of variables declared and manipulated, but nothing complicated. Lets begin with what information we will need then move towards how to manipulate that information to get what we need.

#### What We Need
+ Current First Name
+ Current Last Name
+ New First Name
+ New Last Name

#### Manipulation
+ $SAMName
  + This can be made by 
