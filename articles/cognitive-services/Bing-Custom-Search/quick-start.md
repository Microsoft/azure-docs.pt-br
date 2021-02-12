---
title: 'Início Rápido: criar a primeira instância da Pesquisa Personalizada do Bing'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para criar uma instância personalizada do Bing que pode pesquisar os domínios e as páginas da Web que você definir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 726a60d611abc392bc1a4629c5088ca4c6b703d9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338326"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Início Rápido: criar a primeira instância da Pesquisa Personalizada do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Para usar a Pesquisa Personalizada do Bing, você precisa criar uma instância de pesquisa personalizada que defina o modo de exibição ou a fatia da web. Essa instância conterá os domínios públicos, os sites e as páginas da Web que você deseja pesquisar, juntamente com os ajustes de classificação que possa querer. 

Para criar a instância, use o [portal da Pesquisa Personalizada do Bing](https://customsearch.ai). 

![Uma imagem do portal de Pesquisa Personalizada do Bing](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada

Para criar uma instância de Pesquisa Personalizada do Bing:

1. Clique em **Começar** na página da Web [portal de Pesquisa Personalizada do Bing](https://customsearch.ai) e entre com sua conta Microsoft.

2. Clique em **Nova Instância** e insira um nome descritivo. Você pode alterar o nome da sua instância a qualquer momento.
 
3. Na guia **Ativo** em **Experiência de Pesquisa**, insira a URL de um ou mais sites que você quer incluir em sua pesquisa. 

    > [!NOTE]
    > Instâncias de Pesquisa Personalizada do Bing retornarão apenas os resultados de domínios e páginas da Web públicos e indexados pelo Bing.

4. Você pode usar o lado direito do portal de Pesquisa Personalizada do Bing para inserir uma consulta e examinar os resultados da pesquisa retornados por sua instância de pesquisa. Se nenhum resultado for retornado, tente inserir uma URL diferente.  

5. Clique em **Publicar** para publicar suas alterações no ambiente de produção e atualizar os pontos de extremidade da instância.

6.  Clique na guia **Produção** e em **Pontos de Extremidade**, copie sua **ID de Configuração Personalizada**. Você precisará dessa ID para chamar a API de Pesquisa Personalizada acrescentando-a ao parâmetro de consulta `customconfig=` em suas chamadas.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Chamar o ponto de extremidade da Pesquisa Personalizada do Bing](./call-endpoint-csharp.md)