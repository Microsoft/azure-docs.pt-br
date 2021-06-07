---
title: Habilitar blocos de anotações na conta de Azure Cosmos DB (versão prévia)
description: Os blocos de anotações internos do Azure Cosmos DB permitem que você analise e visualize seus dados de dentro do Portal. Este artigo descreve como habilitar esse recurso para contas do cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 02e8ad5f2b5326f947ba0bca6456ce9d9d3c27d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692769"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Habilitar blocos de anotações para contas de Azure Cosmos DB (versão prévia)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Os blocos de anotações internos para Azure Cosmos DB estão disponíveis atualmente em [29 regiões](#supported-regions). Para usar blocos de anotações, [crie uma nova conta do cosmos](#create-a-new-cosmos-account) ou [habilite blocos de anotações em uma conta existente](#enable-notebooks-in-an-existing-cosmos-account) em uma dessas regiões. 

Os notebooks Jupyter internos do Azure Cosmos DB permitem que você analise e visualize os dados do portal do Azure. Este artigo descreve como habilitar esse recurso para sua conta do Azure Cosmos DB.

## <a name="create-a-new-cosmos-account"></a>Criar uma nova conta do cosmos
A partir de 10 de fevereiro de 2021, novas contas do Azure Cosmos criadas em uma das [regiões com suporte](#supported-regions) terão automaticamente blocos de anotações habilitados. Não há nenhuma configuração adicional necessária para habilitar os blocos de anotações. Use as instruções a seguir para criar uma nova conta:
1. Faça logon no [Portal do Azure](https://portal.azure.com/).
1. Selecione **Criar um recurso** > **Bancos de dados** > **Azure Cosmos DB**.
1. Insira as configurações básicas para a conta.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="A página da nova conta do Azure Cosmos DB":::

1. Selecione **Examinar + criar**. Você pode ignorar a opção de **rede** e **marcas** . 
1. Examine as configurações da conta e selecione **Criar**. São necessários alguns minutos para criar a conta. Aguarde até que a página do portal exiba **Sua implantação está concluída**.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="O painel Notificações do portal do Azure":::

1. Selecione **Ir para recurso** para ir para a página da conta do Azure Cosmos DB.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="A página da conta do Azure Cosmos DB":::

1. Navegue até o painel de **Data Explorer** . Agora você deve ver seu espaço de trabalho de blocos de anotações.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Espaço de trabalho novo Azure Cosmos DB blocos de anotações":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Habilitar blocos de anotações em uma conta existente do cosmos

Você também pode habilitar blocos de anotações em contas existentes. Esta etapa precisa ser feita apenas uma vez por conta.

1. Navegue até o painel de **Data Explorer** em sua conta do cosmos.
1. Selecione **habilitar blocos de anotações**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Criar um novo espaço de trabalho de blocos de anotações no Data Explorer":::

1. Isso solicitará que você crie um novo espaço de trabalho de blocos de anotações. Selecione **concluir configuração.**
1. Sua conta agora está habilitada para usar blocos de anotações!

## <a name="create-and-run-your-first-notebook"></a>Criar e executar seu primeiro bloco de anotações

Para verificar se você pode usar blocos de anotações, selecione um dos blocos de anotações em blocos de anotações de exemplo. Isso salvará uma cópia do bloco de anotações no espaço de trabalho e o abrirá.

Neste exemplo, usaremos **gettingstarted. ipynb**.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Exibir o GettingStarted. ipynb Notebook":::

Para executar o bloco de anotações:
1. Selecione a primeira célula de código que contém o código Python.
1. Selecione **executar** para executar a célula. Você também pode usar **Shift + Enter** para executar a célula.
1. Atualize o painel de recursos para ver o banco de dados e o contêiner que foram criados.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Executar o bloco de anotações de introdução":::

Você também pode selecionar **novo bloco de anotações** para criar um novo bloco de anotações ou carregar um arquivo de bloco de anotações (. ipynb) existente selecionando **carregar arquivo** no menu **meus blocos de anotações** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Criar ou carregar um novo bloco de anotações":::

## <a name="supported-regions"></a>Regiões com suporte
Os notebooks internos para Azure Cosmos DB estão disponíveis atualmente em 29 regiões do Azure. Novas contas do Azure Cosmos criadas nessas regiões terão blocos de anotações habilitados automaticamente. Os notebooks são gratuitos com sua conta. 

- Austrália Central
- Austrália Central 2
- Leste da Austrália
- Sudeste da Austrália
- Sul do Brasil
- Canadá Central
- Leste do Canadá
- Índia Central
- Centro dos EUA
- Leste dos EUA
- Leste dos EUA 2
- França Central
- Sul da França
- Norte da Alemanha
- Centro-Oeste da Alemanha
- Oeste do Japão
- Sul da Coreia
- Centro-Norte dos EUA
- Norte da Europa
- Centro-Sul dos Estados Unidos
- Sudeste Asiático
- Norte da Suíça
- EAU Central
- Sul do Reino Unido
- Oeste do Reino Unido
- Centro-Oeste dos EUA
- Europa Ocidental
- Oeste da Índia
- Oeste dos EUA 2

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os benefícios do [Azure Cosmos DB notebooks Jupyter](cosmosdb-jupyter-notebooks.md)
* [Explorar galeria de exemplos de notebook](https://cosmos.azure.com/gallery.html)
* [Publicar blocos de anotações na Galeria de notebooks Azure Cosmos DB](publish-notebook-gallery.md)
* [Usar recursos e comandos de notebook Python](use-python-notebook-features-and-commands.md)
* [Usar recursos e comandos de notebook C#](use-csharp-notebook-features-and-commands.md)
* [Importar notebooks de um repositório GitHub](import-github-notebooks.md)
