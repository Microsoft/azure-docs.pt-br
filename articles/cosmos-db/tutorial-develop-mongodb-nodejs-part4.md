---
title: Criar um aplicativo Angular com a API do Azure Cosmos DB para MongoDB (Parte 1)
description: Parte 4 da série de tutoriais sobre como criar um aplicativo do MongoDB com Angular e Node no Azure Cosmos DB usando exatamente as mesmas APIs usadas para MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js, devx-track-azurecli
ms.reviewer: sngun
ms.openlocfilehash: 99edeff93bdf75596eea80a238e159548f00679c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790186"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Criar um aplicativo Angular com a API do Azure Cosmos DB para MongoDB – criar uma conta do Cosmos
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este tutorial com várias partes demonstra como criar um aplicativo escrito em Node.js com o Express e o Angular e conectá-lo à sua [conta do Cosmos configurada com a API do Cosmos DB para MongoDB](mongodb-introduction.md).

A Parte 4 do tutorial se baseia na [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) e inclui as seguintes tarefas:

> [!div class="checklist"]
> * Criar um grupo de recursos do Azure usando a CLI do Azure
> * Criar uma conta do Cosmos usando a CLI do Azure

## <a name="video-walkthrough"></a>Passo a passo em vídeo

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, verifique se você concluiu as etapas na [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) do tutorial. 

Nesta seção do tutorial, você pode usar o Azure Cloud Shell (em seu navegador da Internet) ou a [CLI do Azure](/cli/azure/install-azure-cli) instalada localmente.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Este tutorial percorre passo a passo com você as etapas para compilar o aplicativo. Se você deseja baixar o projeto concluído, pode obter o aplicativo concluído no [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) do GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Criar uma conta do Azure Cosmos DB com o comando [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create).

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* No `<cosmosdb-name>`, use o nome exclusivo da sua conta do Azure Cosmos DB; o nome precisa ser exclusivo entre todos os nomes de conta do Azure Cosmos DB no Azure.
* A configuração `--kind MongoDB` permite ao Azure Cosmos DB ter conexões de cliente MongoDB.

Pode levar um minuto ou dois para o comando ser concluído. Quando tiver terminado, a janela do terminal exibirá informações sobre o novo banco de dados. 

Depois que a conta do Azure Cosmos DB foi criada:
1. Abra uma nova janela do navegador e acesse [https://portal.azure.com](https://portal.azure.com)
1. Clique no logotipo do Azure Cosmos DB :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png"::: na barra esquerda e ele mostrará todos os Azure Cosmos DBs que você tem.
1. Clique na conta do Azure Cosmos DB que você acabou de criar, selecione a guia **Visão Geral** e role para baixo para exibir o mapa onde se encontra o banco de dados. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png" alt-text="A captura de tela mostra a Visão geral de uma conta do Azure Cosmos DB.":::

4. Role para baixo no painel de navegação esquerdo e clique na guia **Replicar dados globalmente**; ela exibe um mapa em que você pode ver as diferentes áreas em que pode replicar. Por exemplo, você pode clicar em Sudeste da Austrália ou Leste da Austrália e replicar os dados n a Austrália. Você pode aprender mais sobre replicação global em [Como distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md). Por enquanto, vamos apenas manter uma instância; saberemos como replicá-la se for o caso.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png" alt-text="A captura de tela mostra uma Conta do Azure Cosmos DB com a opção Replicar dados globalmente selecionada.":::

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Criou um grupo de recursos do Azure usando a CLI do Azure
> * Criou uma conta do Azure Cosmos DB usando a CLI do Azure

Você pode prosseguir para a próxima parte do tutorial a fim de conectar o Azure Cosmos DB ao aplicativo usando Mongoose.

> [!div class="nextstepaction"]
> [Usar o Mongoose para se conectar ao Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)