---
title: Como usar valores nomeados nas políticas de Gerenciamento de API do Azure
description: Saiba como usar valores nomeados nas políticas de Gerenciamento de API do Azure. Os valores nomeados podem conter cadeias de caracteres literais, expressões de política e segredos armazenados em Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 2bc9b1c5724fa7bab1fdf5ac9332d87ba03a6d11
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545795"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Usar valores nomeados em políticas de gerenciamento de API do Azure

[As políticas de gerenciamento de API](api-management-howto-policies.md) são um recurso poderoso do sistema que permite que o editor altere o comportamento da API por meio da configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. É possível construir declarações de política usando valores de texto literais, expressões de política e valores nomeados.

*Os valores nomeados* são uma coleção global de pares de nome/valor em cada instância de gerenciamento de API. Não há limite imposto sobre o número de itens na coleção. Os valores nomeados podem ser usados para gerenciar valores de cadeia de caracteres constantes e segredos em todas as políticas e configurações de API. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Valores nomeados na portal do Azure":::

## <a name="value-types"></a>Tipos de valor

|Type  |Descrição  |
|---------|---------|
|Queixa     |  Cadeia de caracteres literal ou expressão de política     |
|Segredo     |   Cadeia de caracteres literal ou expressão de política que é criptografada pelo gerenciamento de API      |
|[Cofre de chaves](#key-vault-secrets)     |  Identificador de um segredo armazenado em um cofre de chaves do Azure.      |

Os valores ou segredos simples podem conter [expressões de política](./api-management-policy-expressions.md). Por exemplo, a expressão `@(DateTime.Now.ToString())` retorna uma cadeia de caracteres que contém a data e a hora atuais.

Para obter detalhes sobre os atributos de valor nomeado, consulte a [referência da API REST](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)do gerenciamento de API.

## <a name="key-vault-secrets"></a>Segredos do cofre de chaves

Os valores secretos podem ser armazenados como cadeias de caracteres criptografadas no gerenciamento de API (segredos personalizados) ou referenciando segredos no [Azure Key Vault](../key-vault/general/overview.md). 

O uso de segredos do Key Vault é recomendado porque ajuda a melhorar a segurança do gerenciamento de API:

* Os segredos armazenados em cofres de chaves podem ser reutilizados em todos os serviços
* [As políticas de acesso](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) granular podem ser aplicadas aos segredos
* Os segredos atualizados no cofre de chaves são automaticamente girados no gerenciamento de API. Após a atualização no cofre de chaves, um valor nomeado no gerenciamento de API é atualizado dentro de 4 horas. Você também pode atualizar manualmente o segredo usando o portal do Azure ou por meio da API REST de gerenciamento.

### <a name="prerequisites-for-key-vault-integration"></a>Pré-requisitos para a integração do Key Vault

1. Para obter as etapas para criar um cofre de chaves, consulte [início rápido: criar um cofre de chaves usando o portal do Azure](../key-vault/general/quick-create-portal.md).
1. Habilite uma [identidade gerenciada](api-management-howto-use-managed-service-identity.md) atribuída pelo sistema ou atribuída pelo usuário na instância do gerenciamento de API.
1. Atribua uma [política de acesso do cofre de chaves](../key-vault/general/assign-access-policy-portal.md) à identidade gerenciada com permissões para obter e listar segredos do cofre. Para adicionar a política:
    1. No portal, navegue até o cofre de chaves.
    1. Selecione **configurações > políticas de acesso > + adicionar política de acesso**.
    1. Selecione **permissões de segredo** e, em seguida, selecione **obter** e **listar**.
    1. Em **selecionar entidade de segurança**, selecione o nome do recurso da sua identidade gerenciada. Se você estiver usando uma identidade atribuída pelo sistema, o principal será o nome da sua instância de gerenciamento de API.
1. Crie ou importe um segredo para o cofre de chaves. Consulte [início rápido: definir e recuperar um segredo de Azure Key Vault usando o portal do Azure](../key-vault/secrets/quick-create-portal.md).

Para usar o segredo do cofre de chaves, [adicione ou edite um valor nomeado](#add-or-edit-a-named-value)e especifique um tipo de **cofre de chaves**. Selecione o segredo no cofre de chaves.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Adicionar ou editar um valor nomeado

### <a name="add-a-key-vault-secret"></a>Adicionar um segredo do Key Vault

Consulte [pré-requisitos para a integração do Key Vault](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Ao usar um segredo do cofre de chaves no gerenciamento de API, tenha cuidado para não excluir o segredo, o cofre de chaves ou a identidade gerenciada usada para acessar o cofre de chaves.

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Em **APIs**, selecione **valores nomeados**  >  **+ Adicionar**.
1. Insira um identificador de **nome** e insira um **nome de exibição** usado para fazer referência à propriedade em políticas.
1. Em **tipo de valor**, selecione **cofre de chaves**.
1. Insira o identificador de um segredo do cofre de chaves (sem versão) ou escolha **selecionar** para selecionar um segredo de um cofre de chaves.
    > [!IMPORTANT]
    > Se você mesmo inserir um identificador secreto do Key Vault, verifique se ele não tem informações de versão. Caso contrário, o segredo não será girado automaticamente no gerenciamento de API após uma atualização no cofre de chaves.
1. Em **identidade do cliente**, selecione uma identidade gerenciada atribuída pelo usuário ou uma existente. Saiba como [Adicionar ou modificar identidades gerenciadas em seu serviço de gerenciamento de API](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > A identidade precisa de permissões para obter e listar segredos do cofre de chaves. Se você ainda não tiver configurado o acesso ao cofre de chaves, o gerenciamento de API solicitará que ele possa configurar automaticamente a identidade com as permissões necessárias.
1. Adicione uma ou mais marcas opcionais para ajudar a organizar seus valores nomeados e, em seguida, **salve**.
1. Selecione **Criar**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Adicionar valor secreto do cofre de chaves":::

### <a name="add-a-plain-or-secret-value"></a>Adicionar um valor simples ou secreto

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Em **APIs**, selecione **valores nomeados**  >  **+ Adicionar**.
1. Insira um identificador de **nome** e insira um **nome de exibição** usado para fazer referência à propriedade em políticas.
1. Em **tipo de valor**, selecione **simples** ou **segredo**.
1. Em **valor**, insira uma expressão de cadeia de caracteres ou de política.
1. Adicione uma ou mais marcas opcionais para ajudar a organizar seus valores nomeados e, em seguida, **salve**.
1. Selecione **Criar**.

Depois que o valor nomeado for criado, você poderá editá-lo selecionando o nome. Se você alterar o nome de exibição, todas as políticas que fazem referência a esse valor nomeado serão atualizadas automaticamente para usar o novo nome de exibição.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar o CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para adicionar um valor nomeado, use o comando [AZ APIM NV Create](/cli/azure/apim/nv#az_apim_nv_create) :

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

Depois de criar um valor nomeado, você pode atualizá-lo usando o comando [AZ APIM NV Update](/cli/azure/apim/nv#az_apim_nv_update) . Para ver todos os seus valores nomeados, execute o comando [AZ APIM NV List](/cli/azure/apim/nv#az_apim_nv_list) :

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Para ver os detalhes do valor nomeado que você criou para este exemplo, execute o comando [AZ APIM NV show](/cli/azure/apim/nv#az_apim_nv_show) :

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Este exemplo é um valor secreto. O comando anterior não retorna o valor. Para ver o valor, execute o comando [AZ APIM NV show-Secret](/cli/azure/apim/nv#az_apim_nv_show_secret) :

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Para excluir um valor nomeado, use o comando [AZ APIM NV Delete](/cli/azure/apim/nv#az_apim_nv_delete) :

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Usar um valor nomeado

Os exemplos nesta seção usam os valores nomeados mostrados na tabela a seguir.

| Nome               | Valor                      | Segredo | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Falso  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Falso  | 

Para usar um valor nomeado em uma política, coloque seu nome de exibição dentro de um par duplo de chaves como `{{ContosoHeader}}` , conforme mostrado no exemplo a seguir:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma política `set-header` e `ContosoHeaderValue` é usado como o valor desse cabeçalho. Quando essa política é avaliada durante uma solicitação ou uma resposta para o gateway de Gerenciamento de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídos por seus respectivos valores.

Os valores nomeados podem ser usados como valores de atributo ou de elemento, como mostra o exemplo anterior, mas também podem ser inseridos ou combinados com parte de uma expressão de texto literal, como mostra o exemplo a seguir:  

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Os valores nomeados também podem conter expressões de política. No exemplo a seguir, a `ExpressionProperty` expressão é usada.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando essa política é avaliada, `{{ExpressionProperty}}` é substituída por seu valor, `@(DateTime.Now.ToString())` . Como o valor é uma expressão de política, a expressão é avaliada e a política prossegue com a execução.

Você pode testá-lo na portal do Azure ou no [portal do desenvolvedor](api-management-howto-developer-portal.md) chamando uma operação que tenha uma política com valores nomeados no escopo. No exemplo a seguir, uma operação é chamada com os dois exemplos de políticas `set-header` anteriores com valores nomeados. Observe que a resposta contém dois cabeçalhos personalizados que foram configurados usando políticas com valores nomeados.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="Testar resposta da API":::

Se você examinar o rastreamento de [API](api-management-howto-api-inspector.md) de saída para uma chamada que inclui as duas políticas de exemplo anteriores com valores nomeados, você poderá ver as duas `set-header` políticas com os valores nomeados inseridos, bem como a avaliação da expressão de política para o valor nomeado que continha a expressão de política.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="Rastreamento do Inspetor de API":::

> [!CAUTION]
> Se uma política fizer referência a um segredo em Azure Key Vault, o valor do cofre de chaves ficará visível para os usuários que têm acesso às assinaturas habilitadas para [rastreamento de solicitação de API](api-management-howto-api-inspector.md).

Embora os valores nomeados possam conter expressões de política, eles não podem conter outros valores nomeados. Se o texto que contém uma referência de valor nomeado for usado para um valor, como `Text: {{MyProperty}}`, essa referência não será resolvida e substituída.

## <a name="delete-a-named-value"></a>Excluir um valor nomeado

Para excluir um valor nomeado, selecione o nome e, em seguida, selecione **excluir** no menu de contexto (**...**).

> [!IMPORTANT]
> Se o valor nomeado for referenciado por qualquer política de gerenciamento de API, você não poderá excluí-lo até remover o valor nomeado de todas as políticas que o utilizam.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre como trabalhar com políticas
    -   [Políticas no Gerenciamento de API](api-management-howto-policies.md)
    -   [Referência de política](./api-management-policies.md)
    -   [Expressões de política](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

