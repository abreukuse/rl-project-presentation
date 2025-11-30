# rl-project-presentation

Este repositório contém a apresentação do projeto de mestrado sobre a aplicação de Aprendizado por Reforço Multiagente (MARL) para otimizar o patrulhamento preventivo e o atendimento de chamadas policiais em ambientes urbanos.

## Sobre o Projeto

O trabalho propõe um modelo baseado em MARL, utilizando a arquitetura Dueling Deep Q-Network (Dueling DQN), integrado a um simulador de eventos discretos. O objetivo é equilibrar a cobertura de hotspots (prevenção) com a minimização do tempo de resposta a ocorrências (reação), validado com dados reais da Brigada Militar de Porto Alegre.

## Visualizar a Apresentação

A apresentação está no formato Marp Markdown (`presentation.md`). Para visualizá-la:

1.  Certifique-se de ter o Marp CLI instalado (`npm install -g @marp-team/marp-cli`).
2.  Execute o comando para iniciar o modo de visualização:
    ```bash
    marp --preview presentation.md
    ```
3.  Para exportar a apresentação para HTML, PDF ou outros formatos:
    ```bash
    marp presentation.md -o presentation.html
    ```

## Autores

- Moacir Almeida Simões Júnior
- Tobias de Abreu Kuse
