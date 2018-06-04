# servidor de Email POSTFIX + DOVECOT

Servidor de email utilizado pelo Heyceitas.com.br.




## Instalação e configuração - Postfix e Dovecot

O tutorial usado para instalação do postfix e dovecot encontra-se no site da
 [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-postfix-e-mail-server-with-dovecot).

**Não usamos certificados SSL para instalação do nosso servidor de email.** 

## Problemas encontrados após a instalação e configuração - Erro de autenticação

Ao tentar fazer uso do email numa conta GMAIL ou por meio do Thunderbird, ambos davam erro de autenticação.

No arquivo syslog foi encontrado o seguinte erro:

```
postfix/smtpd[13801]: warning: SASL authentication failure: Password verification failed

postfix/smtpd[13801]: warning: mail-qt0-f177.google.com[209.85.216.177]: SASL PLAIN authentication failed: authentication failure
```

Descobrimos que o postfix, por padrão, faz uso do sistema Cyrus para fazer a configuração da autenticação SASL. Parte da documentação da DigitalOcean adiciona ao arquivo master.cf algumas linhas para mudar a configuração padrão do postfix para fazer uso do dovecot, mas por algum motivo as mudanças feitas no arquivo master.cf não vingaram. Para solucionar o problema, adicionamos algumas linhas ao arquivo main.cf do Postfix:

```
myhostname = mail.heyceitas.com.br
mydomain = heyceitas.com.br
myorigin = heyceitas.com.br
mydestination = mail.heyceitas.com.br, heyceitas.com.br, localhost, localhost.localdomain
relayhost =
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = all

alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtpd_sasl_auth_enable = yes
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
broken_sasl_auth_clients = yes
home_mailbox = Maildir/
```
Na linha `smtpd_sasl_type = dovecot` identificamos o dovecot como padrão a ser usado.

Para testar o funcionamento, pode ser visto pelo comando telnet a existência de duas novas linhas:

```
telnet localhost 25
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
220 mail.heyceitas.com.br ESMTP Postfix
EHLO google.com
250-mail.heyceitas.com.br
250-PIPELINING
250-SIZE 10240000
250-VRFY
250-ETRN
250-AUTH PLAIN
250-AUTH=PLAIN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250 DSN
```
`250-AUTH PLAIN` e `250-AUTH=PLAIN`, onde a segunda é adicionada pela linha `broken_sasl_auth_clients = yes` para prevenir que ocorra um erro de autenticação a alguns clientes usando versões antigas do Outlook.




