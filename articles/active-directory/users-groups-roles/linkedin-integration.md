---
title: Consentimento do administrador para conexões de conta do LinkedIn – Azure Active Directory | Microsoft Docs
description: Explica como habilitar ou desabilitar as conexões de conta de integração do LinkedIn em aplicativos da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ca46d6df9c32f23d3051d1205c3c6b39e69f5a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164706"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrar as conexões de conta do LinkedIn no Azure Active Directory

Você pode permitir que os usuários em sua organização para acessar suas conexões do LinkedIn dentro de alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários dar consentimento para se conectar suas contas. Você pode integrar sua organização no Azure Active Directory (Azure AD) [Centro de administração](https://aad.portal.azure.com).

> [!IMPORTANT]
> As configuração de conexões de conta no LinkedIn atualmente está sendo distribuído para organizações do AD do Azure. Quando ele é distribuído para sua organização, ele é habilitado por padrão.
> 
> Exceções:
> * A configuração não está disponível para clientes que usam o Microsoft Cloud para o governo dos EUA, Microsoft Cloud Alemanha ou Azure e Office 365 operados pela 21Vianet na China.
> * A configuração está desativada por padrão para locatários provisionados na Alemanha. Observe que a configuração não está disponível para clientes que usam o Microsoft Cloud Alemanha.
> * A configuração está desativada por padrão para locatários provisionados na França.
>
> Depois que as conexões de conta do LinkedIn são habilitadas para a sua organização, as conexões de conta funcionar depois que os usuários de consentir que os aplicativos acessem dados da empresa em seu nome. Para obter informações sobre a configuração de consentimento do usuário, consulte [como remover o acesso do usuário a um aplicativo](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Habilitar conexões de conta do LinkedIn no portal do Azure

Você pode habilitar conexões de conta do LinkedIn para apenas os usuários que você deseja ter acesso de toda a sua organização, somente a usuários selecionados em sua organização.

1. Entrar para o [Centro de administração do Azure AD](https://aad.portal.azure.com/) com uma conta que seja um administrador global para a organização do AD do Azure.
1. Selecione **Usuários**.
1. Na folha **Usuários**, selecione **Configurações de usuário**.
1. Sob **conexões de conta do LinkedIn**, permitir que os usuários se conectem suas contas para acessar suas conexões do LinkedIn dentro de alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários dar consentimento para se conectar suas contas.

    * Selecione **Sim** para habilitar o serviço para todos os usuários em sua organização
    * Selecione **grupo selecionadas** para habilitar o serviço para apenas um grupo de usuários selecionados em sua organização
    * Selecione **não** para retirar o consentimento de todos os usuários em sua organização

    ![Integrar o LinkedIn conexões de conta da organização](./media/linkedin-integration/linkedin-integration.png)

1. Quando terminar, selecione **salvar** para salvar suas configurações.

> [!Important]
> Integração do LinkedIn não está totalmente habilitada para os usuários até que eles consentimento para se conectar suas contas. Nenhum dado é compartilhado quando você habilitar as conexões de conta para os usuários.

### <a name="assign-selected-users-with-a-group"></a>Atribuir a usuários selecionados com um grupo
Substituímos a opção 'Selecionados' que especifica uma lista de usuários com a opção de selecionar um grupo de usuários para que você possa habilitar a capacidade de se conectar a contas LinkedIn e da Microsoft para um único grupo, em vez de muitos usuários individuais. Se você não tiver conexões de conta do LinkedIn habilitadas para usuários individuais selecionados, você não precisa fazer nada. Se você já tiver habilitado conexões de conta do LinkedIn para usuários individuais selecionados, você deve:

1. Obter a lista atual de usuários individuais
1. Mover os usuários individuais habilitados no momento para um grupo
1. Use o grupo do anterior como o grupo selecionado das conexões de conta do LinkedIn definindo no Centro de administração do AD do Azure.

> [!NOTE]
> Mesmo se você não mover os usuários individuais atualmente selecionados a um grupo, eles ainda podem ver as informações do LinkedIn em aplicativos da Microsoft.

### <a name="get-the-current-list-of-selected-users"></a>Obter a lista atual de usuários selecionados

1. Entrar no Microsoft 365 com sua conta de administrador.
1. Vá para https://linkedinselectedusermigration.azurewebsites.net/. Você verá a lista de usuários que são selecionados para conexões de conta do LinkedIn.
1. Exporte a lista para um arquivo CSV.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Mover os usuários individuais selecionados no momento para um grupo

1. Inicializar o PowerShell
1. Instalar o módulo AD do Azure, executando `Install-Module AzureAD`
1. Execute o seguinte script:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Para usar o grupo da etapa 2 como o grupo selecionado das conexões de conta do LinkedIn definindo no Centro de administração do AD do Azure, consulte [LinkedIn habilitar conexões de conta no portal do Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Use a diretiva de grupo para habilitar conexões de conta do LinkedIn

1. Baixe os [arquivos de modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extraia os arquivos **ADMX** e copie-os para o repositório central.
1. Gerenciamento de política de grupo aberto.
1. Crie um Objeto de Política de Grupo com a seguinte configuração: **Configuração do Usuário** > **Modelos Administrativos** > **Microsoft Office 2016** > **Diversos** > **Mostrar recursos do LinkedIn em aplicativos do Office**.
1. Selecione **Habilitado** ou **Desabilitado**.
  
   Estado | Efeito
   ------ | ------
   **Habilitado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está habilitada. Os usuários em sua organização podem usar recursos do LinkedIn em seus aplicativos do Office 2016.
   **Desabilitado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está desabilitada e os usuários finais não podem alterar essa configuração. Os usuários em sua organização não podem usar recursos do LinkedIn nos seus aplicativos do Office 2016.

Essa política de grupo afeta somente os aplicativos do Office 2016 para um computador local. Se os usuários desabilitarem o LinkedIn em seus aplicativos do Office 2016, eles ainda podem ver LinkedIn recursos do Office 365.

## <a name="next-steps"></a>Próximas etapas

* [Consentimento do usuário e dados de compartilhamento para o LinkedIn](linkedin-user-consent.md)

* [Informações do LinkedIn e recursos em seus aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Central de Ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

* [Exibir sua configuração atual de integração do LinkedIn no portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
