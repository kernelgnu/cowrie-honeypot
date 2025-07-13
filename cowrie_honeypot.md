# 🕵️‍♂️ Cowrie SSH Honeypot - Estudo de Comportamento de Ameaças

> Projeto de honeypot utilizando o [Cowrie](https://github.com/cowrie/cowrie) para capturar, analisar e entender tentativas de acesso malicioso via SSH.  
> Criado em ambiente isolado com foco em segurança, análise de ameaças e aprendizado em cibersegurança.

---

## 🔥 O que é este projeto?

Este repositório documenta a instalação, configuração e testes de um **honeypot SSH com Cowrie**, uma ferramenta amplamente utilizada para simular um sistema Linux vulnerável e registrar a atividade de possíveis invasores.

---

## 📦 Tecnologias e Ferramentas

- 🐍 [Cowrie](https://github.com/cowrie/cowrie)
- 🐧 Ubuntu Server 22.04
- 🧪 Hydra, Nmap (para testes internos)

---

## ⚙️ Instalação

### Pré-requisitos:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install git python3 python3-pip python3-venv libffi-dev libssl-dev build-essential libpython3-dev libevent-dev authbind -y
```

### Passo a passo:

```bash
# Criar usuário isolado
sudo adduser --disabled-password --gecos "" cowrie

# Clonar o repositório
sudo su - cowrie
git clone https://github.com/cowrie/cowrie.git
cd cowrie

# Criar ambiente virtual Python
python3 -m venv cowrie-env
source cowrie-env/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# Copiar config
cp etc/cowrie.cfg.dist etc/cowrie.cfg
```

Por padrão, o honeypot escuta na porta 2222. Isso pode ser alterado em `etc/cowrie.cfg`.

---

## 🚀 Execução

```bash
# Iniciar Cowrie
bin/cowrie start

# Verificar logs em tempo real
tail -f var/log/cowrie/cowrie.log
```

---

## 👨‍💻 Testes Recomendados

```bash
# SSH falso
ssh root@localhost -p 2222

# Ataque de força bruta
hydra -s 2222 -V -l root -P /usr/share/wordlists/rockyou.txt ssh://127.0.0.1

# Scan com Nmap
nmap -p 2222 --script ssh-brute 127.0.0.1
```

---

## 📁 Logs úteis

- `var/log/cowrie/cowrie.log` → log geral das sessões
- `var/lib/cowrie/tty/` → sessões falsas gravadas
- `var/lib/cowrie/downloads/` → arquivos que o invasor tentou baixar
- `var/log/cowrie/cowrie.json` → eventos em formato JSON

---

## ⚠️ Atenção

> **NUNCA rode um honeypot em sua máquina de trabalho ou ambiente de produção.**
> Este projeto foi implementado em ambiente controlado para evitar riscos à segurança da rede e da infraestrutura.

---

## 📜 Licença

Este projeto é distribuído sob a licença MIT. Consulte o arquivo `LICENSE` para mais detalhes.

---

## 👨‍💻 Autor

**Guilherme**  
Consultor em Cibersegurança | Infraestrutura | SOC / Blue Team  
[LinkedIn](https://www.linkedin.com/in/guilherme-moncao/)

