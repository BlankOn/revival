# Repository Setup Guide with IRGSH

### 1. Prepare the dependencies and getting the executable binaries of IRGSH

Please consult to https://github.com/blankon/irgsh-go for documentation

### 2. Setup the keyring

The keyring will be derived from a GPG private key. This private key will be the primary security mechanism for this Linux distribution. Failure to secure this key will compromise the security of the entire distribution.

Currently, the private key is stored on the repository server. Some maintainers should also keep a secure backup of this key so that they can rebuild the repository in the event of a disaster.
