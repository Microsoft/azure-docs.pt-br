---
title: Solucionar alertas de entidade de serviço no Azure AD Domain Services | Microsoft Docs
description: Saiba como solucionar problemas de alertas de configuração de entidade de serviço do Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15dba02d3db45d663b8ba0aa7eb93bbc3f388532
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645867"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: Alertas de entidade de serviço no Azure Active Directory Domain Services

As [entidades de serviço](../active-directory/develop/app-objects-and-service-principals.md) são aplicativos que a plataforma Azure usa para gerenciar, atualizar e manter um domínio gerenciado Azure Active Directory Domain Services (Azure AD DS). Se uma entidade de serviço for excluída, a funcionalidade no domínio gerenciado será afetada.

Este artigo ajuda a solucionar problemas e resolver alertas de configuração relacionados à entidade de serviço.

## <a name="alert-aadds102-service-principal-not-found"></a>Alerta AADDS102: Entidade de serviço não encontrada

### <a name="alert-message"></a>Mensagem de alerta

*Uma Entidade de Serviço necessária para o funcionamento correto do Azure AD Domain Services foi excluída do seu diretório do Azure AD. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.*

Se uma entidade de serviço necessária for excluída, a plataforma Azure não poderá executar tarefas de gerenciamento automatizadas. O domínio gerenciado pode não aplicar atualizações corretamente ou fazer backups.

### <a name="check-for-missing-service-principals"></a>Verificar entidades de serviço ausentes

Para verificar qual entidade de serviço está ausente e deve ser recriada, conclua as seguintes etapas:

1. No portal do Azure, selecione **Azure Active Directory** no menu de navegação à esquerda.
1. Selecione **Aplicativos empresariais**. Selecione *Todos os aplicativos* no menu suspenso **Tipo de aplicativo** e, sem seguida, **Aplicar**.
1. Pesquise cada uma das IDs de aplicativo a seguir. Se nenhum aplicativo existente for encontrado, siga as etapas de *Resolução* para criar a entidade de serviço ou registrar novamente o namespace.

    | ID do aplicativo | Resolução |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recriar uma entidade de serviço ausente](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrar novamente no namespace Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Registrar novamente no namespace Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Registrar novamente o namespace Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Recriar entidade de serviço ausente

Se a ID do aplicativo *2565bd9d-DA50-47d4-8b85-4c97f669dc36* não estiver no diretório do Azure AD, use o PowerShell do Azure AD para concluir as etapas a seguir. Para obter mais informações, consulte [PowerShell do Azure ad](/powershell/azure/active-directory/install-adv2).

1. Se necessário, instale o módulo do PowerShell do Azure AD e importe-o da seguinte maneira:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Agora recrie uma entidade de serviço usando o cmdlet [New-AzureADServicePrincipal][New-AzureAdServicePrincipal]:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

A integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

### <a name="re-register-the-microsoft-aad-namespace"></a>Registrar novamente o namespace Microsoft.AAD

Se a ID do aplicativo *443155a6-77f3-45e3-882b-22b3a8d431fb, abba844e-bc0e-44b0-947a-dc74e5d09022 ou d87dcbc6-a371-462e-88e3-28ad15ec4e64 não estiver no diretório do Azure AD, conclua as etapas a seguir para registrar novamente o provedor de recursos Microsoft. AAD:

1. No Portal do Azure, pesquise e selecione **Assinaturas**.
1. Escolha a assinatura associada ao seu domínio gerenciado.
1. No painel de navegação à esquerda, escolha **Provedores de recursos**.
1. Pesquise por *Microsoft. AAD* e, em seguida, selecione **Registrar novamente**.

A integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alerta AADDS105: O aplicativo de sincronização de senha está desatualizado

### <a name="alert-message"></a>Mensagem de alerta

*A entidade de serviço com a ID de aplicativo "d87dcbc6-a371-462e-88e3-28ad15ec4e64" foi excluída e recriada. A recreação deixa por trás das permissões inconsistentes em Azure AD Domain Services recursos necessários para atender ao domínio gerenciado. A sincronização de senhas em seu domínio gerenciado pode ser afetada.*

O Azure AD DS sincroniza automaticamente as contas de usuário e as credenciais do Azure AD. Se houver um problema com o aplicativo Azure AD usado para esse processo, a sincronização de credenciais entre o Azure AD DS e o Azure AD falhará.

### <a name="resolution"></a>Resolução

Para recriar o aplicativo do Azure AD usado para sincronização de credenciais, use o PowerShell do Azure AD para concluir as etapas a seguir. Para obter mais informações, consulte [Instalar o Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Se necessário, instale o módulo do PowerShell do Azure AD e importe-o da seguinte maneira:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Agora exclua o aplicativo e objeto antigos usando os seguintes cmdlets do PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

Depois de excluir ambos os aplicativos, a plataforma Azure os recria automaticamente e tenta retomar a sincronização de senha. A integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

## <a name="next-steps"></a>Próximas etapas

Se ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
