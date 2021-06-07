---
title: Bloquear conexões para conectores de API específicos
description: Restringir a criação e o uso de conexões de API nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 02d9852f6615c3926a02294e0e7eca50f2fbe9a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310028"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Bloquear conexões criadas por conectores em aplicativos lógicos do Azure

Se sua organização não permitir a conexão com recursos restritos ou não aprovados usando seus conectores em aplicativos lógicos do Azure, você poderá bloquear a capacidade de criar e usar essas conexões em fluxos de trabalho de aplicativo lógico. Com [Azure Policy](../governance/policy/overview.md), você pode definir e impor [políticas](../governance/policy/overview.md#policy-definition) que impedem a criação ou o uso de conexões para conectores que você deseja bloquear. Por exemplo, por motivos de segurança, talvez você queira bloquear conexões para plataformas de mídia social específicas ou outros serviços e sistemas.

Este tópico mostra como configurar uma política que bloqueia conexões específicas usando o portal do Azure, mas você pode criar definições de política de outras maneiras, por exemplo, por meio da API REST do Azure, Azure PowerShell, CLI do Azure e modelos Azure Resource Manager. Para obter mais informações, consulte [tutorial: criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

* A ID de referência para o conector que você deseja bloquear. Para obter mais informações, consulte [localizar a ID de referência do conector](#connector-reference-ID).

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>Localizar ID de referência do conector

Se você já tiver um aplicativo lógico com a conexão que deseja bloquear, siga as [etapas para o portal do Azure](#connector-ID-portal). Caso contrário, siga estas etapas:

1. Visite a [lista conectores de aplicativos lógicos](/connectors/connector-reference/connector-reference-logicapps-connectors).

1. Localize a página de referência do conector que você deseja bloquear.

   Por exemplo, se você quiser bloquear o conector do Instagram, que é preterido, vá para esta página:

   `https://docs.microsoft.com/connectors/instagram/`

1. Na URL da página, copie e salve a ID de referência do conector no final sem a barra ( `/` ), por exemplo, `instagram` .

   Posteriormente, ao criar a definição de política, você usará essa ID na instrução de condição da definição, por exemplo:

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Portal do Azure

1. Na [portal do Azure](https://portal.azure.com), localize e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, selecione **modo de exibição de código do aplicativo lógico** para que você possa exibir a definição de JSON do aplicativo lógico.

   ![Abra a "exibição do código do aplicativo lógico" para localizar a ID do conector](./media/block-connections-connectors/code-view-connector-id.png)

1. Localize o `parameters` objeto que contém o `$connections` objeto, que inclui um `{connection-name}` objeto para cada conexão em seu aplicativo lógico e especifica informações sobre essa conexão:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   Por exemplo, para o conector do Instagram, localize o `instagram` objeto, que identifica uma conexão Instagram:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. Para a conexão que você deseja bloquear, localize a `id` propriedade e o valor, que segue este formato: 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   Por exemplo, aqui está a `id` propriedade e o valor de uma conexão Instagram:

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. No `id` valor da propriedade, copie e salve a ID de referência do conector no final, por exemplo, `instagram` .

   Posteriormente, ao criar a definição de política, você usará essa ID na instrução de condição da definição, por exemplo:

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>Criar política para bloquear a criação de conexões

Para bloquear a criação de uma conexão totalmente em um aplicativo lógico, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com). Na caixa de pesquisa do portal, digite `policy` e selecione **política**.

   ![Em portal do Azure, localize e selecione "política"](./media/block-connections-connectors/find-select-azure-policy.png)

1. No menu **política** , em **criação**, selecione **definições**  >  **+ definição de política**.

   ![Selecione "definições" > "+ definição de política"](./media/block-connections-connectors/add-new-policy-definition.png)

1. Em **definição de política**, forneça as informações para a definição de política, com base nas propriedades descritas no exemplo:

   ![Captura de tela que mostra as propriedades de "definição de política".](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Local da definição** | Sim | <*Azure-subscription-name*> | A assinatura do Azure a ser usada para a definição de política <p><p>1. para localizar sua assinatura, selecione o botão de reticências (**...**). <br>2. na lista de **assinaturas** , localize e selecione sua assinatura. <br>3. quando terminar, selecione **selecionar**. |
   | **Nome** | Sim | <*política-definição-nome*> | O nome a ser usado para a definição de política |
   | **Descrição** | Não | <*política-definição-nome*> | Uma descrição para a definição de política |
   | **Categoria** | Sim | **Aplicativos lógicos** | O nome de uma categoria existente ou uma nova categoria para a definição de política |
   | **Imposição de política** | Sim | **Enabled** | Essa configuração especifica se a definição de política deve ser habilitada ou desabilitada quando você salva seu trabalho. |
   ||||

1. Em **regra de política**, a caixa de edição JSON é preenchida previamente com um modelo de definição de política. Substitua este modelo pela sua [definição de política](../governance/policy/concepts/definition-structure.md) com base nas propriedades descritas na tabela abaixo e seguindo esta sintaxe:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | `mode` | `All` | O modo que determina os tipos de recursos que a política avalia. <p><p>Esse cenário define `mode` como `All` , que aplica a política a grupos de recursos do Azure, assinaturas e todos os tipos de recursos. <p><p>Para obter mais informações, consulte [estrutura de definição de política – modo](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | A condição que determina quando impor a regra de política <p><p>Nesse cenário, o `{condition-to-evaluate}` determina se o `api.id` valor em `Microsoft.Web/connections/api.id` corresponde em `*managedApis/{connector-name}` , que especifica um valor curinga (*). <p><p>Para obter mais informações, consulte [estrutura de definição de política – regra de política](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `field` | `Microsoft.Web/connections/api.id` | O `field` valor a ser comparado com a condição <p><p>Nesse cenário, o `field` usa o [*alias*](../governance/policy/concepts/definition-structure.md#aliases), `Microsoft.Web/connections/api.id` , para acessar o valor na Propriedade do conector, `api.id` . |
   | `like` | `*managedApis/{connector-name}` | O operador lógico e o valor a ser usado para comparar o `field` valor <p><p>Nesse cenário, o `like` operador e o caractere curinga (*) verificam se a regra funciona independentemente da região e a cadeia de caracteres, `*managedApis/{connector-name}` , é o valor a ser correspondido onde `{connector-name}` é a ID do conector que você deseja bloquear. <p><p>Por exemplo, suponha que você deseja bloquear a criação de conexões para bancos de dados ou plataformas de mídia social: <p><p>Twitter `twitter` <br>Instagram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>-SQL Server ou SQL do Azure: `sql` <p><p>Para encontrar essas IDs de conector, consulte [Localizar ID de referência do conector](#connector-reference-ID) anteriormente neste tópico. |
   | `then` | `{effect-to-apply}` | O efeito a ser aplicado quando a `if` condição é atendida <p><p>Nesse cenário, o `{effect-to-apply}` é bloquear e reprovar uma solicitação ou operação que não está em conformidade com a política. <p><p>Para obter mais informações, consulte [estrutura de definição de política – regra de política](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | O `effect` é para bloquear a solicitação, que é criar a conexão especificada <p><p>Para obter mais informações, consulte [entender Azure Policy efeitos-negar](../governance/policy/concepts/effects.md#deny). |
   ||||

   Por exemplo, suponha que você deseja bloquear a criação de conexões com o conector do Instagram. Aqui está a definição de política que você pode usar:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   Veja a seguir como a caixa **regra de política** é exibida:

   ![Captura de tela que mostra a caixa "regra de política" com um exemplo de regra de política.](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   Para vários conectores, você pode adicionar mais condições, por exemplo:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. Quando terminar, selecione **Salvar**. Depois de salvar a definição de política, o Azure Policy gera e adiciona mais valores de propriedade à definição de política.

1. Em seguida, para atribuir a definição de política onde você deseja impor a política, [crie uma atribuição de política](#create-policy-assignment).

Para obter mais informações sobre as definições de política do Azure, consulte estes tópicos:

* [Definição da estrutura de política](../governance/policy/concepts/definition-structure.md)
* [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)
* [Definições de políticas internas do Azure Policy para os Aplicativos Lógicos do Azure](./policy-reference.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>Criar política para bloquear usando conexões

Quando você cria uma conexão dentro de um aplicativo lógico, essa conexão existe como um recurso separado do Azure. Se você excluir apenas o aplicativo lógico, a conexão não será excluída automaticamente e continuará a existir até ser excluída. Você pode ter um cenário em que a conexão já existe ou onde você precisa criar a conexão para uso fora de um aplicativo lógico. Você ainda pode bloquear a capacidade de usar uma conexão existente em um aplicativo lógico criando uma política que impede o salvamento de aplicativos lógicos que têm a conexão restrita ou não aprovada.

1. Entre no [portal do Azure](https://portal.azure.com). Na caixa de pesquisa do portal, digite `policy` e selecione **política**.

   ![Em portal do Azure, localize e selecione "política"](./media/block-connections-connectors/find-select-azure-policy.png)

1. No menu **política** , em **criação**, selecione **definições**  >  **+ definição de política**.

   ![Selecione "definições" > "+ definição de política"](./media/block-connections-connectors/add-new-policy-definition.png)

1. Em **definição de política**, forneça as informações para a definição de política, com base nas propriedades descritas no exemplo e continua usando Instagram como o exemplo:

   ![Propriedades de definição de política](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Local da definição** | Sim | <*Azure-subscription-name*> | A assinatura do Azure a ser usada para a definição de política <p><p>1. para localizar sua assinatura, selecione o botão de reticências (**...**). <br>2. na lista de **assinaturas** , localize e selecione sua assinatura. <br>3. quando terminar, selecione **selecionar**. |
   | **Nome** | Sim | <*política-definição-nome*> | O nome a ser usado para a definição de política |
   | **Descrição** | Não | <*política-definição-nome*> | Uma descrição para a definição de política |
   | **Categoria** | Sim | **Aplicativos lógicos** | O nome de uma categoria existente ou uma nova categoria para a definição de política |
   | **Imposição de política** | Sim | **Enabled** | Essa configuração especifica se a definição de política deve ser habilitada ou desabilitada quando você salva seu trabalho. |
   ||||

1. Em **regra de política**, a caixa de edição JSON é preenchida previamente com um modelo de definição de política. Substitua este modelo pela sua [definição de política](../governance/policy/concepts/definition-structure.md) com base nas propriedades descritas na tabela abaixo e seguindo esta sintaxe:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | `mode` | `All` | O modo que determina os tipos de recursos que a política avalia. <p><p>Esse cenário define `mode` como `All` , que aplica a política a grupos de recursos do Azure, assinaturas e todos os tipos de recursos. <p><p>Para obter mais informações, consulte [estrutura de definição de política – modo](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | A condição que determina quando impor a regra de política <p><p>Nesse cenário, o `{condition-to-evaluate}` determina se a saída da cadeia de caracteres de `[string(field('Microsoft.Logic/workflows/parameters'))]` , contém a cadeia de caracteres, `{connector-name}` . <p><p>Para obter mais informações, consulte [estrutura de definição de política – regra de política](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | O valor a ser comparado com a condição <p><p>Nesse cenário, a `value` é a saída da cadeia de caracteres de `[string(field('Microsoft.Logic/workflows/parameters'))]` , que converte o `$connectors` objeto dentro do `Microsoft.Logic/workflows/parameters` objeto em uma cadeia de caracteres. |
   | `contains` | `{connector-name}` | O operador lógico e o valor a ser usado para comparar com a `value` Propriedade <p><p>Nesse cenário, o `contains` operador garante que a regra funcione independentemente de onde `{connector-name}` aparece, em que a cadeia de caracteres, `{connector-name}` , é a ID do conector que você deseja restringir ou bloquear. <p><p>Por exemplo, suponha que você deseja bloquear usando conexões para bancos de dados ou plataformas de mídia social: <p><p>Twitter `twitter` <br>Instagram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>-SQL Server ou SQL do Azure: `sql` <p><p>Para encontrar essas IDs de conector, consulte [Localizar ID de referência do conector](#connector-reference-ID) anteriormente neste tópico. |
   | `then` | `{effect-to-apply}` | O efeito a ser aplicado quando a `if` condição é atendida <p><p>Nesse cenário, o `{effect-to-apply}` é bloquear e reprovar uma solicitação ou operação que o não está em conformidade com a política. <p><p>Para obter mais informações, consulte [estrutura de definição de política – regra de política](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | O `effect` é `deny` ou bloqueia a solicitação para salvar um aplicativo lógico que usa a conexão especificada <p><p>Para obter mais informações, consulte [entender Azure Policy efeitos-negar](../governance/policy/concepts/effects.md#deny). |
   ||||

   Por exemplo, suponha que você deseja bloquear o salvamento de aplicativos lógicos que usam conexões Instagram. Aqui está a definição de política que você pode usar:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   Veja a seguir como a caixa **regra de política** é exibida:

   ![Regra para definição de política](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. Quando terminar, selecione **Salvar**. Depois de salvar a definição de política, o Azure Policy gera e adiciona mais valores de propriedade à definição de política.

1. Em seguida, para atribuir a definição de política onde você deseja impor a política, [crie uma atribuição de política](#create-policy-assignment).

Para obter mais informações sobre as definições de política do Azure, consulte estes tópicos:

* [Definição da estrutura de política](../governance/policy/concepts/definition-structure.md)
* [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)
* [Definições de políticas internas do Azure Policy para os Aplicativos Lógicos do Azure](./policy-reference.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>Criar atribuição de política

Em seguida, você precisa atribuir a definição de política na qual deseja impor a política, por exemplo, a um único grupo de recursos, a vários grupos de recursos, ao locatário do Azure Active Directory (Azure AD) ou à assinatura do Azure. Para essa tarefa, siga estas etapas para criar uma atribuição de política:

1. Se você se desconectou, entre novamente no [portal do Azure](https://portal.azure.com). Na caixa de pesquisa do portal, digite `policy` e selecione **política**.

   ![Em portal do Azure, localize e selecione "política"](./media/block-connections-connectors/find-select-azure-policy.png)

1. No menu **política** , em **criação**, selecione **atribuições**  >  **atribuir política**.

   ![Selecione "atribuições" > "atribuir"](./media/block-connections-connectors/add-new-policy-assignment.png)

1. Em **noções básicas**, forneça estas informações para a atribuição de política:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Escopo** | Sim | Os recursos em que você deseja impor a atribuição de política. <p><p>1. ao lado da caixa **escopo** , selecione o botão de reticências (**...**). <br>2. na lista **assinatura** , selecione a assinatura do Azure. <br>3. opcionalmente, na lista **grupo de recursos** , selecione o grupo de recursos. <br>4. quando terminar, selecione **selecionar**. |
   | **Exclusões** | Não | Todos os recursos do Azure a serem excluídos da atribuição de política. <p><p>1. ao lado da caixa **exclusões** , selecione o botão de reticências (**...**). <br>2. na lista de **recursos** , selecione o recurso > **Adicionar ao escopo selecionado**. <br>3. quando terminar, selecione **salvar**. |
   | **Definição de política** | Sim | O nome da definição de política que você deseja atribuir e impor. Este exemplo continua com a política Instagram de exemplo, "bloquear conexões Instagram". <p><p>1. ao lado da caixa **definição de política** , selecione o botão de reticências (**...**). <br>2. Localize e selecione a definição de política usando o filtro de **tipo** ou a caixa de **pesquisa** . <br>3. quando terminar, selecione **selecionar**. |
   | **Nome da atribuição** | Sim | O nome a ser usado para a atribuição de política, se for diferente da definição de política |
   | **ID de atribuição** | Sim | A ID gerada automaticamente para a atribuição de política |
   | **Descrição** | Não | Uma descrição para a atribuição de política |
   | **Imposição de política** | Sim | A configuração que habilita ou desabilita a atribuição de política |
   | **Atribuída por** | Não | O nome da pessoa que criou e aplicou a atribuição de política |
   ||||

   Por exemplo, para atribuir a política a um grupo de recursos do Azure usando o exemplo Instagram:

   ![Propriedades de atribuição de política](./media/block-connections-connectors/policy-assignment-basics.png)

1. Quando terminar, selecione **Review + create**.

   Depois de criar uma política, talvez seja necessário aguardar até 15 minutos antes de a política entrar em vigor. As alterações também podem ter efeitos atrasados semelhantes.

1. Depois que a política entrar em vigor, você poderá [testar sua política](#test-policy).

Para obter mais informações, consulte [início rápido: criar uma atribuição de política para identificar recursos sem conformidade](../governance/policy/assign-policy-portal.md).

<a name="test-policy"></a>

## <a name="test-the-policy"></a>Testar a política

Para testar a política, comece a criar uma conexão usando o conector restrito agora no designer do aplicativo lógico. Continuando com o exemplo Instagram, quando você entra no Instagram, obtém esse erro de que seu aplicativo lógico falhou ao criar a conexão:

![Falha de conexão devido à política aplicada](./media/block-connections-connectors/connection-failure-message.png)

A mensagem inclui essas informações:

| Descrição | Conteúdo |
|-------------|---------|
| Motivo da falha | `"Resource 'instagram' was disallowed by policy."` |
| Nome da atribuição | `"Block Instagram connections"` |
| ID de atribuição | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| ID de definição de política | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Azure Policy](../governance/policy/overview.md)