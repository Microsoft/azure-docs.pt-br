---
title: Tutorial de MongoDB, React e Node.js para Azure | Microsoft Docs
description: "Saiba como criar um aplicativo do MongoDB com React e Node.js no Azure Cosmos DB usando exatamente as mesmas APIs usadas para MongoDB com esta série de tutoriais em vídeo."
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
ms.custom: mvc
ms.openlocfilehash: 622a26da8b7d5f5c3a242c5c88a4ae479095bf6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Criar um aplicativo do MongoDB com React e Azure Cosmos DB  

Este tutorial em vídeo com várias partes demonstra como criar um aplicativo de acompanhamento de hero com um front-end em React. O aplicativo usou Node e Express para o servidor, se conecta ao Azure Cosmos DB com a [API MongoDB](mongodb-introduction.md)e se conecta ao front-end em React para a parte de servidor do aplicativo. O tutorial também demonstra como fazer o dimensionamento de apontar e clicar do Azure Cosmos DB no portal do Azure e como implantar o aplicativo na Internet para que todos possam acompanhar seus heroes favoritos. 

O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) dá suporte a conexões de cliente do MongoDB e, portanto, você pode usar o Azure Cosmos DB no lugar do MongoDB, mas com o mesmo código que usa para aplicativos do MongoDB; só que com benefícios adicionais, como implantação em nuvem simples, dimensionamento e leitura e gravação muito rápidas.  

Este tutorial com várias partes aborda as seguintes tarefas:

> [!div class="checklist"]
> * Introdução
> * Configurar o projeto
> * Criar a interface do usuário com o React
> * Criar uma conta do Azure Cosmos DB usando o portal do Azure
> * Usar o Mongoose para se conectar ao Azure Cosmos DB
> * Adicionar operações React, Criar, Atualizar e Excluir ao aplicativo

Deseja criar esse mesmo aplicativo com Angular? Confira a [série de tutoriais em vídeo do Angular](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Projeto concluído
Obter o aplicativo completo [do GitHub](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Introdução 

Neste vídeo, Burke Holland oferece uma introdução ao Azure Cosmos DB e orienta você por meio do aplicativo que é criado nesta série de vídeos. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Configuração do projeto

Este vídeo mostra como configurar o Express e o React no mesmo projeto. Burke, em seguida, fornece um passo a passo do código no projeto.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Criar a interface do usuário

Este vídeo mostra como criar a IU (interface do usuário) com o React. 

> [!NOTE]
> O CSS referenciado neste vídeo pode ser encontrado no [repositório GitHub react-cosmosdb](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Conectar-se ao Azure Cosmos DB

Este vídeo mostra como criar uma conta do Azure Cosmos DB no portal do Azure, instalar os pacotes do MongoDB e do Mongoose e conectar o aplicativo à conta recém-criada usando a cadeia de conexão do Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Ler e criar heroes no aplicativo

Este vídeo mostra como ler e criar heroes no banco de dados do Cosmos DB e como testar esses métodos usando o Postman e a IU do React. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Excluir e atualizar heroes no aplicativo

Este vídeo mostra como excluir e atualizar heroes no aplicativo e exibir as atualizações na interface do usuário. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Concluir o aplicativo

Este vídeo mostra como concluir o aplicativo e concluir a conexão da interface do usuário com a API de back-end. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, siga as seguintes etapas para excluir todos os recursos criados neste tutorial no portal do Azure. 

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um aplicativo com React, Node, Express e Azure Cosmos DB 
> * Criar uma conta do Azure Cosmos DB
> * Conectar o aplicativo à conta do Azure Cosmos DB
> * Testar o aplicativo usando o Postman
> * Executar o aplicativo e adicionar heroes ao banco de dados

Volte aqui para conferir outros vídeos desta série de tutoriais que abordarão a implantação do aplicativo e a replicação global dos seus dados.

Agora, você pode seguir para o próximo tutorial e saber como importar dados do MongoDB para o Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importar dados do MongoDB no BD Cosmos do Azure](mongodb-migrate.md)
 
