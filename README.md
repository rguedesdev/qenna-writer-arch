# Qenna Writer — repositório Arch Linux

Este repositório empacota o Qenna Writer original:
https://github.com/Pedrovskigg/qenna-writer

O GitHub Actions verifica diariamente se houve atualização no projeto
original. Quando encontra um novo commit, compila o pacote em Arch Linux
e publica os arquivos para instalação pelo Pacman.

## Configuração no computador

Depois da primeira publicação, adicione ao final de /etc/pacman.conf:

    [qenna-writer-arch]
    SigLevel = Optional
    Server = https://rguedesdev.github.io/qenna-writer-arch/

Depois execute:

    sudo pacman -Syu

A verificação do upstream é diária. A atualização no computador acontece
quando o Pacman é executado, não em segundo plano.

Este repositório aceita pacotes sem assinatura. A comunicação com o
GitHub Pages usa HTTPS, mas isso não substitui assinatura criptográfica.
