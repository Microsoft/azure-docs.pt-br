---
title: Criar e gerenciar grupos de ações no portal do Azure
description: Este artigo mostra como criar e gerenciar grupos de ações no portal do Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: dukek
ms.openlocfilehash: fb067e603c181482a863dc9fd75556e32a801bc6
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772341"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerenciar grupos de ações no portal do Azure
Um grupo de ações é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure. Alertas do Azure Monitor e da Integridade do Serviço usam grupos de ações para notificar usuários de que um alerta foi disparado. Vários alertas podem usar o mesmo grupo de ação ou grupos de ações diferentes dependendo dos requisitos do usuário. 

Este artigo mostra como criar e gerenciar grupos de ação no Portal do Azure.

Cada ação é composta das seguintes propriedades:

* **Tipo**: a notificação ou ação executada. Exemplos incluem o envio de um email de chamada, SMS, voz; ou disparando vários tipos de ações automatizadas. Veja os tipos mais adiante neste artigo.
* **Name**: um identificador exclusivo dentro do grupo de ações.
* **Detalhes**: os detalhes correspondentes que variam por *tipo*.

Para saber mais sobre como usar modelos do Azure Resource Manager para configurar grupos de ação, veja [Modelos do Resource Manager de grupos de ações](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Como criar um grupo de ações usando o portal do Azure

1. No [portal do Azure](https://portal.azure.com), pesquise e selecione **Monitor**. O painel **Monitor** consolida todas as configurações e dados de monitoramento em uma exibição.

1. Selecione **alertas** e, em seguida, selecione **Gerenciar ações**.

    ![Botão Gerenciar Ações](./media/action-groups/manage-action-groups.png)
    
1. Selecione **Adicionar grupo de ação** e preencha os campos relevantes na experiência do assistente.

    ![O comando "Adicionar grupo de ações"](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Definir configurações básicas do grupo de ações

Em **detalhes do projeto**:

Selecione a **assinatura** e o **grupo de recursos** em que o grupo de ações é salvo.

Em **Detalhes da instância**:

1. Insira um **nome de grupo de ação**.

1. Insira um **nome de exibição**. O nome de exibição é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

      ![Caixa de diálogo Adicionar grupo de ações](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Configurar notificações

1. Clique no botão **Avançar: notificações >** para ir para a guia **notificações** ou selecione a guia **notificações** na parte superior da tela.

1. Defina uma lista de notificações a serem enviadas quando um alerta for disparado. Forneça o seguinte para cada notificação:

    a. **Tipo de notificação**: selecione o tipo de notificação que você deseja enviar. As opções disponíveis são:
      * Função de Azure Resource Manager de email – envie um email para os usuários atribuídos a determinadas funções de ARM de nível de assinatura.
      * Email/SMS/Push/voz-Envie esses tipos de notificação para destinatários específicos.
    
    b. **Nome**: Insira um nome exclusivo para a notificação.

    c. **Detalhes**: com base no tipo de notificação selecionado, insira um endereço de email, número de telefone, etc.
    
    d. **Esquema comum de alertas**: Você pode optar por habilitar o [esquema de alerta comum](./alerts-common-schema.md), que fornece a vantagem de ter um conteúdo de alerta extensível e unificado em todos os serviços de alerta no Azure Monitor.

    ![A guia notificações](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Configurar ações

1. Clique no botão **Avançar: ações >** para ir para a guia **ações** ou selecione a guia **ações** na parte superior da tela.

1. Defina uma lista de ações a serem disparadas quando um alerta for disparado. Forneça o seguinte para cada ação:

    a. **Tipo de ação**: selecione runbook de automação, Azure Function, ITSM, aplicativo lógico, webhook seguro, webhook.
    
    b. **Nome**: Insira um nome exclusivo para a ação.

    c. **Detalhes**: com base no tipo de ação, insira um URI de webhook, um aplicativo do Azure, uma conexão de ITSM ou um runbook de automação. Para Ação do ITSM, além disso, especifique **Item de Trabalho** e outros campos necessários para a ferramenta de ITSM.
    
    d. **Esquema comum de alertas**: Você pode optar por habilitar o [esquema de alerta comum](./alerts-common-schema.md), que fornece a vantagem de ter um conteúdo de alerta extensível e unificado em todos os serviços de alerta no Azure Monitor.
    
    ![A guia ações](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Criar o grupo de ações

1. Você pode explorar as configurações de **Marcas** se desejar. Isso permite associar pares de chave/valor ao grupo de ações para sua categorização e é um recurso disponível para qualquer recurso do Azure.

    ![A guia marcas](./media/action-groups/action-group-4-tags.png)
    
1. Clique em **Revisar + criar** para revisar as configurações. Isso fará uma validação rápida de suas entradas para garantir que todos os campos obrigatórios sejam selecionados. Se houver problemas, eles serão relatados aqui. Depois de examinar as configurações, clique em **criar** para provisionar o grupo de ações.
    
    ![A guia revisar + criar](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Quando você configura uma ação para notificar uma pessoa por email ou SMS, elas recebem uma confirmação indicando que foram adicionadas ao grupo de ações.

## <a name="manage-your-action-groups"></a>Gerenciar seus grupos de ação

Depois de criar um grupo de ações, você poderá ver os **Grupos de ações** selecionando **Gerenciar ações** na página de aterrissagem **Alertas** no painel **Monitor**. Selecione o grupo de ações que você deseja gerenciar:

* Adicionar, editar ou remover ações.
* Excluir o grupo de ação.

## <a name="action-specific-information"></a>Informações específicas da ação

> [!NOTE]
> Confira [Limites do serviço de assinatura para monitoramento](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) para limites numéricos em cada um dos itens abaixo.  

### <a name="automation-runbook"></a>Runbook de automação
Veja os [Limites do serviço de assinatura do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) para limites nos conteúdos do Runbook.

É possível ter um número limitado de ações de runbook em um grupo de ações. 

### <a name="azure-app-push-notifications"></a>Notificações por push do aplicativo Azure
Habilite notificações por push para o [aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/) fornecendo o endereço de email que você usa como sua ID de conta ao configurar o aplicativo móvel do Azure.

É possível ter um número limitado de ações ao aplicativo Azure em um grupo de ações.

### <a name="email"></a>Email
Os emails serão enviados dos endereços de email a seguir. Certifique-se de que a filtragem de email esteja configurada adequadamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

É possível ter um número limitado de ações de email em um grupo de ações. Confira o artigo [informações de limitação da taxa](./alerts-rate-limiting.md).

### <a name="email-azure-resource-manager-role"></a>Email para a Função do Azure Resource Manager
Envie o email aos membros da função de assinatura. O email será enviado somente aos membros do **usuário do Azure AD** da função. O email não será enviado aos grupos ou às entidades de serviço do Azure AD.

Um email de notificação é enviado somente para o endereço de *email principal* .

Se você não estiver recebendo notificações em seu *email primário*, poderá tentar as seguintes etapas:

1. Em portal do Azure vá para *Active Directory*.
2. Clique em todos os usuários (no painel esquerdo), você verá a lista de usuários (no painel direito).
3. Selecione o usuário para o qual você deseja examinar as informações de *email primário* .

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Exemplo de como examinar o perfil do usuário." border="true":::

4. Em perfil do usuário em informações de contato, se a guia "email" estiver em branco, clique no botão *Editar* na parte superior e adicione seu *email principal* e pressione o botão *salvar* na parte superior.

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Exemplo de como adicionar email primário." border="true":::

É possível ter um número limitado de ações de email em um grupo de ações. Confira o artigo [informações de limitação da taxa](./alerts-rate-limiting.md).

### <a name="function"></a>Função
Chama um ponto de extremidade de gatilho HTTP existente no [Azure Functions](../../azure-functions/functions-get-started.md). Para manipular uma solicitação, seu ponto de extremidade deve manipular o verbo HTTP POST.

É possível ter um número limitado de ações de função em um grupo de ações.

### <a name="itsm"></a>ITSM
Ação de ITSM exige uma Conexão de ITSM. Saiba como criar uma [Conexão de ITSM](./itsmc-overview.md).

É possível ter um número limitado de ações de ITSM em um grupo de ações. 

### <a name="logic-app"></a>Aplicativo Lógico
É possível ter um número limitado de ações de aplicativo lógico em um grupo de ações.

### <a name="secure-webhook"></a>Webhook Seguro

> [!NOTE]
> Usar a ação de webhook requer que o ponto de extremidade de webhook de destino não exija que os detalhes do alerta funcionem com êxito ou seja capaz de analisar as informações de contexto de alerta fornecidas como parte da operação de POSTAgem. Se o ponto de extremidade do webhook não puder manipular as informações de contexto de alerta por conta própria, você poderá usar uma solução como uma [ação de aplicativo lógico](./action-groups-logic-app.md) para uma manipulação personalizada das informações de contexto de alerta para corresponder ao formato de dados esperado do webhook.
> O usuário deve ser o **proprietário** da entidade de serviço de webhook para garantir que a segurança não seja violada. Como qualquer cliente do Azure pode acessar todas as IDs de objeto por meio do portal, sem verificar o proprietário, qualquer pessoa pode adicionar o webhook seguro a seu próprio grupo de ação para a notificação de alerta do Azure monitor que viola a segurança.

A ação de webhook dos grupos de ações permite que você aproveite o Azure Active Directory para proteger a conexão entre o grupo de ações e a API Web protegida (ponto de extremidade do webhook). O fluxo de trabalho geral para aproveitar essa funcionalidade é descrito abaixo. Para obter uma visão geral dos aplicativos do Azure AD e das entidades de serviço, confira a [Visão geral da plataforma de identidade da Microsoft (v2.0)](../../active-directory/develop/v2-overview.md).

1. Crie um aplicativo do Azure AD para sua API Web protegida. Consulte [API Web protegida: registro de aplicativo](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Configure sua API protegida para ser [chamada por um aplicativo daemon](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Habilite grupos de ações para usar seu aplicativo Azure AD.

    > [!NOTE]
    > Você deve ser membro da [função Administrador do aplicativo Azure AD](../../active-directory/roles/permissions-reference.md#all-roles) para executar esse script.
    
    - Modifique a chamada Connect-AzureAD do script do PowerShell para usar sua ID de locatário do Azure AD.
    - Modifique a variável do script do PowerShell $myAzureADApplicationObjectId para usar a ID de objeto do seu aplicativo do Azure AD.
    - Execute o script modificado.
    
3. Configure a ação de webhook segura do grupo de ações.
    - Copie o valor $myApp.ObjectId do script e insira-o no campo ID de objeto do aplicativo na definição de ação do webhook.
    
    ![Proteger a ação de webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Proteger o script do PowerShell de webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
Confira as [informações de limitação de taxa](./alerts-rate-limiting.md) e o [comportamento de alerta do SMS](./alerts-sms-behavior.md) para obter informações adicionais importantes. 

É possível ter um número limitado de ações de SMS em um grupo de ações.

> [!NOTE]
> Se a interface do usuário do grupo de ações do portal do Azure não permitir que você selecione o código do país/região, o SMS não terá suporte para seu país/região.  Se o código de seu país/região não estiver disponível, você poderá votar no [UserVoice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) para que seu país/região seja adicionado. Enquanto isso, uma solução alternativa é fazer com que seu grupo de ações chame um webhook para um provedor de SMS de terceiros com suporte em seu país/região.  

Os preços de países/regiões com suporte são listados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

**Lista de países em que há suporte para notificação de SMS**

| Código do país | Nome do país |
|:---|:---|
| 61 | Austrália |
| 43 | Áustria |
| 32 | Bélgica |
| 55 | Brasil |
| 1 |Canadá |
| 56 | Chile |
| 86 | China |
| 420 | República Tcheca |
| 45 | Dinamarca |
| 372 | Estônia |
| 358 | Finlândia |
| 33 | França |
| 49 | Alemanha |
| 852 | RAE de Hong Kong |
| 91 | Índia |
| 353 | Irlanda |
| 972 | Israel |
| 39 | Itália |
| 81 | Japão |
| 352 | Luxemburgo |
| 60 | Malásia |
| 52 | México |
| 31 | Países Baixos |
| 64 | Nova Zelândia |
| 47 | Noruega |
| 351 | Portugal |
| 1 | Porto Rico |
| 40 | Romênia |
| 65 | Singapura |
| 27 | África do Sul |
| 82 | Coreia do Sul |
| 34 | Espanha |
| 41 | Suíça |
| 886 | Taiwan |
| 44 | Reino Unido |
| 1 | Estados Unidos |

### <a name="voice"></a>Voz
Confira o artigo sobre [informações de limitação de taxa](./alerts-rate-limiting.md) para comportamento adicional importante.

É possível ter um número limitado de ações de voz em um grupo de ações.

> [!NOTE]
> Se a interface do usuário do grupo de ações do portal do Azure não permitir que você selecione o código do país/região, as chamadas de voz não terão suporte para seu país/região. Se o código de seu país/região não estiver disponível, você poderá votar no [UserVoice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) para que seu país/região seja adicionado.  Enquanto isso, uma solução alternativa é fazer com que seu grupo de ações chame um webhook para um provedor de chamada de voz de terceiros com suporte em seu país/região.  
> Somente o código de país com suporte hoje no grupo de ação portal do Azure para notificação por voz é + 1 (Estados Unidos). 

Os preços de países/regiões com suporte são listados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>webhook

> [!NOTE]
> Usar a ação de webhook requer que o ponto de extremidade de webhook de destino não exija que os detalhes do alerta funcionem com êxito ou seja capaz de analisar as informações de contexto de alerta fornecidas como parte da operação de POSTAgem. Se o ponto de extremidade do webhook não puder manipular as informações de contexto de alerta por conta própria, você poderá usar uma solução como uma [ação de aplicativo lógico](./action-groups-logic-app.md) para uma manipulação personalizada das informações de contexto de alerta para corresponder ao formato de dados esperado do webhook.

WebHooks são processados usando as seguintes regras
- Uma chamada de webhook é tentada no máximo três vezes.
- A chamada será repetida se uma resposta não for recebida dentro do período de tempo limite ou se um dos seguintes códigos de status HTTP for retornado: 408, 429, 503 ou 504.
- A primeira chamada aguardará 10 segundos por uma resposta.
- A segunda e terceira tentativas aguardarão 30 segundos por uma resposta.
- Depois que as 3 tentativas de chamar o webhook falharam, nenhum grupo de ação chamará o ponto de extremidade por 15 minutos.

Consulte os [endereços IP do grupo de ações](../app/ip-addresses.md) para os intervalos de endereços IP de origem.


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [comportamento de alertas por SMS](./alerts-sms-behavior.md).  
* Tenha uma [compreensão do esquema de webhook de alerta do log de atividades](./activity-log-alerts-webhook.md).  
* Saiba mais sobre [conector ITSM](./itsmc-overview.md).
* Saiba mais sobre a [limitação de taxa](./alerts-rate-limiting.md) para alertas.
* Obtenha uma [visão geral dos alertas do log de atividades](./alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](../../service-health/alerts-activity-log-service-notifications-portal.md).
