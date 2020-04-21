# Conceitos básicos de React Native
**Um simples aplicativo que faz requisições como GET e POST para nossa [api](https://github.com/AdeLuigi/conceitos-basicos-nodejs "Clique e veja como desenvolver a api")**


Antes de começar este tutorial é de suma importancia que você já tenha a nossa [api](https://github.com/AdeLuigi/conceitos-basicos-nodejs "Clique e veja como desenvolver a api") clonada e rodando em seu ambiente de desenvolvimento, pois criaremos um fron-end que consumirá os dados dessa [api](https://github.com/AdeLuigi/conceitos-basicos-nodejs "Clique e veja como desenvolver a api").

---
## Rodando o projeto
````git
$ git clone https://github.com/AdeLuigi/conceitos-basicos-react-native.git
$ cd conceitos-basicos-react-native
$ yarn
````
**Neste momento o servidor já deve estar pronto para rodar**
````git
$ yarn android
````
## O que você verá implementado?
* Uso da biblioteca Axios para nos auxiliar a fazer requisções para a nossa [api](https://github.com/AdeLuigi/conceitos-basicos-nodejs "Clique e veja como desenvolver a api").
* Utilização dos React Hooks como useState e useEffect.
* Listagem e adição de likes👍 em nosso repositório.
---
## A aplicação
````javascript
import React, { useEffect, useState } from "react";
import api from './services/api';

import {
  SafeAreaView,
  View,
  FlatList,
  Text,
  StatusBar,
  StyleSheet,
  TouchableOpacity,
} from "react-native";


export default function App() {
  const [ repositories, setRepository ] = useState([]);

  useEffect(() => {
    api.get('repositories').then(response => {
      setRepository(response.data)
    })
  },[])

  async function handleLikeRepository(id) {
    const response = await api.post(`repositories/${id}/like`);
    const update = response.data;

    const  repositoryUpdate = repositories.map(repository => {
      if(repository.id === id){
        return update
      }else{
        return repository
      }
      
    })
    setRepository(repositoryUpdate)
  }

  return (
    <>
      <StatusBar barStyle="light-content" backgroundColor="#7159c1" />
      <SafeAreaView style={styles.container}>
        <FlatList
            data={repositories}
            keyExtractor={repository => repository.id}
            renderItem={({ item:repository }) => (
            <View style={styles.repositoryContainer}>
            <Text style={styles.repository}>{repository.title}</Text>
                <FlatList 
                data={repository.techs}
                horizontal
                keyExtractor={tech => tech}
                renderItem={({item:tech}) =>
                    <View style={styles.techsContainer}>
                    <Text style={styles.tech}>{tech}</Text>
                    </View>
                }
                />
            <View style={styles.likesContainer}>
                <Text
                style={styles.likeText}
                // Remember to replace "1" below with repository ID: {`repository-likes-${repository.id}`}
                testID={`repository-likes-${repository.id}`}
                >
                {repository.likes} curtidas
                </Text>
            </View>
            <TouchableOpacity
                style={styles.button}
                onPress={() => handleLikeRepository(repository.id)}
                // Remember to replace "1" below with repository ID: {`like-button-${repository.id}`}
                testID={`like-button-${repository.id}`}
            >
                <Text style={styles.buttonText}>Curtir</Text>
            </TouchableOpacity>
            </View>
            )}
        />
      </SafeAreaView>
    </>
  );
}
````
Como diria Jack o Estripador, vamos por partes. A primeira linha representa os imports dos Hooks (useStete e useEffect) e do React, sempre que vamos desenvolver uma aplicação que use o React nós precisamos importar o React. 
````javascript
import React, { useEffect, useState } from "react";
````
Logo em seguida nós iremos importar a [api](https://github.com/AdeLuigi/conceitos-basicos-nodejs "Clique e veja como desenvolver a api"), com essa função nós conseguiremos fazer requicições para o nosso back-end.
````javascript
import api from './services/api';
````
No React Native não existem elementos html como h1, p, ul, ao inves disso nós utilizamos components do RN, parece meio confuso e é, mas vou explicar. <br>Na web nós utilizamos a tag div para declarar um container, no RN nós utilizamos a View para isso, na web nós utilizamos h1 para declarar um texto de importancia na página, no RN nós usamos o Text.

Todos os elementos do React Native não tem valor semantico, se na web o **H1** é mais importante que o **H2**, no RN Text é Text, não existe uma distinção.
````javascript
import {
  SafeAreaView,
  View,
  FlatList,
  Text,
  StatusBar,
  StyleSheet,
  TouchableOpacity,
} from "react-native";
````
Depois de importar todos os components que precisaremos para a criação do app, tá na hora de criar o component App. logo em seguida nós desestruturamos a função useState, nós utilizaremos a variável **repositories** para armazenar os repositórios e a função **setRepository** para nudar o valor de **repositories** indiretamente.
````javascript
export default function App() {
  const [ repositories, setRepository ] = useState([]);
````
Quando iniciarmos o aplicativo nós queremos que todos os repositórios sejam carregados, por isso utilizaremos o hook **useEffect** para nos auxiliar nesta tarefa. O hook **useEffect** recebe dois parâmetros, o primeiro é a função que será executada e o segundo a variável que será observada, passando o segundo parâmetro como vazio o hook só será chamado uma vez quando o app carregar, aqui nós faremos uma requisição do tipo get para a nossa api e iremos obter todos os repositórios cadastrados.
````javascript
useEffect(() => {
  api.get('repositories').then(response => {
      setRepository(response.data)
  })
},[]);
````
A função **handleLikeRepository** ficará encarregada de adicionar likes no repositório. Toda vez que o botão like for apertado faremos um post na rota de like daquele repositório, depois nós iremos alterar o valor da variável repositories com os novos valores de likes👍 daquele repositório.
````javascript
async function handleLikeRepository(id) {
  const response = await api.post(`repositories/${id}/like`);
  const update = response.data;

  const  repositoryUpdate = repositories.map(repository => {
    if(repository.id === id){
      return update
    }else{
      return repository
    }
    
  })
  setRepository(repositoryUpdate)
}
````
Em seguida nós temos o que será renderizado pelo component App:
````html
  return (
    <>
      <StatusBar barStyle="light-content" backgroundColor="#7159c1" />
      <SafeAreaView style={styles.container}>
        <FlatList
            data={repositories}
            keyExtractor={repository => repository.id}
            renderItem={({ item:repository }) => (
            <View style={styles.repositoryContainer}>
            <Text style={styles.repository}>{repository.title}</Text>
                <FlatList 
                data={repository.techs}
                horizontal
                keyExtractor={tech => tech}
                renderItem={({item:tech}) =>
                    <View style={styles.techsContainer}>
                    <Text style={styles.tech}>{tech}</Text>
                    </View>
                }
                />
            <View style={styles.likesContainer}>
                <Text
                style={styles.likeText}
                // Remember to replace "1" below with repository ID: {`repository-likes-${repository.id}`}
                testID={`repository-likes-${repository.id}`}
                >
                {repository.likes} curtidas
                </Text>
            </View>
            <TouchableOpacity
                style={styles.button}
                onPress={() => handleLikeRepository(repository.id)}
                // Remember to replace "1" below with repository ID: {`like-button-${repository.id}`}
                testID={`like-button-${repository.id}`}
            >
                <Text style={styles.buttonText}>Curtir</Text>
            </TouchableOpacity>
            </View>
            )}
        />
      </SafeAreaView>
    </>
  );
}
````
Como no 