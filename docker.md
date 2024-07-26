# UID and GUI remap

When start a project, create a user in Dockerfile and on the host with the same UID/GUI.

On host:
```bash
#!/bin/bash

USERNAME="username"
USER_ID="2024"

set -eux
groupadd -r $USERNAME -g $USER_ID
useradd -r -g $USER_ID -u $USER_ID --home-dir=/home/${USERNAME} --shell=/bin/bash $USERNAME;
mkdir -p /home/${USERNAME}; chown ${USERNAME}:${USERNAME} /home/${USERNAME}
```

In Dockerfile:
```
# ----- users, groups, permissions
USER root
RUN set -eux; \
    groupadd -r username -g 2024; \
    useradd -r -g 2024 -u 2024 --home-dir=/home/username --shell=/bin/bash username; \
    mkdir -p /home/username; chown username:username /home/username;
```

And finally we can add our user to the group 2024
```bash
groupadd -r slerest -g 2024
```

This script resolve all permission problem, add more directory to the loop if needed:
```bash
#!/bin/bash

set -eu

for d in \
    db/ \
    data/ \
    py/ \
    pgadmin/
do
     if [ ! -d $d ]
     then
         echo "Skip $d (no such directory)"
         continue
     fi

    sudo chown -R username:username $d
    # Give all the user right to the group, so the developper host user can be
    # added in the "nepsys" group and have the right on the files share beween
    # host and container.
    sudo chmod -R g=u $d
done
```
