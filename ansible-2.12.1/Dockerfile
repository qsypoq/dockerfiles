FROM python:3.9

RUN pip3 install ansible-core==2.12.1 ansible
    
WORKDIR /ansible

CMD [ "ansible-galaxy", "--version" ]
