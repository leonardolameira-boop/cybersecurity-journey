# Access Control Lists (ACL) in Active Directory

Access Control Lists (ACLs) define who can do what on an object in Active Directory or on file system objects like folders and files.  
They are essential for security, governance, and enforcing the Principle of Least Privilege across your domain.

This document explains the components of ACLs, how they work in AD DS, and where they appeared directly in your lab.

---

## 1. What Is an ACL?

An ACL (Access Control List) is a list of permissions attached to an object.

Objects that use ACLs include:

- Active Directory objects (OUs, users, groups, computers, GPOs)
- File system objects (folders, files — NTFS permissions)
- Registry keys
- Services
- Printers

An ACL determines which users or groups can perform which actions on the object.

---

## 2. ACL Components — ACEs

An ACL contains multiple ACEs (Access Control Entries).  
Each ACE is one permission entry specifying:

- Who (user or group)
- What permission they have
- Where that permission applies

Example ACE (one line):

GG_FINANCE_RW — Modify — This folder only

This entire line is one ACE inside the ACL.

---

## 3. Types of ACLs

### 3.1 DACL (Discretionary Access Control List)

Defines which users or groups are allowed or denied access.

Common DACL permissions in AD:

- Read
- Write
- Reset Password
- Create/Delete Child
- Modify Group Membership
- Modify Permissions
- Full Control

Common DACL permissions in NTFS:

- Read
- Write
- Modify
- Full Control

---

### 3.2 SACL (System Access Control List)

Defines which actions generate audit logs, such as:

- Password resets  
- Permission changes  
- Failed access attempts  
- Object creation/deletion  
- Privileged group membership changes  

These logs support:

- SOC monitoring  
- SIEM ingestion  
- Auditing  
- Forensics  

---

## 4. ACL vs NTFS Permissions (Not the Same)

Both use ACLs, but apply to different objects.

AD ACL:
- Applies to AD objects like OUs, users, groups, GPOs
- Example permissions: Reset Password, Create/Delete Child, Modify Membership

NTFS ACL:
- Applies to files and folders
- Example permissions: Read, Write, Modify, Full Control

You used NTFS ACLs for Finance$, HR$, Sales shares.  
You used AD ACLs when restricting who can create users in the FINANCE OU.

---

## 5. Real Examples From Your Lab

### Example 1 — NTFS Permissions (Finance$)

You configured:

- GG_FINANCE_RW → Modify  
- Other departments → No Access  

This is an NTFS ACL.

---

### Example 2 — Delegating Control in OUs

Path:

OU=FINANCE,DC=DUNNES,DC=local

Only HR Admins could:

- Create users  
- Delete users  

This uses AD ACLs with Create/Delete Child permissions.

---

### Example 3 — GPO Security

Your Group Policies have ACLs defining:

- Who can read the GPO  
- Who can edit the GPO  
- Who can link the GPO to an OU  

These are AD ACLs.

---

### Example 4 — Audit Logs (SACL)

When someone:

- Changes a password  
- Modifies a group  
- Tries unauthorized access  
- Alters ACLs  

The SACL logs these events in the Security Log.

---

## 6. Viewing ACLs in PowerShell

Get ACL of an OU:

(Get-ACL "AD:\OU=FINANCE,DC=DUNNES,DC=local").Access

Add an Access Rule:

$identity = "DUNNES\GG_FINANCE_RW"
$ou       = "AD:\OU=FINANCE,DC=DUNNES,DC=local"

$rule = New-Object System.DirectoryServices.ActiveDirectoryAccessRule(
    $identity,
    "CreateChild,DeleteChild",
    "Allow"
)

$acl = Get-Acl $ou
$acl.AddAccessRule($rule)
Set-Acl -Path $ou -AclObject $acl

---

## 7. Summary

- ACL = full list of permissions  
- ACE = one permission entry (one line)  
- DACL = controls access  
- SACL = controls auditing  
- NTFS ACL = for files/folders  
- AD ACL = for Active Directory objects  

---

## 8. Why ACLs Matter

ACLs support:

- Least Privilege  
- Zero Trust  
- PAM / JIT  
- Defense in Depth  
- Audit & SIEM visibility  
- Prevention of privilege escalation  

ACLs are a core foundation of AD security and Blue Team operations.

