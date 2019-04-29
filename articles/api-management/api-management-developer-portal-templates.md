---
title: Personalizar o portal do desenvolvedor do Gerenciamento de API usando modelos - Azure | Microsoft Docs
description: Aprenda a personalizar o portal de desenvolvedor de Gerenciamento de API do Azure usando modelos.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 00d5e3df78e85d19a519786dad1a1b176ad7fa08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836986"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Como personalizar o portal de desenvolvedor de Gerenciamento de API do Azure usando modelos

Há três maneiras básicas para personalizar o portal do desenvolvedor no Gerenciamento de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de layout da página][modify-content-layout]
* [Atualizar os estilos usados para elementos de página entre o portal do desenvolvedor][customize-styles]
* [Modificar os modelos usados para as páginas geradas pelo portal][portal-templates] (explicado neste guia)

Modelos são usados para personalizar o conteúdo das páginas de portal de desenvolvedor geradas pelo sistema (por exemplo, documentos de API, produtos, autenticação do usuário etc.). Com a sintaxe do [DotLiquid](http://dotliquidmarkup.org/), e um conjunto fornecido de recursos de cadeia de caracteres localizados, ícones e controles de página, você conta com muita flexibilidade para configurar o conteúdo das páginas como quiser.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Visão geral de modelos de portal do desenvolvedor

A edição dos modelos é feita no **Portal do desenvolvedor** ao entrar como administrador. Para chegar lá, primeiro abra o Portal do Azure e clique em **Portal do desenvolvedor** na barra de ferramentas de serviço da sua instância de Gerenciamento de API.

Para acessar os modelos de portal do desenvolvedor, clique no ícone de personalizar à esquerda para exibir o menu de personalização e clique em **Modelos**.

![Modelos de portal do desenvolvedor][api-management-customize-menu]

A lista de modelos exibe várias categorias de modelos que abrangem diferentes páginas no portal do desenvolvedor. Cada modelo é diferente, mas as etapas para editá-los e publicar as alterações são as mesmas. Para editar um modelo, clique no nome do modelo.

![Modelos de portal do desenvolvedor][api-management-templates-menu]

Clicar em um modelo levará você para a página de portal do desenvolvedor, que é personalizável pelo modelo. Neste exemplo, o modelo da **Lista de produtos** é exibido. O modelo da **Lista de produtos** controla a área da tela indicada pelo retângulo vermelho.

![Modelo de lista de produtos][api-management-developer-portal-templates-overview]

Alguns modelos, como o de **Perfil do Usuário** , personalizam partes diferentes da mesma página.

![Modelos de perfil do usuário][api-management-user-profile-templates]

O editor para cada modelo de portal do desenvolvedor tem duas seções exibidas na parte inferior da página. O lado esquerdo exibe o painel de edição para o modelo e o lado direito exibe o modelo de dados para o modelo.

O painel de edição de modelo contém a marcação que controla a aparência e o comportamento da página correspondente no portal do desenvolvedor. A marcação no modelo usa a sintaxe [DotLiquid](http://dotliquidmarkup.org/) . É um editor popular para DotLiquid é [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Todas as alterações feitas ao modelo durante a edição são exibidas em tempo real no navegador, mas não são visíveis aos clientes até você [salvar](#to-save-a-template) e [publicar](#to-publish-a-template) o modelo.

![Marcação de modelo][api-management-template]

O painel **Dados de modelo** fornece um guia para o modelo de dados para as entidades que estão disponíveis para uso em um modelo específico. Ele fornece este guia, exibindo os dados dinâmicos mostrados no momento no portal do desenvolvedor. Você pode expandir os painéis de modelo clicando no canto superior direito do retângulo do painel **Dados do modelo** .

![Modelo de dados de modelo][api-management-template-data]

No exemplo anterior, há dois produtos exibidos no portal do desenvolvedor que foram recuperados dos dados exibidos no painel **Dados de modelo**, conforme mostrado no exemplo a seguir:

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
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
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

A marcação no modelo de **Lista de produtos** processa os dados para fornecer a saída desejada ao iterar por meio da coleção de produtos para exibir informações e um link para cada produto individual. Observe os elementos `<search-control>` e `<page-control>` na marcação. Eles controlam a exibição da pesquisa e os controles de paginação na página. O `ProductsStrings|PageTitleProducts` é uma referência de cadeia de caracteres localizada que contém o texto do cabeçalho `h2` da página. Para obter uma lista de recursos de cadeia de caracteres, controles de página e ícones disponíveis para uso em modelos de portal do desenvolvedor, consulte [referência de modelos de portal do desenvolvedor do Gerenciamento de API](api-management-developer-portal-templates-reference.md).

```html
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

## <a name="to-save-a-template"></a>Para salvar um modelo
Para salvar um modelo, clique em Salvar no editor de modelos.

![Salvar modelo][api-management-save-template]

Alterações salvas não são ativas no portal do desenvolvedor até serem publicadas.

## <a name="to-publish-a-template"></a>Para publicar um modelo
Modelos salvos podem ser publicados individualmente ou em conjunto. Para publicar um modelo individual, clique em Publicar no editor de modelos.

![Publicar modelo][api-management-publish-template]

Clique em **Sim** para confirmar e tornar o modelo ativo no portal do desenvolvedor.

![Confirmar publicação][api-management-publish-template-confirm]

Para publicar todas as versões do modelo atualmente não publicado, clique em **Publicar** na lista de modelos. Modelos não publicados são designados por um asterisco após o nome do modelo. Neste exemplo, os modelos de **Lista de produtos** e **Produtos** estão sendo publicados.

![Publicar modelos][api-management-publish-templates]

Clique em **Publicar personalizações** para confirmar.

![Confirmar publicação][api-management-publish-customizations]

Modelos publicados recentemente entrarão em vigor imediatamente no portal do desenvolvedor.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para reverter um modelo para a versão anterior
Para reverter um modelo para a versão publicada anterior, clique em Reverter no editor de modelos.

![Reverter modelo][api-management-revert-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-revert-template-confirm]

A versão anteriormente publicada de um modelo fica ativa no portal do desenvolvedor quando a operação de reversão é concluída.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar um modelo para a versão padrão
A restauração dos modelos para a versão padrão é um processo de duas etapas. Primeiro, os modelos devem ser restaurados e, em seguida, as versões restauradas devem ser publicadas.

Para restaurar um único modelo para a versão padrão, clique em Restaurar no editor de modelos.

![Reverter modelo][api-management-reset-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todos os modelos para as versões padrão, clique em **Restaurar modelos padrão** na lista de modelos.

![Restaurar modelos][api-management-restore-templates]

Os modelos restaurados devem ser publicados individualmente ou todos de uma vez, seguindo as etapas em [Para publicar um modelo](#to-publish-a-template).

## <a name="next-steps"></a>Próximas etapas
Para obter informações de referência para modelos do portal do desenvolvedor, recursos de cadeia de caracteres, ícones e controles de página, consulte [referência de modelos de portal do desenvolvedor do Gerenciamento de API](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
