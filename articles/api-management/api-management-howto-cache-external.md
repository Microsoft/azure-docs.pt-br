---
title: Usar um cache externo no Gerenciamento de API do Azure|Microsoft Docs
description: Saiba como configurar e usar um cache externo no Gerenciamento de API do Azure. O uso de um cache externo permite que você supere algumas limitações do cache interno.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 6288a10e111e42629abf5e09b84a6a7791dcfe95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018215"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>Usar um cache externo compatível com Redis no gerenciamento de API do Azure

Além de utilizar o cache interno, o gerenciamento de API do Azure permite armazenar em cache as respostas em um cache externo compatível com Redis, por exemplo, o cache do Azure para Redis.

O uso de um cache externo permite que você supere algumas limitações do cache interno:

* Evitar que o cache fique periodicamente desmarcado durante as atualizações de Gerenciamento de API
* Ter mais controle sobre sua configuração de cache
* Armazenar mais dados do que seu nível de Gerenciamento de API permite
* Usar o armazenamento em cache com a camada Consumo do Gerenciamento de API
* Habilitar o Caching nos [gateways de gerenciamento de API auto-hospedados](self-hosted-gateway-overview.md)

Para saber mais sobre o cache, veja [Políticas de armazenamento em cache do Gerenciamento de API](api-management-caching-policies.md) e [Armazenamento em cache personalizado no Gerenciamento de API do Azure](api-management-sample-cache-by-key.md).

![Traga seu próprio cache para o APIM](media/api-management-howto-cache-external/overview.png)

O que você aprenderá:

> [!div class="checklist"]
> * Adicionar um cache externo no Gerenciamento de API

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

+ [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)
+ Entender o [cache no Gerenciamento de API do Azure](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"></a> Criar cache do Azure para Redis

Esta seção explica como criar um Cache do Azure para Redis no Azure. Se você já tiver um Cache do Azure para Redis, dentro ou fora do Azure, <a href="#add-external-cache">ignore</a> esta seção e siga para a próxima.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"></a> Implantar o cache Redis no kubernetes

Para caching, os gateways hospedados internamente dependem exclusivamente de caches externos. Para que o cache seja um gateway de hospedagem interna eficaz e o cache no qual eles dependem devem estar localizados próximos um do outro para minimizar as latências de pesquisa e armazenamento. Implantar um cache Redis no mesmo cluster kubernetes ou em um cluster separado próximo são as melhores opções. Siga este [link](https://github.com/kubernetes/examples/tree/master/guestbook) para aprender a implantar o cache Redis em um cluster kubernetes.

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Adicionar um cache externo

Siga as etapas abaixo para adicionar um Cache do Azure para Redis externo no Gerenciamento de API do Azure.

![Captura de tela que mostra como adicionar um cache externo do Azure para Redis no gerenciamento de API do Azure.](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> A configuração **use from** especifica uma região do Azure ou um local de gateway de hospedagem interna que usará o cache configurado. Os caches configurados como **padrão** serão substituídos por caches com um valor de local ou região correspondente específico.
>
> Por exemplo, se o Gerenciamento de API estiver hospedado nas regiões: Leste dos EUA, Sudeste Asiático e Europa Ocidental, haverá dois caches configurados, um para **Padrão** e um para **Sudeste Asiático**. O Gerenciamento de API que está em **Sudeste Asiático** usará seu próprio cache, enquanto as outras duas regiões usarão a entrada de cache **Padrão**.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Adicionar um Cache do Azure para Redis da mesma assinatura

1. Navegue até sua instância de Gerenciamento de API no portal do Azure.
2. Escolha a guia **Cache externo** no menu à esquerda.
3. Clique no botão **+ Adicionar**.
4. Escolha seu cache no campo suspenso **Instância de cache**.
5. Selecione **padrão** ou especifique a região desejada no campo suspenso **usar de** .
6. Clique em **Save** (Salvar).

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Adicionar um Cache do Azure para Redis hospedado fora da assinatura atual do Azure ou do Azure em geral

1. Navegue até sua instância de Gerenciamento de API no portal do Azure.
2. Escolha a guia **Cache externo** no menu à esquerda.
3. Clique no botão **+ Adicionar**.
4. Escolha **Personalizar** no campo suspenso **Instância de cache**.
5. Selecione **padrão** ou especifique a região desejada no campo suspenso **usar de** .
6. Forneça a cadeia de conexão do Cache do Azure para Redis no campo **Cadeia de conexão**.
7. Clique em **Save** (Salvar).

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>Adicionar um cache Redis a um gateway auto-hospedado

1. Navegue até sua instância de Gerenciamento de API no portal do Azure.
2. Escolha a guia **Cache externo** no menu à esquerda.
3. Clique no botão **+ Adicionar**.
4. Escolha **Personalizar** no campo suspenso **Instância de cache**.
5. Especifique o local do gateway de hospedagem interna desejado ou o **padrão** no campo de lista suspensa **usar de** .
6. Forneça a cadeia de conexão do cache Redis no campo **cadeia de conexão**.
7. Clique em **Save** (Salvar).

## <a name="use-the-external-cache"></a>Usar o cache externo

Depois que o cache externo é configurado no Gerenciamento de API do Azure, ele pode ser usado pelas políticas de cache. Confira [Adicionar cache para melhorar o desempenho no Gerenciamento de API do Azure](api-management-howto-cache.md) para ver etapas detalhadas.

## <a name="next-steps"></a><a name="next-steps"> </a>Próximas etapas

* Para saber mais sobre as políticas de cache, veja [Políticas de cache][Caching policies] na [Referência de política do Gerenciamento de API][API Management policy reference].
* Para saber mais sobre itens de cache por chave usando expressões de política, confira [Cache personalizado no Gerenciamento de API do Azure](api-management-sample-cache-by-key.md).

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md
