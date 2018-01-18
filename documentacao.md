
1. [Introdução](#introdução)
2. [Objetivos](#objetivos)
3. [Infraestrutura Usada](#infraestrutura-usada)
4. [Embasamento Teórico](#embasamento-teórico)
	* [MariaDB](#mariadb)
	* [NGinx](#nginx)
	* [Postfix](#postfix)
5. [Metodologia (passo a passo)](#metodologia-passo-a-passo)
	* [Instalação do banco de dados MariaDB](#instalação-do-banco-de-dados-mariadb)
	* [Trabalhando o Hardening e fortificando a segurança do banco de dados](#trabalhando-o-hardening-e-fortificando-a-segurança-do-banco-de-dados)
	* [Instalação do NGINX](#instalação-do-nginx)
6. [Criando Certificado SSL](#criando-certificado-ssl)
	*	[Print do arquivo de configuração](#print-do-arquivo-de-configuração)
7. [Instalando Postfix](#instalando-postfix)
	*	[Criando certificado SSL](#criando-certificado-ssl)
	*	[Configurando o Postfix](#configurando-o-postfix)
8. [Configurando o Wordpress](#configurando-o-wordpress)
	*	[Configurando o plugin](#configurando-o-plugin)
9. [Conclusão](#conclusão)

# INTRODUÇÃO

Com o intuito de atender as demandas do desafio proposto pela *SmarttBot* em criar um blog voltado a ensinar aos pais como dar aos seus filhos educação financeira, o projeto foi desenvolvido com solução baseada em software livre como solicitado usando como base as ferramentas *NGINX*, um servidor web, *MariaDB* como solução para o banco de dados, o famoso *MTA (Mail agent Transfer)* de código aberto *Postfix* e a linguagem de script *open source* com foco no ambiente *web* e de uso geral *PHP*, criando dessa forma um ambiente seguro e propício ao desenvolvimento de conteúdo informativo que a empresa pretende levar ao seu público através da ferramenta Wordpress.

## OBJETIVOS

Concretizar desafio hipotético proposto, desenvolver, configurar e deixar funcional a infraestrutura e seus componentes.

## INFRAESTRUTURA USADA

A [Linode](https://www.linode.com) é um provedora de hospedagem na nuvem, fundada em 2003, por *Christopher Aker*, que fornece infraestrutura para mais de 400.000 clientes no mundo todo em 8 data centers na América do Norte, Europa e Ásia. Assinantes podem criar um servidor em menos de um minuto e escalonar o serviço sob demanda, pagando apenas por uso, sem contratos de longo prazo. Os planos da empresa são 100% em SSD com processadores Intel E5, um dos melhores para a hospedagem cloud. Possui recursos como 1 GB de RAM, 1 Core de CPU, 20 GB de SSD e 1 TB de transferência em uma rede de 40 Gbps.

## EMBASAMENTO TEÓRICO

### 1.1 . MariaDB
O *MySQL* originalmente foi criado por uma empresa finlandesa / sueca, a *MySQL AB*, fundada por *David Axmark*, *Allan Larsson* e *Michael “Monty” Widenius*. A primeira versão do MySQL apareceu em 1995. Inicialmente foi criado para uso pessoal, mas em pouco tempo evoluiu para um banco de dados empresarial e tornou-se o software de código aberto mais popular do mundo. Em janeiro de 2008, a *Sun Microsystems* adquiriu o *MySQL* por US $ 1 bilhão. Logo após, a Oracle adquiriu a *Sun Microsystems* após obter a aprovação da Comissão Europeia no final de 2009, que inicialmente parou a transação devido a preocupações de que tal fusão prejudicaria os mercados de banco de dados, já que o *MySQL* era o principal concorrente do produto de banco de dados da *Oracle*. Em meio a diversas incertezas referente a administração da *Oracle* junto ao *MySQL*, os desenvolvedores originais do *MySQL* se dividiram e criaram o [MariaDB](https://mariadb.org) em 2009. Com o passar do tempo, o *MariaDB* gradativamente foi substituindo o *MySQL*.

#### Ponto forte:

- Desenvolvimento mais aberto
- Segurança mais rápida e transparente
- Recursos mais avançados
- Melhor desempenho
- Galera cluster multi-master
- Fácil migração e compatibilidade
	
### 1.2. NGINX

[NGINX](http://nginx.org) [engine x] é um servidor proxy HTTP e reverso, bem como um servidor de proxy de e-mail, escrito por Igor Sysoev desde 2005. O NGINX é um servidor web rápido, leve, e com inúmeras possibilidades de configuração para melhor performance. Tecnicamente, o NGINX consome menos memória que o Apache, pois lida com requisições Web através do conceito de “event-based web server”; já o Apache é baseado no conceito “process-based server”. Eles não são necessariamente “concorrentes”, Apache e NGINX podem trabalhar juntos. É possível diminuir o consumo de memória do Apache fazendo com que as requisições Web passem primeiro pelo NGINX. Desse modo, o Apache não precisa servir arquivos estáticos, e pode depender do bom controle de cache feito pelo NGINX

#### Ponto forte:

- Pouco consumo de memória
- Alto nível de desempenho
	
### 1.3. Postfix

Em computação, [Postfix](http://www.postfix.org) é um agente de transferência de e-mails (MTA) livre e de código aberto que encaminha e entrega e-mails, e tem como objetivo ser uma alternativa segura ao *Sendmail*, muito utilizado em servidores [UNIX](http://opengroup.org/unix). O Postfix foi originalmente escrito em 1997 por *Wietse Venema* no *Centro de Pesquisa IBM Thomas J. Watson* e teve a sua primeira versão lançada em 1998. O software também conhecido como *VMailer* e *IBM Secure Mailer*, continua a ser ativamente desenvolvido pelo seu criador e outros contribuidores e é o agente de transferência de e-mails padrão de inúmeros sistemas operativos como o [Linux](https://www.linuxfoundation.org)[²](http://kernel.org), *OS X*, o *Ubuntu*, e o [NetBSD](https://www.netbsd.org).

#### Ponto forte:

- Implementação de ferramentas de segurança
- Agilidade na implementação e configuração da ferramenta
- Alto poder de desempenho
	
## METODOLOGIA (PASSO A PASSO)

### Instalação do banco de dados MariaDB

Na solução proposta foi utilizado o repositório centos oficial do [MariaDB](https://downloads.mariadb.org/mariadb/repositories/).
Instalação do banco de dados:

`# yum install MariaDB-server MariaDB-client -y`

### Trabalhando o Hardening e fortificando a segurança do banco de dados:

Configurado a senha *root* de acesso, removido usuário anônimo, *base test* e desabilitado *login* remoto.

`# mysql_secure_installation`

***OBS**:  Todo o ambiente foi instalado e configurado em modo seguro chroot com exceção do MariaDB.*

#### Instalação do NGINX

Na solução proposta foi utilizado o repositório oficial [EPEL](https://dl.fedoraproject.org/pub/epel/6/x86_64/) para CentOS 6.

`# yum install nginx`

#### Criando Certificado SSL:

`openssl genrsa -des3 -out server.key 1024`

`openssl req -new -key server.key -out server.csr`

`openssl rsa -in server.key.tk -out server.key`

`openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt`

Abaixo detalhes das mudanças:

`Adicionado index.php na linha de índice.`

`Alterado o nome do servidor em server_name para o nome de domínio www.smarttbot.tk`

`Alterado a raiz para /usr/share/nginx/html/wordpress;`

`Adicionando suporte a ssl;`

`Adicionado a seção location ~ \.php$ {;`

#### Print do arquivo de configuração:

`# vi wordpress_ssl.conf`

`# HTTPS server configuration`

       server {
           listen       443;
           root /usr/share/nginx/html/wordpress;
           index index.php index.html index.htm;
           server_name www.smarttbot.tk;
           error_page 404 /404.html;
           error_page 500 502 503 504 /50x.html;
           location = /50x.html {
           root /usr/share/nginx/html;
           }
           ssl on;
           ssl_certificate /etc/nginx/ssl/server.crt;
           ssl_certificate_key /etc/nginx/ssl/server.key;
       location ~ \.php$ {
           root /usr/share/nginx/html/wordpress;
           fastcgi_pass   127.0.0.1:9000;
           fastcgi_index  index.php;
           fastcgi_param  SCRIPT_FILENAME   $document_root$fastcgi_script_name;
           include        fastcgi_params;
           }
       }

### Instalando Postfix:

Postfix instalado do repositório padrão do [CentOS](https://www.centos.org).

`# yum install postfix`

#### Criando certificado SSL:

***OBS**: Postfix instalado para somente enviar e-mail.*

`# mkdir -p /etc/postfix/ssl`

`# cd /etc/postfix/ssl`

`# openssl req -new -outform PEM -out smarttbot.cert -newkey rsa:2048 -nodes -keyout smarttbot.key -keyform PEM -days 365 -x509`

#### Configurando o postfix:

Segue abaixo *print* do arquivo `main.cf` e as configurações utilizadas no Postfix.

`# cd /etc/postfix`

`# vi main.cf`

  

     smtpd_banner = $myhostname ESMTP $mail_name
       biff = no
       append_dot_mydomain = no
       readme_directory = no
	   
       smtp_tls_cert_file = /etc/postfix/ssl/smarttbot.cert
       smtp_tls_key_file = /etc/postfix/ssl/smarttbot.key
       smtp_use_tls=yes
       smtp_tls_session_cache_database = btree:$data_directory/smtp_tls_session_cache
       smtp_tls_security_level = may
       smtp_tls_mandatory_protocols = !TLSv1, !TLSv1.1
       smtp_tls_mandatory_ciphers = high
       
	   inet_interfaces = loopback-only
       local_transport = error:local delivery is disabled
       mynetworks_style = host
       
	   alias_maps = hash:/etc/aliases
       alias_database = hash:/etc/aliases
       
	   relayhost =
       mailbox_size_limit = 0
       recipient_delimiter =
       inet_protocols = ipv4

#### Instalando Wordpress:

Fazendo download da última versão do Wordpress:

`# cd /usr/share/nginx/html/wordpress`

`# wget https://br.wordpress.worg/wordpress-4.9.1-pt_BR.zip`

#### Descompactando o pacote:

`# unzip wordpress-4.9.1-pt_BR.zip`

#### Acessando MariaDB:

`# mysql -u root -p`

Criando usuário, base de dados e definindo permissões:

       MariaDB > create database wordpress;
       MariaDB > CREATE USER wordpressuser@localhost;
       MariaDB > SET PASSWORD FOR wordpressuser@localhost=PASSWORD("wu8Aeph9sah3Yee9");
       MariaDB > GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED BY 'wu8Aeph9sah3Yee9';

`# cp wp-config-sample.php wp-config.php`
`# vi wp-config.php`

configuração do arquivo `wp-config.php`, segue abaixo o *print*.

  

     // ** MySQL settings - You can get this info from your web host ** //
       /** The name of the database for WordPress */
       define('DB_NAME', 'wordpress');
       /** MySQL database username */
       define('DB_USER', 'wordpressuser');
       /** MySQL database password */
       define('DB_PASSWORD', 'wu8Aeph9sah3Yee9');
       /** MySQL hostname */
       define('DB_HOST', 'localhost');
	   
Start no Nginx:
`Service nginx start`

### Configurando o Wordpress:

https://www.smarttbot.tk/wp-admin/install.php

Na tela de instalação configuramos o título do site, o usuário e senha do administrador e o endereço de e-mail.

Instalando plugin **WP Mail SMTP** para envio de e-mail:

`# cd /usr/share/nginx/html/wordpress/plugins`

`# wget https://downloads.wordpress.org/plugin/wp-mail-smtp.zip`

`# unzip wp-mail-smtp.zip`

#### Configurando o plugin:

Acessar o Wordpress com usuário admin, clicar em plugins, o plugin `wp-mail-smtp` se encontrará visível para instalação clicando em *install*.

Com a instalação feita vamos a configuração:

  

     Em:
       From Email = prova@smarttbot.tk
       From Name = Educação Financeiro
       Mailer = Other SMTP
       Escolhida a opção Other SMTP:
       SMTP Host = localhost
       SMTP Port = 25
       Encryption = none
       Auto TLS = Ativo

Salvar e sair.

Com essas etapas o Wordpress está pronto para o trabalho.

# Conclusão

Com a finalização dos trabalhos, foi feita a união de ferramentas propostas pela *SmarttBot* e de escolhas do desenvolvedor para que a criação de ambiente seguro e de fácil acesso para a inserção de conteúdo informativo através da ferramenta *Wordpress*, a configuração sendo realizada em servidor da *Linode* de altíssima confiança, com a ferramenta *MariaDB* e *NGINX* servindo de base, a escolha do *Postfix* para o disparo de mensagem eletrônicas de forma segura com o uso de *SSL/TLS*, o uso de repositórios oficiais (*EPEL*) para da confiabilidade a montagem de toda estrutura o que garante segurança e qualidade no uso do blog pelos visitantes e da administração pelo o desenvolvedor de conteúdos.
