# Aula 2 - Docker Desktop

## 1. Introdução: Configuração do Ambiente de Desenvolvimento com Docker e VS Code

O Docker é uma plataforma de contêineres que permite empacotar uma aplicação e todas as suas dependências em um único "contêiner". Isso garante que a aplicação funcione da mesma forma em qualquer lugar, independentemente do ambiente.

O vídeo da aula 02 se concentra no passo a passo para instalar o **_Docker Desktop_** e o **_VS Code_** no Linux. O vídeo também mostra como usar o Git e algumas extensões importantes do VS Code para trabalhar de forma eficiente. O ponto alto é a demonstração de como criar um Dockerfile para uma aplicação Node.js, transformando-a em uma imagem Docker, que é o primeiro passo para a automação e a orquestração de ambientes.

### 1.1 Exemplos Práticos com Comandos: Intalação do Docker

Abaixo encontram-se os comandos necessários para a instalação do Docker em um sistema baseado em Debian/Ubuntu.

```
# Remove versões antigas do Docker
sudo apt remove docker.io
sudo apt autoremove

# Adiciona o repositório Docker
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Adiciona seu usuário ao grupo do Docker para não precisar usar o sudo
sudo usermod -aG docker <seu_usuario>

# Habilita o serviço do Docker
sudo systemctl enable docker --now
```

## Comandos Essenciais do Docker

- **docker build -t <nome_da_imagem> .**: Este comando cria uma imagem Docker a partir de um Dockerfile. O -t define a tag (nome) da imagem, e o . indica que o Dockerfile está no diretório atual.

- **docker images**: Lista todas as imagens Docker que você tem no seu sistema.

- **docker ps**: Exibe os contêineres que estão em execução.

- **docker run <nome_da_imagem>**: Inicia um contêiner a partir de uma imagem.