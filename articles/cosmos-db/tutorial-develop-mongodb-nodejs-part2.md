---
title: Criar um aplicativo Node.js Express com a API do Azure Cosmos DB para MongoDB (Parte 2)
description: Parte 2 da série de tutoriais sobre como criar um aplicativo do MongoDB com Angular e Node no Azure Cosmos DB usando exatamente as mesmas APIs usadas para MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: e3af713d6e120e66a0cde5116f4556bb8caedf55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097833"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Criar um aplicativo Angular com a API do Azure Cosmos DB para MongoDB – criar um aplicativo Node.js Express
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este tutorial com várias partes demonstra como criar um aplicativo escrito em Node.js com o Express e o Angular e conectá-lo à sua [conta do Cosmos configurada com a API do Cosmos DB para MongoDB](mongodb-introduction.md).

A Parte 2 do tutorial se baseia na [introdução](tutorial-develop-mongodb-nodejs.md) e inclui as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a CLI do Angular e o TypeScript
> * Criar um novo projeto usando Angular
> * Criar o aplicativo usando a estrutura do Express
> * Testar o aplicativo no Postman

## <a name="video-walkthrough"></a>Passo a passo em vídeo

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, não deixe de assistir ao [vídeo de introdução](tutorial-develop-mongodb-nodejs.md).

Este tutorial também requer: 
* Versão do [Node.js](https://nodejs.org/) 8.4.0 ou superior.
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) ou seu editor de código favorito.

> [!TIP]
> Este tutorial percorre passo a passo com você as etapas para compilar o aplicativo. Se você deseja baixar o projeto concluído, pode obter o aplicativo concluído no [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) do GitHub.

## <a name="install-the-angular-cli-and-typescript"></a>Instalar a CLI do Angular e o TypeScript

1. Abra um prompt de comando do Windows ou uma janela de terminal do Mac e instale a CLI do Angular.

    ```bash
    npm install -g @angular/cli
    ```

2. Instale o TypeScript digitando o comando a seguir no prompt. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Usar a CLI do Angular para criar um novo projeto

1. No prompt de comando, altere para a pasta em que você deseja criar o novo projeto e execute o comando a seguir. Este comando cria uma nova pasta e o projeto chamado angular-cosmosdb e instala os componentes do Angular necessários para um novo aplicativo. Ele usa a configuração mínima (--minimal) e especifica que o projeto usa Sass (uma sintaxe semelhante a CSS com o sinalizador scss --style).

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. Depois de concluir o comando, altere os diretórios na pasta src/client.

    ```bash
    cd angular-cosmosdb
    ```

3. Em seguida, abra a pasta no Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Crie o aplicativo usando a estrutura do Express

1. No Visual Studio Code, no painel **Explorer**, clique com o botão direito do mouse na pasta **src**, clique em **Nova Pasta** e nomeie a nova pasta *servidor*.

2. No painel **Explorer**, clique com o botão direito do mouse na pasta **servidor**, clique em **Novo Arquivo** e nomeie o novo arquivo *index.js*.

3. Novamente no prompt de comando, use o comando a eguir para instalar o analisador de corpo. Isso ajuda nosso aplicativo a analisar os dados JSON que são transmitidos pelas APIs.

    ```bash
    npm i express body-parser --save
    ```

4. No Visual Studio Code, copie o código a seguir no arquivo js. Esse código:
    * Referencia o Express
    * Extrai o analisador de corpo para ler dados JSON no corpo das solicitações
    * Usa um recurso interno denominado caminho
    * Define conjuntos de variáveis para facilitar a localização do nosso código
    * Configura uma porta
    * Inicia o Express
    * Informa ao aplicativo como usar o middleware que seria usado para servir o servidor
    * Serve tudo o que está na pasta dist, que será o conteúdo estático
    * Serve o aplicativo e o index.html para todas as solicitações GET não encontradas no servidor (para deep links)
    * Inicia o servidor com app.listen
    * Usa uma função arrow para registrar que a porta está ativa
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. No Visual Studio Code, no painel **Explorer**, clique com o botão direito do mouse na pasta **servidor** e clique em **Novo arquivo**. Chame o novo arquivo de *routes.js*. 

6. Copie o código a seguir em **routes.js**. Esse código:
   * Referencia o roteador Express
   * Obtém os heroes
   * Envia o JSON para um hero definido

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Salva todos os arquivos modificados. 

8. No Visual Studio Code, clique no botão **Depurar** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: e no botão de engrenagem :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png":::. O novo arquivo launch.json abre no Visual Studio Code. 

8. Na linha 11 do arquivo launch.json, altere `"${workspaceFolder}\\server"` para `"program": "${workspaceRoot}/src/server/index.js"` e salve o arquivo.

9. Clique no botão **Iniciar Depuração** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png"::: para executar o aplicativo.

    O aplicativo deve ser executado sem erros.

## <a name="use-postman-to-test-the-app"></a>Usar o Postman para testar o aplicativo

1. Abra o Postman e coloque `http://localhost:3000/api/heroes` na caixa GET. 

2. Clique no botão **Enviar** e obtenha a resposta json do aplicativo. 

    Essa resposta mostra que o aplicativo está em execução localmente. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png" alt-text="Postman mostrando a solicitação e a resposta":::


## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Criou um projeto Node.js usando a CLI do Angular
> * Testou o aplicativo usando o Postman

Você pode prosseguir para a próxima parte do tutorial a fim de criar a interface do usuário.

> [!div class="nextstepaction"]
> [Criar a interface do usuário com Angular](tutorial-develop-mongodb-nodejs-part3.md)
