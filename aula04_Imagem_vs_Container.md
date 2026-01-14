# Aula 4 - Diferença entre imagem e container

## 1. Introdução: O que é Docker e Como Funciona?

**Imagem** é o molde/_blueprint_ (somente leitura) de uma aplicação com código e dependências, enquanto o **contêiner** é a instância viva e executável dessa imagem, um ambiente isolado que roda o _software_, permitindo modificações (como arquivos criados) que não afetam a imagem original, que pode ser usada para criar vários contêineres.

---

## 2. Conceitos Essenciais e Comandos

A grande diferença entre Contêineres vs. Máquinas Virtuais (_VMs_):

- **Contêineres**: Compartilham o _kernel_ do sistema operacional do hospedeiro. Isso os torna extremamente leves e rápidos para iniciar e parar.
- **_VMs_**: Executam um sistema operacional completo, com seu próprio _kernel_, o que as torna mais pesadas e demoradas para iniciar.

Essa diferença de arquitetura é o que faz do Docker uma ferramenta tão poderosa para o desenvolvimento e a implantação de aplicações modernas.

### 2.2 Comandos Fundamentais do Dockerfile

O **Dockerfile** é a "receita" para construir sua imagem. É essencial conhecer as diretivas mais comuns:

- **FROM**: Define a imagem base. Por exemplo, **FROM ubuntu:latest**.
- **WORKDIR**: Define o diretório de trabalho dentro da imagem.
- **COPY**: Copia arquivos do seu sistema local para a imagem.
- **RUN**: Executa comandos dentro da imagem durante o processo de construção. Use para instalar dependências.
- **CMD**: Define o comando padrão que será executado quando o contêiner for iniciado.

### 2.2 Comandos Essenciais do Docker CLI

- **docker build .**: Cria uma imagem Docker a partir de um Dockerfile no diretório atual.
- **docker images**: Lista as imagens que você tem no seu sistema.
- **docker run <nome_da_imagem>**: Cria e inicia um contêiner a partir de uma imagem.
- **docker ps**: Lista os contêineres que estão em execução.

---

## 3. Tópicos Avançados

