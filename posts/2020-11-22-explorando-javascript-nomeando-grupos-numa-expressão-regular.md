---
title: "Explorando Javascript: Nomeando grupos numa expressão regular"
description: Separando uma expressão regular em grupos nomeados
date: 2020-11-22 07:36:45
image: assets/img/screen-shot-2020-11-22-at-19.44.47.png
category: dev
background: "#008BF8"
---
Vamos supor que você tenha uma string que vai ser recebida na sua aplicação. Essa string tem um padrão, possui várias informações contidas nela e você precisa salvar essas informações separadamente.

Encontre o padrão que a string está e aplique uma expressão regular. Geralmente quando preciso criar uma expressão regular eu uso esse site aqui: [](https://regexr.com/)<https://regexr.com/> (não sei criar de cabeça e sempre preciso entender como vou fazer na aba de **Cheatsheet**).

Para este caso vamos ter por exemplo uma string que contém as seguintes informações:

* Número de CPF
* Data de nascimento
* Nome

Essas informações vão estar contidas numa string e separadas por traço `-` e vão vir dessa forma:

"**001.002.003-99**-**10-08-1997**-**Fulano Pessoa**"

Nesse exemplo as informações separadas estariam assim:

* Número de CPF: **001.002.003-99**
* Data de nascimento: **10-08-1997**
* Nome: **Fulano Pessoa**

Criei esse regex básico pra poder pegar as informações:

```jsx
/([0-9]{3}\.[0-9]{3}\.[0-9]{3}-[0-9]{2})-([0-9]{2}-[0-9]{2}-[0-9]{4})-(.*)/
```

Cada grupo está indicado por parênteses `(` `)`.

* Número de CPF: **(\[0-9]{3}\.\[0-9]{3}\.\[0-9]{3}-\[0-9]{2})**
* Data de nascimento: **(\[0-9]{2}-\[0-9]{2}-\[0-9]{4})**
* Nome: **(.*)**

Obs: Não estou fazendo um regex totalmente correto de data, estou só contando a quantidade de números da data mesmo.

Para aplicamos o regex no javascript seria dessa forma:

```jsx
const infoCompleta = "001.002.003-99-10-08-1997-Fulano Pessoa"

const expressao = /([0-9]{3}\.[0-9]{3}\.[0-9]{3}-[0-9]{2})-([0-9]{2}-[0-9]{2}-[0-9]{4})-(.*)/;

const dados = expressao.exec(infoCompleta)

console.log(dados)
/*
[
  '001.002.003-99-10-08-1997-Fulano Pessoa',
  '001.002.003-99',
  '10-08-1997',
  c
  index: 0,
  input: '001.002.003-99-10-08-1997-Fulano Pessoa',
  groups: undefined
]
*/
```

Após executar a expressão é retornado um array para a variável dados. E as informações separadas conseguimos pegar a partir da posição \[1].

Se fossemos pegar as informações dessa forma:

```jsx
const cpf = dados[1]
// '001.002.003-99'
const dataNascimento = dados[2]
// '10-08-1997'
const nomePessoa = dados [3]
// '10-08-1997'
```

Funciona? Funciona... Mas poderíamos fazer de uma forma melhor.

## Alternativa

Ao criar a expressão, podemos acrescentar o nome do grupo dentro do grupo que está nomeando e antes da expressão, dessa forma:

* Número de CPF: (**?<cpf>**\[0-9]{3}\.\[0-9]{3}\.\[0-9]{3}-\[0-9]{2})
* Data de nascimento: (**?<dataNascimento>**\[0-9]{2}-\[0-9]{2}-\[0-9]{4})
* Nome: (**?<nomePessoa>**.*)

com uma `?`, um `<`, o `nomeDoGrupo`, um `>` .

Colocando na nossa expressão, ela agora fica dessa forma:

```jsx
const expressao = /(?<cpf>[0-9]{3}\.[0-9]{3}\.[0-9]{3}-[0-9]{2})-(?<dataNascimento>[0-9]{2}-[0-9]{2}-[0-9]{4})-(?<nomePessoa>.*)/;
```

E executando o código usando essa expressão conseguimos isso:

```jsx
const infoCompleta = "001.002.003-99-10-08-1997-Fulano Pessoa"

const expressao = /(?<cpf>[0-9]{3}\.[0-9]{3}\.[0-9]{3}-[0-9]{2})-(?<dataNascimento>[0-9]{2}-[0-9]{2}-[0-9]{4})-(?<nomePessoa>.*)/;

const dados = expressao.exec(infoCompleta)

console.log(dados)

/*[
  '001.002.003-99-10-08-1997-Fulano Pessoa',
  '001.002.003-99',
  '10-08-1997',
  'Fulano Pessoa',
  index: 0,
  input: '001.002.003-99-10-08-1997-Fulano Pessoa',
  groups: {
    cpf: '001.002.003-99',
    dataNascimento: '10-08-1997',
    nomePessoa: 'Fulano Pessoa'
  }
]*/
```

Ainda retorna um array, porém uma das posições `group` dessa vez tem um objeto preenchido (antes ela estava vindo como `undefined`).

E nesse objeto vem com as propriedades nomeadas que definimos ao nomear os grupos na expressão.

Para pegar estes valores agora podemos simplesmente pegar dessa forma com Object Destructuring (já expliquei sobre Object Destructuring aqui: [Explorando Javascript: Object Destructuring](https://blog.talitaoliveira.com.br/explorando-javascript-object-destructuring/)):

```jsx
const { cpf, dataNascimento, nomePessoa } = dados.groups
```

Referência:

[](https://github.com/tc39/proposal-regexp-named-groups)<https://github.com/tc39/proposal-regexp-named-groups>

\~🌟\~

Bem.. é isso. 😉

Não é sempre que precisamos usar expressões regulares, mas um dia aparece. Essa semana foi preciso usar no projeto e lembrei que já tinha visto sobre "Regexp Named Groups", já tinha até apresentado numa palestra no JS Day falando das novidades do ecmascript em 2018, apresentei ela juntamente com Simone Amorim, foi bem massa.

Coloquei o exemplo no meu github: [](https://github.com/talitaoliveira/ecmascript-studies/blob/master/src/es9/regexp-named-group.js)<https://github.com/talitaoliveira/ecmascript-studies/blob/master/src/es9/regexp-named-group.js>.

E comentei no medium sobre como foi a experiência de ter palestrado (foi a terceira palestra que fiz na vida e o nervosismo tomou conta do mesmo jeito): [](https://medium.com/@litaoliveira/pareando-palestra-no-jsday-2018-7e18a7426220)<https://medium.com/@litaoliveira/pareando-palestra-no-jsday-2018-7e18a7426220>

Enfim... é algo simples que trago nesse post.

Nada de importante, nem reflexivo, ou algo do tipo, só explorando javascript e documentando aqui no blog :).

Até a próxima. 🤙🏽