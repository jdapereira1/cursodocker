# Aula 6 - Comando COPY

## 1. Introdução

O comando ```COPY`` é a ferramenta que utilizda para transferir arquivos da máquina local para dentro da imagem Docker que está sendo construída. É possível utilizar o comando de diferentes formas, desde copiar um único arquivo até usar caracteres curinga para copiar múltiplos arquivos de uma vez.

---

## 2. Conceitos Essenciais e Comandos

### 2.1 Sintaxe do Comando ```COPY```

- **Sintaxe Básica:** ```COPY <origem> <destino>```

    - ```<origem>```: O caminho do arquivo ou diretório na sua máquina local (no contexto de construção).
    - ```<destino>```: O caminho dentro da imagem Docker para onde os arquivos serão copiados.

### 2.2 Comando ```WORKDIR```

O comando ```WORKDIR``` é um companheiro muito útil para o ```COPY```. Ele define o diretório de trabalho dentro da imagem, tornando os caminhos relativos mais fáceis de gerenciar.

**Dockerfile**

```Dockerfile
# Define o diretório de trabalho dentro da imagem
WORKDIR /app

# Copia os arquivos do diretório local para o diretório de trabalho na imagem
COPY . .
```

### 2.3 Comando Essenciais do Docker CLI

- ```docker build -t <nome_da_imagem> .```: Cria uma imagem Docker a partir de um Dockerfile. O ```.``` no final indica que o contexto de construção é o diretório atual.
- ```docker rmi <id_da_imagem>```: Remove uma imagem do seu sistema. Isso é importante para liberar espaço em disco quando você não precisa mais de uma imagem.
- ```docker rm -f <id_do_container>```: Remove um contêiner, mesmo que ele esteja em execução (```-f```).

**COPY vs. ADD**

_Regra geral_: usar ```COPY``` para copiar arquivos locais e ```ADD``` apenas quando precisar baixar um arquivo de uma URL ou descompactar um arquivo (.tar, .zip, etc.) automaticamente.

---

## 3. Tópicos Avançados

### 3.1. ```COPY``` vs. ```ADD```: Por que a preferência?

Embora ambos os comandos copiem arquivos do _host_ para o sistema de arquivos do contêiner, o ```COPY``` é preferível em 95% dos casos devido à transparência e previsibilidade.
- ```COPY```**(Explícito):** Faz apenas uma coisa: copia arquivos ou diretórios locais para o contêiner. O que você vê é o que você obtém.
- ```ADD```**(Implícito/"Mágico"):** Possui funcionalidades extras que podem causar efeitos colaterais indesejados:
    1. **Extração Automática:** Se a fonte for um arquivo compactado local (tar, gzip, bzip2), o ```ADD``` irá descompactá-lo automaticamente no destino. Se você quisesse apenas copiar o arquivo ```.tar.gz``` sem extrair, o ```ADD``` falharia nessa intenção.
    2. **URLs Remotas:** O ```ADD``` permite baixar arquivos diretamente de uma URL. Embora pareça útil, isso cria camadas extras na imagem e dificulta a limpeza do arquivo baixado (ele persiste na camada anterior).

**Veredito Técnico:** Use ```ADD``` apenas se você tiver a intenção explícita de extrair um tarball local automaticamente. Para downloads remotos, prefira usar ```RUN curl/wget``` (pois permite baixar e limpar o cache na mesma camada). Para todo o resto, use ```COPY```.

### 3.2 Otimização com ```.dockerignore```

O arquivo ```.dockerignore``` não serve apenas para "organização"; ele é fundamental para a performance do Build Context.

O Processo Técnico: Quando você executa ```docker build .```, a primeira coisa que o cliente Docker (CLI) faz é empacotar todo o diretório atual e enviá-lo para o Docker Daemon (o servidor). Esse pacote é chamado de Build Context.

Se você não tiver um ```.dockerignore```, pastas pesadas como ```.git```, ```node_modules``` (JavaScript), ```venv``` (Python) ou arquivos binários de compilação locais (C++) são enviados ao daemon antes mesmo do Dockerfile ser lido.

**Ganhos ao usar** ```.dockerignore```**:**

1. **Tempo de Build:** Reduz drasticamente o tempo de upload do contexto para o daemon.
2. **Tamanho da Imagem:** Evita que arquivos desnecessários sejam copiados acidentalmente via ```COPY . .```.
3. **Segurança:** Previne que arquivos sensíveis (como ```.env```, chaves privadas ou credenciais AWS locais) sejam "assados" dentro da imagem final.

### 3.3 Camadas (Layers) e a Eficiência do COPY

O Docker utiliza um sistema de arquivos UnionFS. Cada instrução no Dockerfile (```RUN```, ```COPY```, ```ADD```) cria uma nova camada delta (apenas a diferença em relação à anterior). Essas camadas são somente leitura e empilhadas.

**O Mecanismo de Cache:** O Docker verifica se a instrução atual já foi executada anteriormente com o mesmo conteúdo. Se sim, ele usa a camada em cache (instantâneo). Se não, ele invalida o cache daquela camada e de todas as subsequentes.

**A Estratégia de Ordenação do ```COPY```:** Para otimizar o tempo de _build_, devemos ordenar as instruções da menos frequente para a mais frequente em termos de mudança.

- **Dependências (Mudam pouco):** Bibliotecas e pacotes.
- **Código Fonte (Muda muito):** A lógica da aplicação editada diariamente.

**Exemplo Prático (Python):**

_Ineficiente_:

Dockerfile:
```Dockerfile
COPY . .            # Copia tudo (código + requirements). Se o código mudar, invalida o cache aqui.
RUN pip install -r requirements.txt # O cache foi invalidado acima, então o pip roda de novo (LENTO!)
```

_Eficiente_:

Dockerfile:
```Dockerfile
COPY requirements.txt .  # Copia APENAS a definição de dependências.
RUN pip install -r requirements.txt # Só roda de novo se requirements.txt mudar.
COPY . .                 # Copia o código. Se mudar, só recria esta camada final (RÁPIDO).
```

### 3.4. Otimização de Imagens (Tamanho e Segurança)

Para criar imagens de nível de produção, focamos em reduzir a superfície de ataque e o consumo de armazenamento/banda.

**A. Redução de Tamanho**

1. **_Multi-stage Builds_ (Crucial para C++ e Go):** Permite usar uma imagem pesada com compiladores (GCC, Maven) para construir o artefato e copiar apenas o binário resultante para uma imagem final minúscula.
2. **Imagens Base Leves:** Prefira ```alpine``` (super leve, ~5MB) ou ```distroless``` (Google) em vez de imagens baseadas em Ubuntu/Debian completas, a menos que haja dependências estritas de glibc.
3. **Limpeza na Mesma Camada:** Ao usar RUN, instale, use e limpe na mesma instrução para evitar que arquivos temporários fiquem persistidos em camadas ocultas.
    - Ex: ```RUN apt update && apt install -y pacote && rm -rf /var/lib/apt/lists/*```

**B. Melhoria de Segurança**

1. **Usuário Não-Root:** Por padrão, contêineres rodam como root. Crie um usuário e mude para ele.
    Dockerfile
    ```Dockerfile
    RUN useradd -m appuser
    USER appuser
    ```
2. **Imutabilidade:** Garanta que o sistema de arquivos seja somente leitura onde possível ou use o ```COPY --chown``` para restringir permissões.

3. **Atualizações de Segurança:** Mesmo em imagens estáveis, execute atualizações de segurança do SO base durante o _build_ (se a política da empresa permitir _builds_ não-determinísticos) ou use _scanners_ de imagem (como Docker Scout ou Trivy) no pipeline de CI/CD.