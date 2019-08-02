---
title: Como usar Valores Nomeados nas políticas de Gerenciamento de API do Azure
description: Saiba como usar Valores Nomeados nas políticas de Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/22/2019
ms.author: apimpm
ms.openlocfilehash: 46f4e1b3df5f1c77a57d432297685d6d1a0a14a8
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405805"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Como usar Valores Nomeados nas políticas de Gerenciamento de API do Azure

As políticas de gerenciamento de API são um recurso poderoso do sistema que permitem que o portal do Azure altere o comportamento da API por meio da configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. É possível construir declarações de política usando valores de texto literais, expressões de política e valores nomeados.

Cada instância de serviço do Gerenciamento de API tem uma coleção de propriedades de pares de chave/valor, chamada Valores Nomeados, que são globais à instância do serviço. Não há limite imposto sobre o número de itens na coleção. Valores nomeados podem ser usados para gerenciar valores de cadeia de caracteres constantes em todas as políticas e configuração de API. Cada valor nomeado pode ter os seguintes atributos:

| Atributo      | Tipo            | Descrição                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | cadeia de caracteres          | Usado para referenciar a propriedade em políticas. Uma cadeia de uma a 256 caracteres. Somente letras, números, ponto e traço são permitidos. |
| `Value`        | cadeia de caracteres          | Valor real. Não deve estar vazio nem consistir apenas em espaços em branco. Máximo de 4096 caracteres de comprimento.                                     |
| `Secret`       | boolean         | Determina se o valor é um segredo e se deve ser criptografado ou não.                                                            |
| `Tags`         | matriz de cadeias de caracteres | Usado para filtrar a lista de propriedades. Até 32 marcas.                                                                                    |

![Valores nomeados](./media/api-management-howto-properties/named-values.png)

Os valores nomeados podem conter cadeias de caracteres literais e [expressões de política](/azure/api-management/api-management-policy-expressions). Por exemplo, o valor de `Expression` é uma expressão de política que retorna uma cadeia de caracteres que contém a data e hora atuais. O valor `Credential` nomeado é marcado como um segredo, portanto, seu valor não é exibido por padrão.

| Nome       | Valor                      | Secret | Marcas          |
| ---------- | -------------------------- | ------ | ------------- |
| Valor      | 42                         | False  | números vitais |
| Credencial | ••••••••••••••••••••••     | verdadeiro   | segurança      |
| Expressão | @(DateTime.Now.ToString()) | False  |               |

## <a name="to-add-and-edit-a-property"></a>Para adicionar e editar uma propriedade

![Adicionar uma propriedade](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **Valores nomeados**.
3. Pressione **+Adicionar**.

    Nome e Valor são valores obrigatórios. Se o valor dessa propriedade for confidencial, marque a caixa de seleção Valor confidencial. Insira uma ou mais marcas opcionais para ajudar a organizar seus valores nomeados e, em seguida, clique em Salvar.

4. Clique em **Criar**.

Quando a propriedade é criada, você pode editá-la clicando na propriedade. Se você alterar o nome da propriedade, todas as políticas que fizerem referência a essa propriedade serão automaticamente atualizadas para usar o novo nome.

Para saber mais sobre como editar uma propriedade usando a API REST, confira [Editar uma propriedade usando a API REST](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-property"></a>Para excluir uma propriedade

Para excluir uma propriedade, clique em **Excluir** ao lado da propriedade que você quer excluir.

> [!IMPORTANT]
> Se a propriedade for consultada por quaisquer políticas, não será possível excluí-la até que você remova a propriedade de todas as políticas que a utilizam.

Para saber mais sobre como excluir uma propriedade usando a API REST, confira [Excluir uma propriedade usando a API REST](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Para pesquisar e filtrar Valores Nomeados

A guia **Valores nomeados** inclui pesquisa e filtragem de recursos para ajudá-lo a gerenciar seus valores nomeados. Para filtrar a lista de propriedades pelo nome da propriedade, insira um termo de pesquisa na caixa de texto **Propriedade de pesquisa** . Para exibir todos os valores nomeados, desmarque a caixa de texto **Propriedade de pesquisa** e pressione enter.

Para filtrar a lista de propriedades por valores de marca, insira uma ou mais marcas na caixa de texto **Filtrar por marcas** . Para exibir todos os valores nomeados, desmarque a caixa de texto **Filtrar por marcas** e pressione enter.

## <a name="to-use-a-property"></a>Para usar uma propriedade

Para usar uma propriedade em uma política, coloque o nome da propriedade entre chaves como `{{ContosoHeader}}`, conforme mostrado no exemplo a seguir:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma política `set-header` e `ContosoHeaderValue` é usado como o valor desse cabeçalho. Quando essa política é avaliada durante uma solicitação ou uma resposta para o gateway de Gerenciamento de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídos pelos valores de suas respectivas propriedades.

Os valores nomeados podem ser usados como valores de atributo ou de elemento, como mostra o exemplo anterior, mas também podem ser inseridos ou combinados com parte de uma expressão de texto literal, como mostra o exemplo a seguir: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Os valores nomeados também podem conter expressões de política. No exemplo a seguir, o `ExpressionProperty` é usado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando essa política é avaliada, `{{ExpressionProperty}}` é substituído por seu valor: `@(DateTime.Now.ToString())`. Como o valor é uma expressão de política, a expressão é avaliada e a política prossegue com a execução.

Você pode testar isso no portal do desenvolvedor chamando uma operação que tenha uma política com valores nomeados no escopo. No exemplo a seguir, uma operação é chamada com os dois exemplos de políticas `set-header` anteriores com valores nomeados. Observe que a resposta contém dois cabeçalhos personalizados configurados usando políticas com valores nomeados.

![Portal do desenvolvedor][api-management-send-results]

Se você analisar o [rastreamento do Inspetor de API](api-management-howto-api-inspector.md) de uma chamada que inclui os dois exemplos de política anteriores com valores nomeados, será possível ver as duas políticas `set-header` com os valores de propriedade inseridos, bem como a avaliação da expressão de política para a propriedade que continha a expressão de política.

![Rastreamento do Inspetor de API][api-management-api-inspector-trace]

Enquanto os valores de propriedade podem conter expressões de política, os valores de propriedade não podem conter outros valores nomeados. Se um texto contendo uma referência de propriedade for usado para um valor de propriedade, por exemplo, `Property value text {{MyProperty}}`, essa referência de propriedade não será substituída e será incluída como parte do valor da propriedade.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre como trabalhar com políticas
    -   [Políticas no Gerenciamento de API](api-management-howto-policies.md)
    -   [Referência de política](/azure/api-management/api-management-policies)
    -   [Expressões de política](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
