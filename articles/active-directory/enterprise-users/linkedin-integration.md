---
title: Consentimento do administrador para conexões de conta do LinkedIn – Azure AD | Microsoft Docs
description: Explica como habilitar ou desabilitar conexões de conta de integração do LinkedIn em aplicativos da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ffa937f91af7078b28331a655f99397d3821def
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545886"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrar conexões de conta do LinkedIn no Azure Active Directory

Você pode permitir que os usuários em sua organização acessem suas conexões do LinkedIn em alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários consentim se conectarem às suas contas. Você pode integrar sua organização no [centro de administração](https://aad.portal.azure.com)do Azure Active Directory (AD do Azure).

> [!IMPORTANT]
> Atualmente, a configuração de conexões da conta do LinkedIn está sendo distribuída para as organizações do Azure AD. Quando ela é distribuída para sua organização, ela é habilitada por padrão.
>
> Exceções:
>
> * A configuração não está disponível para clientes que usam Microsoft Cloud para o governo dos EUA, Microsoft Cloud Alemanha ou Azure e Microsoft 365 operadas pela 21Vianet na China.
> * A configuração está desativada por padrão para organizações do Azure AD provisionadas na Alemanha. Observe que a configuração não está disponível para clientes que usam o Microsoft Cloud Alemanha.
> * A configuração está desativada por padrão para organizações provisionadas na França.
>
> Depois que as conexões de conta do LinkedIn estiverem habilitadas para sua organização, as conexões de conta funcionarão depois que os usuários consentirem os aplicativos que acessam os dados da empresa Para obter informações sobre a configuração de consentimento do usuário, consulte [como remover o acesso de um usuário a um aplicativo](../manage-apps/methods-for-removing-user-access.md).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Habilitar conexões de conta do LinkedIn no portal do Azure

Você pode habilitar as conexões de conta do LinkedIn somente para os usuários aos quais você deseja ter acesso, de toda a organização para apenas os usuários selecionados em sua organização.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com/) com uma conta que seja um administrador global da organização do Azure AD.
1. Selecione **Usuários**.
1. Na página **usuários** , selecione **configurações de usuário**.
1. Em **conexões de conta do LinkedIn**, permita que os usuários conectem suas contas para acessar suas conexões do LinkedIn em alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários consentim se conectarem às suas contas.

    * Selecione **Sim** para habilitar o serviço para todos os usuários em sua organização
    * Selecione o **grupo selecionado** para habilitar o serviço apenas para um grupo de usuários selecionados em sua organização
    * Selecione **não** para retirar o consentimento de todos os usuários em sua organização

    ![Integrar conexões de conta do LinkedIn na organização](./media/linkedin-integration/linkedin-integration.png)

1. Quando terminar, selecione **salvar** para salvar as configurações.

> [!Important]
> A integração do LinkedIn não está totalmente habilitada para seus usuários até que eles consentissem conectar suas contas. Nenhum dado é compartilhado quando você habilita conexões de conta para seus usuários.

### <a name="assign-selected-users-with-a-group"></a>Atribuir usuários selecionados a um grupo

Substituímos a opção ' Selected ' que especifica uma lista de usuários com a opção de selecionar um grupo de usuários para que você possa habilitar a capacidade de conectar o LinkedIn e contas da Microsoft para um único grupo em vez de muitos usuários individuais. Se você não tiver conexões de conta do LinkedIn habilitadas para usuários individuais selecionados, você não precisará fazer nada. Se você tiver habilitado anteriormente as conexões de conta do LinkedIn para usuários individuais selecionados, deverá:

1. Obter a lista atual de usuários individuais
1. Mover os usuários individuais atualmente habilitados para um grupo
1. Use o grupo do anterior como o grupo selecionado na configuração conexões de conta do LinkedIn no centro de administração do Azure AD.

> [!NOTE]
> Mesmo que você não mova os usuários individuais atualmente selecionados para um grupo, eles ainda poderão ver informações do LinkedIn nos aplicativos da Microsoft.

### <a name="move-currently-selected-users-to-a-group"></a>Mover usuários selecionados no momento para um grupo

1. Crie um arquivo CSV dos usuários que estão selecionados para conexões de conta do LinkedIn.
1. Entre Microsoft 365 com sua conta de administrador.
1. Inicie o PowerShell.
1. Instalar o módulo do AD do Azure executando `Install-Module AzureAD`
1. Execute o seguinte script:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Para usar o grupo da etapa dois como o grupo selecionado na configuração de conexões da conta do LinkedIn no centro de administração do Azure AD, consulte [habilitar conexões de conta do LinkedIn no portal do Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Usar Política de Grupo para habilitar conexões de conta do LinkedIn

1. Baixe os [arquivos de modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extraia os arquivos **ADMX** e copie-os para o repositório central.
1. Abra Gerenciamento de Política de Grupo.
1. Crie um objeto de política de grupo com a seguinte configuração: **configuração do usuário**  >  **modelos administrativos**  >  **Microsoft Office 2016**  >  **diversos**  >  **recursos do LinkedIn em aplicativos do Office**.
1. Selecione **Habilitado** ou **Desabilitado**.
  
   Estado | Efeito
   ------ | ------
   **Enabled** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está habilitada. Os usuários em sua organização podem usar os recursos do LinkedIn em seus aplicativos do Office 2016.
   **Desabilitado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está desabilitada e os usuários finais não podem alterar essa configuração. Os usuários em sua organização não podem usar recursos do LinkedIn nos seus aplicativos do Office 2016.

Essa política de grupo afeta somente os aplicativos do Office 2016 para um computador local. Se os usuários desabilitarem o LinkedIn em seus aplicativos do Office 2016, eles ainda poderão ver os recursos do LinkedIn no Microsoft 365.

## <a name="next-steps"></a>Próximas etapas

* [Consentimento do usuário e compartilhamento de dados para o LinkedIn](linkedin-user-consent.md)

* [Informações do LinkedIn e recursos em seus aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Central de Ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

* [Exibir sua configuração atual de integração do LinkedIn no portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)