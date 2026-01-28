# II - Projeto Lógico de Banco de Dados – Sistema de Oficina Mecânica

## Descrição do Projeto Lógico

Este projeto apresenta o modelo lógico e a implementação física de um banco de dados relacional para um Sistema de Controle e Gerenciamento de Ordens de Serviço (OS) em uma oficina mecânica.

O modelo lógico foi derivado diretamente do modelo conceitual (DER) criado no desafio anterior, respeitando rigorosamente os princípios do modelo relacional, normalização e integridade referencial.

Além da criação do esquema do banco de dados, o projeto contempla:

Persistência de dados para testes

Consultas SQL simples e avançadas

Uso de cláusulas SELECT, WHERE, ORDER BY, HAVING

Junções entre múltiplas tabelas

Criação de atributos derivados por meio de expressões SQL

## Mapeamento do Modelo Conceitual para o Modelo Relacional

Principais decisões de modelagem:

Relacionamentos 1:N foram implementados com chaves estrangeiras

Relacionamentos N:M foram resolvidos com tabelas associativas

Valores de serviços e peças foram armazenados na OS para preservar histórico

Campos de status utilizam domínio controlado via VARCHAR

## Esquema Lógico – Tabelas

Script SQL – Criação do Banco de Dados (DDL)
CREATE DATABASE oficina;
USE oficina;

-- CLIENTE
CREATE TABLE cliente (
    id_cliente INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    telefone VARCHAR(20),
    endereco VARCHAR(150)
);

-- VEICULO
CREATE TABLE veiculo (
    id_veiculo INT AUTO_INCREMENT PRIMARY KEY,
    placa VARCHAR(10) UNIQUE NOT NULL,
    modelo VARCHAR(50),
    marca VARCHAR(50),
    ano INT,
    id_cliente INT NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente)
);

-- EQUIPE
CREATE TABLE equipe (
    id_equipe INT AUTO_INCREMENT PRIMARY KEY,
    nome_equipe VARCHAR(50)
);

-- MECANICO
CREATE TABLE mecanico (
    id_mecanico INT AUTO_INCREMENT PRIMARY KEY,
    codigo VARCHAR(20),
    nome VARCHAR(100),
    endereco VARCHAR(150),
    especialidade VARCHAR(50),
    id_equipe INT,
    FOREIGN KEY (id_equipe) REFERENCES equipe(id_equipe)
);

-- SERVICO
CREATE TABLE servico (
    id_servico INT AUTO_INCREMENT PRIMARY KEY,
    descricao VARCHAR(100),
    valor_mao_obra DECIMAL(10,2)
);

-- PECA
CREATE TABLE peca (
    id_peca INT AUTO_INCREMENT PRIMARY KEY,
    descricao VARCHAR(100),
    valor_unitario DECIMAL(10,2)
);

-- ORDEM DE SERVICO
CREATE TABLE ordem_servico (
    id_os INT AUTO_INCREMENT PRIMARY KEY,
    data_emissao DATE,
    data_previsao_conclusao DATE,
    data_conclusao DATE,
    status_os VARCHAR(30),
    autorizado BOOLEAN,
    valor_total DECIMAL(10,2),
    id_veiculo INT,
    id_equipe INT,
    FOREIGN KEY (id_veiculo) REFERENCES veiculo(id_veiculo),
    FOREIGN KEY (id_equipe) REFERENCES equipe(id_equipe)
);

-- OS_SERVICO (N:M)
CREATE TABLE os_servico (
    id_os INT,
    id_servico INT,
    quantidade INT,
    valor_servico DECIMAL(10,2),
    PRIMARY KEY (id_os, id_servico),
    FOREIGN KEY (id_os) REFERENCES ordem_servico(id_os),
    FOREIGN KEY (id_servico) REFERENCES servico(id_servico)
);

-- OS_PECA (N:M)
CREATE TABLE os_peca (
    id_os INT,
    id_peca INT,
    quantidade INT,
    valor_peca DECIMAL(10,2),
    PRIMARY KEY (id_os, id_peca),
    FOREIGN KEY (id_os) REFERENCES ordem_servico(id_os),
    FOREIGN KEY (id_peca) REFERENCES peca(id_peca)
);

## Persistência de Dados (DML)

INSERT INTO cliente (nome, telefone, endereco)
VALUES ('João Silva', '62999999999', 'Rua A');

INSERT INTO veiculo (placa, modelo, marca, ano, id_cliente)
VALUES ('ABC1234', 'Civic', 'Honda', 2020, 1);

INSERT INTO equipe (nome_equipe)
VALUES ('Equipe Alpha');

INSERT INTO mecanico (codigo, nome, endereco, especialidade, id_equipe)
VALUES ('M001', 'Carlos Mecânico', 'Rua B', 'Motor', 1);

INSERT INTO servico (descricao, valor_mao_obra)
VALUES ('Troca de óleo', 150.00),
       ('Alinhamento', 120.00);

INSERT INTO peca (descricao, valor_unitario)
VALUES ('Filtro de óleo', 40.00),
       ('Óleo 5W30', 90.00);

INSERT INTO ordem_servico
(data_emissao, data_previsao_conclusao, status_os, autorizado, id_veiculo, id_equipe)
VALUES (CURDATE(), CURDATE() + INTERVAL 2 DAY, 'Em andamento', TRUE, 1, 1);

INSERT INTO os_servico VALUES (1, 1, 1, 150.00);
INSERT INTO os_peca VALUES (1, 1, 1, 40.00);

## Consultas SQL (Queries)

Pergunta 1: Quais são os clientes cadastrados?

SELECT * FROM cliente;

Pergunta 2: Quais OS estão autorizadas?

SELECT id_os, status_os
FROM ordem_servico
WHERE autorizado = TRUE;

Pergunta 3: Qual o valor total calculado da OS (atributo derivado)?

SELECT 
    os.id_os,
    SUM(s.valor_servico * s.quantidade) +
    SUM(p.valor_peca * p.quantidade) AS valor_calculado
FROM ordem_servico os
LEFT JOIN os_servico s ON os.id_os = s.id_os
LEFT JOIN os_peca p ON os.id_os = p.id_os
GROUP BY os.id_os;

Pergunta 4: Ordens de serviço ordenadas por data de emissão

SELECT id_os, data_emissao, status_os
FROM ordem_servico
ORDER BY data_emissao DESC;

Pergunta 5: Equipes com mais de uma OS (HAVING)

SELECT 
    id_equipe,
    COUNT(*) AS total_os
FROM ordem_servico
GROUP BY id_equipe
HAVING COUNT(*) > 1;

Pergunta 6: Detalhes completos da OS (JOIN complexo)

SELECT 
    c.nome AS cliente,
    v.placa,
    os.id_os,
    os.status_os,
    e.nome_equipe
FROM ordem_servico os
JOIN veiculo v ON os.id_veiculo = v.id_veiculo
JOIN cliente c ON v.id_cliente = c.id_cliente
JOIN equipe e ON os.id_equipe = e.id_equipe;

