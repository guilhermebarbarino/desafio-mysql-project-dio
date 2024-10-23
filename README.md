# desafio-mysql-project-dio

---

## **Script SQL – Criação do Esquema de Banco de Dados**

```sql
-- Criação das Tabelas
CREATE TABLE Cliente (
    idCliente INT PRIMARY KEY,
    tipoCliente ENUM('PJ', 'PF') NOT NULL,
    nome VARCHAR(100) NOT NULL
);

CREATE TABLE Fornecedor (
    idFornecedor INT PRIMARY KEY,
    nomeFornecedor VARCHAR(100) NOT NULL
);

CREATE TABLE Produto (
    idProduto INT PRIMARY KEY,
    nomeProduto VARCHAR(100) NOT NULL,
    preco DECIMAL(10, 2) NOT NULL
);

CREATE TABLE Estoque (
    idEstoque INT PRIMARY KEY,
    local VARCHAR(45) NOT NULL
);

CREATE TABLE EmEstoque (
    Produto_idProduto INT,
    Estoque_idEstoque INT,
    quantidade INT NOT NULL,
    PRIMARY KEY (Produto_idProduto, Estoque_idEstoque),
    FOREIGN KEY (Produto_idProduto) REFERENCES Produto(idProduto),
    FOREIGN KEY (Estoque_idEstoque) REFERENCES Estoque(idEstoque)
);

CREATE TABLE Pedido (
    idPedido INT PRIMARY KEY,
    Cliente_idCliente INT,
    status VARCHAR(45),
    codigoRastreio VARCHAR(100),
    FOREIGN KEY (Cliente_idCliente) REFERENCES Cliente(idCliente)
);

CREATE TABLE Produto_has_Pedido (
    Produto_idProduto INT,
    Pedido_idPedido INT,
    quantidade INT,
    PRIMARY KEY (Produto_idProduto, Pedido_idPedido),
    FOREIGN KEY (Produto_idProduto) REFERENCES Produto(idProduto),
    FOREIGN KEY (Pedido_idPedido) REFERENCES Pedido(idPedido)
);

CREATE TABLE Pagamento (
    idPagamento INT PRIMARY KEY,
    tipoPagamento VARCHAR(50) NOT NULL,
    Pedido_idPedido INT,
    FOREIGN KEY (Pedido_idPedido) REFERENCES Pedido(idPedido)
);

CREATE TABLE Terceiro_Vendedor (
    idTerceiro INT PRIMARY KEY,
    nomeVendedor VARCHAR(100) NOT NULL
);

CREATE TABLE Terceiro_Vendedor_has_Produto (
    Terceiro_idTerceiro INT,
    Produto_idProduto INT,
    PRIMARY KEY (Terceiro_idTerceiro, Produto_idProduto),
    FOREIGN KEY (Terceiro_idTerceiro) REFERENCES Terceiro_Vendedor(idTerceiro),
    FOREIGN KEY (Produto_idProduto) REFERENCES Produto(idProduto)
);

CREATE TABLE Produto_has_Fornecedor (
    Produto_idProduto INT,
    Fornecedor_idFornecedor INT,
    PRIMARY KEY (Produto_idProduto, Fornecedor_idFornecedor),
    FOREIGN KEY (Produto_idProduto) REFERENCES Produto(idProduto),
    FOREIGN KEY (Fornecedor_idFornecedor) REFERENCES Fornecedor(idFornecedor)
);
```

---

## **Inserção de Dados para Testes**

```sql
-- Inserindo dados de exemplo
INSERT INTO Cliente (idCliente, tipoCliente, nome) VALUES (1, 'PF', 'João Silva');
INSERT INTO Cliente (idCliente, tipoCliente, nome) VALUES (2, 'PJ', 'Tech Solutions LTDA');

INSERT INTO Fornecedor (idFornecedor, nomeFornecedor) VALUES (1, 'Fornecedor A');
INSERT INTO Fornecedor (idFornecedor, nomeFornecedor) VALUES (2, 'Fornecedor B');

INSERT INTO Produto (idProduto, nomeProduto, preco) VALUES (1, 'Teclado', 150.00);
INSERT INTO Produto (idProduto, nomeProduto, preco) VALUES (2, 'Mouse', 100.00);

INSERT INTO Estoque (idEstoque, local) VALUES (1, 'Centro de Distribuição SP');
INSERT INTO Estoque (idEstoque, local) VALUES (2, 'Centro de Distribuição RJ');

INSERT INTO EmEstoque (Produto_idProduto, Estoque_idEstoque, quantidade) VALUES (1, 1, 50);
INSERT INTO EmEstoque (Produto_idProduto, Estoque_idEstoque, quantidade) VALUES (2, 2, 100);

INSERT INTO Pedido (idPedido, Cliente_idCliente, status, codigoRastreio) VALUES (1, 1, 'Enviado', 'BR12345');
INSERT INTO Pedido (idPedido, Cliente_idCliente, status, codigoRastreio) VALUES (2, 2, 'Processando', 'BR67890');

INSERT INTO Produto_has_Pedido (Produto_idProduto, Pedido_idPedido, quantidade) VALUES (1, 1, 2);
INSERT INTO Produto_has_Pedido (Produto_idProduto, Pedido_idPedido, quantidade) VALUES (2, 2, 1);

INSERT INTO Pagamento (idPagamento, tipoPagamento, Pedido_idPedido) VALUES (1, 'Cartão de Crédito', 1);
INSERT INTO Pagamento (idPagamento, tipoPagamento, Pedido_idPedido) VALUES (2, 'Boleto', 2);

INSERT INTO Terceiro_Vendedor (idTerceiro, nomeVendedor) VALUES (1, 'Vendedor X');
INSERT INTO Terceiro_Vendedor_has_Produto (Terceiro_idTerceiro, Produto_idProduto) VALUES (1, 1);
```

---

## **Queries Complexas**

### 1. Quantos pedidos foram feitos por cada cliente?
```sql
SELECT Cliente.nome, COUNT(Pedido.idPedido) AS totalPedidos
FROM Cliente
JOIN Pedido ON Cliente.idCliente = Pedido.Cliente_idCliente
GROUP BY Cliente.nome;
```

### 2. Algum vendedor também é fornecedor?
```sql
SELECT Terceiro_Vendedor.nomeVendedor, Fornecedor.nomeFornecedor
FROM Terceiro_Vendedor
JOIN Produto_has_Fornecedor ON Terceiro_Vendedor.idTerceiro = Produto_has_Fornecedor.Fornecedor_idFornecedor
JOIN Fornecedor ON Produto_has_Fornecedor.Fornecedor_idFornecedor = Fornecedor.idFornecedor;
```

### 3. Relação de produtos, fornecedores e estoques
```sql
SELECT Produto.nomeProduto, Fornecedor.nomeFornecedor, Estoque.local
FROM Produto
JOIN Produto_has_Fornecedor ON Produto.idProduto = Produto_has_Fornecedor.Produto_idProduto
JOIN Fornecedor ON Produto_has_Fornecedor.Fornecedor_idFornecedor = Fornecedor.idFornecedor
JOIN EmEstoque ON Produto.idProduto = EmEstoque.Produto_idProduto
JOIN Estoque ON EmEstoque.Estoque_idEstoque = Estoque.idEstoque;
```

### 4. Relação de nomes dos fornecedores e nomes dos produtos
```sql
SELECT Fornecedor.nomeFornecedor, Produto.nomeProduto
FROM Produto
JOIN Produto_has_Fornecedor ON Produto.idProduto = Produto_has_Fornecedor.Produto_idProduto
JOIN Fornecedor ON Produto_has_Fornecedor.Fornecedor_idFornecedor = Fornecedor.idFornecedor;
```

---

