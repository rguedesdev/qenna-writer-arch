# Qenna Writer — repositório Arch Linux

O código-fonte original permanece em:
https://github.com/Pedrovskigg/qenna-writer

O GitHub Actions verifica diariamente se há atualizações no projeto
original. Quando encontra uma nova versão, compila o pacote em Arch Linux
e publica o pacote e o índice do Pacman em GitHub Releases.

## Configuração

Adicione ao final de /etc/pacman.conf:

    [qenna-writer-arch]
    SigLevel = Optional
    Server = https://github.com/rguedesdev/qenna-writer-arch/releases/download/current/

Depois execute:

    sudo pacman -Syu

A verificação do upstream é diária. A atualização no computador ocorre
quando o Pacman é executado.

Os pacotes são publicados sem assinatura criptográfica. A comunicação
utiliza HTTPS, mas a assinatura GPG poderá ser configurada posteriormente.
