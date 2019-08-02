---
title: Permitir ou bloquear convites para organizações específicas – Azure Active Directory | Microsoft Docs
description: Mostra como um administrador pode usar o Portal do Azure ou o PowerShell para definir um acesso ou negar lista para permitir ou impedir usuários B2B de determinados domínios.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62cbe68bcf191c7ee6fc906bc8ba8ea66e3efb31
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233889"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Permitir ou bloquear convites para usuários B2B de organizações específicas

Você pode usar uma lista de permissões ou uma lista de negações para permitir ou bloquear convites para usuários B2B de organizações específicas. Por exemplo, se você quiser bloquear domínios de endereço de email pessoal, você pode configurar uma lista de negações que contenham domínios como Gmail.com e Outlook.com. Ou, se sua empresa tiver uma parceria com outras empresas como Contoso.com, Fabrikam.com e Litware.com e você quiser restringir convites para somente essas organizações, você pode adicionar Contoso.com, Fabrikam.com e Litware.com à sua lista de permissões.
  
## <a name="important-considerations"></a>Considerações importantes

- Você pode criar uma lista de permissões ou uma lista de negações. Você não pode configurar os dois tipos de listas. Por padrão, qualquer domínio que ão esteja na lista de permissão ou na lista de negação, e vice-versa. 
- Você pode criar apenas uma política por organização. Você pode atualizar a política para incluir mais domínios ou você pode excluir a política para criar uma nova. 
- O número de domínios que você pode adicionar a uma lista de permissões ou lista de negações é limitado apenas pelo tamanho da política. O tamanho máximo de toda a política é 25 KB (25.000 caracteres), que inclui a lista de permissões ou a lista de negações e quaisquer outros parâmetros configurados para outros recursos.
- Esta lista funciona independentemente das listas de permissão/bloqueio do OneDrive for Business e SharePoint Online. Se você quiser restringir o compartilhamento de arquivos no SharePoint Online, será necessário configurar uma lista de permissão ou negação para o One Drive for Business e para o SharePoint Online. Para obter mais informações, consulte [Compartilhamento de domínios restritos no SharePoint Online e OneDrive for Business ](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- A lista não se aplica a usuários externos que já resgataram o convite. A lista será aplicada depois que for configurada. Se um convite do usuário estiver em um estado pendente e você definir uma política que bloqueia seu domínio, a tentativa do usuário para resgatar o convite falhará.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Configure a lista de permissão ou negação no portal

Por padrão, a **Permitir o envio de convites para qualquer domínio (mais inclusivo)** está habilitada. Nesse caso, você pode convidar usuários B2B de qualquer organização.

### <a name="add-a-deny-list"></a>Adicionar uma lista de negações

Esse é o cenário mais comum, onde sua organização deseja trabalhar com quase qualquer empresa, mas quer impedir que os usuários de domínios específicos sejam convidados como usuários B2B.

Adicionar uma lista de negações:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **Usuários** > **Configurações de usuário**.
3. Em **Usuários externos**, selecione **Gerenciar configurações de colaboração externa**.
4. Em **Restrições de colaboração**, selecione **Negar convites para os domínios especificados**.
5. Em **DOMÍNIOS DE DESTINO**, insira o nome de um dos domínios que você deseja bloquear. Para vários domínios, insira cada domínio em uma nova linha. Por exemplo:

   ![Mostra a opção de negar com domínios adicionados](./media/allow-deny-list/DenyListSettings.png)
 
6. Quando terminar, clique em **Salvar**.

Depois de definir a política, se tentar convidar um usuário de um domínio bloqueado, você receberá uma mensagem dizendo que o domínio do usuário está bloqueado no momento pela sua política de convite.
 
### <a name="add-an-allow-list"></a>Adicionar uma lista de permissões

Esta é uma configuração mais restritiva, em que é possível definir domínios específicos na lista de permissões e restringir convites para outras organizações ou domínios que não são mencionados. 

Se você quiser usar uma lista de permissões, certifique-se de avaliar completamente quais são as suas necessidades de negócio. Se você fizer essa política muito restritiva, seus usuários podem optar por enviar documentos por email ou encontrar outras formas de colaboração não sancionadas pelo TI.


Para adicionar uma lista de permissões:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **Usuários** > **Configurações de usuário**.
3. Em **Usuários externos**, selecione **Gerenciar configurações de colaboração externa**.
4. Em **Restrições de colaboração**, selecione **Permitir convites somente para os domínios especificados (mais restritivos)** .
5. Em **DOMÍNIOS DE DESTINO**, insira o nome de um dos domínios que você deseja permitir. Para vários domínios, insira cada domínio em uma nova linha. Por exemplo:

   ![Mostra a opção de permitir com domínios adicionados](./media/allow-deny-list/AllowListSettings.png)
 
6. Quando terminar, clique em **Salvar**.

Depois de definir a política, se tentar convidar um usuário de um domínio que não está na lista de permissões, você receberá uma mensagem dizendo que o domínio do usuário está bloqueado no momento pela sua política de convite.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Alternar da lista de permissão para lista de negação e vice-versa 

Se você alternar de uma política para o outra, isso descartará a configuração da política existente. Certifique-se de fazer backup dos detalhes da sua configuração antes de executar a alternação. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Defina a política de permissão ou negação usando o PowerShell

### <a name="prerequisite"></a>Pré-requisito

Para definir a lista de permissão ou negação usando o PowerShell, você deve instalar a versão de visualização do Microsoft Azure Active Directory para Windows PowerShell. Especificamente, instale a versão do módulo AzureADPreview versão 2.0.0.98 ou posterior.

Para verificar a versão do módulo (e se ele está instalado):
 
1. Abra o Windows PowerShell como um usuário com privilégios elevados (Executar como Administrador). 
2. Execute o seguinte comando para verificar se você tem todas as versões do Módulo do Microsoft Azure Active Directory para o Windows PowerShell instalado no seu computador:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Se o módulo não estiver instalado ou se você não tem uma versão necessária, siga um destes procedimentos:

- Se nenhum resultado for retornado, execute o seguinte comando para instalar a versão mais recente do módulo AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se apenas o módulo do Windows Azure for exibido nos resultados, execute os comandos a seguir para instalar o módulo AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se apenas o módulo AzureADPreview for exibido nos resultados, mas a versão for menor que 2.0.0.98, execute os seguintes comandos para atualizá-lo: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Se os módulos AzureAD e AzureADPreview forem exibidos nos resultados, mas a versão do módulo AzureADPreview for menor do que 2.0.0.98, execute os comandos a seguir para atualizá-lo: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Use os cmdlets de AzureADPolicy para configurar a política

Para criar uma lista de permissão ou negação, use o cmdlet [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview). O exemplo a seguir mostra como definir uma lista de negações que bloqueia o domínio "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

O mesmo exemplo é exibido a seguir, mas com de definição da política embutida.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Para definir uma lista de permissão ou negação, use o cmdlet [AzureADPolicy conjunto](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview). Por exemplo:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Para obter a política, use o cmdlet [AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview). Por exemplo:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Para remover a política, use o cmdlet [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview). Por exemplo:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do Azure AD B2B, consulte [O que é a colaboração do Azure AD B2B?](what-is-b2b.md)
- Para obter informações sobre acesso condicional e colaboração B2B, consulte [acesso condicional para usuários de colaboração B2B](conditional-access.md).



