---
title: Tutorial de MongoDB, Angular e Node para Azure - parte 6 | Microsoft Docs
description: "Parte 6 da série de tutoriais sobre como criar um aplicativo do MongoDB com Angular e Node no Azure Cosmos DB usando exatamente as mesmas APIs usadas para MongoDB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.openlocfilehash: 4dee49f99118cc99c21ce23e32db3686c58cf4a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-6-add-post-put-and-delete-functions-to-the-app"></a>Criar um aplicativo do MongoDB com Angular e Azure Cosmos DB - parte 6: adicionar funções Post, Put e Delete ao aplicativo

Este tutorial com várias partes demonstra como criar uma novo aplicativo [API do MongoDB](mongodb-introduction.md) escrito em Node.js com Express e Angular e conectá-lo ao seu banco de dados do Azure Cosmos DB.

A Parte 6 do tutorial se baseia na [Parte 5](tutorial-develop-mongodb-nodejs-part5.md) e inclui as seguintes tarefas:

> [!div class="checklist"]
> * Criar funções Post, Put e Delete para o serviço hero
> * Execute o aplicativo

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, verifique se você concluiu as etapas na [Parte 5](tutorial-develop-mongodb-nodejs-part5.md) do tutorial.

> [!TIP]
> Este tutorial percorre passo a passo com você as etapas para compilar o aplicativo. Se você deseja baixar o projeto concluído, pode obter o aplicativo concluído no [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) do GitHub.

## <a name="add-a-post-function-to-the-hero-service"></a>Adicionar uma função Post ao serviço hero

1. No Visual Studio Code, abra **routes.js** e **hero.service.js** lado a lado pressionando o botão **Editor de Divisão** ![Botão Editor de Divisão no Visual Studio ](./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png).

    Veja que a linha 7 de routes.js etá chamando a função `getHeroes` na linha 5 do **hero.service.js**.  Precisamos criar esse mesmo emparelhamento para as funções post, put e delete. 

    ![routes.js e hero.service.js no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png)
    
    Vamos iniciar codificando o serviço hero. 

2. Copie o código a seguir em **hero.service.js** depois da função `getHeroes` e antes de `module.exports`. Esse código:  
   * Usa o modelo de hero para postar um novo hero.
   * Verifica as respostas para ver se há um erro e retorna um valor de status de 500.

   ```javascript
   function postHero(req, res) {
     const originalHero = { id: req.body.id, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. Em **hero.service.js**, atualize o `module.exports` para incluir a nova função `postHero`. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. Em **routes.js**, adicione um roteador à função `post` depois do roteador `get`. Esse roteador posta um hero por vez. Estruturar o arquivo de roteador dessa maneira mostra claramente todos os pontos de extremidade de API disponíveis e deixa o trabalho de verdade para o arquivo **hero.service.js**.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Verifique se tudo funcionou executando o aplicativo. No Visual Studio Code, salve todas as suas alterações, clique no botão **Depurar** ![Ícone de depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png) no lado esquerdo e clique no botão **Iniciar depuração** ![Ícone Iniciar depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png).

6. Agora volte para o navegador da Internet e abra a guia Rede de ferramentas de Desenvolvedor pressionando F12 na maioria dos computadores. Navegue até [http://localhost:3000](http://localhost:3000) para observar as chamadas feitas pela rede.

    ![Guia Rede no Chrome que mostra a atividade de rede](./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png)

7. Adicione um novo hero clicando no botão **Adicionar Novo Hero**. Insira uma ID "999", nome "Vinicius" dizendo "Olá" e clique em **Salvar**. Você deve ver na guia Rede que enviou uma solicitação POST para um novo hero. 

    ![Guia Rede no Chrome que mostra a atividade de rede para as funções Get e Post](./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png)

    Agora vamos voltar e adicionar as funções Put e Delete ao aplicativo.

## <a name="add-the-put-and-delete-functions"></a>Adicionar as funções Put e Delete

1. Em **routes.js**, adicione os roteadores `put` e `delete` depois do roteador de post.

    ```javascript
    router.put('/hero/:id', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:id', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Copie o código a seguir em **hero.service.js** depois da função `checkServerError`. Esse código:
   * Cria as funções `put` e `delete`
   * Verifica se o hero foi encontrado
   * Executa o tratamento de erro 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       id: parseInt(req.params.id, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ id: originalHero.id }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const id = parseInt(req.params.id, 10);
     Hero.findOneAndRemove({ id: id })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. Em **hero.service.js**, exporte os novos módulos:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Agora que nós atualizamos o código, clique no botão **Reiniciar** ![Botão Reiniciar no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png) no Visual Studio Code.

5. Atualize a página no navegador e clique no botão **Adicionar Novo Hero**. Adicione um novo hero com uma ID "9", nome "Starlord" dizendo "Olá". Clique no botão **Salvar** para salvar o novo hero.

6. Agora selecione o hero **Starlord** e altere a narração de "Olá" para "Tchau" e clique no botão **Salvar**. 

    Agora você pode selecionar a ID na guia Rede para mostrar a carga. Você pode ver na carga que a fala agora está definida como "Tchau".

    ![Aplicativo Heroes e guia Rede mostrando a carga](./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png) 

    Você também pode excluir um hero na interface de usuário e ver o tempo que leva para concluir a operação de exclusão. Tente isso clicando no botão "Excluir" para o hero chamado "Vinicius".

    ![Aplicativo Heroes e guia Rede mostrando o tempo que leva para concluir as funções](./media/tutorial-develop-mongodb-nodejs-part6/times.png) 

    Se você atualizar a página, a guia Rede mostrará o tempo necessário para obter os heroes. Quando leva pouco tempo, grande parte disso depende de onde seus dados estão localizados no mundo e da sua capacidade de replicá-lo geograficamente em uma área perto de seus usuários. Você pode encontrar mais informações sobre a replicação geográfica no próximo tutorial que será lançado em breve.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Adicionou funções Post, Put e Delete ao aplicativo 

Confira esta página em breve para ver outros vídeos desta série de tutoriais.

