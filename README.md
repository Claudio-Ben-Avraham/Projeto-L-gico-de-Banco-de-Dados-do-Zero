# Projeto de Banco de Dados para Oficina

Este projeto tem como objetivo a modelagem e implementação de um banco de dados para o contexto de uma oficina mecânica. O sistema gerencia informações sobre clientes, veículos, serviços prestados, pagamentos e funcionários da oficina. A modelagem do banco de dados segue o modelo relacional e está dividida em várias entidades, como clientes, veículos, serviços, funcionários, pagamentos e ordens de serviço.

## Descrição do Projeto Lógico

A estrutura do banco de dados foi projetada para controlar as operações de uma oficina mecânica, incluindo as seguintes principais entidades:

### Tabelas:

1. **Cliente**: Contém informações dos clientes da oficina.
2. **Veículo**: Registra os veículos que pertencem aos clientes.
3. **Funcionário**: Armazena informações dos funcionários da oficina.
4. **Serviço**: Descreve os tipos de serviços prestados pela oficina.
5. **Ordem de Serviço (OS)**: Registra as ordens de serviço abertas para clientes, especificando os serviços a serem realizados em veículos.
6. **Pagamento**: Armazena informações sobre os pagamentos realizados pelos clientes pelas ordens de serviço.

### Relacionamentos:

- **Cliente** pode ter múltiplos **Veículos**.
- **Ordem de Serviço** é associada a um **Cliente** e a um ou mais **Serviços**.
- **Pagamento** está relacionado a uma **Ordem de Serviço**.
- **Funcionário** pode realizar **Serviços** nas **Ordens de Serviço**.

### Refinamentos no Modelo:

- Um **Cliente** pode ter múltiplos **Veículos** associados.
- A **Ordem de Serviço** pode conter múltiplos **Serviços** realizados.
- **Pagamento** pode ser feito de diversas formas, sendo possível registrar múltiplos pagamentos por **Ordem de Serviço**.

## Estrutura do Banco de Dados

![Projeto-do-Zero](https://github.com/user-attachments/assets/664e8070-5053-4f83-8607-ad744c6e2996)

# Consultas SQL

## 1. Quantos veículos cada cliente possui?

SELECT cliente_id, COUNT(veiculo_id) AS total_veiculos
FROM Veiculo
GROUP BY cliente_id;

## 2. Lista de funcionários e os serviços que realizaram em ordens de serviço.

SELECT f.nome AS funcionario, s.descricao AS servico, COUNT(os.ordem_id) AS total_servicos
FROM Funcionario f
JOIN OrdemServico_Servico oss ON f.funcionario_id = oss.funcionario_id
JOIN Servico s ON oss.servico_id = s.servico_id
JOIN OrdemServico os ON oss.ordem_id = os.ordem_id
GROUP BY f.nome, s.descricao;

## 3. Relatório de pagamentos realizados para ordens de serviço finalizadas.

SELECT o.ordem_id, p.valor, p.data_pagamento, p.forma_pagamento
FROM Pagamento p
JOIN OrdemServico o ON p.ordem_id = o.ordem_id
WHERE o.status = 'Finalizado'
ORDER BY p.data_pagamento DESC;

## 4. Listagem de ordens de serviço com mais de um serviço.

SELECT os.ordem_id, COUNT(oss.servico_id) AS total_servicos
FROM OrdemServico os
JOIN OrdemServico_Servico oss ON os.ordem_id = oss.ordem_id
GROUP BY os.ordem_id
HAVING COUNT(oss.servico_id) > 1;

## 5. Total de faturamento por serviço prestado.

SELECT s.descricao, SUM(p.valor) AS total_faturado
FROM Servico s
JOIN OrdemServico_Servico oss ON s.servico_id = oss.servico_id
JOIN Pagamento p ON oss.ordem_id = p.ordem_id
GROUP BY s.descricao
ORDER BY total_faturado DESC;

## 6. Clientes com mais de uma ordem de serviço finalizada.

SELECT cliente_id, COUNT(ordem_id) AS total_ordens
FROM OrdemServico
WHERE status = 'Finalizado'
GROUP BY cliente_id
HAVING COUNT(ordem_id) > 1;
