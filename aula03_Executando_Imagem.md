# Aula 3 - Executando a imagem

## 1. Introdução: Docker Desktop e o Ciclo de Vida da Imagem

Na prática o grande benefício do Docker é **a capacidade de rodar uma aplicação sem ter suas dependências instaladas na máquina hospedeira**.

No Docker existem dois conceitos fundamentais:

- **Imagem:** O modelo estático da aplicação com todas as suas dependências e
- **Contêiner:** A instância em execução dessa imagem.

Adicionalmente, a interface gráfica **Docker Desktop** pode ser usada para gerenciar imagens e contêineres, ressaltando que, como administrador, é essencial dominar a linha de comando.

O ponto principal da aula é a portabilidade que o Docker oferece. Ao empacotar a aplicação em um contêiner, é garantido que ela funcionará da mesma forma em qualquer ambiente, eliminando problemas de compatibilidade e simplificando o processo de desenvolvimento e implantação.

## 2. Exemplos Práticos com Comandos

### 2.1 Gerenciando Imagens e Contêineres no terminal

- **docker images**: Este é o comando mais básico para ver quais imagens você tem disponíveis no seu sistema. É o primeiro passo para saber o que você pode executar.
- **docker run <nome_da_imagem>**: Cria e inicia um novo contêiner a partir de uma imagem. O comando é simples e é a porta de entrada para executar qualquer aplicação.
- **docker ps**: Exibe os contêineres que estão em execução no momento. É muito útil para verificar se sua aplicação está rodando.
- **docker ps -a**: Exibe todos os contêineres, incluindo os que já foram encerrados. É perfeito para limpar o ambiente.

### 2.2 O Conceito de Portabilidade do Docker

A aula demonstra um conceito muito importante: não é necessário ter o **Node.js** instalado na sua máquina para rodar a aplicação. Isso acontece porque a imagem Docker já contém um sistema operacional mínimo (alpine) e a versão do Node.js necessária. Tal fato é a essência do "contêiner": **um ambiente isolado e auto-suficiente**.
