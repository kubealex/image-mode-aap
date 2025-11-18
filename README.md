## Using a 'helper' image to avoid polluting the final image 

```bash
FROM registry.redhat.io/rhel10/rhel-bootc:latest as builder
RUN dnf install -y ansible-core 
RUN mkdir -p /root/ansible
ADD playbook.yml /root/ansible/
RUN cp -p /usr/bin/ansible-config /root/ansible/ansible-config
RUN cp -p /usr/bin/ansible-galaxy /root/ansible/ansible-galaxy
RUN cp -p /usr/bin/ansible-playbook /root/ansible/ansible-playbook


FROM registry.redhat.io/rhel10/rhel-bootc:latest 
# DEMO PURPOSE ONLY, ADD ADDITIONAL STEPS HERE (requirements install, packages, etc)

# Below this line we mount the paths from the builder image
RUN --mount=type=bind,from=builder,source=/usr/lib/python3.12/site-packages,target=/usr/lib/python3.12/site-packages,ro \
    --mount=type=bind,from=builder,source=/usr/lib64/python3.12/site-packages,target=/usr/lib64/python3.12/site-packages,ro \
    --mount=type=bind,from=builder,source=/root/ansible,target=/root/ansible,ro \
    /root/ansible/ansible-playbook /root/ansible/playbook.yml
```

## Direct method, needs cleanup after running the automation 

```bash
FROM registry.redhat.io/rhel10/rhel-bootc:latest as builder
RUN dnf install -y ansible-core 
RUN mkdir -p /root/ansible
ADD playbook.yml /root/ansible/
RUN ansible-playbook /root/ansible/playbook.yml
```
