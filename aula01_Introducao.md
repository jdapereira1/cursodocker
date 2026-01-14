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


## 3. Softwares Adicionais

### 3.1 Git

**Git e GitHub Desktop**
- Instalar o Git e GitHub Desktop no SO utilizado
- Configurar via terminal: 
  - git config --global user.name "xxxx"
  - git config --global user.email "xxxx@xxx"

### 3.2 Extensões do VsCode

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