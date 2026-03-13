🔐 Brute Force com Medusa e Kali Linux

Projeto do Desafio DIO — Simulação de ataques de força bruta em ambiente controlado utilizando Kali Linux, Medusa e ambientes vulneráveis (Metasploitable 2 / DVWA).

Configuração Inicial

1º – Instalar o Kali Linux e o Metasploitable no VirtualBox.

2º – Iniciar as duas máquinas virtuais.

3º – Criar um snapshot do Metasploitable para poder restaurar o sistema caso algo dê errado:

Passo a passo:

Abra o VirtualBox.

Selecione a máquina Metasploitable.

Clique em Máquina.

Clique em Criar Snapshot.

Adicione nome e descrição.

Clique em OK.

✔ Isso cria um ponto de restauração para voltar ao estado original da máquina se necessário.

 <img width="911" height="645" alt="Image" src="https://github.com/user-attachments/assets/8f5ea201-0044-4578-bb2d-3a0cbbaf67f2" /> 


Acessar o metasploitable com o login padrão: msfadmin e senha padrão: msfadmin

<img width="893" height="641" alt="Image" src="https://github.com/user-attachments/assets/b67a040c-bec6-4006-b0ad-904f2797c4b3" />
<img width="721" height="468" alt="Image" src="https://github.com/user-attachments/assets/76004bdc-12d8-45fa-9239-9da566aa9acc" />
<img width="710" height="475" alt="Image" src="https://github.com/user-attachments/assets/b19c6951-5489-4e60-935d-584e954e345f" />
<img width="716" height="467" alt="Image" src="https://github.com/user-attachments/assets/45053c1d-5315-41ac-8273-5627247ccc09" /> 

Simulando Ataque FTP Simulando um cenário de auditoria em um servidor FTP que pode conter falhas de segurança
1º - Enumeração para descobrir quais serviços estão disponíveis no sistema com suspeita de vulnerabilidade. comando: nmap -sV -p 21,22,80,445,139 nú.me.ro.ip

Este comando escaneia as portas 21,22,80,445 e 139. O parâmetro -sV identifica a versão do serviço que está rodando em cada porta.

Se a porta ftp estiver aberta tentaremos conectá-la diretamente.
<img width="904" height="638" alt="Image" src="https://github.com/user-attachments/assets/7a2e58b7-32e4-4fcf-8dda-e831fc7f61cc" />

Criando nomes de usuários e senhas comuns (wordlists) em diferentes arquivos e rodando o ataque
 Comandos para criar e salvar no Kali Linux arquivo de texto com possíveis nomes de usuários e arquivo com senhas comuns.

Comando usuários: echo -e "user\nmsfadmin\nadmin\nroot" > users.txt

Comando senhas: echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
<img width="880" height="711" alt="Image" src="https://github.com/user-attachments/assets/aa36dd52-b746-43a9-8e19-d7fb92182fc9" />
Rodando o ataque com a Medusa

Comando: medusa -h nú.me.ro.ip -U users.txt -P pass.txt -M ftp -t6

Onde -t6 significa que estamos usando 6 threads simultâneas, o que torna o ataque mais rápido.

No ataque foram encontrados o login msfadmin e a senha msfadmin como credenciais válidas. Isso significa que conseguimos acessar o sistema via ftp com essas credenciais.
<img width="904" height="696" alt="Image" src="https://github.com/user-attachments/assets/4406198c-1179-4ddf-b7ec-c24de9a49b20" />

Simulando Ataque web (http) Simulando ataque brute force em formulários de login web (http) no sistema dvwa
  Acessar, no navegador firefox do Kali Linux, o endereço nú.me.ro.ip/dvwa/login.php para visualizar a página de teste de login do dvwa.

Na sequência abrir o painel de ferramentas do desenvolvedor na página de teste de login do dvwa clicando em f12 e em seguida clicar na guia network, na navegação do tipo POST e em Request, que nos mostrará tudo o que o navegador está enviando e recebendo durante a interação, incluindo os nomes dos parâmetros que o servidor espera receber. A Medusa vai simular em cima destes parâmetros.
<img width="883" height="718" alt="Image" src="https://github.com/user-attachments/assets/33a30581-a6b5-4662-bda6-3598145bea7c" />
No terminal do Kali, após criadas as wordlists de usuários e de senhas, rodar o seguinte comando com a Medusa.

Comando: medusa -h nú.me.ro.ip -U users.txt -P pass.txt -M http

-m PAGE:'/dvwa/login.php'

-m FORM:'username=^USER^&password=^PASS^&Login=Login'

-m 'FAIL=Login failed' -t 6

As credenciais corretas encontradas aparecerão com a palavra SUCCESS.

Em seguida utilizamos o primeiro login e senha encontrados para acessar o sistema.
<img width="884" height="711" alt="Image" src="https://github.com/user-attachments/assets/7b7251f9-375d-4f12-b6dd-9f0cdc320708" />
<img width="908" height="698" alt="Image" src="https://github.com/user-attachments/assets/d82593f7-26ea-44ae-a634-9db4e003e772" /> 
Recomendações de Mitigação (reformulado)

Para reduzir os riscos de ataques de força bruta, como aqueles realizados com ferramentas como o Medusa no Kali Linux, é importante utilizar uma estratégia de segurança em várias camadas. Essa estratégia deve combinar políticas de senhas robustas, proteção da rede e mecanismos de monitoramento contínuo.

1. Reforçar as políticas de autenticação

Essa é a principal barreira contra acessos não autorizados.

Senhas seguras:
Defina regras que exijam senhas longas (pelo menos 14 caracteres) e complexas, combinando letras maiúsculas, minúsculas, números e símbolos. Também é importante evitar senhas baseadas em dados pessoais ou fáceis de adivinhar.

Autenticação multifator (MFA):
Adote a autenticação com dois ou mais fatores. Nesse modelo, além da senha, o usuário precisa confirmar sua identidade com outro método, como um código enviado para o celular ou aplicativo autenticador. Assim, mesmo que a senha seja descoberta, o acesso ainda fica protegido.

Limitação de tentativas de login:
Configure o sistema para bloquear temporariamente uma conta ou endereço IP após várias tentativas de login incorretas. Isso dificulta ataques automatizados que tentam muitas combinações de senha.

Uso de CAPTCHA:
Implemente ferramentas como CAPTCHA ou reCAPTCHA para verificar se quem está tentando acessar o sistema é realmente uma pessoa. Esse mecanismo ajuda a impedir que programas automatizados realizem tentativas de login em massa.

2. Aplicar medidas de segurança na rede

Essas medidas ajudam a proteger a infraestrutura antes mesmo de o ataque atingir o sistema de autenticação.

Firewalls:
Utilize firewalls para monitorar e bloquear tráfego suspeito na rede. Em servidores web, um Firewall de Aplicação Web (WAF) pode oferecer proteção adicional contra acessos maliciosos.

Monitoramento de endereços IP:
Acompanhe os IPs que tentam acessar o sistema. Caso um mesmo endereço realize um número excessivo de tentativas de login, ele pode ser bloqueado automaticamente.

Honeypots:
Implemente honeypots, que são sistemas falsos criados para atrair invasores. Eles simulam serviços reais e registram as ações do atacante, permitindo identificar tentativas de invasão e proteger os sistemas verdadeiros.

Mudança de portas padrão:
Alterar portas padrão de serviços, como o SSH (que normalmente usa a porta 22), pode dificultar a ação de scripts automatizados utilizados por atacantes.

3. Reforçar a segurança do sistema

Além da proteção externa, também é essencial fortalecer o próprio sistema.

Atualizações frequentes:
Mantenha sistemas operacionais e aplicativos sempre atualizados para corrigir falhas de segurança que poderiam ser exploradas por invasores.

Remoção de contas desnecessárias:
Exclua ou desative contas de usuários que não são mais utilizadas, reduzindo possíveis pontos de entrada para ataques.

Proteção de dados sensíveis:
Garanta que informações importantes, especialmente senhas e dados confidenciais, estejam criptografadas. Dessa forma, mesmo que haja um acesso indevido ao sistema, os dados permanecerão protegidos.


