# Bootcamp_Heineken
## Refinando um Projeto Conceitual de Banco de Dados – E-COMMERCE


Modelo Conceitual de Banco de Dados – Sistema de E-commerce

📌 Descrição do Projeto

Este projeto apresenta a modelagem conceitual de um banco de dados para um sistema de e-commerce, desenvolvida com foco em boas práticas de modelagem, integridade dos dados e regras de negócio reais aplicadas em sistemas corporativos.

O modelo foi refinado a partir de um esquema inicial, incorporando diferenciação entre clientes Pessoa Física (PF) e Pessoa Jurídica (PJ), suporte a múltiplas formas de pagamento por cliente e controle completo do processo de entrega, incluindo status e código de rastreio.

O objetivo é demonstrar domínio conceitual em modelagem de dados, servindo como base para futuras implementações lógicas e físicas em SGBDs relacionais.

🎯 Objetivo do Desafio

Refinar o modelo conceitual de banco de dados adicionando as seguintes regras de negócio:

Diferenciação entre clientes Pessoa Física e Pessoa Jurídica

Cadastro de múltiplas formas de pagamento por cliente

Controle de entregas com status e código de rastreamento

📐 Regras de Negócio Implementadas

🔹 Cliente PF e PJ

Um cliente pode ser Pessoa Física (PF) ou Pessoa Jurídica (PJ).

Um cliente não pode ser PF e PJ simultaneamente.

Os dados comuns são centralizados na entidade Cliente, enquanto os dados específicos são armazenados em entidades especializadas.

🔹 Formas de Pagamento

Um cliente pode cadastrar mais de uma forma de pagamento.

As formas de pagamento ficam associadas ao cliente, independentemente do pedido.

Cada pedido utiliza uma forma de pagamento previamente cadastrada.

🔹 Entrega

Cada pedido possui uma entrega associada.

A entrega contém:

Status do envio

Código de rastreio

Datas relevantes do processo logístico

🧱 Entidades do Modelo

🔸 Cliente

id_cliente (PK)

tipo_cliente (PF | PJ)

email

telefone

data_cadastro

🔸 Cliente_PF

id_cliente (PK / FK)

cpf

nome

data_nascimento

🔸 Cliente_PJ

id_cliente (PK / FK)

cnpj

razao_social

nome_fantasia

🔸 Pagamento

id_pagamento (PK)

id_cliente (FK)

tipo_pagamento (Cartão, PIX, Boleto, etc.)

descricao

ativo

🔸 Pedido

id_pedido (PK)

id_cliente (FK)

data_pedido

status_pedido

valor_total

🔸 Entrega

id_entrega (PK)

id_pedido (FK)

status_entrega

codigo_rastreio

data_envio

data_entrega

🔗 Relacionamentos

Cliente 1:1 Cliente_PF ou Cliente_PJ

Cliente 1:N Pagamento

Cliente 1:N Pedido

Pedido 1:1 Entrega

🛠 Ferramentas Utilizadas

Modelagem conceitual desenvolvida com:

brModelo / MySQL Workbench (ou ferramenta equivalente)

draw.io
