---
title: Coletando parâmetros necessários
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Os parâmetros para todos os contêineres de serviços cognitivas
ms.service: cognitive-services
ms.topic: include
ms.date: 7/24/2019
ms.author: dapine
ms.openlocfilehash: 636a41fde345a08db1549e53626522962f9cf74f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488737"
---
## <a name="gathering-required-parameters"></a>Coletando parâmetros necessários

Há três parâmetros principais para todos os contêineres de serviços cognitivas que são necessários. O contrato de licença de usuário final (**EULA**) deve estar presente com um valor `accept`de. Além disso, uma URL de ponto de extremidade e uma chave de API são necessárias.

### <a name="endpoint-uri-endpointuri"></a>URI do ponto de extremidade`{Endpoint_URI}`

O valor do URI do **ponto de extremidade** está disponível na página de *visão geral* portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *visão geral* , focalize o ponto de extremidade e `Copy to clipboard` um <span class="docon docon-edit-copy x-hidden-focus"></span> ícone será exibido. Copie e use onde for necessário.

![Coletar o URI do ponto de extremidade para uso posterior](../media/overview-endpoint-uri.png)

### <a name="keys-apikey"></a>Novas`{API_Key}`

Essa chave é usada para iniciar o contêiner e está disponível na página chaves do portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *chaves* e clique no `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone.

![Obter uma das duas chaves para uso posterior](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API de serviço cognitiva. Não compartilhe suas chaves. Armazene-os com segurança, por exemplo, usando Azure Key Vault. Também recomendamos regenerar essas chaves regularmente. Apenas uma chave é necessária para fazer uma chamada à API. Ao regenerar a primeira chave, você pode usar a segunda chave para obter acesso contínuo ao serviço.