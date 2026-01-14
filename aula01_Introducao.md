# DOMINE DOCKER DO ZERO AO DEPLOY! | Curso Essencial

## Aula 1 - INTRODUÇÃO

## 1. Introdução

O nome **_Docker_** vem do inglês e possui dois significados principais que se relacionam diretamente com a função da tecnologia:

- **Estivador (_Docker_):** Em inglês, "docker" é um termo para "estivador", que é o profissional responsável por carregar e descarregar navios, especialmente os contêineres. Essa é uma analogia perfeita, já que o Docker, como plataforma, é o "estivador" que empacota, transporta e executa os "contêineres" de _software_.
- **Contêiner (_Container_)**: Embora "docker" não signifique diretamente "contêiner", a tecnologia **_Docker_** é fundamentalmente sobre contêineres de _software_. Esses contêineres são pacotes padronizados que incluem tudo o que um _software_ precisa para rodar (código, bibliotecas, configurações, etc.), garantindo que ele funcione de forma consistente em qualquer ambiente.

A logo do **_Docker_**, uma baleia carregando contêineres, também reforça essa ideia de transporte e gerenciamento de contêineres, consolidando a analogia com o trabalho de um estivador.

Embora tanto **_Docker_** quanto máquinas virtuais (do Inglês, _Virtual Machines, VMs_) sejam tecnologias de virtualização que permitem isolar ambientes para a execução de softwares, eles operam em camadas diferentes e, por isso, possuem características e casos de uso distintos.

### 1.1 Máquinas Virtuais (VMs)

Uma **máquina virtual (MV)** é uma emulação de um computador físico completo. Pense nela como um computador dentro do seu computador.

- **Camada de Virtualização:** Uma MV virtualiza o _hardware_ (CPU, memória, disco, rede). Para isso, ela usa um _software_ chamado **_Hypervisor_** (ou monitor de MV). O _Hypervisor_ cria e gerencia as _VMs_, alocando recursos do _hardware_ físico para cada uma delas.
- **Sistema Operacional (SO) Convidado:** Cada MV executa um Sistema Operacional (SO) convidado completo (_Windows, Linux, macOS_, etc.) sobre o _Hypervisor_. Isso significa que cada MV tem seu próprio _kernel_, seus próprios processos de inicialização e suas próprias bibliotecas.
- **Isolamento:** As _VMs_ oferecem um isolamento robusto. Se uma MV for comprometida por um vírus, por exemplo, as outras _VMs_ no mesmo _host_ não serão afetadas, pois cada uma tem seu próprio SO isolado.
- **Recursos:** Cada MV precisa de uma quantidade significativa de recursos (CPU, RAM, espaço em disco) para rodar seu SO convidado, mesmo que a aplicação em si seja pequena. Isso as torna mais "pesadas" e lentas para inicializar.
- **Portabilidade:** _VMs_ são menos portáteis. Mover uma MV entre diferentes _hypervisors_ ou provedores de nuvem pode ser um desafio e, muitas vezes, requer reconfiguração.

### 1.2 Docker (Contêineres)

O Docker é uma plataforma que utiliza a tecnologia de contêineres. Ao contrário das _VMs_, os contêineres não virtualizam o _hardware_ completo, mas sim o sistema operacional.

- **Camada de Virtualização:** O Docker virtualiza a camada de aplicação e o espaço do usuário do sistema operacional. Ele roda diretamente sobre o SO _host_ e compartilha o _kernel_ do SO _host_ entre os contêineres.
- **Sistema Operacional (SO) Convidado:** Contêineres não incluem um SO convidado completo. Eles empacotam apenas o código da aplicação, suas dependências, bibliotecas e configurações necessárias para rodar.
- **Isolamento:** O isolamento dos contêineres é feito através de recursos do próprio _kernel_ do SO _host_ (como _namespaces_ e _cgroups_). Embora ofereçam um bom nível de isolamento, não é tão completo quanto o de uma MV, pois compartilham o mesmo _kernel_.
- **Recursos:** Contêineres são muito mais leves e eficientes em termos de recursos. Como compartilham o _kernel_ do SO _host_ e não precisam inicializar um SO completo, eles iniciam em segundos (ou milissegundos) e consomem menos CPU e memória. Isso permite rodar muito mais contêineres em uma mesma máquina física do que VMs.
- **Portabilidade:** Contêineres são altamente portáteis. Uma vez que uma aplicação é _"dockerizada"_ (empacotada em um contêiner), ela pode ser executada de forma consistente em qualquer ambiente que tenha o Docker instalado (seu _laptop_, um servidor, a nuvem), independentemente do SO subjacente (desde que o Docker seja compatível com ele).

### 1.3 Conclusão

A escolha entre Docker e VMs depende das suas necessidades.

- Use **_VMs_** se você precisar de:
  - **Isolamento de segurança máximo** entre ambientes.
  - Executar **diferentes sistemas operacionais** no mesmo _hardware_ (ex: Windows em um host Linux).
  - Compatibilidade com **aplicações legadas** que exigem um SO específico.

- Use **Docker** se você precisar de:
  - **Eficiência de recursos** e alta densidade de aplicações por host.
  - **Inicialização rápida** e escalabilidade ágil.
  - **Portabilidade** e consistência do ambiente de desenvolvimento para produção.
  - Trabalhar com **microsserviços** e DevOps.

É importante notar que **Docker pode ser executado dentro de uma Máquina Virtual**. Por exemplo, é comum executar o Docker Desktop em um Mac ou Windows, que por sua vez, usa uma MV Linux leve para rodar os contêineres. Isso combina a segurança da MV com a flexibilidade e eficiência dos contêineres.

---

## 2. Instalação do Docker no Linux

Instalar o Docker no Linux é um processo bem direto, mas os comandos exatos podem variar um pouco dependendo da distribuição que você usa (Ubuntu, Fedora, CentOS, etc.).

### 2.1 Antes de Começar: Desinstalar Versões Antigas

É uma boa prática remover qualquer instalação antiga do Docker para evitar conflitos. Use os comandos abaixo, se aplicável à sua distribuição:

**Para Debian/Ubuntu:**

Bash

```sudo apt remove docker docker-engine docker.io containerd runc
```

**Para CentOS/Fedora:**

Bash

```sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
sudo dnf remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine # Para Fedora mais recente
```

### 2.2 Processo de instalação

A forma mais recomendada é usar o repositório oficial do Docker para garantir que sempre o recebimento das versões mais recentes e seguras.

Seguir o tutorial apresentado no seguinte endereço eletrônico:
[Install Docker Desktop on Linux](https://docs.docker.com/desktop/setup/install/linux/)

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

---

## 5. Softwares Adicionais

### 5.1 Git

**Git e GitHub Desktop**
- Instalar o Git e GitHub Desktop no SO utilizado
- Configurar via terminal: 
  - git config --global user.name "xxxx"
  - git config --global user.email "xxxx@xxx"

### 5.2 Extensões do VsCode

- C/C++
- C/C++ Extension Pack
- C/C++ Themes
- CMake Tools
- Debugger for Java
- Extension Pack for Java
- GitHub Copilot Chat
- GitHub Pull Requests
- Gradle for Java
- Jupyter
- Jupyter Cell Tags
- Jupyter Keymap
- Jupyter Notebook Renderers
- Jupyter Slide Show
- Language Support for Java(TM) by Red Hat
- Markdown Preview Github Styling
- Material Icon Theme
- Maven for Java
- PlatformIO IDE
- Portuguese (Brazil) Language Pack for Visual Studio Code
- Prettier - Code formatter
- Project Manager for Java
- Pylance
- Python
- Python Debugger
- Python Environments
- Test Runner for Java