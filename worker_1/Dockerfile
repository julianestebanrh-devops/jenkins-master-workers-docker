# Utiliza una imagen base de Ubuntu 22.04
FROM ubuntu:22.04

# Actualiza la lista de paquetes e instala el servidor SSH
# Instala Node.js (puedes cambiar la versión según tus necesidades)
RUN apt-get update && \
    apt-get install -y openssh-server nodejs && \
    rm -rf /var/lib/apt/lists/*

# Instala Docker & Docker Compose
RUN apt update && apt install -y \
    docker.io \
    docker-compose \
    default-jre

# Configura el servidor SSH
RUN mkdir /var/run/sshd \
    && echo 'root:password' | chpasswd \
    && sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && echo 'PermitUserEnvironment yes' >> /etc/ssh/sshd_config

# Crea un usuario llamado "jenkins_worker_1"
RUN useradd -m -d /home/jenkins_home -s /bin/bash jenkins_worker_1

# Agrega al usuario actual al grupo staff y al grupo docker. Esto le da al usuario actual los permisos necesarios para ejecutar contenedores Docker.
RUN usermod -a -G staff,docker jenkins_worker_1

# Cambia a usuario "jenkins_worker_1" y genera una clave SSH
USER jenkins_worker_1
RUN mkdir /home/jenkins_home/.ssh \
    && ssh-keygen -t rsa -f /home/jenkins_home/.ssh/id_rsa -N ""

# Copia la clave pública al archivo "authorized_keys"
RUN cat /home/jenkins_home/.ssh/id_rsa.pub >> /home/jenkins_home/.ssh/authorized_keys \
    && chmod 600 /home/jenkins_home/.ssh/authorized_keys \
    && chmod 700 /home/jenkins_home/.ssh

# Cambia de nuevo al usuario root
USER root

# Expone el puerto SSH
EXPOSE 22

# Inicia el servidor SSH
CMD ["/usr/sbin/sshd", "-D"]