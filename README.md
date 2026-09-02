# 🚀 Home Server & Private Cloud Infrastructure (ZimaOS)

Bem-vindo ao repositório de documentação e arquitetura do meu **Home Lab / Private Cloud**. Este projeto consiste em um servidor de infraestrutura autohospedada rodando **ZimaOS**, focado em gerenciamento de rede, automação de processos, armazenamento privado, controle de acesso seguro e orquestração de serviços via **Docker**.

O objetivo principal deste ecossistema é simular ambientes corporativos de alta disponibilidade, segurança cibernética, virtualização e gestão de infraestrutura de TI.

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

                  ┌─────────────────────────────────────────────────┐
                  │                 WAN / INTERNET                  │
                  └───────────────────────┬─────────────────────────┘
                                          │
                   ┌──────────────────────┴───────────────────────┐
                   │  Túneis & Segurança (Zero Trust / VPN / DNS) │
                   │  Tailscale | Cloudflared | DuckDNS | Pi-hole │
                   └──────────────────────┬───────────────────────┘
                                          │
   ┌──────────────────────────────────────┼──────────────────────────────────────┐
   │                                      │                                      │
┌──┴──────────────────────┐   ┌───────────┴───────────┐   ┌──────────────────────┴──┐
│ Dados & Produtividade   │   │  Serviços Globais &   │   │ Dev & Gerenciamento de  │
│ Nextcloud | MariaDB     │   │  Projetos / Aplicações│   │ Código                  │
└─────────────────────────┘   │  Crafty Controller    │   │ Gitea                   │
                              └───────────────────────┘   └─────────────────────────┘
                                      ---

## 📋 Detalhamento Téchnico dos Containers

### 🌐 1. Segurança de Rede, Tunneling & Resolução DNS

* **Pi-hole (`pihole/pihole`)**
  * **Finalidade Técnica:** Servidor DNS recursivo com bloqueio de anúncios e rastreadores em nível de rede (Sinkhole DNS).
  * **Aplicação Prática:** Aumenta a privacidade da rede interna, reduz tráfego indesejado e previne ataques de phishing bloqueando domínios maliciosos antes da resolução IP.

* **Tailscale (`tailscale/tailscale`)**
  * **Finalidade Técnica:** Malha de VPN privada (Mesh VPN) baseada no protocolo **WireGuard**.
  * **Aplicação Prática:** Permite acesso remoto seguro de qualquer lugar aos serviços internos do servidor sem expor portas publicamente no roteador (Zero Trust Access).

* **Cloudflared (`cloudflare/cloudflared`)**
  * **Finalidade Técnica:** Cliente de túnel seguro (Cloudflare Tunnels).
  * **Aplicação Prática:** Publica serviços web selecionados para a internet protegendo o IP público real do servidor contra ataques de DDoS e varreduras de portas.

* **Duck DNS (`linuxserver/duckdns`)**
  * **Finalidade Técnica:** Provedor de DNS Dinâmico (DDNS).
  * **Aplicação Prática:** Atualiza dinamicamente o registro de IP público do servidor na web, garantindo conectividade contínua mesmo em links residenciais/comerciais com IP dinâmico.

* **Registro.br Integrator / Dynamic Updater**
  * **Finalidade Técnica:** Automação de sincronização de IP público para domínios nacionais (`.br`).
  * **Aplicação Prática:** Mantém a resolução DNS de domínios personalizados mapeados diretamente para a infraestrutura do servidor.

---

### 💾 2. Gestão de Dados, Produtividade & Bancos de Dados

* **Nextcloud (`nextcloud`)**
  * **Finalidade Técnica:** Plataforma de armazenamento e nuvem privada para sincronização de arquivos, agenda e contatos.
  * **Aplicação Prática:** Substituto *self-hosted* corporativo para Google Drive/OneDrive, focado na soberania de dados, controle de versão de arquivos e compartilhamento seguro.

* **MariaDB (`mariadb`)**
  * **Finalidade Técnica:** Sistema Gerenciador de Banco de Dados Relacional (SGBD) baseado em SQL.
  * **Aplicação Prática:** Atua como backend persistente e otimizado para o Nextcloud e outros microsserviços do ambiente, utilizando volumes dedicados para integridade dos dados.

---

### 💻 3. DevOps, Controle de Versão & Automação

* **Gitea (`gitea/gitea`)**
  * **Finalidade Técnica:** Servidor Git leve autohospedado para controle de versão de código.
  * **Aplicação Prática:** Gerenciamento local de repositórios, pipelines de CI/CD simples e versionamento de arquivos de configuração (IaC), garantindo rastreabilidade de mudanças.

* **Zima Cron (`zima-cron`)**
  * **Finalidade Técnica:** Agendador de tarefas e automação no nível do SO/Docker.
  * **Aplicação Prática:** Execução automatizada de rotinas de manutenção, rotação de logs e verificações periódicas do sistema.

* **ZVM & Backup Utilities**
  * **Finalidade Técnica:** Módulos de virtualização e estratégias de backup contínuo do ZimaOS.
  * **Aplicação Prática:** Garantia de continuidade de negócios (*Disaster Recovery*), permitindo restauração rápida de volumes e configurações dos containers.

---

### 🎮 4. Aplicações de Propósito Específico & Utilitários de Rede

* **Crafty Controller (`arcaneos/crafty-web`)**
  * **Finalidade Técnica:** Painel de gerenciamento e orquestração de servidores de jogos baseados em Java/Bedrock.
  * **Aplicação Prática:** Gestão de processos, alocação dinâmica de memória RAM, controle de portas e automação de rotinas para servidores multiplayer.

* **PeerDrop (`peerdrop`)**
  * **Finalidade Técnica:** Aplicação de transferência rápida de arquivos via protocolo P2P/WebRTC na rede local.
  * **Aplicação Prática:** Compartilhamento direto de arquivos entre dispositivos sem necessidade de upload para servidores intermediários.

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
