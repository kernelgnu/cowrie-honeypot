# WireGuard VPN Server + Monitoramento

📅 **Última atualização**: 12/07/2025  
🛡️ **Autor**: Guilherme (Consultor em Cibersegurança | Infraestrutura | SOC / Blue Team)

## 📌 Descrição

Este projeto configura um servidor VPN usando **WireGuard**, com:

- 🔄 **Roteamento completo do tráfego**
- 📊 **Monitoramento ativo do túnel**
- 📝 **Logs de atividade**
- 🚨 **Detecção de peers conectados**

Tudo isso com foco em **segurança**, **visibilidade operacional** e **controle real** da VPN, como manda o Blue Team raiz.

---

## ⚙️ Tecnologias Utilizadas

- [WireGuard](https://www.wireguard.com/) – VPN moderna, rápida e segura
- [Cockpit](https://cockpit-project.org/) – Painel de administração web
- `cron`, `iptables`, `bash`, `journalctl`

---

## 🧱 Estrutura do Projeto

```
/opt/cockpit-wireguard/
├── status.sh               # Mostra status do WireGuard + últimos logs
/var/log/
├── wireguard-monitor.log   # Log de atividade de monitoramento
├── wireguard-peers.log     # Log dos peers conectados
├── wg-peer-watch.log       # Log de novos peers detectados
```

---

## 🚀 Instalação

### 1. Instale WireGuard
```bash
sudo apt update && sudo apt install wireguard -y
```

### 2. Configure o servidor em `/etc/wireguard/wg0.conf`
> [Veja modelo completo no script `wg0.conf`](./wg0.conf)

### 3. Ative IP Forwarding
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

### 4. Inicie o serviço
```bash
sudo systemctl enable --now wg-quick@wg0
```
---

## 📈 Monitoramento Automático

### Script de verificação do túnel (`wg-monitor.sh`)

Executado via `cron` a cada 5 minutos:
```bash
*/5 * * * * /usr/local/bin/wg-monitor.sh
```

---

## 🔒 Segurança

- Porta UDP 51820 aberta
- Tráfego roteado com mascaramento NAT
- Sem log de payloads (WireGuard é minimalista)
- Logs de peers e conexões protegidos por permissões

---

## 📄 Licença

MIT - Faça bom uso, mas seja ético.
