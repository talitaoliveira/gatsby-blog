---
title: "React: Capturando erros com ErrorBoundary"
description: Usando o componente ErrorBoundary para exibir uma mensagem ou
  componente de erro amigável
date: 2020-09-27 10:21:00
image: assets/img/screen-shot-2020-09-27-at-22.30.42.png
category: dev
background: "#008BF8"
---
![Desenho de uma mulher com macacão azul e camisa preta por baixo e um capacete amarelo, segurando uma bandeira vermelha. Atras dela tem uma pedra grande e ao lado direito dela uma pedra maior. Entre ela e a pedra do lado direito há uma sinalização de triangulo com uma exclamação no meio.](assets/img/screen-shot-2020-09-27-at-22.30.42.png "Desenho de uma mulher com macacão azul e camisa preta por baixo e um capacete amarelo, segurando uma bandeira vermelha. Atras dela tem uma pedra grande e ao lado direito dela uma pedra maior. Entre ela e a pedra do lado direito há uma sinalização de triangulo com uma exclamação no meio.")

\
Contexto

O projeto Base

Adicionando ErrorBoundary

Ao vivo

Referência

Minha motivação e reflexão

## Contexto

**ErrorBoundary** no React se trata de um componente que vai ser responsável por capturar erros de qualquer componente filho que lance esse erro, e quando esse erro é capturado pode se exibir uma página de erro.

Substituir a pagina

## O projeto base:

Para o teste eu usei o mesmo projeto que fiz no post que adiciona o Loader que foi feito em CSS. Se quiser pode ver aqui esses posts:

* Criando um "loader" simples com CSS
* Exibindo um Loader para uma requisição no React

Mudei um pouco do projeto que fiz inicialmente somente separar um pouco os componentes.

Antes:

* No componente App.js tava criando direto um `form`
* usava um componente `UserData` para renderizar os dados do usuário

Agora:

* Criei uma pasta chamada **FormUserData** em componets
* O `form` está num componente chamado `FormUserData.js` dentro dessa pasta
* Movi a pasta do componente **UserData** para dentro de **FormUserData**

A pasta **src** está basicamente assim:

```
src
└─ components
|   └─ FormUserData
|	  |  └─ FormUserData.js
|   |  └─ FormUserData.css
|   |  └─ UserData
|   |     └─ UserData.js
|   |     └─ UserData.css
|   └─ Loader
└─ App.js
```

E agora no **App.js** só chamo o componente **FormUserData**.

```jsx
//App.js

import React from 'react'
import FormUserData from './components/FormUserData/FormUserData'

import './App.css';

const App = () => {
  return (
    <div className="app">
       <FormUserData></FormUserData>
    </div>
  );
}

export default App;
```

Eu fiz isso pois vai me ajudar no uso do componente **ErrorBoundary**, já que ele pega exceções dos filhos dele.... Vamos continuar pra entender melhor..

## Adicionando ErrorBoundary

Agora que organizei melhor o projeto conseguimos inserir o **ErrorBoundary** de forma mais organizada. Para isso precisamos:

* Criar um componente do **ErrorBoundary;**
* Colocar o componente do **ErrorBoundary** onde ele vai ser chamado quando houver erro dos componentes filhos;
* Adaptar o componente **FormUserData** para lançar a exceção;

### Criar um componente do **ErrorBoundary;**

Infelizmente o componente **ErrorBoundary** precisa ser um componente de classe. Nele precisam ser definidos um destes métodos de ciclo de vida:

`static getDerivedStateFromError()`: é invocado quando um erro é lançado, recebe o erro e retorna a atualização do state que vai ajudar a verificação para renderizar ou não o erro.

Veja mais sobre esse método aqui: [](https://pt-br.reactjs.org/docs/react-component.html#static-getderivedstatefromerror)<https://pt-br.reactjs.org/docs/react-component.html#static-getderivedstatefromerror>

`componentDidCatch()`: é invocado quando um erro é lançado e recebe dois parâmetros que registram informações do erro.

1. `error` - O erro que foi lançado.
2. `info` - Um objeto com informações sobre o componente que lançou o erro.

Veja mais sobre esse método aqui: [](https://pt-br.reactjs.org/docs/react-component.html#componentdidcatch)<https://pt-br.reactjs.org/docs/react-component.html#componentdidcatch>

No meu componente optei por usar o `componentDidCatch()` pois quero uma informação do erro, a mensagem do throw.

```jsx
//ErrorBoundary.js

import React from "react";

class ErrorBoundary extends React.Component {
    constructor(props) {
      super(props);
      this.state = { error: null, errorInfo: null };
    }
    
    componentDidCatch(error, errorInfo) {
      this.setState({
        error: error,
        errorInfo: errorInfo
      })
    }
    
    render() {
      if (this.state.errorInfo) {
        return <h2>{this.state.error.message}</h2>
      }
      return this.props.children;
    }  
  }

export default ErrorBoundary;
```

* No construtor é setado um state para o error e errorInfo, ambos nullo a princípio;
* O `componentDidCatch()` recebendo `error` e `errorInfo`, esse método vai ser invocado quando acontecer o erro e é "setado" no estado de `error` e `errorInfo` o que veio no momento que o método foi invocado.
* Na função de `render()` verificamos se há algo no state de `errorInfo` (`this.state.errorInfo`), caso haja algo (tenha chegado no `componentDidCatch()` e colocado os valores no estado deles) renderizamos nossa mensagem de erro, nesse ponto poderíamos também criar um componente que seria uma página de erro amigável e substituir por esse `h2`, mas quis deixar simples.
* Caso não aconteça nada, é retornado o próprio componente filho.

Esse componente foi criado dentro da pasta **components**:

```
src
└─ components
|   └─ ErrorBoundary
|	  |  └─ ErrorBoundary.js
|   └─ FormUserData
|	  |  └─ FormUserData.js
|   |  └─ FormUserData.css
|   |  └─ UserData
|   |     └─ UserData.js
|   |     └─ UserData.css
|   └─ Loader
└─ App.js
```

### Chamar o componente ErrorBounday e colocar os filhos

Com o componente **ErrorBoundary** pronto, podemos inclui-lo onde queremos que apareça o erro. No meu caso vou incluir ele no arquivo **App.js** e colocar o **FormUserData** como filho dele.

Agora App.js fica assim:

```jsx
//App.js

import React from 'react'
import FormUserData from './components/FormUserData/FormUserData'
import ErrorBoundary from './components/ErrorBoundary/ErrorBoundary'

import './App.css';

const App = () => {
  return (
    <div className="app">
      <ErrorBoundary>
        <FormUserData></FormUserData>
      </ErrorBoundary>
    </div>
  );
}

export default App;
```

> Massa! Tá pronto, é só isso?

Não... Ainda precisamos fazer o componente filho FormUserData lançar alguma exceção para que o ErrorBoundary capture e exiba a mensagem/componente de erro.

### Adaptar o componente **FormUserData** para lançar a exceção

Para isso vamos no arquivo **FormUserData.js** que agora está dessa forma:

```jsx
import React, { useState } from 'react';
import Loader from '../Loader/Loader'
import UserData from './UserData/UserData'

import './FormUserData.css';

const FormUserData = () => {
  const [showLoader, setShowLoader] = useState(false)
  const [username, setUsername] = useState('')
  const [userData, setUserData] = useState({})


  const handleForm = (e) => {
    e.preventDefault();
    setShowLoader(true)
    fetch(`https://api.github.com/users/${username}`)
        .then(res => res.json())
        .then(data => {
          setUserData(data)
          setShowLoader(false)
        })
        .catch((err) => {
          setShowLoader(false)
        })
  }

  return (
    <>
      <form onSubmit={handleForm} className="form-github-user">
        <label htmlFor="github-user" className="label-github-user">Digite seu usuário do Github:</label>
        <input type="text" id="github-user" className="input-github-user" onChange={e=>setUsername(e.target.value)}/>
        <button type="submit" className="btn-submit">Enviar</button>
      </form>
      {showLoader && <Loader/>}
			<UserData data={userData} />
		</>
  );
}

export default FormUserData;
```

E vamos adicionar a ele:

* uma variável de estado para mensagem de erro \[\`errorMessage, setErrorMessage]\`, que vai ser uma string que se **inicia vazia**;
* uma outra variavel de estado para verificar se houve erro ou não `[hasError, setHasError]`, que vai ser um **boolean** que se **inicia false**;
* 🚨 Um `if` dentro do segundo `.then`, nele é onde é pego o que foi retornado da API e inserido na variável de userData. Esse `if` vai ser responsável por verificar se no que foi retornado da API tem uma mensagem "**Not Found**", caso venha essa mensagem atualizamos os valores das variáveis `errorMessage`com a mensagem que queremos pegar e `hasError` para **true**.
* 🚨 Um `if` que vai fazer a verificação da `hasError`, caso essa variável seja **true** é lançado uma nova exceção pegando a mensagem que foi armazenada em `errorMessage`;

O componente fica assim agora:

```jsx
import React, { useState } from 'react';
import Loader from '../Loader/Loader'
import UserData from './UserData/UserData'

import './FormUserData.css';

const FormUserData = () => {
  const [showLoader, setShowLoader] = useState(false)
  const [username, setUsername] = useState('')
	const [userData, setUserData] = useState({})
  const [hasError, setHasError] = useState(false)
  const [errorMessage, setErrorMessage] = useState('')
  
  const handleForm = (e) => {
    e.preventDefault();
    setShowLoader(true)
    fetch(`https://api.github.com/users/${username}`)
        .then(res => res.json())
        .then(data => {
          if(data.message === "Not Found") {
            setErrorMessage('Nenhum usuário encontrado, recarrege a pagina')
            setHasError(true)
          }
          setUserData(data)
          setShowLoader(false)
        })
        .catch((err) => {
          setHasError(true)
          setShowLoader(false)
        })
  }

  if(hasError) {
    throw new Error(errorMessage);
  }

  const isObjectEmpty = (myObject) => {
    return Object.entries(myObject).length > 0
  }

  return (
    <>
      <form onSubmit={handleForm} className="form-github-user">
        <label htmlFor="github-user" className="label-github-user">Digite seu usuário do Github:</label>
        <input type="text" id="github-user" className="input-github-user" onChange={e=>setUsername(e.target.value)}/>
        <button type="submit" className="btn-submit">Enviar</button>
      </form>
      {showLoader && <Loader/>}
      <UserData data={userData} />
    </>
  );
}

export default FormUserData;
```

## **Ao vivo**

![Gif mostrando o preenchimento do input "Digite seu usuário do Github" com texto "talitaoliveira", depois clicando no botão enviar e em seguida é mostrado o resultado da buca: uma foto, e dados de nome, localização, bio e dada de criação do perfil (informações retornadas do github). Em seguida é adicionadas letras aleatorias junto ao texto digitado anteriormente, simulando um usuário inexistente, depois cliando em enviar e em seguda é mostrado uma mensagem: "Nenhum usuário encontrado. Recarregue a página"](assets/img/errorboundaryexample.gif "Gif mostrando o preenchimento do input \"Digite seu usuário do Github\" com texto \"talitaoliveira\", depois clicando no botão enviar e em seguida é mostrado o resultado da buca: uma foto, e dados de nome, localização, bio e dada de criação do perfil (informações retornadas do github). Em seguida é adicionadas letras aleatorias junto ao texto digitado anteriormente, simulando um usuário inexistente, depois cliando em enviar e em seguda é mostrado uma mensagem: \"Nenhum usuário encontrado. Recarregue a página\"")

Como o erro é mostrado quando não encontra um usuário, para testar basta adicionar um nome de usuário inexistente.

Pode ser visto aqui: [](https://talitaoliveira.github.io/using-loader-wait-request-react/)<https://talitaoliveira.github.io/using-loader-wait-request-react/>

## Referência:

[](https://pt-br.reactjs.org/docs/error-boundaries.html#gatsby-focus-wrapper)<https://pt-br.reactjs.org/docs/error-boundaries.html#gatsby-focus-wrapper>

\~🌟\~

Bem.. é isso. 😉

Essa semana no projeto estava com Thiago começando a implementação da chamada da API, e Gabriel e Hugo estavam trabalhando na parte da página de erro. Como as duas partes se complementavam, precisamos ficar alinhados como estava sendo feito as duas partes para juntar depois, e eles mostraram que fizeram usando a **ErrorBoundary** e fiquei curiosa para entender melhor como funcionava, e decidi escrever sobre. 🙃

Achei interessante, no começo quando tava testando tava apanhando para fazer funcionar, e descobri que estava errando ao chamar o componente **ErrorBoundary** no mesmo componente que estava sendo lançada a exceção, daí entendi que a exceção precisa ser chamada em componentes filhos da **ErrorBoundary** (passei horas nisso, e quase desistia de postar pq não tava rolando.. Mas foi.. 😛).

Espero que este post possa ser útil para alguém 😊.

Até a próxima. 🤙🏽