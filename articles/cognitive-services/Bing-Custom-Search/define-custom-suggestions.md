---
title: Definição de sugestões personalizadas da Sugestão Automática – Pesquisa Personalizada do Bing
titlesuffix: Azure Cognitive Services
description: Descreve como configurar a Sugestão Automática Personalizada com sugestões personalizadas
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: bbad72b41a177bdbafd6cf98bfd2025190d98b16
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128950"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Configurar experiência de sugestão automática personalizada

A sugestão automática personalizada retorna uma lista de cadeias de consulta de pesquisa sugeridas que são relevantes para sua experiência de pesquisa. As cadeias de consulta sugeridas são baseadas em uma cadeia de consulta parcial que o usuário fornece na caixa de pesquisa. A lista conterá no máximo 10 sugestões. 

Você especifique se deseja retornar somente as sugestões personalizadas ou para incluir também as sugestões do Bing. Se você incluir sugestões do Bing, as sugestões personalizadas aparecem antes das sugestões do Bing. Se você fornecer um número suficiente de sugestões relevantes, a lista de sugestões retornada poderá não incluir as sugestões do Bing. As sugestões do Bing estão sempre no contexto da instância de Pesquisa Personalizada. 

Para configurar as sugestões de consulta de pesquisa para sua instância, clique na guia **Sugestão automática**.  

> [!NOTE]
> Para usar esse recurso, você precisa assinar a Pesquisa Personalizada no nível apropriado (confira os [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Pode levar até 24 horas para que as sugestões sejam refletidas no ponto de extremidade de serviço (API ou interface do usuário hospedada).

## <a name="enable-bing-suggestions"></a>Habilitar sugestões do Bing

Para habilitar as sugestões do Bing, alterne o controle deslizante **Sugestões automáticas do Bing** para a posição ativada. O controle deslizante fica azul.

## <a name="add-your-own-suggestions"></a>Adicionar suas próprias sugestões

Para adicionar suas próprias sugestões de cadeia de consulta, adicione-as à lista em **Sugestões definidas pelo usuário**. Depois de adicionar uma sugestão na lista, pressione a tecla Enter ou clique no ícone **+**. Você pode especificar a sugestão em qualquer idioma. Você pode adicionar no máximo 5 mil sugestões de cadeia de consulta.

## <a name="upload-suggestions"></a>Carregar sugestões

Como opção, você pode carregar uma lista de sugestões de um arquivo. O arquivo precisa conter uma cadeia de consulta pesquisa por linha. Para carregar o arquivo, clique no ícone de upload e selecione o arquivo a ser carregado. O serviço extrai as sugestões do arquivo e adiciona-as à lista.

## <a name="remove-suggestions"></a>Remover sugestões

Para remover uma sugestão de cadeia de consulta, clique no ícone de remoção próximo da sugestão que você deseja remover.

## <a name="block-suggestions"></a>Bloquear sugestões

Ao incluir as sugestões do Bing, você poderá adicionar uma lista de cadeias de consulta de pesquisa, que você não quer o Bing retorne. Para adicionar cadeias de consulta bloqueadas, clique em **Mostrar sugestões bloqueadas**. Adicione a cadeia de consulta à lista e pressione a tecla Enter ou clique no ícone **+**. Você pode adicionar no máximo 50 cadeias de consulta bloqueadas.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Pode levar até 24 horas para que as alterações de configuração personalizada de Sugestão Automática Personalizada entram em vigor.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Habilitando a sugestão automática na interface do usuário hospedada

Para habilitar as sugestões de cadeia de consulta para a interface do usuário hospedada, clique em **Interface do usuário hospedada**. Role para baixo até a seção **Configuração Adicional**. Em **Pesquisa na Web**, selecione **Habilitado** para **Habilitar sugestão automática**. Para habilitar a sugestão automática, você precisa selecionar um layout que inclui uma caixa de pesquisa.


## <a name="calling-the-autosuggest-api"></a>Chamando a API de Sugestão Automática

Para obter cadeias de consulta sugeridas usando a API de Pesquisa Personalizada do Bing, envie uma solicitação `GET` ao ponto de extremidade a seguir.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

A resposta contém uma lista de objetos `SearchAction` que contêm as cadeias de consulta sugeridas.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Cada sugestão inclui um campo `displayText` e `query`. O campo `displayText` contém a consulta sugerida que é usada para preencher a lista suspensa da caixa de pesquisa.

Se o usuário selecionar uma cadeia de consulta sugerida na lista suspensa, use a cadeia de consulta no campo `query` ao chamar a [API de Pesquisa Personalizada do Bing](overview.md).


## <a name="next-steps"></a>Próximas etapas

- [Obter sugestões personalizadas](./get-custom-suggestions.md)
- [Pesquisar sua instância personalizada](./search-your-custom-view.md)
- [Configurar e consumir a interface de usuário personalizada hospedada](./hosted-ui.md)
