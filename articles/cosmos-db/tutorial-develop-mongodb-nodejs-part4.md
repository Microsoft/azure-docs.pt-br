---
title: Tutorial de MongoDB, Angular e Node para Azure - parte 4 | Microsoft Docs
description: "Parte 4 da série de tutoriais sobre como criar um aplicativo do MongoDB com Angular e Node no Azure Cosmos DB usando exatamente as mesmas APIs usadas para MongoDB"
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
ms.openlocfilehash: 6fd88c58c0067deab08d502be4bd624c297b5fde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Criar um aplicativo do MongoDB com Angular e Azure Cosmos DB - parte 4: criar uma conta do Azure Cosmos DB usando a CLI do Azure

Este tutorial com várias partes demonstra como criar uma novo aplicativo [API do MongoDB](mongodb-introduction.md) escrito em Node.js com Express, Angular e seu banco de dados do Azure Cosmos DB.

A Parte 4 do tutorial se baseia na [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) e inclui as seguintes tarefas:

> [!div class="checklist"]
> * Criar um grupo de recursos do Azure usando a CLI do Azure
> * Criar uma conta do Azure Cosmos DB usando a CLI do Azure

## <a name="video-walkthrough"></a>Passo a passo em vídeo

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, verifique se você concluiu as etapas na [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) do tutorial. 

Nesta seção do tutorial, você pode usar o Azure Cloud Shell (em seu navegador) ou a [CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) instalado localmente. Se você usar a CLI do Azure localmente, verifique se está executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` no prompt de comando para verificar a versão. 

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Este tutorial percorre passo a passo com você as etapas para compilar o aplicativo. Se você deseja baixar o projeto concluído, pode obter o aplicativo concluído no [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) do GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Criar uma conta do Azure Cosmos DB com o comando [`az cosmosdb create`](/cli/azure/cosmosdb#create).

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* No `<cosmosdb-name>`, use o nome exclusivo da sua conta do Azure Cosmos DB; o nome precisa ser exclusivo entre todos os nomes de conta do Azure Cosmos DB no Azure.
* A configuração `--kind MongoDB` permite ao Azure Cosmos DB ter conexões de cliente MongoDB.

Pode levar um minuto ou dois para o comando ser concluído. Quando tiver terminado, a janela do terminal exibirá informações sobre o novo banco de dados. 

Depois que a conta do Azure Cosmos DB foi criada:
1. Abra uma nova janela do navegador e vá para [https://portal.azure.com](https://portal.azure.com)
1. Clique no logotipo do Azure Cosmos DB ![Ícone do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) na barra de ferramentas à esquerda e ele mostrará todos os Azure Cosmos DBs que você tem.
1. Clique na conta do Azure Cosmos DB que você acabou de criar, selecione a guia **Visão Geral** e role para baixo para exibir o mapa onde se encontra o banco de dados. 

    ![Nova conta do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. Role para baixo no painel de navegação esquerdo e clique na guia **Replicar dados globalmente**; ela exibe um mapa em que você pode ver as diferentes áreas em que pode replicar. Por exemplo, você pode clicar em Sudeste da Austrália ou Leste da Austrália e replicar os dados n a Austrália. Você pode aprender mais sobre replicação global em [Como distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md). Por enquanto, vamos apenas manter uma instância; saberemos como replicá-la se for o caso.

    ![Nova conta do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Criou um grupo de recursos do Azure usando a CLI do Azure
> * Criou uma conta do Azure Cosmos DB usando a CLI do Azure

Você pode prosseguir para a próxima parte do tutorial a fim de conectar o Azure Cosmos DB ao aplicativo usando Mongoose.

> [!div class="nextstepaction"]
> [Usar o Mongoose para se conectar ao Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)
