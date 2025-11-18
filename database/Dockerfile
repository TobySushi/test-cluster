FROM ubuntu:20.04

RUN apt update -y && apt install munge -y && apt install vim -y && apt install build-essential -y && apt install git -y && apt-get install mariadb-server -y && apt install wget -y

ARG DEBIAN_FRONTEND=noninteractive
RUN apt install slurmd slurm-client -y
RUN apt install sudo -y && apt install python3.9 python3-pip -y && useradd -m admin -s /usr/bin/bash -d /home/admin && echo "admin:admin" | chpasswd && adduser admin sudo && echo "admin     ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN apt install postgresql postgresql-contrib -y

COPY slurm.conf /etc/slurm-llnl/
COPY cgroup.conf /etc/slurm-llnl/
COPY docker-entrypoint.sh /etc/slurm-llnl/

WORKDIR /home/admin

EXPOSE 5432

RUN apt update -y && apt install libopenmpi-dev -y && pip3 install mpi4py
RUN sed -i "s/#listen_addresses = 'localhost'/listen_addresses = '*'/" /etc/postgresql/*/main/postgresql.conf
RUN echo "host all all 172.19.0.0/16 md5" >> /etc/postgresql/*/main/pg_hba.conf
RUN service postgresql start && \
    sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'toby';" && \
    service postgresql stop


ENTRYPOINT ["/etc/slurm-llnl/docker-entrypoint.sh"]