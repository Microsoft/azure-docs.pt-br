---
title: Proteja sua API com o Gerenciamento de API do Azure | Microsoft Docs
description: "Saiba como proteger sua API com cotas e políticas de limitação (limite de taxa)."
services: api-management
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 450dc368-d005-401d-ae64-3e1a2229b12f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 300b1d762a61c810dbffde5aaacd8a85f12c9fca
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Proteja sua API com limites de taxa usando o Gerenciamento de API do Azure
Este guia mostra como é fácil adicionar proteção à API de back-end configurando políticas de cota e limite de taxa com o Gerenciamento de API do Azure.

Neste tutorial, você criará um produto de API de "Avaliação Gratuita" que permite aos desenvolvedores fazerem até 10 chamadas por minuto e no máximo 200 chamadas por semana para sua API usando as políticas [Limitar taxa de chamadas por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [Definir cota de uso por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). Depois você publicará a API e testará a política de limite de taxa.

Para ver os cenários de limitação mais avançados usando as políticas [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey), consulte [Limitação de solicitação avançada com o Gerenciamento de API do Azure](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Para criar um produto
Nesta etapa você criará um produto de avaliação gratuita que não requer aprovação de assinatura.

> [!NOTE]
> Se já tiver um produto configurado e desejar usá-lo para este tutorial, você pode pular para [Configurar políticas de cota e de limite de taxa de chamada][Configure call rate limit and quota policies] e seguir o tutorial a partir daí, usando seu produto em vez do produto Avaliação Gratuita.
> 
> 

Para começar, clique em **Portal do Editor** no Portal do Azure para acessar o serviço de Gerenciamento de API.

![Portal do editor][api-management-management-console]

> Se ainda não criou uma instância do serviço de Gerenciamento de API, confira [Criar uma instância do serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Gerenciar sua primeira API no Gerenciamento de API do Azure][Manage your first API in Azure API Management].
> 
> 

Clique em **Produtos** no menu **Gerenciamento de API** à esquerda para exibir a página **Produtos**.

![Adicionar produto][api-management-add-product]

Clique em **Adicionar produto** para exibir a caixa de diálogo **Adicionar novo produto**.

![Adicionar novo produto][api-management-new-product-window]

Na caixa**Título**, digite **Avaliação Gratuita**.

Na caixa **Descrição**, digite o seguinte texto: **Os assinantes poderão executar 10 chamadas/minuto até um máximo de 200 chamadas/semana após o qual o acesso é negado.**

Os produtos de Gerenciamento de API podem ser protegidos ou abertos. Os produtos protegidos devem ser assinados antes que possam ser usados. Os produtos abertos podem ser usados sem uma assinatura. Verifique se **Exigir assinatura** está marcado para criar um produto protegido que requer uma assinatura. Esta é a configuração padrão.

Se deseja que um administrador analise e aceite ou rejeite as tentativas de assinatura para este produto, marque **Requerer aprovação de assinatura**. Se a caixa de seleção não estiver marcada, as tentativas de assinatura serão aprovadas automaticamente. Neste exemplo, as assinaturas são aprovadas automaticamente; por isso, não marque a caixa.

Para permitir que contas de desenvolvedor assinem várias vezes o novo produto, marque a caixa de seleção **Permitir várias assinaturas simultâneas** . Este tópico não utiliza várias inscrições simultâneas, por isso deixe-o desmarcado.

Após inserir todos os valores, clique em **Salvar** para criar o produto.

![Produto adicionado][api-management-product-added]

Por padrão, os novos produtos ficam visíveis para os usuários no grupo **Administradores** . Vamos adicionar o grupo **Desenvolvedores** . Clique em **Avaliação Gratuita**, em seguida, clique na guia **Visibilidade**.

> No Gerenciamento de API, os grupos são usados para gerenciar a visibilidade dos produtos para desenvolvedores. Os produtos dão visibilidade aos grupos e os desenvolvedores podem exibir e assinar os produtos visíveis para os grupos aos quais pertencem. Para obter mais informações, confira [Como criar e usar grupos no Gerenciamento de API do Azure][How to create and use groups in Azure API Management].
> 
> 

![Adicionar um grupo de desenvolvedores][api-management-add-developers-group]

Marque a caixa de seleção **Desenvolvedores**, em seguida, clique em **Salvar**.

## <a name="add-api"> </a>Para adicionar uma API ao produto
Nesta etapa do tutorial você adicionará a API de ECO a uma produto de avaliação gratuita.

> Cada instância de serviço de Gerenciamento de API vem pré-configurada com uma API de ECO que pode ser usada para experimentar e aprender sobre o Gerenciamento de API. Para saber mais, confira [Gerenciar sua primeira API no Gerenciamento de API do Azure][Manage your first API in Azure API Management].
> 
> 

Clique em **Produtos** no menu **Gerenciamento de API** à esquerda, em seguida, clique em **Avaliação Gratuita** para configurar o produto.

![Configurar produto][api-management-configure-product]

Clique em **Adicionar API ao produto**.

![Adicionar API ao produto][api-management-add-api]

Selecione a **API de Eco** e clique em **Salvar**.

![Adicionar API de Eco][api-management-add-echo-api]

## <a name="policies"> </a>Para configurar o limite de taxa de chamada e as políticas de cota
Os limites de taxa e as cotas são configurados no editor de políticas. As duas políticas que adicionaremos neste tutorial são [Limitar taxa de chamadas por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [Definir cota de uso por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). Essas políticas devem ser aplicadas no escopo do produto.

Clique em **Políticas** no menu **Gerenciamento de API** à esquerda. Na lista **Produto**, clique em **Avaliação Gratuita**.

![Política de produtos][api-management-product-policy]

Clique em **Adicionar política** para importar o modelo de política e começar a criar o limite de taxa e as políticas de cota.

![Adicionar política][api-management-add-policy]

A taxa de limite e as políticas de cota são políticas de entrada; portanto, posicione o cursor no elemento de entrada.

![Editor de políticas][api-management-policy-editor-inbound]

Percorra a lista de políticas e localize a entrada **Limitar taxa de chamadas por assinatura**.

![Declarações de políticas][api-management-limit-policies]

Depois do cursor estar posicionado no elemento da política de **entrada**, clique na seta ao lado **Limitar taxa de chamadas por assinatura** para inserir o modelo de política.

```xml
<rate-limit calls="number" renewal-period="seconds">
<api name="name" calls="number">
<operation name="name" calls="number" />
</api>
</rate-limit>
```

Como você pode ver no trecho, a política permite a configuração de limites para as APIs e operações do produto. Neste tutorial, não usaremos esse recurso, por isso, exclua os elementos **api** e **operation** do elemento **rate-limit**, de modo que apenas o elemento **rate-limit** permaneça, como mostrado no exemplo a seguir.

```xml
<rate-limit calls="number" renewal-period="seconds">
</rate-limit>
```

No produto de Avaliação Gratuita, a taxa máxima permitida é de 10 chamadas por minuto, portanto, digite **10** como o valor para o atributo **calls** e **60** para o atributo **renewal-period**.

```xml
<rate-limit calls="10" renewal-period="60">
</rate-limit>
```

Para configurar a política **Definir cota de uso por assinatura**, posicione seu cursor imediatamente abaixo do elemento **rate-limit** recém-adicionado no elemento **inbound**, depois, localize e clique na seta à esquerda de **Definir cota de uso por assinatura**.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
<api name="name" calls="number" bandwidth="kilobytes">
<operation name="name" calls="number" bandwidth="kilobytes" />
</api>
</quota>
```

Assim como a política **Limitar taxa de chamada por assinatura**, a política **Definir cota de uso por assinatura** permite a definição de limites para em operações e APIs do produto. Neste tutorial, não usaremos esse recurso, por isso, exclua os elementos **api** e **operation** do elemento **quota**, como mostra o exemplo a seguir.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
</quota>
```

As cotas podem ser baseadas no número de chamadas por intervalo, largura de banda ou ambos. Neste tutorial, não estamos limitando com base na largura de banda, por isso, exclua o atributo **largura de banda** .

```xml
<quota calls="number" renewal-period="seconds">
</quota>
```

No produto de Avaliação Gratuita, a cota é de 200 chamadas por semana. Especifique **200** como o valor para o atributo **calls** e especifique **604800** como o valor do atributo **renewal-period**.

```xml
<quota calls="200" renewal-period="604800">
</quota>
```

> Os intervalos de política são especificados em segundos. Para calcular o intervalo para uma semana, você pode multiplicar o número de dias (7) pelo número de horas em um dia (24), pelo número de minutos em uma hora (60), pelo número de segundos em um minuto (60): 7 * 24 * 60 * 60 = 604800.
> 
> 

Ao finalizar a configuração da política, ela deve corresponder ao exemplo a seguir.

```xml
<policies>
    <inbound>
        <rate-limit calls="10" renewal-period="60">
        </rate-limit>
        <quota calls="200" renewal-period="604800">
        </quota>
        <base />

</inbound>
<outbound>

    <base />

    </outbound>
</policies>
```

Após configurar as políticas desejadas, clique em **Salvar**.

![Salvar política][api-management-policy-save]

## <a name="publish-product"> </a> Para publicar o produto
Agora que as APIs foram adicionadas e as políticas configuradas, o produto deve ser publicado para ser usado pelos desenvolvedores. Clique em **Produtos** no menu **Gerenciamento de API** à esquerda, em seguida, clique em **Avaliação Gratuita** para configurar o produto.

![Configurar produto][api-management-configure-product]

Clique em **Publicação**, em seguida, clique em **Sim, publicar** para confirmar.

![Publicar produto][api-management-publish-product]

## <a name="subscribe-account"> </a>Para assinar um produto com uma conta de desenvolvedor
Agora que o produto foi publicado, ele está disponível para inscrição e para ser usado pelos desenvolvedores.

> Os administradores de uma instância de Gerenciamento de API são inscritos automaticamente em cada produto. Nesta etapa do tutorial, inscreveremos um das contas de desenvolvedor não administrador no produto de avaliação gratuita. Se sua conta de desenvolvedor fizer parte da função Administradores, você pode prosseguir com esta etapa, embora já esteja inscrito.
> 
> 

Clique em **Usuários** no menu **Gerenciamento de API** à esquerda, em seguida, clique no nome da sua conta de desenvolvedor. Neste exemplo, estamos usando a conta de desenvolvedor **Clayton Gragg** .

![Configurar desenvolvedor][api-management-configure-developer]

Clique em **Adicionar assinatura**.

![Adicionar assinatura][api-management-add-subscription-menu]

Selecione **Avaliação Gratuita** e clique em **Assinar**.

![Adicionar assinatura][api-management-add-subscription]

> [!NOTE]
> Neste tutorial, várias inscrições simultâneas não estão habilitadas para o produto Avaliação Gratuita. Se elas estivessem, você seria solicitado a nomear a assinatura, conforme mostrado no exemplo a seguir.
> 
> 

![Adicionar assinatura][api-management-add-subscription-multiple]

Depois de clicar em **Assinar**, o produto aparecerá na lista **Assinatura** do usuário.

![Assinatura adicionada][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Para chamar uma operação e testar o limite de taxa
Agora que o produto de Avaliação Gratuita está configurado e publicado, podemos chamar algumas operações e testar a política da taxa de limite.
Alterne para o portal do desenvolvedor clicando em **Portal do desenvolvedor** no menu superior à direita.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** no menu superior, em seguida, clique em **API de Eco**.

![Portal do desenvolvedor][api-management-developer-portal-api-menu]

Clique em **Recurso GET**, em seguida, clique em **Experimentar**.

![Abrir console][api-management-open-console]

Mantenha os valores de parâmetros padrão e selecione a chave de assinatura para o produto Avaliação Gratuita.

![Chave de assinatura][api-management-select-key]

> [!NOTE]
> Se você tem várias inscrições certifique-se de selecionar a chave de **Avaliação Gratuita**ou as políticas que foram configuradas nas etapas anteriores não entrarão em vigor.
> 
> 

Clique em **Enviar**e exiba a resposta. Observe o **Status de resposta** **200 OK**.

![Resultados da operação][api-management-http-get-results]

Clique em **Enviar** a uma taxa maior do que a política de limite de taxa de dez chamadas por minuto. Uma vez excedida a política de limite de taxa, um status de resposta de **429 muitas solicitações** será retornado.

![Resultados da operação][api-management-http-get-429]

O **Conteúdo de resposta** indica o intervalo restante antes que as tentativas sejam bem-sucedidas.

Quando a política de limite de taxa de 10 chamadas por minuto estiver em vigor, as chamadas subsequentes falharão até que 60 segundos tenham se passado a partir da primeira das 10 chamadas com êxito para o produto, antes que o limite de taxa tenha sido excedido. Neste exemplo, o intervalo restante é de 54 segundos.

## <a name="next-steps"> </a>Próximas etapas
* Para obter mais informações e ver uma demonstração da definição de limites de taxa e cotas, consulte o vídeo a seguir.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Manage your first API in Azure API Management]: api-management-get-started.md
[How to create and use groups in Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configure call rate limit and quota policies]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota

