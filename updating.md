# Atualização de documentos (Update)

Toda a atualização é um processo que modifica a estrutura do documento, seja incluindo ou apagando um campo, atualizando um valor ou modificando todo o documento.

## Incluindo um novo atributo ou atualizando o valor de um atributo já existente com o método `updateOne()` e o operador `$set`

Antes de efetuar um update é sempre importante efetuar uma busca, afim de validar os dados que serão atualizados e evitar surpresas inesperadas.

No exemplo abaixo, queremos adicionar um atributo `startsWithO: true` para os pokemons que o nome comece com a letra `O`. Antes de mais nada, vamos fazer a pesquisa e encontrar os pokemons:

```js
// Query
db.pokemon.find({name: /^O/})
// Result
{ "_id" : 49, "types" : [ "Grass", "Poison" ], "name" : "Oddish", "legendary" : false, "hp" : 45, "attack" : 50, "defense" : 55, "speed" : 30, "generation" : 1 }

{ "_id" : 104, "types" : [ "Rock", "Ground" ], "name" : "Onix", "legendary" : false, "hp" : 35, "attack" : 45, "defense" : 160, "speed" : 70, "generation" : 1 }

{ "_id" : 150, "types" : [ "Rock", "Water" ], "name" : "Omanyte", "legendary" : false, "hp" : 35, "attack" : 40, "defense" : 100, "speed" : 35, "generation" : 1 }

{ "_id" : 151, "types" : [ "Rock", "Water" ], "name" : "Omastar", "legendary" : false, "hp" : 70, "attack" : 60, "defense" : 125, "speed" : 55, "generation" : 1 }

{ "_id" : 243, "types" : [ "Water" ], "name" : "Octillery", "legendary" : false, "hp" : 75, "attack" : 105, "defense" : 75, "speed" : 45, "generation" : 2 }

{ "_id" : 561, "types" : [ "Water" ], "name" : "Oshawott", "legendary" : false, "hp" : 55, "attack" : 55, "defense" : 45, "speed" : 45, "generation" : 5 }
```

Agora que já sabemos quais dados nosso update pode impactar, para atualizar o documento e criar ou atualizar o valor do atributo `startsWithO: true` usamos o seguinte comando:

```js
db.pokemon.updateOne({name: /^O/}, { $set: { startsWithO: true } })
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```

Repare no retorno do comando, onde diz `"matchedCount" : 1` que significa que ele encontrou um documento que começa com a letra `O` e em seguida vemos o valor  `"modifiedCount" : 1 ` que nos diz que 1 documento foi modificado.

Para ver o documento que foi modificado, executamos a seguinte query:

```js
db.pokemon.findOne({name: /^O/})
{
        "_id" : 49,
        "types" : [
                "Grass",
                "Poison"
        ],
        "name" : "Oddish",
        "legendary" : false,
        "hp" : 45,
        "attack" : 50,
        "defense" : 55,
        "speed" : 30,
        "generation" : 1,
        "startsWithO" : true
}
```

## Atualizando mais de um documento com o método `updateMany()` e o operador `$set`

Com o mesmo cenário anterior, podemos atualizar os demais documentos que comecem com `O` adicionando ou atualizado o valor do atributo `"startsWithO" : true`. Para isso executamos o seguinte:

```js
> db.pokemon.updateMany({name: /^O/}, { $set: { startsWithO: true } })
{ "acknowledged" : true, "matchedCount" : 6, "modifiedCount" : 5 }
```

Se reparamos no retorno do comando, onde diz `"matchedCount" : 6` que significa que ele encontrou 6 documentos que começam com a letra `O` e em seguida vemos o valor  `"modifiedCount" : 5 ` que nos diz que 5 documentos foi modificados. 

Isso ocorre porque já haviamos atualizado um documento antes com o mesmo filtro.

## Usando o operador `$unset` para remover um atributo do documento

Continuando com o mesmo cenário anterior, vamos remover o atributo `startsWithO: true`.

Para remover um atributo de um documento com o `$unset`, executamos o seguinte comando:

```js
> db.pokemon.updateMany({name: /^O/}, { $unset: { startsWithO: false } })
{ "acknowledged" : true, "matchedCount" : 6, "modifiedCount" : 6 }
```

Se reparamos no retorno do comando, onde diz `"matchedCount" : 6` que significa que ele encontrou 6 documentos que começam com a letra `O` e em seguida vemos o valor  `"modifiedCount" : 6 ` que nos diz que 6 documentos foi modificados. 

## Incrementando valores com o operador `$inc`

Para incrementarmos o valor de um atributo do tipo numérico, podemos utilizar o operador `$inc` nos métodos `updateOne()` e `updateMany()`.

No exemplo abaixo, vamos incrementar 10 pontos no ataque de todos os pokemons do tipo Fogo:

```js
> db.pokemon.updateMany({types: "Fire"}, { $inc: {attack: 10} })
{ "acknowledged" : true, "matchedCount" : 64, "modifiedCount" : 64 }
```

Para decrementar, também utilizamos o operador `$inc`, como no exemplo abaixo, onde vamos voltar ao estado original dos pontos de ataque, decrementando 10 pontos.

```js
> db.pokemon.updateMany({types: "Fire"}, { $inc: {attack: -10} })
{ "acknowledged" : true, "matchedCount" : 64, "modifiedCount" : 64 }
```


## Multiplicando valores com o operador `$mul`

Para multiplicar o valor de um atributo do tipo numérico, podemos usar o operador `$mul`, passando o valor de quantas vezes queremos multiplicar o valor do atributo.

No exemplo abaixo, vamos multiplicar o valor dos pontos de ataque de todos os pokemons de fogo por 3:

 ```js
> db.pokemon.updateMany({types: "Fire"}, { $mul: {attack: 3} })
{ "acknowledged" : true, "matchedCount" : 64, "modifiedCount" : 64 }
```

## Limitando os valores com os operadores `$min` e `$max`

Os operadores `$min` e `$max` são limitadores.

Para o operador `$min` passamos um valor de limite superior. No caso de um update, se o valor do atributo for maior que o limite superior, ele será atualizado para o valor do limite superior.

No exemplo abaixo, vamos aproveitar a multiplicação dos pontos de ataque por 3 nos pokemons de tipo fogo e atualizar todos os pokemons que estiver com mais de 150 de ataque para o valor 150:

```js
> db.pokemon.updateMany({ types: "Fire" }, { $min: { attack: 150 } })
{ "acknowledged" : true, "matchedCount" : 64, "modifiedCount" : 56 }
```

Para o operador `$max` passamos um valor de limite inferior. No caso de um update, se o valor do atributo for menor que o limite inferior, ele será atualizado para o valor do limite inferior.

No exemplo abaixo, vamos atualizar todos os pokemons que possuem pontos de ataque menor que 75 para 75.

```js
> db.pokemon.updateMany({}, { $max: { attack: 75 } })
{ "acknowledged" : true, "matchedCount" : 800, "modifiedCount" : 351 }
```

## Operador `$currentDate`

Podemos utilizar o operador `$currentDate` para atualizar uma data dentro de um documento.
Uma aplicação poderia ser em um campo updatedAt, que pode ser utilizado como um marcador temporal de quando o documento foi atualizado pela última vez.

No exemplo abaixo, estamos fazendo um update na collection de pokemons e atualizando o atributo `updatedAt` para um valor no formato ISODate, que é o padrão desse operador:

```js
db.pokemon.updateMany({ types: "Bug" }, { 
    $set: { name: "É do tipo Bug mesmo" },
    $currentDate: {
      updatedAt: true
    }
})
```

Em outro exemplo abaixo, estamos fazendo um update na collection de pokemons e atualizando o atributo `updatedAt` para um valor no formato Unix Timestamp:

```js
db.pokemon.updateMany({ types: "Bug" }, { 
    $set: { name: "É do tipo Bug" },
    $currentDate: {
      updatedAt: { $type: "timestamp" }
    }
})
```
## Atualizar ou incluir com a opção `upsert: true`

Existem situações em que não temos certeza que exista no banco um documento que de match com a query do nosso update.

Nesse caso, podemos fazer o uso da opção `upsert: true` como terceiro parametro em nossos métodos de update.

No exemplo abaixo, queremos que seja verificado se existe um pokemon com o nome `"Mega Charmander"`. Se existir irá atualizar o valor do atributo attack para 9000. Senão existir, será criado um pokemon com o nome `"Mega Charmander"` e com atributo `attack: 9000`: 

```js
db.pokemon.updateOne(
  { name: "Mega Charmander" }, 
  { $set: { attack: 9000 } },
  { upsert: true }
)
```

## Upsert com atributos extras para insert

É possível passar para o comando update com `upsert: true`, junto com o operador `$set` o operador `$setOnInsert` com novos atributos que só serão criados caso o documento seja criado.
O novo documento também vai criar os campos passados dentro do `$set`. 

```js
db.pokemon.updateOne(
  { name: "Ultra Charmander" }, 
  { 
    $set: { attack: 9000 }, 
    $setOnInsert: {
      defense: 5000,
      hp: 10000,
      speed: 1000
    }
  },
  { upsert: true }
)
```

## Atualizando elementos de um array

Para atualizar um elemento dentro de um array, precisamos fazer uma query que procure o elemento dentro do array e nos parametros de update, temos que passar no set o um operador que vai conter a posição no array do elemento, que é o `atributo.$`.

No exemplo abaixo, vamos atualizar um pokemon mudando um tipo de Poison para Lasanha:

```js
> db.pokemon.updateOne(
  { _id: 1, types: "Poison" }, 
  { $set: { "types.$": "Lasanha" } }
)
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.pokemon.findOne({ _id: 1 })
{
        "_id" : 1,
        "types" : [
                "Grass",
                "Lasanha"
        ],
        "name" : "Bulbasaur",
        "legendary" : false,
        "hp" : 45,
        "attack" : 75,
        "defense" : 49,
        "speed" : 45,
        "generation" : 1
}
```

Também é possível atualizar todos os elementos de uma só vez, passando o operador `atributo.$[]`.

No exemplo abaixo, vamos atualizar todos os elementos do atributo types para `"Pizza"`:

```js
> db.pokemon.updateOne(
  { _id: 1 }, 
  { $set: { "types.$[]": "Pizza" } }
)
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
> db.pokemon.findOne({_id:1})
{
        "_id" : 1,
        "types" : [
                "Pizza",
                "Pizza"
        ],
        "name" : "Bulbasaur",
        "legendary" : false,
        "hp" : 45,
        "attack" : 75,
        "defense" : 49,
        "speed" : 45,
        "generation" : 1
}
```

## Adicionando um item no array com o operador `$push`

Para incluir um novo item no final do array, podemos utilizar operador `$push` em update.

No exemplo abaixo, vamos incluir no final, um novo item chamado Pizza:

```js
db.pokemon.updateOne(
  { _id: 1 }, 
  { $push: { types: "Pizza" } }
)
```

## Adicionando mais de um valor no array com o operador `$each`

Para adicionar mais de um valor em um atributo do tipo array, podemos utilizar o operador `$each`.

No exemplo abaixo, vamos adicionar 3 novos itens ao atributo `types`:

```js
db.pokemon.updateOne(
  { _id: 1 }, 
  { $push: { 
      types: {
        $each: ["Hotdog", "Bauru", "Sorvete"]  
      }
    } 
  }
);
```

## Controlando a posição da inclusão no array

Para incluir um ou mais itens no array em uma determinada posição, usamos em conjunto com o operador `$each` o operador `$position` que recebe o indice do array onde os itens deve inserido após.

No exemplo abaixo, vamos incluir um novo item após o terceiro item do array que está no indice 2:


```js
db.pokemon.updateOne(
  { _id: 1 }, 
  { $push: { 
      types: {
        $each: ["Esfirra"],
        $position: 2
      }
    } 
  }
);
```

## Evitando a adição de um item duplicado no array com o operador `$addToSet`

Para adicionar um ou mais items em um array sem duplicações, podemos utilizar o operador `$addToSet`, que pode inserir tanto com um único item quanto vários.

No exemplo abaixo, queremos adicionar um novo item chamado "Hamburguer", que caso não exista no array, ele será incluído no final e caso exista não vai modificar o array:

```js
db.pokemon.updateOne(
  { _id: 1 }, 
  { $addToSet: { 
      types: "Hamburguer" 
    } 
  }
);
```

Já no exemplo abaixo, utilizamos o operador `$each` para tentar incluir mais de um item no array. Nesse caso, só serão incluídos no final do array os items  que ainda não existem e caso todos já existam, o documento não será modificado.

```js
db.pokemon.updateOne(
  { _id: 1 }, 
  { $addToSet: { 
      types: {
        $each: ["Esfirra",  "Hamburguer", "Mousse"]
      }
    } 
  }
);
```

## Ordenando os itens de um array com o operador `$sort`

Para utilizar o operador `$sort` será necessário também a utilização do operador `$each`. Esse comportamento acontece porque o operador `$sort` é usado em conjunto com operador `$push`.

No exemplo abaixo, para ordenar os itens de forma crescente, o operador `$set` deve ter o valor `1`, porém caso quisessemos ordenar de forma decrescente, o valor teria que ser `-1`:

```js
db.pokemon.updateOne(
  { _id: 1 }, 
  { $push: { 
      types: {
        $each: [],
        $sort: 1
      }
    } 
  }
);
```

 
