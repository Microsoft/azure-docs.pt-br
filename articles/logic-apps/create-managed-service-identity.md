---
title: Autenticar com identidades gerenciadas – Aplicativos Lógicos do Azure | Microsoft Docs
description: Para autenticar sem precisar entrar, você pode criar uma identidade gerenciada (anteriormente chamada de Identidade de Serviço Gerenciada ou MSI) para que o aplicativo lógico possa acessar recursos em outros locatários do Azure AD (Azure Active Directory) sem credenciais ou segredos
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: b157db5032bd62ab443209f201b4ceded6e44cb5
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385558"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Autenticar e acessar os recursos com identidades gerenciadas em Aplicativos Lógicos do Azure

Para acessar recursos em outros locatários do Azure AD (Azure Active Directory) e autenticar sua identidade sem precisar entrar, o aplicativo lógico pode usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecida como Identidade de Serviço Gerenciada ou MSI), em vez de credenciais ou segredos. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou trocar segredos. Este artigo mostra como é possível configurar e usar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico. Para obter mais informações sobre identidades gerenciadas, confira [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Seu aplicativo lógico pode usar identidades gerenciadas somente com conectores que dão suporte a identidades gerenciadas. Atualmente, somente o conector HTTP dá suporte a identidades gerenciadas.
>
> No momento, pode haver até 10 fluxos de trabalho de aplicativo lógico com identidades gerenciadas atribuídas pelo sistema em cada assinatura do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* O aplicativo lógico em que você deseja usar a identidade gerenciada atribuída pelo sistema. Se você não tiver um aplicativo lógico, confira [Criar seu primeiro fluxo de trabalho de aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Habilitar identidade gerenciada

Para identidades gerenciadas atribuídas pelo sistema, você não precisa criar manualmente essa identidade. Para configurar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico, você pode usar estes recursos: 

* [Portal do Azure](#azure-portal) 
* [Modelos do Gerenciador de Recursos do Azure](#template) 
* [PowerShell do Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Portal do Azure

Para habilitar uma identidade gerenciada atribuída pelo sistema para o seu aplicativo lógico usando o portal do Azure, ative a configuração **Sistema atribuído** nas configurações de identidade do seu aplicativo lógico.

1. No [Portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Identidade**. 

1. Em **Sistema atribuído** > **Status**, escolha **Ativar**. Em seguida, escolha **Salvar** > **Sim**.

   ![Habilitar a configuração de identidade gerenciada](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Agora, o aplicativo lógico tem uma identidade gerenciada atribuída pelo sistema registrada no Azure Active Directory:

   ![GUIDs para ID de objeto](./media/create-managed-service-identity/object-id.png)

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------| 
   | **ID do objeto** | <*identity-resource-ID*> | Um GUID (Identificador Global Exclusivo) que representa a identidade gerenciada atribuída pelo sistema para o seu aplicativo lógico em um locatário do Azure Active Directory | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Se você desejar automatizar a criação e a implantação de recursos do Azure, como os aplicativos lógicos, use os [Modelos do Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Para criar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico por meio de um modelo, adicione o elemento `"identity"` e a propriedade `"type"` à definição de fluxo de trabalho do aplicativo lógico em seu modelo de implantação: 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Por exemplo:

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

Quando o Azure cria o aplicativo lógico, a definição de fluxo de trabalho do aplicativo lógico inclui estas propriedades adicionais:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriedade | Valor | Descrição | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Um GUID (Identificador Global Exclusivo) que representa o aplicativo lógico no locatário do Azure Active Directory e, às vezes, aparece como "ID de objeto" ou `objectID` | 
| **tenantId** | <*Azure-AD-tenant-ID*> | Um GUID (identificador global exclusivo) que representa o locatário do Azure AD do qual o aplicativo lógico agora é membro. Dentro do locatário do Azure AD, a entidade de serviço tem o mesmo nome que a instância do aplicativo lógico. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Acessar recursos com identidade gerenciada

Depois de criar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico, você poderá [fornecer a essa identidade o acesso a outros recursos do Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Em seguida, você poderá usar essa identidade para autenticação, como qualquer outra [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> A identidade gerenciada atribuída pelo sistema e o recurso em que você deseja atribuir acesso precisam ter a mesma assinatura do Azure.

### <a name="assign-access-to-managed-identity"></a>Atribuir acesso à identidade gerenciada

Para fornecer acesso a outro recurso do Azure para a identidade gerenciada atribuída pelo sistema do aplicativo lógico, siga estas etapas:

1. No portal do Azure, acesse o recurso do Azure em que deseja atribuir acesso à identidade gerenciada. 

1. No menu do recurso, selecione **controle de acesso (iam)** . Na barra de ferramentas, escolha **Adicionar** > **Adicionar atribuição de função**.

   ![Adicionar atribuição de função](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. Em **Adicionar atribuição de função**, selecione a **Função** desejada para a identidade. 

1. Na propriedade **Atribuir acesso a**, selecione **Usuário, grupo ou entidade de serviço do Azure AD**, se ainda não estiver selecionado.

1. Na caixa **Selecionar**, iniciando com o primeiro caractere no nome do aplicativo lógico, insira o nome do aplicativo lógico. Quando seu aplicativo lógico for exibido, selecione-o.

   ![Selecione o aplicativo lógico com a identidade gerenciada](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Quando terminar, escolha **Salvar**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Autenticar com a identidade gerenciada no aplicativo lógico

Depois de configurar o aplicativo lógico com uma identidade gerenciada atribuída pelo sistema e atribuir o acesso ao recurso desejado para essa identidade, você poderá usar essa identidade para autenticação. Por exemplo, você pode usar uma ação de HTTP para que seu aplicativo lógico possa enviar uma solicitação HTTP ou chamar esse recurso. 

1. No aplicativo lógico, adicione a ação **HTTP**.

1. Forneça os detalhes necessários sobre essa ação, como o local do **Método** e do **URI** da solicitação ao recurso que você deseja chamar.

   Por exemplo, suponha que você está usando a autenticação do AAD (Azure Active Directory) com [um desses serviços do Azure que oferecem suporte ao Azure Active Directory](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   Na caixa **URI**, digite a URL do ponto de extremidade para o serviço do Azure. 
   Assim, se você está usando o Azure Resource Manager, insira esse valor na propriedade **URI**:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. Na ação de HTTP, escolha **Mostrar opções avançadas**.

1. Na lista **Autenticação**, selecione **Identidade Gerenciada**. Depois de selecionar essa autenticação, a propriedade **Público-alvo** aparece com o valor da ID do recurso padrão:

   ![selecione "Identidade Gerenciada"](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > Na propriedade **Público-alvo**, o valor da ID do recurso deve corresponder exatamente ao que o Azure Active Directory espera, incluindo todas as barras à direita necessárias. 
   > Você pode encontrar esses valores de ID do recurso nesta [tabela que descreve os serviços do Azure que oferecem suporte ao Azure Active Directory](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Por exemplo, se você estiver usando a ID de recurso do Azure Resource Manager, o URI deverá ter uma barra à direita.

1. Continue criando o aplicativo lógico da maneira desejada.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Remover a identidade gerenciada

Para desabilitar uma identidade gerenciada atribuída pelo sistema no aplicativo lógico, siga as etapas semelhantes às de configuração da identidade usando o portal do Azure, dos modelos de implantação do Azure Resource Manager ou do Azure PowerShell.

Quando você exclui o aplicativo lógico, o Azure remove automaticamente a identidade atribuída pelo sistema ao aplicativo lógico do Azure AD.

### <a name="azure-portal"></a>Portal do Azure

Para remover uma identidade gerenciada atribuída pelo sistema para o seu aplicativo lógico usando o portal do Azure, desative a configuração **Sistema atribuído** nas configurações de identidade do seu aplicativo lógico.

1. No [Portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Identidade**. 

1. Em **Sistema atribuído** > **Status**, escolha **Desativar**. Em seguida, escolha **Salvar** > **Sim**.

   ![Desligar a configuração de identidade gerenciada](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Modelo de implantação

Se você criou a identidade gerenciada atribuída pelo sistema do aplicativo lógico com um modelo de implantação do Azure Resource Manager, defina a propriedade `"type"` do elemento `"identity"` para `"None"`. Essa ação também exclui a ID da entidade de segurança do Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).
