# 🍯 Honeypot Inteligente com Cowrie (CLI)

Bem-vindo ao projeto de Honeypot Inteligente!  
Este repositório contém um guia completo para implementar um honeypot SSH e Telnet de **média-alta interação** utilizando a poderosa ferramenta **Cowrie**.  

O foco principal é a **configuração, gestão e análise de dados inteiramente através da linha de comando (CLI)**, capacitando analistas de segurança e pesquisadores a capturar **Táticas, Técnicas e Procedimentos (TTPs)** de atacantes em um ambiente controlado.

---

## ⚠️ AVISO DE SEGURANÇA CRÍTICO ⚠️
**NUNCA** execute um honeypot numa rede de produção, corporativa ou na sua máquina pessoal.  

- Utilize sempre um ambiente **completamente isolado** (ex: VM em nuvem).  
- Não armazene dados sensíveis.  
- Não conecte este ambiente a sistemas críticos.  

Este projeto é **para fins educacionais e de pesquisa**.  
Você é o único responsável por qualquer atividade maliciosa que ocorra no servidor que hospeda este honeypot. **Proceda com cautela.**

---

## 🚀 Funcionalidades Principais
- **Alta Interação**: emula um shell Linux funcional, permitindo execução real de comandos.  
- **Captura de TTPs**: registra desde logins até tentativas de exfiltração de dados.  
- **Análise de Malware**: salva automaticamente ficheiros baixados via `wget` ou `curl`.  
- **Logs em JSON**: estruturados e prontos para análise com `jq` ou integração em SIEM.  
- **Gestão via CLI**: todo o ciclo de vida do honeypot é realizado pelo terminal.  

---

## 🛠️ Tecnologias Utilizadas
- **Motor do Honeypot**: [Cowrie](https://github.com/cowrie/cowrie)  
- **Sistema Operativo**: Ubuntu 22.04 LTS
- **Análise de Logs**: [jq](https://stedolan.github.io/jq/)  

---

## 📋 Pré-requisitos
- Máquina virtual  
- Linux baseado em Debian (**Ubuntu 22.04 LTS recomendado**).  
- Acesso root ou utilizador com privilégios `sudo`.  
- Conhecimentos básicos de CLI Linux.  

---

## ⚙️ Guia de Instalação e Configuração

### 1. Preparação do Servidor
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git python3-virtualenv python3-dev libssl-dev libffi-dev build-essential libpython3-dev python3-pip virtualenv
```

### 2. Criar Utilizador Dedicado
```bash
sudo adduser cowrie
sudo usermod -aG sudo cowrie
su - cowrie
```

### 3. Instalar o Cowrie
```bash
git clone http://github.com/cowrie/cowrie
cd cowrie

virtualenv --python=python3 cowrie-env
source cowrie-env/bin/activate

pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Configuração Básica
```bash
cp etc/cowrie.cfg.dist etc/cowrie.cfg
nano etc/cowrie.cfg
```
Edite pelo menos o `hostname`:
```ini
[honeypot]
hostname = web-server-01
```

### 5. Redirecionamento de Porta
```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
```
Para persistência:
```bash
sudo apt install iptables-persistent
```

---

## 🕹️ Executando o Honeypot
```bash
# Iniciar
bin/cowrie start

# Status
bin/cowrie status

# Parar
bin/cowrie stop
```

---

## 📊 Análise de Dados
Logs principais: `var/log/cowrie/cowrie.json`  

### Instalar `jq`
```bash
sudo apt install jq
```

### Visualizar logs em tempo real
```bash
tail -f var/log/cowrie/cowrie.json | jq
```

### Exemplos de análise
```bash
# IPs, utilizadores e senhas de logins bem-sucedidos
cat var/log/cowrie/cowrie.json | jq 'select(.eventid == "cowrie.login.success") | {timestamp, src_ip, username, password}'

# Listar comandos únicos executados por atacantes, ordenados por frequência
cat var/log/cowrie/cowrie.json | jq 'select(.eventid == "cowrie.command.input") | .input' | sort | uniq -c | sort -rn

# URLs de malware tentadas
cat var/log/cowrie/cowrie.json | jq 'select(.eventid == "cowrie.session.file_download") | {timestamp, url, outfile}'

# Top 10 senhas mais usadas em ataques de força bruta
cat var/log/cowrie/cowrie.json | jq 'select(.eventid == "cowrie.login.failed") | .password' | sort | uniq -c | sort -rn | head -n 10
```

---

## 🤝 Como Contribuir
1. Faça um **Fork** deste repositório  
2. Crie uma **branch** (`git checkout -b feature/sua-feature`)  
3. Commit (`git commit -m 'Adiciona nova feature'`)  
4. Push (`git push origin feature/sua-feature`)  
5. Abra um **Pull Request**  
