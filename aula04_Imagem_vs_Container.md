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

### 3.1 O Ciclo de Vida: ```docker run```, ```docker start``` e ```docker stop```

A confusão entre ```run``` e ```start``` é um dos erros mais comuns de iniciantes, levando à criação desnecessária de múltiplos contêineres.

- ```docker run``` **(Criação + Execução):**

    - Este é o comando "tudo em um". Ele diz ao _daemon_ do Docker para criar um novo contêiner a partir de uma imagem e, em seguida, ***iniciá-lo***.
    - Tecnicamente, ele executa um ```docker create``` (aloca o sistema de arquivos e prepara a camada de escrita) seguido imediatamente de um ```docker start```.
    - ***Ponto Crítico:*** Ao executar docker run 10 vezes seguidas, será obtido 10 contêineres distintos (e 9 deles provavelmente ficarão "órfãos" consumindo espaço em disco se não forem removidos).

- ```docker start``` **(Retomada):**

    - Este comando é usado para iniciar um contêiner que já existe, mas está no estado ```stopped``` (parado).
    - Ele reutiliza as configurações, volumes e, crucialmente, a camada de escrita (dados) que o contêiner já possuía antes de ser desligado.

- ```docker stop``` **(Parada Graciosa):**

    - Envia um sinal ```SIGTERM``` ao processo principal (PID 1) dentro do contêiner. Isso dá à aplicação a chance de salvar estados, fechar conexões de banco de dados e finalizar processos filhos "graciosamente".
    - Se o processo não encerrar após um período (padrão de 10 segundos), o Docker envia um ```SIGKILL```, forçando o encerramento imediato.

### 3.2 Mapeamento de Portas (_Port Mapping_)

Por padrão, um contêiner Docker opera em isolamento de rede (geralmente em uma rede bridge). Isso significa que, mesmo que seu servidor _web_ esteja rodando na porta 80 dentro do contêiner, ele é inacessível para o mundo exterior (seu host ou a internet).

**Como funciona:** O mapeamento de portas cria uma regra de _NAT_ (_Network Address Translation_) no _firewall_ do _host_ (iptables no Linux). Isso encaminha o tráfego de uma porta específica na interface de rede do Host para uma porta específica dentro do Contêiner.

**Sintaxe e Importância:** Usa-se a flag ```-p HOST_PORT:CONTAINER_PORT```.

- Exemplo: ```docker run -p 8080:80 nginx```
- A requisição chega em ```localhost:8080``` (Host) $\rightarrow$ Docker intercepta $\rightarrow$ Encaminha para ```porta 80``` (Contêiner).
I

**Importância para Web Apps:** Sem isso, não é possível visualizar a aplicação no navegador. Além disso, permite rodar múltiplas instâncias do mesmo serviço (ex: três servidores web) no mesmo host, apenas variando a porta externa (8080, 8081, 8082) enquanto internamente todos escutam na porta 80.

### 3.3 Persistência de Dados: Volumes vs. Bind Mounts

Contêineres são efêmeros. Se um contêiner é removido (```docker rm```), qualquer dado escrito na sua camada de leitura/escrita (R/W Layer) é perdido para sempre. Para bancos de dados ou logs, isso é inaceitável.

Existem duas formas principais de persistir dados:

**A. Volumes (Recomendado para Produção e Banco de Dados)**

Os volumes são áreas de armazenamento gerenciadas inteiramente pelo Docker.

- ```Localização:``` Ficam em uma área protegida do host (geralmente ```/var/lib/docker/volumes/```).
- **Vantagens:** São independentes do sistema de arquivos do _host_, funcionam bem entre diferentes SOs, são mais seguros e podem ser compartilhados entre contêineres facilmente.
- **Uso:** ```docker run -v meu_volume:/caminho/no/container ...```

**B. _Bind Mounts_ (Recomendado para Desenvolvimento)**

Mapeiam um arquivo ou diretório exato do seu _Host_ para dentro do Contêiner.

- **Localização:** Qualquer lugar no seu disco (ex: ```/home/usuario/projeto```).

- **Vantagens:** Alterações no _Host_ são refletidas instantaneamente no Contêiner.

- **Cenário Ideal:** Você edita o código-fonte (```index.php``` ou ```app.py```) na sua IDE no Windows/Linux, e o servidor web dentro do contêiner atualiza imediatamente.

- **Uso:** ```docker run -v /home/user/projeto:/app ...``

### 3.4 Orquestração com Docker Compose

O ```docker-compose``` é uma ferramenta fundamental para definir e rodar aplicações multi-contêiner. Ele usa um arquivo YAML para configurar todos os serviços, redes e volumes.

Para orquestrar um Servidor _Web_ (ex: Python Flask) e um Banco de Dados (PostgreSQL), você cria um arquivo ```docker-compose.yml```:

YAML

```YAML
version: '3.8'
services:
  # Serviço 1: Nossa Aplicação Web
  web-app:
    build: .                 # Constrói imagem baseada no Dockerfile local
    ports:
      - "5000:5000"          # Mapeamento de portas
    volumes:
      - .:/app               # Bind Mount: Código editado no host reflete no container
    environment:
      - DB_HOST=db_server    # Nome do serviço abaixo (DNS automático)
    depends_on:
      - db_server            # Garante ordem de início

  # Serviço 2: Banco de Dados
  db_server:
    image: postgres:15-alpine
    volumes:
      - pg_data:/var/lib/postgresql/data # Volume nomeado (persistência real)
    environment:
      - POSTGRES_PASSWORD=segredo

# Definição do Volume Gerenciado pelo Docker
volumes:
  pg_data:
  ```

**Conceitos Chave no Compose:**

- **Service Discovery (DNS):** Note que no ```web-app```, definimos o _host_ do banco como ```db_server```. O Docker cria uma rede interna onde o nome do serviço resolve automaticamente para o IP do contêiner correto. Não é necessário saber o IP.
- **Infrastructure as Code (IaC):** Toda a sua infraestrutura de desenvolvimento está documentada e versionada neste arquivo.
- **Comando Único:** Basta rodar ```docker-compose up``` e todo o ambiente sobe.