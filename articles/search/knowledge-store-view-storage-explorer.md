---
title: Exibir um repositório de conhecimento com o Gerenciador de Armazenamento
titleSuffix: Azure Cognitive Search
description: Exiba e analise um repositório de conhecimento da Pesquisa Cognitiva do Azure com o Gerenciador de Armazenamento do portal do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85566028"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Exibir um repositório de conhecimento com o Gerenciador de Armazenamento

Neste artigo, você aprenderá, por exemplo, como se conectar e explorar uma loja de conhecimento usando Gerenciador de Armazenamento no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

+ Siga as etapas em [Criar um repositório de conhecimento no portal do Azure](knowledge-store-create-portal.md) para criar o repositório de conhecimento de exemplo usado neste passo a passo.

+ Também será necessário o nome da conta de armazenamento do Azure que você usou para criar o repositório de conhecimento, junto com sua chave de acesso do portal do Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Exibir, editar e consultar uma loja de conhecimento em Gerenciador de Armazenamento

1. Na portal do Azure, [abra a conta de armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que você usou para criar a loja de conhecimento.

1. No painel de navegação esquerdo da conta de armazenamento, clique em **Gerenciador de Armazenamento**.

1. Expanda a lista **TABELAS** para mostrar uma lista de projeções de tabela do Azure que foram criadas quando você executou o assistente de **Importação de Dados** em seus dados de exemplo de resenhas de hotéis.

Selecione qualquer tabela para exibir os dados aprimorados, incluindo frases-chave e pontuações de opiniões.

   ![Exibir tabelas no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Exibir tabelas no Gerenciador de Armazenamento")

Para alterar o tipo de dados para qualquer valor de tabela ou para alterar valores individuais em sua tabela, clique em **Editar**. Quando você alterar o tipo de dados para qualquer coluna na linha de tabela, ele será aplicado a todas as linhas.

   ![Editar tabelas no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Editar tabelas no Gerenciador de Armazenamento")

Para executar consultas, clique em **Consultar** na barra de comandos e insira suas condições.  

   ![Consultar tabelas no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Consultar tabelas no Gerenciador de Armazenamento")

## <a name="clean-up"></a>Limpeza

Quando já estiver trabalhando na sua assinatura, analise se você ainda precisa dos recursos criados no fim de um projeto. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Próximas etapas

Conecte essa loja de conhecimento para Power BI para análise mais profunda ou avance com código, usando a API REST e o postmaster para criar um repositório de conhecimento diferente.

> [!div class="nextstepaction"]
> [Conectar-se com Power bi](knowledge-store-connect-power-bi.md) 
>  [Criar um repositório de conhecimento em repouso](knowledge-store-create-rest.md)
