# Ansible
Repositorio para atividade da matéria de Computação Orientada a Serviços - Unitins

# Relatório Ansible - Computação Orientada a Serviços

Este repositório documenta a automação de servidores na AWS utilizando Ansible. Foram configuradas instâncias para gerenciar a instalação dos serviços Nginx e Apache.

## Ambiente

- **Instâncias AWS:**
  - **ControlNodeAnsible:** Gerencia os servidores.
  - **ServidorNginx1:** Destinado à instalação do Nginx.
  - **ServidorNginx2:** Destinado à instalação do Apache.
- **Chaves de Acesso:** `chaveControlNode`, `ServidorNginx1` e `ServidorNginx2`

## Principais Passos

1. **Configuração Local e Acesso:**
   - Transferência das chaves via `scp`
   - Conexão via SSH:
     ```bash
     ssh -i "chaveControlNode.pem" ubuntu@<ip-da-instância>
     ```

2. **Instalação e Configuração do Ansible:**
   - Atualização do sistema e instalação do Ansible:
     ```bash
     sudo apt update && sudo apt upgrade -y
     sudo apt install software-properties-common
     sudo add-apt-repositor --yes --update ppa:ansible/ansible
     sudo apt install ansible
     ansible --version
     ```
   - Configuração do arquivo `/etc/ansible/hosts`:
     ```ini
     [servidores]
     ec2-18-229-142-92.sa-east-1.compute.amazonaws.com ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubuntu/ServidorNginx1.pem ansible_python_interpreter=/usr/bin/python3

     [apache]
     ec2-15-228-166-136.sa-east-1.compute.amazonaws.com ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubuntu/ServidorNginx2.pem ansible_python_interpreter=/usr/bin/python3
     ```

3. **Execução dos Playbooks:**
   - **Nginx:**
     - Arquivo: `playbook-nginx.yml`
     ```yaml
     ---
     - name: Instalar o nginx
       hosts: servidores
       become: true
       tasks:
         - name: Instalar nginx
           apt:
             name: nginx
             state: present
     ```
     - Executar:
       ```bash
       ansible-playbook playbook-nginx.yml
       ```
   - **Apache:**
     - Arquivo: `playbook-apache.yml`
     ```yaml
     ---
     - name: Instalar o apache
       hosts: apache
       become: true
       tasks:
         - name: Instalar apache
           apt:
             name: apache2
             update_cache: yes
             state: latest
     ```
     - Executar:
       ```bash
       ansible-playbook playbook-apache.yml
       ```

## Resultados

- **Validação:** Testes com `ansible -m ping`, `uptime` e `ip a` confirmaram a conectividade e o sucesso na instalação dos serviços.
