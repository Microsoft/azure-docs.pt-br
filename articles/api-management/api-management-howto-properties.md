---
title: "Como usar as propriedades nas políticas de Gerenciamento de API do Azure"
description: "Saiba como usar as propriedades nas políticas de Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 6f39b00f-cf6e-4cef-9bf2-1f89202c0bc0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 3b0fe2a300038e13cc488bdb4f50f8be270ea8f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Como usar as propriedades nas políticas de Gerenciamento de API do Azure
As políticas de Gerenciamento de API representam um recurso poderoso do sistema e permitem ao editor alterar o comportamento da API por meio de configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. É possível construir declarações de política usando valores de texto literais, expressões de política e propriedades. 

Cada instância de serviço do Gerenciamento de API tem uma coleção de propriedades de pares de chave/valor que são globais à instância do serviço. Essas propriedades podem ser usadas para gerenciar valores de cadeia de caracteres constantes em todas as configurações e as políticas de API. Cada propriedade tem os atributos a seguir.

| Atributo | Tipo | Descrição |
| --- | --- | --- |
| Name |string |O nome da propriedade. Ele pode conter apenas letras, dígitos, ponto, traço e caracteres de sublinhado. |
| Valor |string |O valor da propriedade. Ele não pode ficar vazio ou conter apenas espaços em branco. |
| Segredo |Booliano |Determina se o valor é um segredo e se deve ser criptografado ou não. |
| Marcas |matriz de cadeias de caracteres |Marcas opcionais que, quando fornecidas, podem ser usadas para filtrar a lista de propriedades. |

As propriedades são configuradas no portal do editor na guia **Propriedades** . No exemplo a seguir, três propriedades são configuradas.

![Propriedades][api-management-properties]

Os valores de propriedade podem conter cadeias de caracteres literais e [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx). A tabela a seguir mostra as propriedades dos três exemplos anteriores e seus atributos. O valor de `ExpressionProperty` é uma expressão de política que retorna uma cadeia de caracteres com a data e a hora atuais. A propriedade `ContosoHeaderValue` é marcada como um segredo e, portanto, seu valor não é exibido.

| Name | Valor | Segredo | Marcas |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |Falso |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |Verdadeiro |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |Falso | |

## <a name="to-use-a-property"></a>Para usar uma propriedade
Para usar uma propriedade em uma política, coloque o nome da propriedade entre dois pares de chaves, como em `{{ContosoHeader}}`, de acordo com o exemplo a seguir.

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma política `set-header` e `ContosoHeaderValue` é usado como o valor desse cabeçalho. Quando essa política é avaliada durante uma solicitação ou uma resposta para o gateway de Gerenciamento de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídos pelos valores de suas respectivas propriedades.

As propriedades podem ser usadas como valores de atributo ou de elemento, como mostra o exemplo anterior, mas também podem ser inseridas ou combinadas com parte de uma expressão de texto literal, como mostra o exemplo a seguir: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

As propriedades também podem conter expressões de política. No exemplo a seguir, o `ExpressionProperty` é usado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando essa política é avaliada, `{{ExpressionProperty}}` é substituído por seu valor: `@(DateTime.Now.ToString())`. Como o valor é uma expressão de política, a expressão é avaliada e a política prossegue com a execução.

Você pode testar isso no portal do desenvolvedor chamando uma operação que tenha uma política com propriedades no escopo. No exemplo a seguir, uma operação é chamada com os dois exemplos de políticas `set-header` anteriores com propriedades. Observe que a resposta contém dois cabeçalhos personalizados configurados usando políticas com propriedades.

![Portal do desenvolvedor][api-management-send-results]

Se você analisar o [rastreamento do Inspetor de API](api-management-howto-api-inspector.md) de uma chamada que inclui os dois exemplos de política anteriores com propriedades, será possível ver as duas políticas `set-header` com os valores de propriedade inseridos, bem como a avaliação da expressão de política para a propriedade que continha a expressão de política.

![Rastreamento do Inspetor de API][api-management-api-inspector-trace]

Observe que, embora os valores de propriedade possam conter expressões de política, os valores de propriedade não podem conter outras propriedades. Se um texto contendo uma referência de propriedade for usado para um valor de propriedade, por exemplo, `Property value text {{MyProperty}}`, essa referência de propriedade não será substituída e será incluída como parte do valor da propriedade.

## <a name="to-create-a-property"></a>Para criar uma propriedade
Para criar uma propriedade, clique em **Adicionar propriedade** na guia **Propriedades**.

![Adicionar propriedade][api-management-properties-add-property-menu]

**Nome** e **Valor** são valores obrigatórios. Se o valor dessa propriedade for um segredo, marque a caixa de seleção **Isto é um segredo** . Insira uma ou mais marcas opcionais para ajudar a organizar suas propriedades e clique em **Salvar**.

![Adicionar propriedade][api-management-properties-add-property]

Quando uma nova propriedade for salva, a caixa de texto **Propriedade de pesquisa** será preenchida com o nome da nova propriedade e a nova propriedade será exibida. Para exibir todas as propriedades, desmarque a caixa de texto **Propriedade de pesquisa** e pressione enter.

![Propriedades][api-management-properties-property-saved]

Para saber mais sobre como criar uma propriedade usando a API REST, confira [Criar uma propriedade usando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Para editar uma propriedade
Para editar uma propriedade, clique em **Editar** ao lado da propriedade que você quer editar.

![Editar propriedade][api-management-properties-edit]

Faça as alterações desejadas e clique em **Salvar**. Se você alterar o nome da propriedade, todas as políticas que fizerem referência a essa propriedade serão automaticamente atualizadas para usar o novo nome.

![Editar propriedade][api-management-properties-edit-property]

Para saber mais sobre como editar uma propriedade usando a API REST, confira [Editar uma propriedade usando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Para excluir uma propriedade
Para excluir uma propriedade, clique em **Excluir** ao lado da propriedade que você quer excluir.

![Excluir propriedade][api-management-properties-delete]

Clique em **Sim, excluir** para confirmar.

![Confirmar exclusão][api-management-delete-confirm]

> [!IMPORTANT]
> Se a propriedade for consultada por quaisquer políticas, não será possível excluí-la até que você remova a propriedade de todas as políticas que a utilizam.
> 
> 

Para saber mais sobre como excluir uma propriedade usando a API REST, confira [Excluir uma propriedade usando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Para pesquisar e filtrar propriedades
A guia **Propriedades** inclui recursos de pesquisa e filtragem para ajudar você a gerenciar suas propriedades. Para filtrar a lista de propriedades pelo nome da propriedade, insira um termo de pesquisa na caixa de texto **Propriedade de pesquisa** . Para exibir todas as propriedades, desmarque a caixa de texto **Propriedade de pesquisa** e pressione enter.

![Pesquisar][api-management-properties-search]

Para filtrar a lista de propriedades por valores de marca, insira uma ou mais marcas na caixa de texto **Filtrar por marcas** . Para exibir todas as propriedades, desmarque a caixa de texto **Filtrar por marcas** e pressione enter.

![Filtro][api-management-properties-filter]

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como trabalhar com políticas
  * [Políticas no Gerenciamento de API](api-management-howto-policies.md)
  * [Referência de política](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Assista a uma visão geral em vídeo
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Use-Properties-in-Policies/player]
> 
> 

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

