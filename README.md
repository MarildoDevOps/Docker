############################# Windows 10 Pro + Docker
# Baixando a imagem do centos7 do docker hub
docker pull centos

# Criando o container com 
docker run -it --name ANSIBLE -p 8080:2121 centos /bin/bash

# Dentro do container ( /bin/bash )
yum update -y
yum install -y openssh-server
yum install -y openssh-clients
yum install vim net-tools wget mtr dos2unix
yum groupinstall 'Development Tools'

# Criando um usuario local + a chave de acesso
useradd nsvcadm
mkdir -p /home/nsvcadm/.ssh
touch /home/nsvcadm/.ssh/authorized_keys
chown nsvcadm /home/nsvcadm/.ssh -R
chmod 600 /home/nsvcadm/.ssh/authorized_keys

# Gerando a chave principal do sistema
ssh-keygen -q -J "" -t rsa -f /etc/ssh/ssh_host_rsa_key

# Edite o arquivo /etc/ssh/sshd_config e comente as linhas abaixo
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
UseDNS no

# Ligando o serviço de sshd
/usr/sbin/sshd -D &

# Definir a senha dos usuarios
passwd nsvcadm
passwd root

# Sainda do console do docker sem derrubar a maquina
CRTL + PQ --> sai e mantem o container rodando

# Acesse a maquina por SSH agora

# Gerando sua chave pessoal do root
# Como a chave do devops é rsa vamos criar a nossa como dsa
cd /root/.ssh
ssh-keygen -q -N "" -t dsa
touch authorized_keys
chmod 600 authorized_keys
chown root authorized_keys

# instalando o Ansible dentro do docker agora
yum install ansible

# Coloque o 127.0.0.01 no final do arquivo /etc/ansible/hosts
echo 127.0.0.1 >> /etc/ansible/hosts

# Comando para rodar o ansible
# -u root --> especifica o usuário
# -k      --> solicita senha
ansible all -m ping -u root -k

# Resulto esperado
127.0.0.1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
