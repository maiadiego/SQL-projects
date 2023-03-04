## Estudo de caso: Analisando o inventário de uma loja de roupas eCommerce
Em primeiro momento, vamos criar duas bases de dados que iremos trabalhar: **products** e **inventory**. A seguir, iremos preenchelas com os devidos valores.
A base **products** possui três colunas que irão representar o identificador, nome, categoria e preço de um produto da loja. 
```sql
CREATE TABLE products (
product_id SERIAL PRIMARY KEY,
product_name VARCHAR(50),
product_category VARCHAR(20),
product_price NUMERIC(10,2)
);
```
Agora vamos preencher com alguns nomes de produtos 
```sql
INSERT INTO products (product_name, product_category, product_price)
VALUES ('Moletom', 'Agasalhos', 19.99),
('Regata', 'Roupas de verao', 29.99),
('Camisa Manga Comprida', 'Agasalhos', 39.99),
('Kit Cuecas', 'Roupas intimas', 49.99),
('Short de praia', 'Roupas de verao', 59.99);
```
![Tabela products](https://github.com/maiadiego/SQL-projects/blob/master/imagens/tabela_produto.png)

Seguindo os mesmos passos para gerar a tabela **inventory**
```sql
CREATE TABLE inventory (
  product_id INT,
  inventory_date DATE,
  inventory_level INT
);
```
```sql 
INSERT INTO inventory (product_id, inventory_date, inventory_level)
VALUES (1, '2022-01-01', 100),
       (2, '2022-01-01', 200),
       (3, '2022-01-01', 150),
       (4, '2022-01-01', 75),
       (5, '2022-01-01', 250),
       (1, '2022-01-02', 80),
       (2, '2022-01-02', 180),
       (3, '2022-01-02', 100),
       (4, '2022-01-02', 60),
       (5, '2022-01-02', 220),
       (1, '2022-01-03', 50),
       (2, '2022-01-03', 150),
       (3, '2022-01-03', 75),
       (4, '2022-01-03', 80),
       (5, '2022-01-03', 200);
```

![Tabela inventory](https://github.com/maiadiego/SQL-projects/blob/master/imagens/tabela_inventory.png)

Tendo sido criadas as devidas tabelas, vamos começar a fazer algumas perguntas para os nossos dados com foco na análise do inventário da loja.
### 1. Quais são os cinco principais produtos com as quantidades de estoque mais altas na data de estoque mais recente?
```sql
SELECT p.product_name, i.inventory_level
FROM products p
JOIN inventory i ON p.product_id = i.product_id
WHERE i.inventory_date = (SELECT MAX(inventory_date) FROM inventory)
ORDER BY i.inventory_level DESC
LIMIT 5; 
```
![5 products](https://github.com/maiadiego/SQL-projects/blob/master/imagens/5-products-high-inventory.png)

### 2. Qual é a quantidade de estoque total para cada categoria de produto na data de estoque mais recente?
```sql
SELECT p.product_category, SUM(i.inventory_level) AS total_inventory_level
FROM products p
JOIN inventory i ON p.product_id = i.product_id
WHERE i.inventory_date = (SELECT MAX(inventory_date) FROM inventory)
GROUP BY p.product_category;
```
![](https://github.com/maiadiego/SQL-projects/blob/master/imagens/total_inventory_level.png)

### 3. Qual é a quantidade média de estoque para cada categoria de produto no mês de Janeiro de 2022?
```sql
SELECT p.product_category, AVG(i.inventory_level) AS avg_inventory_level
FROM products p
JOIN inventory i ON p.product_id = i.product_id
WHERE i.inventory_date >= '2022-01-01' AND i.inventory_date < '2022-02-01'
GROUP BY p.product_category;
```
![](https://github.com/maiadiego/SQL-projects/blob/master/imagens/avg_inventory_level.png)

### 4. Qual é a tendência geral nas quantidades de estoque para cada categoria de produto no mês de Janeiro de 2022?
```sql 
SELECT p.product_category, i.inventory_date, AVG(i.inventory_level) AS avg_inventory_level
FROM products p
JOIN inventory i ON p.product_id = i.product_id
WHERE i.inventory_date >= '2022-01-01' AND i.inventory_date < '2022-02-01'
GROUP BY p.product_category, i.inventory_date
ORDER BY p.product_category, i.inventory_date;
```
![](https://github.com/maiadiego/SQL-projects/blob/master/imagens/avg_inventory_level_tendency.png)



