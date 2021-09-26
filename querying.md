
Busca um nome igual a `Pikachu`

```js
db.pokemon.find({name: /Pikachu/ }).pretty()
```


Busca um nome começando com `Pi`

```js
db.pokemon.find({name: /^Pi/ }).pretty()
```


Busca um nome terminando com  `chu`

```js
db.pokemon.find({name: /chu$/ }).pretty()
```

# Usando Projections 
Para habilitar um campo, basta passar o nome do campo e o valor 1, como por exemplo: `{ name: 1 }`.
Para desabilitar um campo, basta passar o nome do campo e o valor 0, como por exemplo: `{ _id: 0 }`.

Busca um nome começando com `Pik` e retorna somente os campos `name` e `_id`, sendo o campo `_id` sempre visivel.

```js
db.pokemon.find({name: /^Pik/ }, { name: 1 }).pretty()
```

Busca um nome começando com `Pik` e retorna o campo `name` e oculta o campo `_id`.

```js
db.pokemon.find({name: /^Pik/ }, { name: 1, _id: 0 }).pretty()
```

## Ignorando Maísculas e Minúsculas

Busca um nome começando com `pi`, ignorando maísculas ou minusculas (opção `i` no final da RegEx) e retorna os campos `name` e `_id`.

```js
// Query
db.pokemon.find({name: /^pi/i }, {name: true}).pretty()

//Retorno do comando
{ "_id" : 22, "name" : "Pidgeotto" }
{ "_id" : 23, "name" : "Pidgeot" }
{ "_id" : 21, "name" : "Pidgey" }
{ "_id" : 31, "name" : "Pikachu" }
{ "_id" : 137, "name" : "Pinsir" }
{ "_id" : 187, "name" : "Pichu" }
{ "_id" : 220, "name" : "Pineco" }
{ "_id" : 240, "name" : "Piloswine" }
{ "_id" : 439, "name" : "Piplup" }
{ "_id" : 559, "name" : "Pignite" }
{ "_id" : 579, "name" : "Pidove" }
```

# Operadores de consulta (Query Selectors)

É possível usar operadores especifícos de acordo com o tipo de dado do atributo.

[Documentação Query selectors](https://docs.mongodb.com/manual/reference/operator/query/#query-selectors)

## Operadores númericos

Para buscar um pokemon com ataque maior ou igual a 120, podemos usar o operador `$gte` (Greater Than or Equal):

```js
// Query
db.pokemon.find({ attack: { $gte: 120  } }, { name: 1, attack: 1, _id: 0  })
```

Para buscar um pokemon com ataque menor ou igual a 10, podemos usar o operador `$lte` (Less Than or Equal):

```js
// Query
db.pokemon.find({ attack: { $lte: 10  } }, { name: 1, attack: 1, _id: 0  })
```

## Operadores de lista

Os operadores de lista são `$in`(Included) e `$nin` (Not Included).

Para recuperar todos os pokemons que são ou do tipo `"Fire"` ou do tipo `"Rock"`, podemos usar o seguinte comando:
```js
// Query
db.pokemon.find({ types: { $in: ["Fire", "Rock"]  }  }, { name: 1, types: 1, _id: 0  })
```

Para recuperar todos os pokemons que NÃO são ou do tipo `"Fire"` ou do tipo `"Rock"`, podemos usar o seguinte comando:
```js
// Query
db.pokemon.find({ types: { $nin: ["Fire", "Rock"]  }  }, { name: 1, types: 1, _id: 0  })
```

## Usando o $in e o $nin em campos de texto
Também é possível buscar em campos de string, passando os valores exatos ou usando RegEx, como numa busca normal em um campo de texto:

Exemplo de busca para retornar apenas o `Pikachu` e o `Bulbasaur`:

```js
// Query
db.pokemon.find({ name: { $in: [ "Pikachu", "Bulbasaur"] } })
```

Exemplo de busca para retornar pokemons com o nome iniciando com `Pi` e o `Bu`:

```js
// Query
db.pokemon.find({ name: { $in: [/^Pi/, /^Bu/] } })
```


## Usando um intervalo de valores

Para efetuar uma busca de todos os pokemons com a defesa maior que 10 e menor ou igual a 20, posso fazer a seguinte query:

```js
// Query
db.pokemon.find({ defense: { $gt: 10, $lte: 20  } }, { _id: 0, name: 1, defense: 1 })
// Resultado
{ "name" : "Jigglypuff", "defense" : 20 }
{ "name" : "Abra", "defense" : 15 }
{ "name" : "Pichu", "defense" : 15 }
{ "name" : "Igglybuff", "defense" : 15 }
{ "name" : "Smoochum", "defense" : 15 }
{ "name" : "Carvanha", "defense" : 20 }
{ "name" : "Feebas", "defense" : 20 }
{ "name" : "DeoxysAttack Forme", "defense" : 20 }
```

## Operador $or (ou)

Quando necessário válidar um ou outro valor é possível usar o operador `$or`. Esse operador espera um array com duas ou mais queries. 

```js
// Query
db.pokemon.find({ 
  $or: [
    {
      defense: { $gt: 10, $lte: 20  } 
    }, 
    {
      defense: 100  
    }  
  ]}, 
  { 
    _id: 0, 
    name: 1, 
    defense: 1
  }
)
```
## Operador $or em uma consulta combinando duas queries

No exemplo abaixo, a querie está procurando por pokemons que tenham defesa E hp maior ou igual a 80 OU ataque E velocidade maior ou igual a 80.

```js
// Query
db.pokemon.find({ 
  $or: [
    {
      defense: { $gte: 80  },
      hp: { $gte: 80  }
    }, 
    {
      attack: { $gte: 80  },
      speed: { $gte: 80  } 
    }  
  ]}, 
  { 
    _id: 0, 
    name: 1, 
    defense: 1,
    hp: 1,
    attack: 1,
    speed: 1
  }
).pretty()
```

## Usando o método `sort()` para ordenação

Para ordenar os dados, é possível utilizar o método `sort()`, passando como parâmetro o campo ou os campos que devem ser utilizados para ordenação.

Para ordenar de forma crescente, basta passar o nome do campo e o valor `1`, por exemplo: `sort({ hp: 1})`.

Para ordenar de forma decrescente, basta passar o nome do campo e o valor `-1`, por exemplo: `sort({ hp: 1})`.

É possível também ordenar por mais de um campo, como por exemplo, se quero ordenar pelo campo `hp` de forma crescente e pelo campo `defense` de forma descrescente, faço o seguinte: `sort({ hp: 1, defense: -1})`.

No exemplo abaixo, a ordenação será feita através do campo `hp` de forma crescente: 
```js
// Query
db.pokemon.find({ 
  $or: [
    {
      defense: { $gte: 80  },
      hp: { $gte: 80  }
    }, 
    {
      attack: { $gte: 80  },
      speed: { $gte: 80  } 
    }  
  ]}, 
  { 
    _id: 0, 
    name: 1, 
    defense: 1,
    hp: 1,
    attack: 1,
    speed: 1
  }
).sort({ hp: 1 }).pretty()
```

## Operador `$size`

O operador `$size` pode ser utilizado para filtrar pela quantidade de itens em um array.

No exemplo abaixo, estamos buscando os pokemons que possuem 2 tipos:

```js
// Query
db.pokemon.find({ types: { $size: 3 } }, { _id: 0, name: 1, types: 1 } )
```

## Operador `$all`

O operador `$all` pode ser utilizado para filtrar itens em um array, trazendo a exata correspondência passada no parâmetro.

No exemplo abaixo, estamos buscando os pokemons que possuem exatamente os tipos `"Rock"` e `"Flying"`.

```js
// Query
db.pokemon.find({ types: { $all: ["Rock", "Flying"] } }, { _id: 0, name: 1, types: 1 } )
// Resultado
{ "types" : [ "Rock", "Flying" ], "name" : "Mega Aerodactyl" }
{ "types" : [ "Rock", "Flying" ], "name" : "Aerodactyl" }
{ "types" : [ "Rock", "Flying" ], "name" : "Archen" }
{ "types" : [ "Rock", "Flying" ], "name" : "Archeops" }
```

## Operador `$and`

Usado para quando queremos que 2 ou mais valores sejam obrigatórios em uma query

Forma explicita: 

```js
db.pokemon.find(
  {
    $and: [
      { types: "Flying" }, 
      { legendary: true }
    ]
  },
  { 
    _id: 0, 
    name: 1, 
    types: 1 
  } 
)
```

Forma implicita: 

```js
db.pokemon.find(
  { 
    types: "Flying", 
    legendary: true 
  },
  { 
    _id: 0, 
    name: 1, 
    types: 1 
  } 
)
```

## Usando os métodos `skip()` e `limit()` para efetuar paginação de dados

Quando queremos uma paginação de um grande volume de dados, podemos limitar o retorno dos dados através do método `limit()`, passando como parametro quantidade de registros desejada.

No exemplo abaixo, estamos retornando os 10 primeiros pokemons do tipo `Fire` com o maior ataque:

```js
> db.pokemon.find({ types: "Fire" }, {_id: 0, name: 1, attack: 1}).sort({ attack: -1}).skip().limit(10)
{ "name" : "Primal Groudon", "attack" : 180 }
{ "name" : "Mega Blaziken", "attack" : 160 }
{ "name" : "Darmanitan Standard Mode", "attack" : 140 }
{ "name" : "Ho-oh", "attack" : 130 }
{ "name" : "Mega Charizard X", "attack" : 130 }
{ "name" : "Flareon", "attack" : 130 }
{ "name" : "Emboar", "attack" : 123 }
{ "name" : "Reshiram", "attack" : 120 }
{ "name" : "Blaziken", "attack" : 120 }
{ "name" : "Mega Camerupt", "attack" : 120 }
```

Já neste exemplo, estamos utilizando o metodo `skip(10)`, para pegar os próximos 10 pokemons do tipo `Fire` com maior ataque, o que seria equivalente a página 2 do nosso total de pokemons:

```js
> db.pokemon.find({ types: "Fire" }, {_id: 0, name: 1, attack: 1}).sort({ attack: -1}).skip(10).limit(10)
{ "name" : "Entei", "attack" : 115 }
{ "name" : "Arcanine", "attack" : 110 }
{ "name" : "Volcanion", "attack" : 110 }
{ "name" : "Infernape", "attack" : 104 }
{ "name" : "Mega Charizard Y", "attack" : 104 }
{ "name" : "Camerupt", "attack" : 100 }
{ "name" : "Victini", "attack" : 100 }
{ "name" : "Rapidash", "attack" : 100 }
{ "name" : "Moltres", "attack" : 100 }
{ "name" : "Simisear", "attack" : 98 }
```

## Consultando com filtro em campo embedded

Quando quero fazer um find, filtrando por um valor de um documento dentro do outro, posso utilizar o dot notation, que basicamente é como acessar uma propriedade de um objeto no JavaScript.
Um detalhe importante: sempre o campo e o atributo devem estar em volta de aspas, como no exemplo abaixo:

```js
db.pokemon.find({ "battle_points.hp" : {$lte: 79}}).pretty()
