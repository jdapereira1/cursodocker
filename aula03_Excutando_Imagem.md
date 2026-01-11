# Aula 3 - Executando a imagem

## Introdução: Docker Desktop e o Ciclo de Vida da Imagem

Na prática o grande benefício do Docker é **a capacidade de rodar uma aplicação sem ter suas dependências instaladas na máquina hospedeira**.

No Docker existem dois conceitos fundamentais:
- **Imagem:** O modelo estático da aplicação com todas as suas dependências e 
- **Contêiner:** A instância em execução dessa imagem.

Ele mostra como usar tanto a interface gráfica do Docker Desktop quanto a linha de comando para gerenciar imagens e contêineres, ressaltando que, como administrador, é essencial dominar a linha de comando.

O ponto principal da aula é a portabilidade que o Docker oferece. Ao empacotar a aplicação em um contêiner, você garante que ela funcionará da mesma forma em qualquer ambiente, eliminando problemas de compatibilidade e simplificando o processo de desenvolvimento e implantação