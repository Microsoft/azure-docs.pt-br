---
title: Autenticar com identidades gerenciadas
description: Acessar recursos protegidos por Azure Active Directory sem entrar com credenciais ou segredos usando uma identidade gerenciada
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 03/09/2021
ms.openlocfilehash: 7796fc7e2032559ca3ff5c738c46fe025719942d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556614"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticar o acesso a recursos do Azure usando identidades gerenciadas em Aplicativos Lógicos do Azure

Para acessar facilmente outros recursos protegidos pelo Azure Active Directory (Azure AD) e autenticar sua identidade, seu aplicativo lógico pode usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) (antigamente identidade de serviço gerenciada ou msi), em vez de credenciais, segredos ou tokens do Azure AD. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais porque você não precisa gerenciar segredos ou usar diretamente tokens do Azure AD.

Os Aplicativos Lógicos do Azure dão suporte a identidades gerenciadas [*atribuídas pelo sistema*](../active-directory/managed-identities-azure-resources/overview.md) e [*atribuídas pelo usuário*](../active-directory/managed-identities-azure-resources/overview.md). Seu aplicativo lógico ou conexões individuais podem usar a identidade atribuída pelo sistema ou uma *única* identidade atribuída pelo usuário, que você pode compartilhar em um grupo de aplicativos lógicos, mas não ambos.

## <a name="where-can-logic-apps-use-managed-identities"></a>Onde os aplicativos lógicos podem usar identidades gerenciadas?

Atualmente, somente [gatilhos e ações internas específicas](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) e [conectores gerenciados específicos](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) que dão suporte ao Azure ad OAuth podem usar uma identidade gerenciada para autenticação. Por exemplo, aqui está uma seleção:

**Gatilhos e ações internas**

* Gerenciamento de API do Azure
* Serviços de Aplicativo do Azure
* Funções do Azure
* HTTP
* HTTP + webhook

> [!NOTE]
> Embora o gatilho e a ação HTTP possam autenticar conexões com contas de armazenamento do Azure por trás de firewalls do Azure usando a identidade gerenciada atribuída pelo sistema, eles não podem usar a identidade gerenciada atribuída pelo usuário para autenticar as mesmas conexões.

**Conectores gerenciados**

* Automação do Azure
* Grade de Eventos do Azure
* Cofre de Chave do Azure
* Azure Resource Manager
* HTTP com o Azure AD

O suporte para conectores gerenciados está atualmente em versão prévia. Para obter a lista atual, consulte [tipos de autenticação para gatilhos e ações que dão suporte à autenticação](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

Este artigo mostra como configurar os dois tipos de identidades gerenciadas para seu aplicativo lógico. Para saber mais, consulte esses tópicos:

* [Gatilhos e ações que dão suporte a identidades gerenciadas](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [Limites de identidades gerenciadas para aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Serviços do Azure que dão suporte à autenticação do Azure AD com identidades gerenciadas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). A identidade gerenciada e o recurso do Azure de destino em que você precisa de acesso devem usar a mesma assinatura do Azure.

* Para conceder a uma identidade gerenciada acesso a um recurso do Azure, você precisa adicionar uma função ao recurso de destino para essa identidade. Para adicionar funções, você precisa de [permissões de administrador do Azure AD](../active-directory/roles/permissions-reference.md) que podem atribuir funções a identidades no locatário do Azure AD correspondente.

* O recurso do Azure de destino que você deseja acessar. Nesse recurso, você adicionará uma função à identidade gerenciada, que ajuda o aplicativo lógico a autenticar o acesso ao recurso de destino.

* O aplicativo lógico no qual você deseja usar o [gatilho ou as ações que dão suporte a identidades gerenciadas](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

## <a name="enable-managed-identity"></a>Habilitar identidade gerenciada

Para configurar a identidade gerenciada que você deseja usar, siga o link para essa identidade:

* [Identidade atribuída ao sistema](#system-assigned)
* [Identidade atribuída ao usuário](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Habilitar identidade atribuída ao sistema

Diferentemente de identidades atribuídas pelo sistema, você não precisa criar manualmente a identidade atribuída ao sistema. Para configurar a identidade atribuída ao sistema para seu aplicativo lógico, estão são as opções que você pode usar:

* [Azure portal](#azure-portal-system-logic-app)
* [Modelos do Gerenciador de Recursos do Azure](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Habilitar a identidade atribuída ao sistema no portal do Azure

1. No [Portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Identidade**. Selecione **Atribuída ao sistema** > **Ativada** > **Salvar**. Quando o Azure solicitar que você confirme, selecione **Sim**.

   ![Habilitar a identidade atribuída ao sistema](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Se você receber um erro de que você pode ter apenas uma identidade gerenciada, seu aplicativo lógico já estará associado à identidade atribuída ao usuário. Para adicionar a identidade atribuída ao sistema, primeiro *remova* a identidade atribuída ao usuário do seu aplicativo lógico.

   Seu aplicativo lógico agora pode usar a identidade atribuída pelo sistema, que é registrada com o Azure AD e é representada por uma ID de objeto.

   ![ID de objeto da identidade atribuída ao sistema](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **ID do objeto** | <*identity-resource-ID*> | Um GUID (identificador global exclusivo) que representa a identidade atribuída ao sistema para o seu aplicativo lógico em um locatário do Azure AD |
   ||||

1. Agora siga as [etapas que fornecem a essa identidade acesso ao recurso](#access-other-resources) mais adiante neste tópico.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Habilitar identidade atribuída ao sistema no modelo do Azure Resource Manager

Para automatizar a criação e a implantação de recursos do Azure, como os aplicativos lógicos, você pode usar [modelos do Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Para habilitar a identidade gerenciada atribuída ao sistema para seu aplicativo lógico no modelo, adicione o objeto `identity` e a propriedade filho `type` à definição de recurso do aplicativo lógico no modelo, por exemplo:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Quando o Azure cria sua definição de recurso de aplicativo lógico, o objeto `identity` obtém estas propriedades adicionais:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriedade (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | O GUID (identificador global exclusivo) do objeto de entidade de serviço para a identidade gerenciada que representa seu aplicativo lógico no locatário do Azure AD. Esse GUID, às vezes, é exibido como uma "ID de objeto" ou `objectID`. |
| `tenantId` | <*Azure-AD-tenant-ID*> | O GUID (identificador global exclusivo) que representa o locatário do Azure AD do qual o aplicativo lógico agora é membro. Dentro do locatário do Azure AD, a entidade de serviço tem o mesmo nome que a instância do aplicativo lógico. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Habilitar identidade atribuída ao usuário

Para configurar uma identidade gerenciada atribuída ao usuário para seu aplicativo lógico, primeiro você deve criar essa identidade como um recurso autônomo do Azure separado. Estas são as opções que você pode usar:

* [Azure portal](#azure-portal-user-identity)
* [Modelos do Gerenciador de Recursos do Azure](#template-user-identity)
* Azure PowerShell
  * [Criar identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Adicionar atribuição de função](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* CLI do Azure
  * [Criar identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Adicionar atribuição de função](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* API REST do Azure
  * [Criar identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Adicionar atribuição de função](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Criar identidade atribuída ao usuário no portal do Azure

1. No [portal do Azure](https://portal.azure.com), na caixa de pesquisa em qualquer página, insira `managed identities` e selecione **Identidades gerenciadas**.

   ![Localizar e selecionar "Identidades Gerenciadas"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Em **identidades Gerenciadas**, selecione **Adicionar**.

   ![Adicionar nova identidade gerenciada](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Forneça informações sobre sua identidade gerenciada e selecione **revisar + criar**, por exemplo:

   ![Criar identidade gerenciada atribuída ao usuário](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Assinatura** | Sim | <*Azure-subscription-name*> | O nome da assinatura do Azure a ser usado |
   | **Grupo de recursos** | Sim | <*Azure-resource-group-name*> | O nome do grupo de recursos a ser usado. Crie um grupo ou selecione um existente. Este exemplo cria um novo grupo chamado `fabrikam-managed-identities-RG` . |
   | **Região** | Sim | <*Azure-region*> | A região do Azure na qual armazenar informações sobre seu recurso. Este exemplo usa "Oeste dos EUA". |
   | **Nome** | Sim | <*user-assigned-identity-name*> | O nome a ser dado à sua identidade atribuída ao usuário. Este exemplo usa o `Fabrikam-user-assigned-identity`. |
   |||||

   Depois de validar esses detalhes, o Azure cria sua identidade gerenciada. Agora você pode adicionar a identidade atribuída ao usuário ao seu aplicativo lógico. Você não pode adicionar mais de uma identidade atribuída ao usuário ao seu aplicativo lógico.

1. No portal do Azure, encontre e abra seu aplicativo lógico no Designer de Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Identidade** e, em seguida, **Atribuída ao usuário** > **Adicionar**.

   ![Adicionar identidade gerenciada atribuída ao usuário](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. No painel **Adicionar identidade gerenciada atribuída ao usuário**, na lista **Assinatura**, selecione sua assinatura do Azure, se ainda não tiver selecionado. Na lista que mostra *todas* as identidades gerenciadas nessa assinatura, localize e selecione a identidade atribuída ao usuário desejada. Para filtrar a lista, na caixa de pesquisa **Identidades gerenciadas atribuídas ao usuário**, insira o nome da identidade ou do grupo de recursos. Quando terminar, selecione **Adicionar**.

   ![Selecione a identidade atribuída ao usuário a ser usada](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Se você receber um erro de que você pode ter apenas uma identidade gerenciada, seu aplicativo lógico já estará associado à identidade atribuída ao sistema. Para adicionar a identidade atribuída ao usuário, primeiro desabilite a identidade atribuída ao sistema em seu aplicativo lógico.

   Seu aplicativo lógico agora está associado à identidade gerenciada atribuída ao usuário.

   ![Associação com a identidade atribuída ao usuário](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Agora siga as [etapas que fornecem a essa identidade acesso ao recurso](#access-other-resources) mais adiante neste tópico.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Criar identidade atribuída ao usuário em um modelo do Azure Resource Manager

Para automatizar a criação e a implantação de recursos do Azure como aplicativos lógicos, você pode usar [modelos do Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), que dão suporte a [identidades atribuídas ao usuário para autenticação](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). Na seção `resources` do seu modelo, a definição de recurso do aplicativo lógico requer estes itens:

* Um objeto `identity` com a propriedade `type` definida como `UserAssigned`

* Um `userAssignedIdentities` objeto filho que especifica o nome e o recurso atribuídos pelo usuário

Este exemplo mostra uma definição de recurso de aplicativo lógico para uma solicitação HTTP PUT e inclui um objeto `identity` não parametrizado. A resposta à solicitação PUT e a operação GET subsequente também têm este objeto `identity`:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Se o modelo também incluir a definição de recurso da identidade gerenciada, você poderá parametrizar o objeto `identity`. Este exemplo mostra como o objeto filho `userAssignedIdentities` faz referência a uma variável `userAssignedIdentity` que você define na seção `variables` do seu modelo. Essa variável referencia a ID de recurso para sua identidade atribuída ao usuário.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Conceder à identidade acesso aos recursos

Para usar a identidade gerenciada do aplicativo lógico para autenticação, configure o acesso para essa identidade no recurso do Azure em que você planeja usar a identidade. Para concluir essa tarefa, atribua a função apropriada a essa identidade no recurso do Azure de destino. Estas são as opções que você pode usar:

* [Azure portal](#azure-portal-assign-access)
* [Modelo do Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* O Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) – para obter mais informações, confira [Adicionar atribuição de função usando o Azure RBAC e o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* CLI do Azure ([az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)) – para obter mais informações, confira [Adicionar atribuição de função usando o Azure RBAC e a CLI do Azure](../role-based-access-control/role-assignments-cli.md).
* [API REST do Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Atribuir acesso no portal do Azure

No recurso de destino do Azure em que você deseja que a identidade gerenciada tenha acesso, conceda a essa identidade acesso baseado em função ao recurso de destino.

1. No [portal do Azure](https://portal.azure.com), acesse o recurso do Azure ao qual você deseja que sua identidade gerenciada tenha acesso.

1. No menu do recurso, selecione **Controle de acesso (IAM)**  > **Atribuições de função** em que você pode examinar as atribuições de função atuais para esse recurso. Na barra de ferramentas, selecione **Adicionar** > **Adicionar atribuição de função**.

   ![Selecione “Adicionar” > “Adicionar atribuição de função”](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se a opção **Adicionar atribuição de função** estiver desabilitada, provavelmente você não terá permissões. Para obter mais informações sobre as permissões com as quais você pode gerenciar funções para recursos, confira [Permissões da função Administrador no Azure Active Directory](../active-directory/roles/permissions-reference.md).

1. Em **Adicionar atribuição de função**, selecione uma **Função** que forneça à sua identidade o acesso necessário ao recurso de destino.

   Para o exemplo deste tópico, sua identidade precisa de uma [função que possa acessar o blob em um contêiner de armazenamento do Azure](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights), portanto, selecione a função de **colaborador de dados de blob de armazenamento** para a identidade gerenciada.

   ![Selecione a função "Colaborador de dados do Blob de Armazenamento"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Siga estas etapas para sua identidade gerenciada:

   * **Identidade atribuída ao sistema**

     1. Na caixa **Atribuir acesso a**, selecione **Aplicativo Lógico**. Quando a propriedade **Assinatura** for exibida, selecione a assinatura do Azure associada à sua identidade.

        ![Selecione o acesso para a identidade atribuída ao sistema](./media/create-managed-service-identity/assign-access-system.png)

     1. Na caixa **Selecionar**, selecione seu aplicativo lógico na lista. Se a lista for muito longa, use a caixa **Selecionar** para filtrar a lista.

        ![Selecione o aplicativo lógico para identidade atribuída ao sistema](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identidade atribuída ao usuário**

     1. Na caixa **Atribuir acesso a**, selecione **Identidade gerenciada atribuída ao usuário**. Quando a propriedade **Assinatura** for exibida, selecione a assinatura do Azure associada à sua identidade.

        ![Selecione o acesso para a identidade atribuída ao usuário](./media/create-managed-service-identity/assign-access-user.png)

     1. Na caixa **Selecionar**, selecione sua identidade na lista. Se a lista for muito longa, use a caixa **Selecionar** para filtrar a lista.

        ![Selecione sua identidade atribuída ao usuário](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Quando terminar, selecione **Salvar**.

   A lista de atribuições de função do recurso de destino agora mostra a função e a identidade gerenciada selecionadas. Este exemplo mostra como você pode usar a identidade atribuída ao sistema para um aplicativo lógico e uma identidade atribuída ao usuário para um grupo de outros aplicativos lógicos.

   ![Adicionar identidades gerenciadas e funções ao recurso de destino](./media/create-managed-service-identity/added-roles-for-identities.png)

   Para obter mais informações, confira [Atribuir um acesso de identidade gerenciada a um recurso usando o portal do Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Agora siga as [etapas para autenticar o acesso com a identidade](#authenticate-access-with-identity) em um gatilho ou uma ação que dá suporte a identidades gerenciadas.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticar o acesso com a identidade gerenciada

Depois de [habilitar a identidade gerenciada para seu aplicativo lógico](#azure-portal-system-logic-app) e [conceder a essa identidade acesso ao recurso ou à entidade de destino](#access-other-resources), você poderá usar essa identidade em [gatilhos e ações que dão suporte a identidades gerenciadas](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

> [!IMPORTANT]
> Se você tiver uma função do Azure em que deseja usar a identidade atribuída pelo sistema, primeiro [habilite a autenticação para Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

Estas etapas mostram como usar a identidade gerenciada com um gatilho ou uma ação por meio do portal do Azure. Para especificar a identidade gerenciada em uma definição de JSON subjacente de um gatilho ou uma ação, confira [Autenticação de identidade gerenciada](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Se você ainda não tiver feito isso, adicione [o gatilho ou a ação que dá suporte a identidades gerenciadas](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > Nem todos os gatilhos e ações dão suporte para permitir que você adicione um tipo de autenticação. Para obter mais informações, consulte [tipos de autenticação para gatilhos e ações que dão suporte à autenticação](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. No gatilho ou na ação que você adicionou, siga estas etapas:

   * **Gatilhos e ações internos que dão suporte ao uso de uma identidade gerenciada**

     1. Adicione a propriedade de **autenticação** se a propriedade ainda não for exibida.

     1. Em **tipo de autenticação**, selecione **identidade gerenciada**.

     Para obter mais informações, consulte [exemplo: autenticar gatilho interno ou ação com uma identidade gerenciada](#authenticate-built-in-managed-identity).
 
   * **Gatilhos e ações de conector gerenciado que dão suporte ao uso de uma identidade gerenciada**

     1. Na página seleção de locatário, selecione **conectar com identidade gerenciada**.

     1. Na página seguinte, forneça um nome de conexão.

        Por padrão, a lista de identidades gerenciadas mostra apenas a identidade gerenciada habilitada no momento, pois um aplicativo lógico dá suporte à habilitação de apenas uma identidade gerenciada por vez, por exemplo:

        ![Captura de tela que mostra a página nome da conexão e a identidade gerenciada selecionada.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     Para obter mais informações, consulte [exemplo: autenticar gatilho de conector gerenciado ou ação com uma identidade gerenciada](#authenticate-managed-connector-managed-identity).

     As conexões que você cria para usar uma identidade gerenciada são um tipo de conexão especial que funciona apenas com uma identidade gerenciada. Em tempo de execução, a conexão usa a identidade gerenciada habilitada no aplicativo lógico. Essa configuração é salva no objeto da definição de recurso do aplicativo lógico `parameters` , que contém o `$connections` objeto que inclui ponteiros para a ID de recurso da conexão junto com a ID de recurso da identidade, se a identidade atribuída pelo usuário estiver habilitada.

     Este exemplo mostra a aparência da configuração quando o aplicativo lógico habilita a identidade gerenciada atribuída pelo sistema:

     ```json
     "parameters": {
        "$connections": {
           "value": {
              "<action-name>": {
                 "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
                 "connectionName": "{connection-name}",
                 "connectionProperties": {
                    "authentication": {
                       "type": "ManagedServiceIdentity"
                    }
                 },
                 "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
              }
           }
        }
     }
     ```

     Este exemplo mostra a aparência da configuração quando o aplicativo lógico habilita uma identidade gerenciada atribuída pelo usuário:

     ```json
     "parameters": {
        "$connections": {
           "value": {
              "<action-name>": {
                 "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
                 "connectionName": "{connection-name}",
                 "connectionProperties": {
                    "authentication": {
                       "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                       "type": "ManagedServiceIdentity"
                    }
                 },
                 "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
              }
           }
        }
     }
     ```

     Durante o tempo de execução, o serviço de aplicativos lógicos verifica se qualquer gatilho de conector gerenciado e ações no aplicativo lógico são configurados para usar a identidade gerenciada e se todas as permissões necessárias estão configuradas para usar a identidade gerenciada para acessar os recursos de destino especificados pelo gatilho e pelas ações. Se for bem-sucedido, o serviço de aplicativos lógicos recuperará o token do Azure AD que está associado à identidade gerenciada e usará essa identidade para autenticar o acesso ao recurso de destino e executar a operação configurada em gatilho e ações.

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Exemplo: autenticar gatilho ou ação interna com uma identidade gerenciada

O gatilho ou ação HTTP pode usar a identidade atribuída pelo sistema que você habilitou para seu aplicativo lógico. Em geral, o gatilho ou a ação HTTP usa essas propriedades para especificar o recurso ou a entidade que você deseja acessar:

| Propriedade | Obrigatório | Descrição |
|----------|----------|-------------|
| **Método** | Sim | O método HTTP usado pela operação que você deseja executar |
| **URI** | Sim | A URL do ponto de extremidade para acessar a entidade ou o recurso do Azure de destino. A sintaxe do URI geralmente inclui a [ID do recurso](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para o recurso ou serviço do Azure. |
| **Cabeçalhos** | Não | Todos os valores de cabeçalho que você precisa ou deseja incluir na solicitação de saída, como o tipo de conteúdo |
| **Consultas** | Não | Todos os parâmetros de consulta que você precisa ou deseja incluir na solicitação, como o parâmetro para uma operação específica ou a versão da API para a operação que você deseja executar |
| **Autenticação** | Sim | O tipo de autenticação a ser usado para autenticar o acesso ao recurso ou à entidade de destino |
||||

Como um exemplo específico, suponha que você queira executar a [operação Blob de Instantâneo](/rest/api/storageservices/snapshot-blob) em um blob na conta de Armazenamento do Azure em que você configurou o acesso para sua identidade anteriormente. No entanto, o [conector do Armazenamento de Blobs do Azure](/connectors/azureblob/) não oferece essa operação no momento. Em vez disso, você pode executar essa operação usando a [ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) ou [outra operação da API REST de Serviço Blob](/rest/api/storageservices/operations-on-blobs).

> [!IMPORTANT]
> Para acessar as contas de armazenamento do Azure por trás de firewalls usando solicitações HTTP e identidades gerenciadas, verifique se você também configurou sua conta de armazenamento com a [exceção que permite o acesso por serviços confiáveis da Microsoft](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

Para executar a [operação Blob de Instantâneo](/rest/api/storageservices/snapshot-blob), a ação HTTP especifica estas propriedades:

| Propriedade | Obrigatório | Valor de exemplo | Descrição |
|----------|----------|---------------|-------------|
| **Método** | Sim | `PUT`| O método HTTP usado pela operação Blob de Instantâneo |
| **URI** | Sim | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | A ID de recurso para um arquivo do Armazenamento de Blobs do Azure no ambiente do Azure Global (público), que usa essa sintaxe |
| **Cabeçalhos** | Para o armazenamento do Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r'}` | Os `x-ms-blob-type` `x-ms-version` valores de cabeçalho, e `x-ms-date` são necessários para operações de armazenamento do Azure. <p><p>**Importante**: no gatilho HTTP de saída e nas solicitações de ação para o Armazenamento do Azure, o cabeçalho requer a propriedade `x-ms-version` e a versão da API para a operação que você deseja executar. O `x-ms-date` deve ser a data atual. Caso contrário, o aplicativo lógico falhará com um `403 FORBIDDEN` erro. Para obter a data atual no formato necessário, você pode usar a expressão no valor de exemplo. <p>Para saber mais, consulte esses tópicos: <p><p>- [Cabeçalhos de solicitação – Blob de Instantâneo](/rest/api/storageservices/snapshot-blob#request) <br>- [Controle de versão para serviços de Armazenamento do Azure](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Consultas** | Somente para a operação de blob de instantâneo | `comp` = `snapshot` | O nome e o valor do parâmetro de consulta para a operação. |
|||||

Este é o a ação HTTP de exemplo que mostra todos esses valores de propriedade:

![Adicionar uma ação HTTP para acessar um recurso do Azure](./media/create-managed-service-identity/http-action-example.png)

1. Depois de adicionar a ação HTTP, adicione a propriedade de **autenticação** à ação http. Na lista **Adicionar novo parâmetro**, selecione **Autenticação**.

   ![Adicione a propriedade “Autenticação” à ação HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nem todos os gatilhos e ações dão suporte para permitir que você adicione um tipo de autenticação. Para obter mais informações, consulte [tipos de autenticação para gatilhos e ações que dão suporte à autenticação](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Na lista **Tipo de autenticação**, selecione **Identidade Gerenciada**.

   ![Para “Autenticação”, selecione “Identidade Gerenciada”](./media/create-managed-service-identity/select-managed-identity.png)

1. Na lista de identidades gerenciadas, selecione entre as opções disponíveis com base em seu cenário.

   * Se você configurou a identidade atribuída ao sistema, selecione **Identidade Gerenciada Atribuída ao Sistema**, se ainda não tiver selecionado.

     ![Selecione “Identidade Gerenciada Atribuída ao Sistema”](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Se você configurou uma identidade atribuída ao usuário, selecione essa identidade, se ainda não tiver selecionado.

     ![Selecionar a identidade atribuída ao usuário](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Este exemplo continua com a **Identidade Gerenciada Atribuída ao Sistema**.

1. Em alguns gatilhos e ações, a propriedade **Público-alvo** também é exibida para que você defina a ID do recurso de destino. Defina a propriedade **Público-alvo** como a [ID de recurso para o recurso ou serviço de destino](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Caso contrário, por padrão, a propriedade **Público-alvo** usa a ID de recurso `https://management.azure.com/`, que é a ID de recurso para o Azure Resource Manager.

   > [!IMPORTANT]
   > Verifique se essa ID de recurso de destino *corresponde exatamente* ao valor que o Azure AD (Active Directory) espera, incluindo barras à direita necessárias. Por exemplo, a ID de recurso para todas as contas de Armazenamento de Blobs do Azure requer uma barra à direita. No entanto, a ID de recurso para uma conta de armazenamento específica não requer uma barra à direita. Confira as [IDs de recurso para os serviços do Azure que dão suporte ao Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Este exemplo define a propriedade **Público-alvo** como `https://storage.azure.com/` para que os tokens de acesso usados para autenticação sejam válidos para todas as contas de armazenamento. No entanto, você também pode especificar uma URL de serviço raiz, `https://fabrikamstorageaccount.blob.core.windows.net`, para uma conta de armazenamento específica.

   ![Definir a propriedade “Público-alvo” como a ID de recurso de destino](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Para obter mais informações sobre como autorizar o acesso com o Azure AD para o Armazenamento do Azure, confira estes tópicos:

   * [Autorizar o acesso a blobs e filas do Azure usando o Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizar o acesso ao Armazenamento do Azure com o Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continue criando o aplicativo lógico da maneira desejada.

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Exemplo: autenticar gatilho ou ação de conector gerenciado com uma identidade gerenciada

A ação de Azure Resource Manager, **ler um recurso**, pode usar a identidade gerenciada que você habilitou para seu aplicativo lógico. Este exemplo mostra como usar a identidade gerenciada atribuída pelo sistema.

1. Depois de adicionar a ação ao seu fluxo de trabalho, na página seleção de locatário, selecione **conectar com identidade gerenciada**.

   ![Captura de tela que mostra Azure Resource Manager ação e "conectar com identidade gerenciada" selecionada.](./media/create-managed-service-identity/select-connect-managed-identity.png)

   A ação agora mostra a página nome da conexão com a lista identidade gerenciada, que inclui o tipo de identidade gerenciada que está habilitado no momento no aplicativo lógico.

1. Na página nome da conexão, forneça um nome para a conexão. Na lista identidade gerenciada, selecione a identidade gerenciada, que é a **identidade gerenciada atribuída pelo sistema** neste exemplo, e selecione **criar**. Se você habilitou uma identidade gerenciada atribuída pelo usuário, selecione essa identidade em vez disso.

   ![Captura de tela que mostra Azure Resource Manager ação com o nome de conexão inserido e "identidade gerenciada atribuída pelo sistema" selecionada.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   Se a identidade gerenciada não estiver habilitada, o seguinte erro será exibido quando você tentar criar a conexão:

   *Você deve habilitar a identidade gerenciada para seu aplicativo lógico e, em seguida, conceder o acesso necessário à identidade no recurso de destino.*

   ![Captura de tela que mostra Azure Resource Manager ação com erro quando nenhuma identidade gerenciada está habilitada.](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. Depois de criar a conexão com êxito, o designer pode buscar quaisquer valores dinâmicos, conteúdo ou esquema usando a autenticação de identidade gerenciada.

1. Continue criando o aplicativo lógico da maneira desejada.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Desabilitar identidade gerenciada

Para parar de usar uma identidade gerenciada para seu aplicativo lógico, você tem estas opções:

* [Azure portal](#azure-portal-disable)
* [Modelos do Gerenciador de Recursos do Azure](#template-disable)
* Azure PowerShell
  * [Remover atribuição de função](../role-based-access-control/role-assignments-powershell.md)
  * [Excluir identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* CLI do Azure
  * [Remover atribuição de função](../role-based-access-control/role-assignments-cli.md)
  * [Excluir identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* API REST do Azure
  * [Remover atribuição de função](../role-based-access-control/role-assignments-rest.md)
  * [Excluir identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Se você exclui seu aplicativo lógico, o Azure remove automaticamente a identidade gerenciada do Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Desabilitar a identidade gerenciada no portal do Azure

No portal do Azure, primeiro remova o acesso da identidade ao [seu recurso de destino](#disable-identity-target-resource). Em seguida, desligue a identidade atribuída ao sistema ou remova a identidade atribuída ao usuário do [seu aplicativo lógico](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Remover o acesso à identidade dos recursos

1. No [portal do Azure](https://portal.azure.com), acesse o recurso do Azure de destino em que você deseja remover o acesso para a identidade gerenciada.

1. No menu do recurso de destino, selecione **Controle de acesso (IAM)** . Na barra de ferramentas, selecione **Atribuições de função**.

1. Na lista de funções, selecione as identidades gerenciadas que você deseja remover. Na barra de ferramentas, selecione **Remover**.

   > [!TIP]
   > Se a opção **Remover** estiver desabilitada, provavelmente você não terá permissões. Para obter mais informações sobre as permissões com as quais você pode gerenciar funções para recursos, confira [Permissões da função Administrador no Azure Active Directory](../active-directory/roles/permissions-reference.md).

A identidade gerenciada agora é removida e não tem mais acesso ao recurso de destino.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Desabilitar a identidade gerenciada no aplicativo lógico

1. No [Portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Identidade** e, em seguida, siga as etapas para sua identidade:

   * Selecione **Atribuída ao sistema** > **Ativada** > **Salvar**. Quando o Azure solicitar que você confirme, selecione **Sim**.

     ![Desabilitar a identidade atribuída ao sistema](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selecione **Atribuído ao usuário** e a identidade gerenciada e, em seguida, selecione **Remover**. Quando o Azure solicitar que você confirme, selecione **Sim**.

     ![Remover a identidade atribuída ao usuário](./media/create-managed-service-identity/remove-user-assigned-identity.png)

A identidade gerenciada agora está desabilitada em seu aplicativo lógico.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Desabilitar a identidade gerenciada no modelo do Azure Resource Manager

Se você tiver criado a identidade gerenciada do aplicativo lógico usando um modelo do Azure Resource Manager, defina a propriedade filho `type` do objeto `identity` como `None`.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Próximas etapas

* [Proteger o acesso e os dados nos Aplicativos Lógicos do Azure](../logic-apps/logic-apps-securing-a-logic-app.md)
