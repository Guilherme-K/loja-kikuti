Neste projeto foi criado uma simulação de banco de dados na plataforma online DB fiddle, 
simulando uma loja de pesca inspirada na empresa kikutispotfishing que é a loja real.
O projeto possui produtos, fornecedores, marcas, funcionarios, clientes e vendas(podendo mostrar os itens da mesma).
Temos 6 consultas utilizando  os conceitos dos Joins, foi utilizado tambem o update e o alter table, para modificar e adicionar 
informações.


Script:
-- criar DB
CREATE DATABASE loja_pesca_kikuti;
USE loja_pesca_kikuti;

-- tabela de clientes
CREATE TABLE clientes (
    id_cliente INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    telefone VARCHAR(20),
    email VARCHAR(100)
);

-- tabela de categorias
CREATE TABLE categorias (
    id_categoria INT AUTO_INCREMENT PRIMARY KEY,
    nome_categoria VARCHAR(50) NOT NULL
);

-- tabela de produtos
CREATE TABLE produtos (
    id_produto INT AUTO_INCREMENT PRIMARY KEY,
    nome_produto VARCHAR(100) NOT NULL,
    preco DECIMAL(10,2) NOT NULL,
    estoque INT NOT NULL,
    id_categoria INT,
    FOREIGN KEY (id_categoria) REFERENCES categorias(id_categoria)
);

-- tabela de vendas
CREATE TABLE vendas (
    id_venda INT AUTO_INCREMENT PRIMARY KEY,
    data_venda DATE NOT NULL,
    id_cliente INT,
    FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente)
);

-- tabela de itens da venba
CREATE TABLE itens_venda (
    id_item INT AUTO_INCREMENT PRIMARY KEY,
    id_venda INT,
    id_produto INT,
    quantidade INT NOT NULL,
    valor_unitario DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (id_venda) REFERENCES vendas(id_venda),
    FOREIGN KEY (id_produto) REFERENCES produtos(id_produto)
);
-- tabela de fornecedores
CREATE TABLE fornecedores (
    id_fornecedor INT AUTO_INCREMENT PRIMARY KEY,
    nome_fornecedor VARCHAR(100) NOT NULL,
    telefone VARCHAR(20),
    email VARCHAR(100)
);
-- tabela de marcacs dos produtos
CREATE TABLE marcas (
    id_marca INT AUTO_INCREMENT PRIMARY KEY,
    nome_marca VARCHAR(50) NOT NULL
);
-- tabela de funcionarios da empresa
CREATE TABLE funcionarios (
    id_funcionario INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    cargo VARCHAR(50),
    salario DECIMAL(10,2)
);



-- categorias
INSERT INTO categorias (nome_categoria) VALUES
('Varas de Pesca'),
('Carretilhas'),
('Iscas Artificiais'),
('Acessórios');

-- clientes
INSERT INTO clientes (nome, telefone, email) VALUES
('João Silva', '11999999999', 'joao@email.com'),
('Maria Souza', '11888888888', 'maria@email.com'),
('Carlos Lima', '11777777777', 'carlos@email.com');

-- produtos
INSERT INTO produtos (nome_produto, preco, estoque, id_categoria) VALUES
('Vara Carbon X', 250.00, 10, 1),
('Carretilha ProFish', 380.00, 5, 2),
('Isca Artificial Tucuna', 35.00, 30, 3),
('Caixa de Pesca', 120.00, 15, 4);

-- vendas
INSERT INTO vendas (data_venda, id_cliente) VALUES
('2026-06-20', 1),
('2026-06-21', 2);

-- itens da venda
INSERT INTO itens_venda (id_venda, id_produto, quantidade, valor_unitario) VALUES
(1, 1, 1, 250.00),
(1, 3, 2, 35.00),
(2, 2, 1, 380.00),
(2, 4, 1, 120.00);
-- fornecedores 
INSERT INTO fornecedores (nome_fornecedor, telefone, email) VALUES
('Pesca Brasil Distribuidora', '11911111111', 'contato@pescabrasil.com'),
('Mundo da Pesca', '11922222222', 'vendas@mundodapesca.com'),
('Fishing Supply', '11933333333', 'comercial@fishingsupply.com');
-- marcas
INSERT INTO marcas (nome_marca) VALUES
('Shimano'),
('Daiwa'),
('Marine Sports');
-- funcionarios
INSERT INTO funcionarios (nome, cargo, salario) VALUES
('Andreia Kikuti', 'Gerente', 4500.00),
('Ronaldo Kikuti', 'Vendedor', 2800.00),
('Guilherme Kikuti', 'Caixa', 2500.00);

-- adicionado id fornecedores
ALTER TABLE produtos
ADD COLUMN id_fornecedor INT,
ADD CONSTRAINT fk_produto_fornecedor
FOREIGN KEY (id_fornecedor)
REFERENCES fornecedores(id_fornecedor);
-- adiciona id marcas conectando com produto
ALTER TABLE produtos
ADD COLUMN id_marca INT,
ADD CONSTRAINT fk_produto_marca
FOREIGN KEY (id_marca)
REFERENCES marcas(id_marca);
-- adiciona id funcionarios relacionando na venda
ALTER TABLE vendas
ADD COLUMN id_funcionario INT,
ADD CONSTRAINT fk_venda_funcionario
FOREIGN KEY (id_funcionario)
REFERENCES funcionarios(id_funcionario);
-- altera o id fornecedor e marca em relação ao produto
UPDATE produtos
SET id_fornecedor = 1, id_marca = 1
WHERE id_produto = 1;
UPDATE produtos
SET id_fornecedor = 2, id_marca = 2
WHERE id_produto = 2;
UPDATE produtos
SET id_fornecedor = 3, id_marca = 3
WHERE id_produto = 3;
UPDATE produtos
SET id_fornecedor = 1, id_marca = 3
WHERE id_produto = 4;

-- funcionario em relação a venda
UPDATE vendas
SET id_funcionario = 2
WHERE id_venda = 1;

UPDATE vendas
SET id_funcionario = 3
WHERE id_venda = 2;

-- compra do cliente 
SELECT
    v.id_venda,
    c.nome AS cliente,
    v.data_venda
FROM vendas v
INNER JOIN clientes c
ON v.id_cliente = c.id_cliente;
-- categoria produtos
SELECT
    p.nome_produto,
    p.preco,
    c.nome_categoria
FROM produtos p
INNER JOIN categorias c
ON p.id_categoria = c.id_categoria;

-- compra do cliente(completo)
SELECT
    v.id_venda,
    cli.nome AS cliente,
    prod.nome_produto,
    iv.quantidade,
    iv.valor_unitario,
    (iv.quantidade * iv.valor_unitario) AS total_item
FROM vendas v
INNER JOIN clientes cli
    ON v.id_cliente = cli.id_cliente
INNER JOIN itens_venda iv
    ON v.id_venda = iv.id_venda
INNER JOIN produtos prod
    ON iv.id_produto = prod.id_produto;
    
-- mostra todos os clientes cadastrados    
SELECT
    c.nome,
    v.id_venda
FROM clientes c
LEFT JOIN vendas v
ON c.id_cliente = v.id_cliente;

-- mostra produtos e todos as suas informações
SELECT
    p.nome_produto,
    c.nome_categoria,
    m.nome_marca,
    f.nome_fornecedor,
    p.preco
FROM produtos p
INNER JOIN categorias c
    ON p.id_categoria = c.id_categoria
INNER JOIN marcas m
    ON p.id_marca = m.id_marca
INNER JOIN fornecedores f
    ON p.id_fornecedor = f.id_fornecedor;

-- mostra quem efetuou a venda, para quem e quando
SELECT
    v.id_venda,
    c.nome AS cliente,
    f.nome AS funcionario,
    v.data_venda
FROM vendas v
INNER JOIN clientes c
    ON v.id_cliente = c.id_cliente
INNER JOIN funcionarios f
    ON v.id_funcionario = f.id_funcionario;
-- mostra as vendas dos funcionarios  
SELECT
    f.nome,
    COUNT(v.id_venda) AS total_vendas
FROM funcionarios f
LEFT JOIN vendas v
    ON f.id_funcionario = v.id_funcionario
GROUP BY f.id_funcionario, f.nome;
-- mostra marca do produto e preco
SELECT
    p.nome_produto,
    m.nome_marca,
    p.preco
FROM produtos p
INNER JOIN marcas m
    ON p.id_marca = m.id_marca;
    
-- junta 7 tabelas
SELECT
    v.id_venda,
    cli.nome AS cliente,
    func.nome AS funcionario,
    prod.nome_produto,
    mar.nome_marca,
    forn.nome_fornecedor,
    iv.quantidade,
    (iv.quantidade * iv.valor_unitario) AS total_item
FROM vendas v
INNER JOIN clientes cli
    ON v.id_cliente = cli.id_cliente
INNER JOIN funcionarios func
    ON v.id_funcionario = func.id_funcionario
INNER JOIN itens_venda iv
    ON v.id_venda = iv.id_venda
INNER JOIN produtos prod
    ON iv.id_produto = prod.id_produto
INNER JOIN marcas mar
    ON prod.id_marca = mar.id_marca
INNER JOIN fornecedores forn
    ON prod.id_fornecedor = forn.id_fornecedor;