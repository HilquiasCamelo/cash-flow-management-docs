# Arquitetura do Sistema de Gestão de Fluxo de Caixa

Este documento descreve a arquitetura do sistema de gestão de fluxo de caixa, incluindo os principais componentes, fluxos de dados e interações entre eles.

## Componentes do Sistema

1. **Autenticação**
    - **API de Autenticação (Gera Token)**: Responsável por gerar tokens para autenticação de usuários.
    - **Valida Token**: Valida os tokens gerados para garantir a segurança das requisições.

2. **Gateway API**
    - Gerencia as requisições e direciona para os serviços apropriados dentro do sistema.

3. **API - Lançamentos**
    - **Producer**: Publica eventos relacionados aos lançamentos financeiros.
    - **Kafka**: Utilizado para gerenciar a comunicação assíncrona entre serviços.
    - **Tópico: Lançamentos**: Canal para troca de mensagens sobre lançamentos.

4. **Fila de Erros**
    - Gerencia mensagens que não puderam ser processadas, permitindo um mecanismo de retry.

5. **Mecanismo de Retry**
    - Reprocessa mensagens falhas para garantir que todas as informações sejam tratadas.

6. **API - Consolidação**
    - **Consumer**: Consome eventos do Kafka e realiza a consolidação das informações financeiras.

7. **Banco de Dados (DB)**
    - Armazena todas as informações de lançamentos e consolidações.

8. **Prometheus e Grafana**
    - **Prometheus**: Monitora a saúde e disponibilidade do sistema.
    - **Grafana**: Visualiza dados de monitoramento e gera relatórios.

## Fluxo de Dados

1. O usuário faz uma requisição para a **API de Autenticação**, que gera um **Token**.
2. O **Token** é validado pela **Gateway API**.
3. A **API - Lançamentos** recebe a requisição e publica um evento no **Kafka**.
4. O evento é enviado para o **Tópico: Lançamentos**.
5. Se ocorrer um erro no processamento do evento, ele é enviado para a **Fila de Erros**.
6. O **Mecanismo de Retry** tenta processar novamente a mensagem.
7. Uma vez processado com sucesso, a mensagem é consumida pela **API - Consolidação**, que armazena os dados no **DB**.
8. O sistema é monitorado por **Prometheus**, com os dados visualizados no **Grafana**, permitindo alertas em caso de problemas.

## Conclusão

A arquitetura proposta proporciona uma gestão eficiente do fluxo de caixa, garantindo a segurança das informações, a disponibilidade dos serviços e um monitoramento contínuo do sistema.

