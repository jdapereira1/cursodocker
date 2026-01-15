# Aula 5 - Docker Imagem

## 1. Conceitos Essenciais e Comandos

Para fixar o conteúdo, vamos ampliar os conceitos e comandos apresentados no vídeo:

**A Grande Diferença: Imagens do Docker Hub vs. Imagens Personalizadas***

- **Imagens do Docker Hub:** São imagens pré-configuradas e otimizadas para tarefas específicas. Usá-las economiza tempo e esforço, pois você não precisa começar do zero.

- **Imagens Personalizadas:** São imagens que você cria a partir de um Dockerfile. Isso permite que você adapte o ambiente exatamente para as necessidades da sua aplicação, instalando as dependências corretas e configurando o sistema.

**Comandos Fundamentais do Dockerfile**

- **FROM:** Define a imagem base. Por exemplo, ```FROM node:alpine``` para começar com uma base leve e já com o Node.js instalado.

- **CMD:** Define o comando padrão que será executado quando o contêiner for iniciado.

**Comandos Essenciais do Docker CLI**

- ```docker build -t <nome_da_imagem> .```**:** Este comando cria uma imagem Docker a partir de um Dockerfile. O ```-t``` atribui uma "tag" (um nome e versão) para a imagem, e o ```.``` indica que o Dockerfile está no diretório atual.

- ```docker images```**:** Lista todas as imagens que você tem no seu sistema.

- ```docker run -it <nome_da_imagem> bash```**:** Cria e inicia um novo contêiner e te dá acesso a um terminal interativo (```-it```) dentro dele. Isso é perfeito para depurar e entender o ambiente.

---

## 2. Tópicos Avançados

### 2.1 CMD vs. ENTRYPOINT: Diferenças e Melhores Práticas ###

Embora ambos instruam o contêiner sobre o que executar ao iniciar, a diferença reside na mutabilidade do comando e em como eles interagem com argumentos passados via CLI (```docker run```).

- **CMD (Command):**

    - **Função:** Define os argumentos padrão para o contêiner.
    - **Comportamento:** Pode ser facilmente sobrescrito. Se o usuário executar ```docker run imagem <comando>```, o ```<comando>``` substitui inteiramente o que estava definido no ```CMD```.

- **ENTRYPOINT:**

    - **Função:** Define o executável principal do contêiner.
    - **Comportamento:** É imutável por padrão (a menos que se use a flag ```--entrypoint```). Argumentos passados no ```docker run``` são **anexados** (_appended_) ao final do comando definido no ```ENTRYPOINT```, em vez de substituí-lo.

**A Prática Recomendada (Padrão "Executável"):** A combinação dos dois é a abordagem mais robusta para imagens de produção. Você usa o ```ENTRYPOINT``` para definir o binário e o ```CMD``` para definir os flags/parâmetros padrão.

_Exemplo prático (Dockerfile):_

**Dockerfile**
```Dockerfile
# Define o executável fixo
ENTRYPOINT ["python", "app.py"]

# Define o argumento padrão (que pode ser trocado pelo usuário)
CMD ["--modo=producao"]
```

- **Se rodar:** ```docker run minha-imagem``` $\rightarrow$ Executa: ```python app.py --modo=producao```
- **Se rodar:** ```docker run minha-imagem --modo=teste``` $\rightarrow$ Executa: ```python app.py --modo=teste```

### 2.2 Orquestração com Docker Compose ###

O ```docker-compose``` atua como um orquestrador local, permitindo definir a infraestrutura da aplicação como código (IaC) através de um arquivo YAML. Ele gerencia o ciclo de vida de múltiplos contêineres que precisam se comunicar.

**Como utilizar (Exemplo Web + Banco):**

Deve ser criado um arquivo ```docker-compose.yml```. A mágica acontece na **Descoberta de Serviço (_Service Discovery_)**; o Docker cria uma rede interna onde o nome do serviço (ex: banco) funciona como um _hostname_ (DNS) acessível pelos outros contêineres.

YAML

```YAML
version: '3.8'

services:
  # Serviço 1: Servidor Web
  web-app:
    build: .                 # Constrói a imagem localmente
    ports:
      - "8080:80"            # Expõe porta 80 do container na 8080 do host
    environment:
      - DB_HOST=banco        # Aponta para o nome do serviço abaixo
    depends_on:
      - banco                # Ordem de inicialização

  # Serviço 2: Banco de Dados
  banco:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=senha
    volumes:
      - dados-db:/var/lib/postgresql/data # Persistência

# Definição de volumes
volumes:
  dados-db:
  ```

Para subir o ambiente, execute:

Bash

```Bash
docker-compose up -d
```
Isso cria a rede, os volumes, constrói a imagem da aplicação e inicia os contêineres na ordem correta.

### 2.3 O Conceito de Camadas (Layers) e Otimização ###

O Docker utiliza um sistema de arquivos chamado **Union File System (UnionFS)**. Uma imagem Docker não é um bloco único, mas uma pilha de camadas somente leitura (_read-only layers_).

**Como funciona:**

- Cada instrução no Dockerfile (```FROM```, ```RUN```, ```COPY```) que modifica o sistema de arquivos cria uma nova camada.
- Essas camadas são empilhadas umas sobre as outras.
- Quando o contêiner roda, o Docker adiciona uma camada fina de **Leitura/Escrita** no topo.

**Importância para Otimização:**

1. **Cache de _Build_:** O Docker faz cache de cada camada. Se você altera a linha 10 do seu Dockerfile, as linhas 1 a 9 (camadas anteriores) são reutilizadas instantaneamente, acelerando drasticamente o _build_.

    - _Estratégia:_ Coloque instruções que mudam pouco (instalação de dependências do sistema) no topo e instruções que mudam muito (código fonte da aplicação) no final.

2. **Tamanho da Imagem:** Como as camadas são aditivas, se você criar um arquivo na Camada A e apagá-lo na Camada B, o arquivo não estará visível no final, mas continuará ocupando espaço na Camada A.

    - _Estratégia:_ Encadeie comandos para limpar o lixo na mesma camada em que ele foi criado.
    - _Ruim (Cria 2 camadas, lixo fica na primeira):_ 
    ```Dockerfile```
    
        ``` Dockerfile
        RUN apt-get update
        RUN apt-get install -y pacote && rm -rf /var/lib/apt/lis`s/
        ```

    - _Bom (Cria 1 camada, lixo é excluído antes da camada fechar):_
     ```Dockerfile```
    
        ``` Dockerfile
        RUN apt-get update && \
        apt-get install -y pacote && \
        rm -rf /var/lib/apt/lists/*
        ```
### 2.4 Diretivas de Volume e Porta no Dockerfile ###

É crucial distinguir entre o que é documentação/preparação no Dockerfile e o que é execução no _runtime_.

**A. Portas:** ```EXPOSE```

- **Diretiva:** ```EXPOSE <porta>```
- **Função:** Funciona principalmente como documentação. Ela informa ao Docker (e a quem lê o arquivo) que a aplicação escuta naquela porta.
- **Importância:** O ```EXPOSE``` não publica a porta para o _host_ automaticamente. Para acessar a aplicação de fora, você ainda precisa usar a -flag- ```-p``` no ```docker run``` ou a chave ```ports``` no ```docker-compose```. Entretanto, se usar a _flag_ ```-P``` (P maiúsculo) no _runtime_, o Docker mapeará todas as portas listadas no ```EXPOSE``` para portas aleatórias no _host_.

**B. Volumes:** ```VOLUME```

- **Diretiva:** ```VOLUME ["/caminho/no/container"]```
- **Função:** Cria um ponto de montagem com um volume anônimo.
- **Importância:**
    - **Diz ao Docker:** "Os dados neste diretório são persistentes e não devem ser salvos na camada container (UnionFS)".
    - Se o usuário iniciar o contêiner sem especificar um volume (via ```-v```), o Docker cria automaticamente um volume anônimo para garantir que esses dados sobrevivam se o contêiner parar (embora seja difícil recuperá-los se o contêiner for removido sem cuidado).
    - Evita problemas de performance de I/O causados pelo UnionFS em diretórios de alta escrita (como bancos de dados).