# Projeto Lógico de Banco de Dados – E-commerce

## Descrição do Projeto

Este projeto apresenta a modelagem lógica e implementação física de um banco de dados relacional para um sistema de e-commerce, desenvolvido a partir de um modelo conceitual (ER/EER) previamente refinado.

O esquema lógico contempla especialização (EER), múltiplas formas de pagamento por cliente, controle de entregas e relacionamentos complexos entre clientes, pedidos, produtos, fornecedores e vendedores.

O objetivo é demonstrar domínio em:

Mapeamento EER → Relacional

Definição correta de PK, FK e constraints

Escrita de queries SQL simples e avançadas

Organização e documentação profissional para portfólio

## Regras de Negócio Implementadas

🔹 Cliente PF e PJ (Especialização – EER)

Um cliente pode ser Pessoa Física (PF) ou Pessoa Jurídica (PJ)

Um cliente não pode ser PF e PJ ao mesmo tempo

Dados comuns ficam na entidade cliente

Dados específicos ficam em cliente_pf e cliente_pj

🔹 Pagamento

Um cliente pode cadastrar mais de uma forma de pagamento

Cada pedido utiliza uma forma de pagamento cadastrada

🔹 Entrega

Cada pedido possui uma entrega

A entrega possui status e código de rastreio

##  Mapeamento EER → Modelo Relacional

Especialização PF/PJ → tabelas separadas com PK = FK

Relacionamentos N:M → tabelas associativas

Valores históricos preservados (ex.: preço do produto no pedido)


# DDL
CREATE DATABASE ecommerce;
USE ecommerce;

-- CLIENTE
CREATE TABLE cliente (
    id_cliente INT AUTO_INCREMENT PRIMARY KEY,
    tipo_cliente ENUM('PF','PJ') NOT NULL,
    email VARCHAR(100) UNIQUE,
    telefone VARCHAR(20)
);

CREATE TABLE cliente_pf (
    id_cliente INT PRIMARY KEY,
    nome VARCHAR(100),
    cpf VARCHAR(14) UNIQUE,
    FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente)
);

CREATE TABLE cliente_pj (
    id_cliente INT PRIMARY KEY,
    razao_social VARCHAR(100),
    cnpj VARCHAR(18) UNIQUE,
    FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente)
);

-- FORNECEDOR
CREATE TABLE fornecedor (
    id_fornecedor INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100)
);

-- VENDEDOR
CREATE TABLE vendedor (
    id_vendedor INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100)
);

-- PRODUTO
CREATE TABLE produto (
    id_produto INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100),
    preco DECIMAL(10,2),
    estoque INT
);

-- PRODUTO_FORNECEDOR (N:M)
CREATE TABLE produto_fornecedor (
    id_produto INT,
    id_fornecedor INT,
    PRIMARY KEY (id_produto, id_fornecedor),
    FOREIGN KEY (id_produto) REFERENCES produto(id_produto),
    FOREIGN KEY (id_fornecedor) REFERENCES fornecedor(id_fornecedor)
);

-- PEDIDO
CREATE TABLE pedido (
    id_pedido INT AUTO_INCREMENT PRIMARY KEY,
    data_pedido DATE,
    status_pedido VARCHAR(30),
    id_cliente INT,
    id_vendedor INT,
    FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente),
    FOREIGN KEY (id_vendedor) REFERENCES vendedor(id_vendedor)
);

-- ITEM_PEDIDO
CREATE TABLE item_pedido (
    id_pedido INT,
    id_produto INT,
    quantidade INT,
    preco_unitario DECIMAL(10,2),
    PRIMARY KEY (id_pedido, id_produto),
    FOREIGN KEY (id_pedido) REFERENCES pedido(id_pedido),
    FOREIGN KEY (id_produto) REFERENCES produto(id_produto)
);

-- PAGAMENTO
CREATE TABLE pagamento (
    id_pagamento INT AUTO_INCREMENT PRIMARY KEY,
    id_cliente INT,
    tipo_pagamento VARCHAR(30),
    FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente)
);

-- ENTREGA
CREATE TABLE entrega (
    id_entrega INT AUTO_INCREMENT PRIMARY KEY,
    id_pedido INT,
    status_entrega VARCHAR(30),
    codigo_rastreio VARCHAR(50),
    FOREIGN KEY (id_pedido) REFERENCES pedido(id_pedido)
);


# DML

INSERT INTO cliente (tipo_cliente, email, telefone)
VALUES ('PF','joao@email.com','62999999999');

INSERT INTO cliente_pf VALUES (1,'João Silva','123.456.789-00');

INSERT INTO fornecedor (nome) VALUES ('Fornecedor A');
INSERT INTO vendedor (nome) VALUES ('Vendedor X');

INSERT INTO produto (nome, preco, estoque)
VALUES ('Notebook', 3500.00, 10);

INSERT INTO produto_fornecedor VALUES (1,1);

INSERT INTO pedido (data_pedido, status_pedido, id_cliente, id_vendedor)
VALUES (CURDATE(),'Finalizado',1,1);

INSERT INTO item_pedido VALUES (1,1,1,3500.00);

INSERT INTO pagamento (id_cliente, tipo_pagamento)
VALUES (1,'Cartão de Crédito');

INSERT INTO entrega (id_pedido, status_entrega, codigo_rastreio)
VALUES (1,'Enviado','BR123456789');


## Respondendo às Perguntas do Desafio

1º) Quantos pedidos foram feitos por cada cliente?

SELECT 
    c.id_cliente,
    COUNT(p.id_pedido) AS total_pedidos
FROM cliente c
LEFT JOIN pedido p ON c.id_cliente = p.id_cliente
GROUP BY c.id_cliente;

2º) Algum vendedor também é fornecedor?

SELECT v.nome
FROM vendedor v
JOIN fornecedor f ON v.nome = f.nome;

3°) Relação de produtos, fornecedores e estoques?

SELECT 
    p.nome AS produto,
    f.nome AS fornecedor,
    p.estoque
FROM produto p
JOIN produto_fornecedor pf ON p.id_produto = pf.id_produto
JOIN fornecedor f ON pf.id_fornecedor = f.id_fornecedor;

4º) Relação de nomes dos fornecedores e nomes dos produtos?

SELECT 
    f.nome AS fornecedor,
    p.nome AS produto
FROM fornecedor f
JOIN produto_fornecedor pf ON f.id_fornecedor = pf.id_fornecedor
JOIN produto p ON pf.id_produto = p.id_produto
ORDER BY f.nome;

5º) Valor total de cada pedido (atributo derivado) ?

SELECT 
    p.id_pedido,
    SUM(i.quantidade * i.preco_unitario) AS valor_total
FROM pedido p
JOIN item_pedido i ON p.id_pedido = i.id_pedido
GROUP BY p.id_pedido
HAVING valor_total > 1000;

