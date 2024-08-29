# ansible_for_vault
This is a place to experiment and share ideas for integrating Ansible and Vault.

## Reset local Administrator password on Windows host (break glass credential rotation)
Vault can generate a password according to a password policy.
Vault can set/track TTLs for passwords.
Vault can audit who accesses a break glass secret, and when.
Ansible can connect to Vault and to the Windows remote machine.
Ansible can reset the user's password.
Ansible can change the expiration settings for a user's password.


### Prerequisites
- Ansible needs an authentication mechanism for Vault. A custom credential type may be created, if necessary. This credential will need to be rotated and updated in Ansible within its TTL.
- Ansible needs authorization in Vault policies to read and write the paths where it should manage passwords.
- Ansible needs authentication and authorization on the remote machines to log in and change the local Administrator password. In this case, a domain administrator account will be used by Ansible to gain this access. The domain Administrator account is stored in the AAP password store.
- 


### Proposed rotation sequence if Ansible uses/stores a different credential (such as domain admin)

1. Ansible logs in to the remote machine with domain admin credential
2. Verify current password matches
    a. Ansible logs into Vault and queries current password

    b. Ansible checks if current password on the remote machine matches the entry in Vault

    b. If the password does not match, alert
3. Ansible uses the Vault API or a Vault module to generate a password using a password policy. The password is returned to Ansible.
4. Ansible makes an API call to Vault to write the password into an appropriate secrets engine. At this time, the best option is kv v2. In the future or for other types of credentials, there may be different secrets engines that support more streamlined workflows.
5. 