# servidor de Email POSTFIX + DOVECOT

Servidor de email utilizado pelo Heyceitas.com.br

## Instalação e configuração - Postfix e Dovecot

O tutorial usado para instalação do postfix e dovecot encontra-se no site da [DigitalOcean]
(https://www.digitalocean.com/community/tutorials/how-to-set-up-a-postfix-e-mail-server-with-dovecot)

**Não usamos certificados SSL para instalação do nosso servidor de email** 

## Problemas encontrados após a instalação e configuração

Ao tentar fazer uso do email numa conta GMAIL ou por meio do Thunderbird, foi encontrado o seguinte erro:
`
warning: mail-qt0-f177.google.com[209.85.216.177]: SASL PLAIN authentication failed: authentication failure
`

