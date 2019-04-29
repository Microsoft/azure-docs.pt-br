---
title: Modelos de produto no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como personalizar o conteúdo das páginas de produto no portal do desenvolvedor do Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 14090e21fb7c6ca07fe63220ffd1d44d483ac869
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61088152"
---
# <a name="product-templates-in-azure-api-management"></a>Modelos de produto no Gerenciamento de API do Azure

O Gerenciamento de API do Azure fornece a capacidade de personalizar o conteúdo das páginas do portal do desenvolvedor usando um conjunto de modelos que configura o respectivo conteúdo. Usando a sintaxe [DotLiquid](http://dotliquidmarkup.org/) e o editor de sua escolha, como o [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), bem como um conjunto fornecido de [Recursos de cadeia de caracteres](api-management-template-resources.md#strings), [Recursos do Glyph](api-management-template-resources.md#glyphs) e [Controles de página](api-management-page-controls.md) localizados, você tem grande flexibilidade para configurar o conteúdo das páginas, conforme a necessidade, usando esses modelos.  
  
 Os modelos desta seção permitem personalizar o conteúdo das páginas de produto no portal do desenvolvedor.  
  
-   [Lista de produtos](#ProductList)  
  
-   [Produto](#Product)  
  
> [!NOTE]
>  Os modelos de amostra padrão estão incluídos na documentação a seguir, mas estão sujeitos à alteração devido a melhorias contínuas. Você pode exibir os modelos padrão em tempo real no portal do desenvolvedor, navegando até os modelos individuais desejados. Para saber mais sobre como trabalhar com modelos, consulte [Como personalizar o portal de desenvolvedor de Gerenciamento de API usando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a> Lista de produtos  
 O modelo **Lista de produtos** permite personalizar o corpo da página de lista de produtos no portal do desenvolvedor.  
  
 ![Lista de produtos](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Modelo padrão  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Controles  
 O modelo `Product list` pode usar os seguintes [controles de página](api-management-page-controls.md).  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [search-control](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|Paginamento|Entidade de [paginação](api-management-template-data-model-reference.md#Paging).|As informações de paginação da coleção de produtos.|  
|Filtragem|Entidade de [filtragem](api-management-template-data-model-reference.md#Filtering).|As informações de filtragem da página de lista de produtos.|  
|Produtos|Coleção de entidades de [produto](api-management-template-data-model-reference.md#Product).|Os produtos visíveis para o usuário atual.|  
  
### <a name="sample-template-data"></a>Amostra de dados do modelo  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="Product"></a> Produto  
 O modelo **Produto** permite personalizar o corpo da página de produtos no portal do desenvolvedor.  
  
 ![Página de produtos do portal do desenvolvedor](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Modelo padrão  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Controles  
 O modelo `Product list` pode usar os seguintes [controles de página](api-management-page-controls.md).  
  
-   [subscribe-button](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|Produto|[Produto](api-management-template-data-model-reference.md#Product)|O produto especificado.|  
|IsDeveloperSubscribed|boolean|Se o usuário atual assinou esse produto.|  
|SubscriptionState|número|O estado da assinatura. Os possíveis estados são:<br /><br /> -   `0 - suspended` – a assinatura está bloqueada e o assinante não pode chamar APIs do produto.<br />-   `1 - active` – a assinatura está ativa.<br />-   `2 - expired` – a assinatura atingiu sua data de validade e foi desativada.<br />-   `3 - submitted` – a solicitação de assinatura foi feita pelo desenvolvedor, mas ainda não foi aprovada ou rejeitada.<br />-   `4 - rejected` – a solicitação de assinatura foi negada por um administrador.<br />-   `5 - cancelled` – a assinatura foi cancelada pelo desenvolvedor ou administrador.|  
|limites|matriz|Essa propriedade foi preterida e não deve ser usada.|  
|DelegatedSubscriptionEnabled|boolean|Se [delegação](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) está habilitada para essa assinatura.|  
|DelegatedSubscriptionUrl|string|Se delegação estiver habilitada, a URL da assinatura delegada.|  
|IsAgreed|boolean|Se o produto tiver termos, se o atual usuário concordou com os termos.|  
|Assinaturas|Coleção de entidades de [Resumo da assinatura](api-management-template-data-model-reference.md#SubscriptionSummary).|As assinaturas para o produto.|  
|Apis|Coleção de entidades de [API](api-management-template-data-model-reference.md#API).|As APIs nesse produto.|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|Se o usuário atual está qualificado para assinar esse produto com relação ao limite de assinatura.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|Se o usuário atual está qualificado para assinar esse produto com relação à permissão ou não de várias assinaturas.|  
  
### <a name="sample-template-data"></a>Amostra de dados do modelo  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como trabalhar com modelos, consulte [Como personalizar o portal de desenvolvedor de Gerenciamento de API usando modelos](api-management-developer-portal-templates.md).