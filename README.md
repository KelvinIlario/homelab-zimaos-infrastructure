# 🚀 Home Server & Private Cloud Infrastructure (ZimaOS)

Bem-vindo ao repositório de documentação e arquitetura do meu **Home Lab / Private Cloud**. Este projeto consiste em um servidor de infraestrutura autohospedada rodando **ZimaOS**, focado em gerenciamento de rede, automação de processos, armazenamento privado, controle de acesso seguro e orquestração de serviços via **Docker**.

O objetivo principal deste ecossistema é simular ambientes corporativos de alta disponibilidade, segurança cibernética, virtualização e gestão de infraestrutura de TI.


<img width="1920" height="1380" alt="screencapture-192-168-31-79-2026-09-02-11_14_11" src="https://github.com/user-attachments/assets/4720b91c-1e27-4a3a-96db-54f4588d76f8" />

---

## 🛠️ Especificações do Hardware & Sistema

* **Sistema Operacional:** ZimaOS (baseado em Linux, voltado para NAS/Edge Computing)
* **Gerenciamento de Containers:** Docker / Docker Compose
* **Recursos do Servidor:**
  * **Armazenamento:** 1.1 TB HD/SSD (125 GB alocados em volumes ativos)
  * **Memória RAM:** 16.0 GB (Uso médio eficiente em idle/load)
  * **Interface de Rede:** Ethernet Gigabit (`eth0`)

---

## 🏗️ Arquitetura e Serviços em Execução (Docker Stacks)

Os serviços estão divididos em camadas lógicas para garantir isolamento de rede, persistência de dados e segurança operacional.

                 ```mermaid
graph TD
    A["🌐 WAN / INTERNET"] --> B["🛡️ Túneis & Segurança<br>(Zero Trust / VPN / DNS)"]
    
    subgraph S1["Segurança & Acesso"]
        B --- T1["Tailscale"]
        B --- T2["Cloudflared"]
        B --- T3["DuckDNS"]
        B --- T4["Pi-hole"]
    end

    B --> C["💾 Dados & Produtividade"]
    B --> D["⚡ Serviços Globais & Projetos"]
    B --> E["💻 Dev & Código"]

    subgraph S2["Aplicações"]
        C --- C1["Nextcloud"]
        C --- C2["MariaDB"]
        
        D --- D1["Crafty Controller"]
        
        E --- E1["Gitea"]
    end

## 📋 Detalhamento Téchnico dos Containers

### 🌐 1. Segurança de Rede, Tunneling & Resolução DNS

* **Pi-hole (`pihole/pihole`)**
  * **Finalidade Técnica:** Servidor DNS recursivo com bloqueio de anúncios e rastreadores em nível de rede (Sinkhole DNS).
  * **Aplicação Prática:** Aumenta a privacidade da rede interna, reduz tráfego indesejado e previne ataques de phishing bloqueando domínios maliciosos antes da resolução IP.
 
  * <img width="1920" height="2728" alt="screencapture-192-168-31-79-8800-admin-2026-09-02-12_11_58" src="https://github.com/user-attachments/assets/82bb4de9-ad8d-4a00-9d26-b46dee91958e" />


* **Tailscale (`tailscale/tailscale`)**
  * **Finalidade Técnica:** Malha de VPN privada (Mesh VPN) baseada no protocolo **WireGuard**.
  * **Aplicação Prática:** Permite acesso remoto seguro de qualquer lugar aos serviços internos do servidor sem expor portas publicamente no roteador (Zero Trust Access).
 
  * <img width="1920" height="912" alt="screencapture-login-tailscale-login-2026-09-02-12_13_36" src="https://github.com/user-attachments/assets/25cf1ece-1d2c-40b9-bca4-c4322260af1d" />


* **Cloudflared (`cloudflare/cloudflared`)**
  * **Finalidade Técnica:** Cliente de túnel seguro (Cloudflare Tunnels).
  * **Aplicação Prática:** Publica serviços web selecionados para a internet protegendo o IP público real do servidor contra ataques de DDoS e varreduras de portas.

* **Duck DNS (`linuxserver/duckdns`)**
  * **Finalidade Técnica:** Provedor de DNS Dinâmico (DDNS).
  * **Aplicação Prática:** Atualiza dinamicamente o registro de IP público do servidor na web, garantindo conectividade contínua mesmo em links residenciais/comerciais com IP dinâmico.
 
  * <img width="1920" height="912" alt="screencapture-duckdns-org-2026-09-02-12_13_53" src="https://github.com/user-attachments/assets/16c11da4-be0d-4abe-86a2-9db0c16d7391" />


* **Registro.br Integrator / Dynamic Updater**
  * **Finalidade Técnica:** Automação de sincronização de IP público para domínios nacionais (`.br`).
  * **Aplicação Prática:** Mantém a resolução DNS de domínios personalizados mapeados diretamente para a infraestrutura do servidor.
 
  * <img width="1920" height="3916" alt="screencapture-registro-br-2026-09-02-12_14_07" src="https://github.com/user-attachments/assets/0874068b-f9a4-4839-9b35-812cff5ce348" />


---

### 💾 2. Gestão de Dados, Produtividade & Bancos de Dados

* **Nextcloud (`nextcloud`)**
  * **Finalidade Técnica:** Plataforma de armazenamento e nuvem privada para sincronização de arquivos, agenda e contatos.
  * **Aplicação Prática:** Substituto *self-hosted* corporativo para Google Drive/OneDrive, focado na soberania de dados, controle de versão de arquivos e compartilhamento seguro.
 
  * <img width="1920" height="912" alt="screencapture-nextcloud-lonker-br-apps-files-files-2026-09-02-12_12_31" src="https://github.com/user-attachments/assets/8f0dc62a-5adc-4dfb-9cf8-2dbec98f8e86" />


* **MariaDB (`mariadb`)**
  * **Finalidade Técnica:** Sistema Gerenciador de Banco de Dados Relacional (SGBD) baseado em SQL.
  * **Aplicação Prática:** Atua como backend persistente e otimizado para o Nextcloud e outros microsserviços do ambiente, utilizando volumes dedicados para integridade dos dados.
  * **OBS: ** Não tem interface, utilizada em conjunto com nextcloud.

---

### 💻 3. DevOps, Controle de Versão & Automação

* **Gitea (`gitea/gitea`)**
  * **Finalidade Técnica:** Servidor Git leve autohospedado para controle de versão de código.
  * **Aplicação Prática:** Gerenciamento local de repositórios, pipelines de CI/CD simples e versionamento de arquivos de configuração (IaC), garantindo rastreabilidade de mudanças.
 
  * <img width="1920" height="2336" alt="screencapture-192-168-31-79-3002-2026-09-02-12_12_15" src="https://github.com/user-attachments/assets/f22893a0-c9a1-4d23-abb1-d59981a68dd2" />


* **Zima Cron (`zima-cron`)**
  * **Finalidade Técnica:** Agendador de tarefas e automação no nível do SO/Docker.
  * **Aplicação Prática:** Execução automatizada de rotinas de manutenção, rotação de logs e verificações periódicas do sistema.
 
  * <img width="1920" height="912" alt="screencapture-192-168-31-79-modules-zima-cron-index-html-2026-09-02-12_20_39" src="https://github.com/user-attachments/assets/b694eb86-61b0-4b93-a82e-cc71cb78400d" />


* **ZVM & Backup Utilities**
  * **Finalidade Técnica:** Módulos de virtualização e estratégias de backup contínuo do ZimaOS.
  * **Aplicação Prática:** Garantia de continuidade de negócios (*Disaster Recovery*), permitindo restauração rápida de volumes e configurações dos containers.
 
  * <img width="1920" height="912" alt="screencapture-192-168-31-79-modules-zimaos-zvm-index-html-2026-09-02-12_21_18" src="https://github.com/user-attachments/assets/eddf9604-abf6-43bd-91e2-591e2948b15b" />

 
  * <img width="1920" height="1380" alt="screencapture-192-168-31-79-2026-09-02-12_20_25" src="https://github.com/user-attachments/assets/d1564c02-ba97-4467-b384-69913e94dafa" />


---

### 🎮 4. Aplicações de Propósito Específico & Utilitários de Rede

* **Crafty Controller (`arcaneos/crafty-web`)**
  * **Finalidade Técnica:** Painel de gerenciamento e orquestração de servidores de jogos baseados em Java/Bedrock.
  * **Aplicação Prática:** Gestão de processos, alocação dinâmica de memória RAM, controle de portas e automação de rotinas para servidores multiplayer.
 
  * <img width="1920" height="912" alt="screencapture-192-168-31-79-8443-login-2026-09-02-12_13_23" src="https://github.com/user-attachments/assets/5c908a6e-556f-4932-8f35-76e995cdbac9" />


* **PeerDrop (`peerdrop`)**
  * **Finalidade Técnica:** Aplicação de transferência rápida de arquivos via protocolo P2P/WebRTC na rede local.
  * **Aplicação Prática:** Compartilhamento direto de arquivos entre dispositivos sem necessidade de upload para servidores intermediários.
 
  * <img width="1920" height="912" alt="screencapture-192-168-31-79-modules-icewhale-peerdrop-index-html-2026-09-02-12_21_02" src="https://github.com/user-attachments/assets/db3339cc-8fef-4974-a8b6-16aff15e3a09" />


---

## 🛡️ Boas Práticas e Conceitos de TI Aplicados

1. **Isolamento e Conteinerização:** Uso de Docker para segmentar dependências de software, evitando conflitos no sistema operacional base.
2. **Infraestrutura Zero Trust:** Acesso aos serviços críticos restrito à malha criptografada da VPN (Tailscale) ou protegido por túneis de aplicação (Cloudflare).
3. **Gerenciamento de Redes:** Segmentação de tráfego, configuração de DNS sinkhole e mapeamento dinâmico de portas.
4. **Persistência e Armazenamento:** Mapeamento correto de volumes Docker para garantir a segurança dos dados em atualizações de imagens.
5. **Monitoramento de Recursos:** Acompanhamento contínuo de métricas de CPU, memória e throughput de rede via Dashboard.

---

## 📌 Próximos Passos (Roadmap)

- [ ] Implementação de **Portainer** para orquestração avançada de Stacks via Docker Compose.
- [ ] Configuração de monitoramento de métricas com **Prometheus + Grafana**.
- [ ] Implementação de proxy reverso centralizado com **Nginx Proxy Manager** e certificados HTTPS automáticos (Let's Encrypt).
