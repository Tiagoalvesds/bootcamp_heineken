## 📌 Descrição do Projeto

Este projeto apresenta a modelagem conceitual de um banco de dados para um Sistema de Controle e Gerenciamento de Ordens de Serviço (OS) em uma oficina mecânica.

O modelo foi desenvolvido do zero, com base exclusivamente na narrativa fornecida, aplicando princípios de modelagem de dados, normalização e regras de negócio reais, comuns em sistemas de gestão de oficinas automotivas.

Sempre que a narrativa não especificou detalhes técnicos ou estruturais, foram feitas decisões de modelagem justificadas, devidamente documentadas neste README para fins de avaliação.

## 🎯 Objetivo do Desafio

Criar um esquema conceitual completo que represente:

Clientes e seus veículos

Ordens de serviço (OS)

Equipes de mecânicos

Serviços executados

Peças utilizadas

Cálculo de valores com base em mão de obra e peças

## 📖 Entendimento da Narrativa

A partir da narrativa, entende-se que:

Clientes levam veículos à oficina

Cada veículo pode gerar uma ou mais Ordens de Serviço

Uma equipe de mecânicos avalia e executa os serviços

Os serviços possuem valores baseados em uma tabela de mão de obra

As peças utilizadas também compõem o valor final da OS

O cliente precisa autorizar a execução dos serviços

Cada OS possui controle de status e datas

## 📐 Regras de Negócio Implementadas

Um cliente pode possuir um ou mais veículos

Um veículo pode possuir várias ordens de serviço ao longo do tempo

Cada ordem de serviço é associada a uma única equipe de mecânicos

Uma equipe pode ser responsável por várias OS

Uma OS pode conter vários serviços e várias peças

O valor total da OS é composto por:

Soma dos valores dos serviços (mão de obra)

Soma dos valores das peças utilizadas

A OS só pode ser executada após autorização do cliente

## 🔗 Relacionamentos Principais

Cliente 1:N Veículo

Veículo 1:N Ordem de Serviço

Equipe 1:N Mecânico

Equipe 1:N Ordem de Serviço

Ordem de Serviço N:M Serviço

Ordem de Serviço N:M Peça

