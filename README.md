# Projeto 01 - DevOps com Vagrant e Ansible

**Instituto Federal da Paraíba (IFPB) - Campus João Pessoa** **Disciplina:** Administração de Sistemas Abertos  
**Professor:** Leonidas Lima  
**Período:** 2025.2

## 👥 Equipe
* **Luis Henrike Marinho da Costa** (Matrícula: 20241380040)
* **Marcelino Marcelo do Nascimento Camilo** (Matrícula: 20241380018)

---

## 📝 Descrição do Projeto
Este projeto tem como objetivo desenvolver competências práticas em **DevOps** e **Infraestrutura como Código (IaC)**. Utilizando **Vagrant** para provisionamento de VMs e **Ansible** para gerenciamento de configuração, foi implementada uma infraestrutura de rede completa contendo serviços de Diretório, DNS, DHCP, Web, Banco de Dados e Armazenamento distribuído.

## 🏗️ Arquitetura e Topologia
O ambiente é composto por 4 máquinas virtuais baseadas em **Debian 12 (Bookworm)**, interconectadas via rede privada (Host-only) gerenciada pelo VirtualBox.

| Hostname | Função | IP (Rede Interna) | Características Principais |
| :--- | :--- | :--- | :--- |
| **arq** | Servidor de Arquivos | `192.168.56.140` | DNS, DHCP, NFS, LVM (3 Discos) |
| **db** | Banco de Dados | `192.168.56.118` | MariaDB, Cliente NFS |
| **app** | Servidor Web | `192.168.56.108` | Apache2, Site Personalizado, Cliente NFS |
| **cli** | Cliente (Workstation) | Dinâmico (DHCP) | Ferramentas Gráficas, X11 Forwarding |

> **Nota:** Os endereços IP foram definidos com base nas matrículas dos integrantes.

---

## 🚀 Serviços e Configurações Implementadas

### 1. Configurações Comuns (Todas as VMs)
* **Sistema:** Atualização completa do OS (`apt update/upgrade`).
* **Timezone/NTP:** Fuso horário definido para `America/Recife` e sincronização via `pool.ntp.br`.
* **Usuários:** Criação do grupo `ifpb` e usuários `luis` e `marcelino` com chaves SSH.
* **Segurança SSH:**
    * Acesso root bloqueado.
    * Autenticação por senha desativada (apenas chave pública).
    * Banner de aviso legal configurado.
* **SUDO:** Permissão total (NOPASSWD) para o grupo `ifpb`.

### 2. Servidor ARQ (Infraestrutura)
* **LVM (Logical Volume Manager):** Agrupamento de 3 discos de 10GB (`sdb`, `sdc`, `sdd`) em um Volume Group `dados`, com um Volume Lógico `ifpb` de 15GB formatado em ext4.
* **DNS (Bind9):** Configurado como autoritativo para a zona `luis.marcelino.devops`. Encaminha requisições externas para `1.1.1.1` e `8.8.8.8`.
* **DHCP (ISC-DHCP-Server):** Distribui IPs na faixa `192.168.56.50-100`. Possui reservas estáticas via MAC Address para as máquinas `db` e `app`.
* **NFS (Network File System):** Exporta o diretório `/dados/nfs` com mapeamento de usuários (`all_squash`) para segurança.

### 3. Servidor DB & APP
* **DB:** Instalação do **MariaDB Server**.
* **APP:** Instalação do **Apache2** com página `index.html` personalizada contendo os dados da equipe.
* **AutoFS:** Ambas montam automaticamente o compartilhamento NFS em `/var/nfs`.

### 4. Cliente CLI
* Instalação de utilitários gráficos (`firefox-esr`, `xauth`).
* Configuração de **X11 Forwarding** para execução remota de aplicações gráficas.

---

## 🛠️ Pré-requisitos
Para executar este projeto, é necessário ter instalado:
* [VirtualBox](https://www.virtualbox.org/)
* [Vagrant](https://www.vagrantup.com/)
* [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

## ▶️ Como Executar

1. **Clone o repositório:**
   ```bash
   git clone [https://github.com/seu-usuario/projeto-asa.git](https://github.com/seu-usuario/projeto-asa.git)
   cd projeto-asa
