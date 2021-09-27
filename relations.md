# Relacionamento One to One

## Relacionamento One to One (Estilo BD Relacional)

Model de Aluno (faz referencia ao model de carterinha pelo atributo `carteirinha_id`)

```json
{
  "_id": 1,
  "cpf": "12345678901",
  "nome": "João da Silva",
  "cidade": "Pato Branco",
  "carteirinha_id": 8
}
```

Model de carteirinha

```json
  "carteirinha": {
    "_id": 8,
    "turma": "ADS012021",
    "ra": 12345
  }
```

## Relacionamento One to One (Estilo NoSQL)

Model Aluno contém o objeto carteirinha como atributo.

```json
{
  "_id": 1,
  "cpf": "12345678901",
  "nome": "João da Silva",
  "cidade": "Pato Branco",
  "carteirinha": {
    "_id": 8,
    "turma": "ADS012021",
    "ra": 12345
  }
}
```

## Relacionamento One to Many (modelo relacional)

Collection de setores

```json
[
  {
    "_id": 2,
    "meta": 4000,
    "nome": "vendas"
  },
  {
    "_id": 4,
    "meta": 10000,
    "nome": "gestores"
  }
]
```

Collection de funcionários faz referência a collection de setores (atributo setor_id)

```json
[
  {
    "_id": 1,
    "nome": "João da Silva",
    "salario": 5500.2,
    "turno": "normal",
    "cargo": "gestor",
    "setor_id": 4
  },
  {
    "_id": 2,
    "nome": "José Pereira",
    "salario": 3500.2,
    "turno": "normal",
    "cargo": "vendedor",
    "setor_id": 2
  }
]
```

## Relacionamento One to Many (modelo Mongo)

Collection de setores possui campo funcionarios como array:

```json
[
  {
    "_id": 2,
    "meta": 4000,
    "nome": "vendas",
    "funcionarios": [
      {
        "_id": 2,
        "nome": "José Pereira",
        "salario": 3500.2,
        "turno": "normal",
        "cargo": "vendedor",
        "setor_id": 2
      }
    ]
  },
  {
    "_id": 4,
    "meta": 10000,
    "nome": "gestores",
    "funcionarios": [
      {
        "_id": 1,
        "nome": "João da Silva",
        "salario": 5500.2,
        "turno": "normal",
        "cargo": "gestor",
        "setor_id": 4
      }
    ]
  }
]
```

# Many to many

Collections de exemplo:
Collection Fornecedor

```
- id
- CNPJ
- Nome
- CEP
```

Collection Produto

```
- id
- Descrição
- Preço
```

Collection FornecedorProduto

```
- fornecedor_id
- produto_id
- Preço
```

Dados fornecedores

```json
[
  {
    "id": "f01",
    "CNPJ": "021321312312",
    "nome": "Fornecedor Bacana",
    "cep": "312312311"
  },
  {
    "id": "f03",
    "CNPJ": "123123412412",
    "nome": "Fornecedor Legal",
    "cep": "123123123"
  }
]
```

Dados Produtos

```json
[
  {
    "id": "p21",
    "descricao": "PC",
    "preco": 2500
  },
  {
    "id": "p22",
    "descricao": "Notebook",
    "preco": 1500
  }
]
```

# Relacionamento Many to Many (modelo relacional)

Usando tabela auxiliar FornecedorProduto

```json
[
  {
    "fornecedor_id": "f01",
    "produto_id": "p21",
    "preco": 2500
  },
  {
    "fornecedor_id": "f01",
    "produto_id": "p22",
    "preco": 2500
  }
]
```

# Relacionamento Many to Many (modo mongo)

Dados fornecedores fazem referência aos produtos

```json
[
  {
    "id": "f01",
    "CNPJ": "021321312312",
    "nome": "Fornecedor Bacana",
    "cep": "312312311",
    "produto_ids": ["p21", "p22"]
  },
  {
    "id": "f03",
    "CNPJ": "123123412412",
    "nome": "Fornecedor Legal",
    "cep": "123123123",
    "produto_ids": ["p22", "p23"]
  }
]
```

Dados Produtos fazem referência aos fornecedores

```json
[
  {
    "id": "p21",
    "descricao": "PC",
    "preco": 2500,
    "fornecedor_ids": ["f01"]
  },
  {
    "id": "p22",
    "descricao": "Notebook",
    "preco": 1500,
    "fornecedor_ids": ["f01"]
  },
  {
    "id": "p23",
    "descricao": "Tablet",
    "preco": 500,
    "fornecedor_ids": ["f03"]
  }
]
```

# Relacionamento Many to Many (modo híbrido)

Dados fornecedores fazem referência aos produtos

```json
[
  {
    "id": "f01",
    "CNPJ": "021321312312",
    "nome": "Fornecedor Bacana",
    "cep": "312312311",
    "produtos": [
      {
        "_id": "p21",
        "preco": 2500
      },
      {
        "_id": "p22",
        "preco": 1400
      }
    ]
  },
  {
    "id": "f03",
    "CNPJ": "123123412412",
    "nome": "Fornecedor Legal",
    "cep": "123123123",
    "produtos": [
      {
        "_id": "p22",
        "preco": 1500
      },
      {
        "_id": "p23",
        "preco": 500
      }
    ]
  }
]
```

Dados Produtos fazem referência aos fornecedores

```json
[
  {
    "id": "p21",
    "descricao": "PC",
    "fornecedores": [{ "_id": "f01", "preco": 2500 }]
  },
  {
    "id": "p22",
    "descricao": "Notebook",
    "fornecedores": [
      { "_id": "f01", "preco": 1400 },
      { "_id": "f03", "preco": 1500 }
    ]
  },
  {
    "id": "p23",
    "descricao": "Tablet",
    "preco": 500,
    "fornecedores": [{ "_id": "f03", "preco": 500 }]
  }
]
```

# Schema Validator

Para criar uma collection que valide os dados dos atributos, basta criar da seguinte forma

```js
db.createCollection("cars", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["model", "year"],
      properties: {
        model: {
          bsonType: "string",
          minLength: 2,
          description: "É necessário informar um modelo.",
        },
        madeBy: {
          bsonType: "string",
        },
        year: {
          bsonType: "int",
          minimum: 1980,
          maximum: 2100,
          description: "É necessário informar um ano de fabricação.",
        },
      },
    },
  },
});

db.cars.insert({ model: "UP", madeBy: "VW", year: NumberInt(2015) });
db.cars.insert({ model: "Gol", madeBy: "VW", year: NumberInt(2005) });
db.cars.insert({ model: "HR-V", madeBy: "Honda", year: NumberInt(2022) });
```

# Capped Colletion - uma colletion trabalhando como fila

```js
db.createCollection("logs", {
  capped: true,
  size: 2048,
  max: 5,
});
```
