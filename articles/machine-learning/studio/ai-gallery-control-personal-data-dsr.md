---
title: Gerenciar dados da Galeria de IA do Azure
titleSuffix: Azure Machine Learning Studio
description: Você pode exportar e excluir seus dados de usuário do produto da Galeria de IA do Azure usando a interface ou a API de Catálogo da Galeria de IA. Este artigo mostra como fazer isso.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 44ff2a5b723c086604acf39e9f975deb53759ae1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752038"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Exibir e excluir dados de usuário do produto da Galeria de IA do Azure

Você pode exibir e excluir seus dados de usuário do produto da Galeria de IA do Azure usando a interface ou a API de Catálogo da Galeria de IA. Este artigo mostra como fazer isso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Exibir os dados na Galeria de IA com a interface do usuário

Você pode exibir os itens publicados por meio da interface do usuário do site da Galeria de IA do Azure. Os usuários podem exibir soluções públicas e não listadas, projetos, experiências e outros itens publicados:

1.  Conecte-se à [Galeria de IA do Azure](https://gallery.azure.ai/).
2.  Clique na imagem de perfil no canto superior direito e, em seguida, no nome da conta para carregar a página do seu perfil.
3.  A página do perfil exibe todos os itens publicados na galeria, incluindo as entradas não listadas.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Use a API do Catálogo da Galeria de IA para exibir seus dados

Você pode programaticamente exibir dados coletados por meio da API do Catálogo da Galeria de IA, que pode ser acessada em https://catalog.cortanaanalytics.com/entities. Para exibir dados, é necessária a ID do autor. Para exibir as entidades não listadas por meio da API de Catálogo, é necessário um token de acesso.

As respostas de catálogo são retornadas no formato JSON.

### <a name="get-an-author-id"></a>Obter uma ID do autor
A ID do autor baseia-se no endereço de email usado ao publicar na Galeria de IA do Azure. Ela não altera:

1.  Conecte-se à [Galeria de IA do Azure](https://gallery.azure.ai/).
2.  Clique na imagem de perfil no canto superior direito e, em seguida, no nome da conta para carregar a página do seu perfil.
3.  A URL na barra de endereços exibe a ID alfanumérica após `authorId=`. Por exemplo, para a URL: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    ID do autor: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Obter o token de acesso

Você precisa de um token de acesso para exibir as entidades não listadas por meio da API de Catálogo. Sem um Token de acesso, os usuários ainda podem exibir as entidades públicas e outras informações do usuário.

Para obter um token de acesso, você precisa inspecionar o `DataLabAccessToken` cabeçalho de uma solicitação HTTP que o navegador faz para a API de catálogo enquanto estiver conectado:

1.  Conecte-se à [Galeria de IA do Azure](https://gallery.azure.ai/).
2.  Clique na imagem de perfil no canto superior direito e, em seguida, no nome da conta para carregar a página do seu perfil.
3.  Abra o painel Ferramentas para Desenvolvedores do navegador pressionando F12, selecione a guia Rede e atualize a página. 
4. Filtre solicitações na cadeia de caracteres *catálogo* digitando na caixa de texto Filtro.
5.  Em solicitações para a URL `https://catalog.cortanaanalytics.com/entities`, localize uma solicitação GET e selecione a guia *Cabeçalhos*. Role para baixo até a seção *Cabeçalhos de Solicitação*.
6.  Sob o cabeçalho `DataLabAccessToken` está o token alfanumérico. Para ajudar a manter seus dados protegidos, não compartilhe este token.

### <a name="view-user-information"></a>Exibir informações do usuário
Usando a ID do autor que você obteve nas etapas anteriores, exiba informações no perfil do usuário substituindo `[AuthorId]` na seguinte URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Por exemplo, esta solicitação de URL:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Retorna uma resposta, como:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Exibir entidades públicas

A API de catálogo armazena informações sobre as entidades publicadas na Galeria de IA do Azure que você também pode exibir diretamente no [site da Galeria de IA](https://gallery.azure.ai/). 

Para exibir as entidades publicadas, visite a URL a seguir, substituindo `[AuthorId]` pela ID do autor obtida em [Obter uma ID do autor](#get-an-author-id) acima.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Por exemplo: 

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Exibir entidades não listadas e públicas

Esta consulta exibe apenas entidades públicas. Para exibir todas as suas entidades, incluindo aquelas não listadas, forneça o acesso ao token obtido na seção anterior.

1.  Usando uma ferramenta como [Postman](https://www.getpostman.com), crie uma solicitação HTTP GET para a URL do catálogo, conforme descrito em [Obter o token de acesso](#get-your-access-token).
2.  Crie um cabeçalho de solicitação HTTP denominado `DataLabAccessToken`, com o valor definido para o token de acesso.
3.  Envie a solicitação HTTP.

> [!TIP]
> Se as entidades não listadas não forem exibidas em respostas da API do catálogo, isso indica que o usuário pode ter um token de acesso inválido ou expirado. Saia da Galeria de IA do Azure e, em seguida, repita as etapas em [Obter o token de acesso](#get-your-access-token) para renovar o token. 
