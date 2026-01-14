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

---

## 2. Comandos Essenciais do Docker

- **docker build -t <nome_da_imagem> .**: Este comando cria uma imagem Docker a partir de um Dockerfile. O -t define a tag (nome) da imagem, e o . indica que o Dockerfile está no diretório atual.

- **docker images**: Lista todas as imagens Docker que você tem no seu sistema.

- **docker ps**: Exibe os contêineres que estão em execução.

- **docker run <nome_da_imagem>**: Inicia um contêiner a partir de uma imagem.

---


## 3. Tópicos Avançados

### 3.1 A Diferença entre "Imagem" e "Contêiner"

Para entender a distinção, a analogia mais comum e precisa na Ciência da Computação é a da Programação Orientada a Objetos (POO): **A Imagem é a Classe, e o Contêiner é o Objeto (instância)**.

- **Imagem (_Docker Image_):**
  - **Definição:** É um pacote executável leve, autônomo e imutável que inclui tudo o que é necessário para executar um pedaço de software: código, runtime, bibliotecas, variáveis de ambiente e arquivos de configuração.
  - **Estrutura Técnica:** Uma imagem é composta por múltiplas camadas (_layers_) de somente leitura (_Read-Only_). Cada instrução no Dockerfile cria uma nova camada que é empilhada sobre a anterior. Isso utiliza um sistema de arquivos **_Union File System_ (_UnionFS_)**, que permite combinar essas camadas em uma única visualização coesa.
  - **Estado:** _Build-time_ (tempo de construção). Ela não está "rodando"; é o artefato estático armazenado em um registro (como o _Docker Hub_).
- **Contêiner (_Docker Container_):**
  - **Definição:** É uma instância em tempo de execução (_runtime_) de uma imagem. É o ambiente onde a aplicação realmente opera.
  - **Estrutura Técnica:** Quando um contêiner é iniciado, o Docker pega a imagem (camadas _read-only_) e adiciona uma camada de leitura e escrita (_Read-Write Layer_) no topo. Todas as mudanças feitas no contêiner (arquivos criados, _logs_, modificações) acontecem apenas nessa camada superior efêmera.
  - **Isolamento:** Contêineres compartilham o _kernel_ do _host_, mas são isolados via _Namespaces_ (isolamento de processos, rede, usuários) e _Cgroups_ (controle de recursos como CPU e RAM).

### 3.2 Orquestração com Docker Compose

O ```docker-compose``` é uma ferramenta para definir e rodar aplicações multi-contêiner. Enquanto o comando ```docker run``` inicia um único contêiner, o Compose lê um arquivo YAML para subir toda a _stack_ de infraestrutura necessária.

**Figura 1 - Arquitetura _Tradional vs Serverless_.**

![Arquitetura Tradional vs Serverless](images/traditional_vs_serverless_arch.jpeg)

#### 3.2.1 Como utilizar para um Servidor Web + Banco de Dados

Deve ser criado um arquivo chamado ```docker-compose.yml``` na raiz do projeto. Abaixo, um exemplo técnico de uma aplicação Python (Flask) conectada a um PostgreSQL:

```YAML
version: '3.8'  # Versão da sintaxe do arquivo

services:
  # Serviço 1: Aplicação Web
  web:
    build: .  # Constrói a imagem baseada no Dockerfile local
    ports:
      - "5000:5000"  # Mapeia porta Host:Container
    environment:
      - DB_HOST=db
      - DB_NAME=meubanco
      - DB_USER=usuario
      - DB_PASS=senha123
    depends_on:
      - db  # Garante que o container 'db' inicie antes do 'web'
    networks:
      - minha-rede

  # Serviço 2: Banco de Dados
  db:
    image: postgres:15-alpine  # Usa imagem oficial leve (Alpine)
    environment:
      - POSTGRES_DB=meubanco
      - POSTGRES_USER=usuario
      - POSTGRES_PASSWORD=senha123
    volumes:
      - pgdata:/var/lib/postgresql/data  # Persistência de dados (Host:Container)
    networks:
      - minha-rede

# Definição de volumes persistentes
volumes:
  pgdata:

# Definição de redes para isolamento
networks:
  minha-rede:
```

#### 3.2.2 Conceitos Chave no Exemplo

1. **Services:** Define os componentes da aplicação. O Docker cria um DNS interno, permitindo que o serviço ```web``` acesse o banco simplesmente usando o _hostname_ ```db```.
2. **Volumes:** Essencial para bancos de dados. Se o contêiner ```db``` for destruído, os dados persistem no volume ```pgdata```.
3. **Depends_on:** Controla a ordem de inicialização (embora não garanta que o serviço dentro do contêiner esteja pronto, apenas que o contêiner iniciou).

Para a execução de tudo: ```docker-compose up -d``` (o _flag_ ```-d``` executa em modo _detached_, ou seja, em segundo plano).

### 3.3 Principais Diretivas de um Dockerfile

O ```Dockerfile``` é o _"blueprint"_ da sua imagem. A ordem das diretivas impacta diretamente o _cache_ de _build_ e o tamanho final da imagem.

```FROM```

- **Função:** Define a imagem base (ex: ubuntu:20.04, python:3.9-slim).
- **Importância:** É o ponto de partida. Escolher imagens "alpine" ou "slim" reduz drasticamente a superfície de ataque e o tamanho do _download_.

```WORKDIR```

- **Função:** Define o diretório de trabalho dentro do contêiner para os comandos seguintes.
- **Importância:** Evita o uso de caminhos absolutos repetitivos e garante organização. Equivalente ao comando ```cd```.

```COPY``` vs ```ADD```

- **Função:** Copiam arquivos do _host_ para a imagem.
- **Importância:** Use preferencialmente COPY. O ADD tem funcionalidades extras (descompactar tarballs, baixar URLs) que podem causar comportamentos imprevisíveis se não forem necessárias.

```RUN```

- **Função:** Executa comandos durante o build da imagem (ex: apt-get install, pip install).
- **Importância:** Cria uma nova camada na imagem. É vital encadear comandos (usando &&) para reduzir o número de camadas e limpar caches na mesma instrução para economizar espaço.

```CMD``` vs ```ENTRYPOINT```

- **Função:** Definem o comando executado quando o contêiner inicia.
- **Importância:**
  - ```CMD```: Define argumentos padrão que podem ser sobrescritos pelo usuário ao rodar ```docker run```.
  - ```ENTRYPOINT```: Configura o contêiner para rodar como um executável. Argumentos passados no ```docker run``` são anexados a ele, não o substituem.

```EXPOSE```

- **Função:** Informa em qual porta a aplicação escuta.
- **Importância:** Funciona mais como documentação. Não publica a porta para o host automaticamente (isso é feito no ```run``` com ```-p```), mas é crucial para comunicação entre contêineres e para quem lê o Dockerfile.

---

## 4. Desafios de Segurança e Mitigação

Segurança em contêineres é um tópico crítico, pois um contêiner mal configurado pode dar acesso ao servidor _host_ (_breakout_).

|Desafio|Explicação Técnica|Mitigação (_Best Practices_)|
|:---:|:---:|:---:|
|Execução como Root|Por padrão, processos no contêiner rodam como ```root```. Se houver uma vulnerabilidade no _runtime_, o atacante ganha privilégios de _root_ no _host_.|Utilize a diretiva ```USER``` no Dockerfile para criar e mudar para um usuário não privilegiado antes do comando final.|
|Imagens Vulneráveis|Usar imagens base antigas ou desconhecidas que contêm CVEs (vulnerabilidades conhecidas) não corrigidas.|Use imagens oficiais e mínimas (ex: Alpine, Distroless). Utilize _scanners_ de segurança (como **Trivy**, **Clair** ou **Docker Scout**) no _pipeline_ de CI/CD.|
|Superfície de Ataque|Imagens com ferramentas desnecessárias (compiladores, _debuggers_, _shell_) facilitam a vida do atacante dentro do contêiner.|Utilize **Multi-stage Builds**. Compile o código em um estágio com todas as ferramentas e copie apenas o binário/artefato final para uma imagem limpa de produção.|
|Segredos Expostos|_Hardcoding_ de senhas, chaves de API ou _tokens_ dentro do Dockerfile ou variáveis de ambiente visíveis.|Nunca use ```ENV``` para senhas no _build_. Use **Docker Secrets** (em Swarm) ou injete segredos em tempo de execução via gerenciadores de segredos (Vault, AWS Secrets Manager).|
|Recursos Ilimitados|Um contêiner comprometido ou com _bug_ (_memory leak_) pode consumir toda a CPU/RAM do _host_, causando Negação de Serviço (DoS).|Defina limites rígidos de recursos no ```docker-compose``` ou na execução (```--memory="512m" --cpus="1.0"```).|
