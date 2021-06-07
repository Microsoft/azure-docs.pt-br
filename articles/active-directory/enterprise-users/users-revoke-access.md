---
title: Revogar o acesso do usuário em uma emergência no Azure Active Directory | Microsoft Docs
description: Como revogar todo o acesso de um usuário no Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 12/02/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 826ca9fc20d8bbcf9a5f90ccc895b9f9867a6be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860568"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Revogar o acesso do usuário no Azure Active Directory

Entre os cenários que podem exigir que um administrador revogue todo o acesso de um usuário incluem contas comprometidas, encerramento de funcionários e outras ameaças internas. Dependendo da complexidade do ambiente, os administradores podem executar várias etapas para garantir que o acesso seja revogado. Em alguns cenários, pode haver um período entre o início da revogação de acesso e quando o acesso é efetivamente revogado.

Para reduzir os riscos, você deve entender como os tokens funcionam. Há muitos tipos de tokens, que se enquadram em um dos padrões mencionados nas seções a seguir.

## <a name="access-tokens-and-refresh-tokens"></a>Tokens de acesso e tokens de atualização

Tokens de acesso e tokens de atualização são frequentemente usados com aplicativos cliente densos e também são usados em aplicativos baseados em navegador, como aplicativos de página única.

- Quando os usuários se autenticam no Azure AD, as políticas de autorização são avaliadas para determinar se o usuário pode receber acesso a um recurso específico.  

- Se autorizado, o Azure AD emite um token de acesso e um token de atualização para o recurso.  

- Tokens de acesso emitidos pelo Azure AD por padrão por último por 1 hora. Se o protocolo de autenticação permitir, o aplicativo poderá autenticar silenciosamente o usuário, passando o token de atualização para o Azure AD quando o token de acesso expirar.

Em seguida, o Azure AD reavalia suas políticas de autorização. Se o usuário ainda estiver autorizado, o Azure AD emitirá um novo token de acesso e o token de atualização.

Os tokens de acesso podem ser uma preocupação de segurança se o acesso precisar ser revogado dentro de um tempo menor do que a vida útil do token, que geralmente é de uma hora. Por esse motivo, a Microsoft está trabalhando ativamente para trazer a [avaliação de acesso contínuo](../conditional-access/concept-continuous-access-evaluation.md) para Microsoft 365 aplicativos, o que ajuda a garantir a invalidação de tokens de acesso quase em tempo real.  

## <a name="session-tokens-cookies"></a>Tokens de sessão (cookies)

A maioria dos aplicativos baseados em navegador usa tokens de sessão em vez de tokens de acesso e de atualização.  

- Quando um usuário abre um navegador e é autenticado em um aplicativo por meio do Azure AD, o usuário recebe dois tokens de sessão. Um do Azure AD e outro do aplicativo.  

- Quando um aplicativo emite seu próprio token de sessão, o acesso ao aplicativo é regido pela sessão do aplicativo. Neste ponto, o usuário é afetado apenas pelas políticas de autorização das quais o aplicativo está ciente.

- As políticas de autorização do Azure AD são reavaliadas sempre que o aplicativo envia o usuário de volta ao Azure AD. A reavaliação geralmente ocorre silenciosamente, embora a frequência dependa de como o aplicativo é configurado. É possível que o aplicativo nunca envie o usuário de volta ao Azure AD, desde que o token de sessão seja válido.

- Para que um token de sessão seja revogado, o aplicativo deve revogar o acesso com base em suas próprias políticas de autorização. O Azure AD não pode revogar diretamente um token de sessão emitido por um aplicativo.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Revogar o acesso de um usuário no ambiente híbrido

Para um ambiente híbrido com Active Directory locais sincronizados com o Azure Active Directory, a Microsoft recomenda que os administradores de ti executem as seguintes ações.  

### <a name="on-premises-active-directory-environment"></a>Ambiente de Active Directory local

Como administrador no Active Directory, conecte-se à sua rede local, abra o PowerShell e execute as seguintes ações:

1. Desabilite o usuário no Active Directory. Consulte [Disable-ADAccount](/powershell/module/addsadministration/disable-adaccount).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Redefina a senha do usuário duas vezes no Active Directory. Consulte [set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword).

    > [!NOTE]
    > O motivo para alterar a senha de um usuário duas vezes é reduzir o risco de Pass-the-hash, especialmente se houver atrasos na replicação de senha local. Se você puder supor que essa conta não está comprometida, poderá redefinir a senha apenas uma vez.

    > [!IMPORTANT] 
    > Não use as senhas de exemplo nos cmdlets a seguir. Certifique-se de alterar as senhas para uma cadeia de caracteres aleatória.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Ambiente do Azure Active Directory

Como administrador no Azure Active Directory, abra o PowerShell, execute ``Connect-AzureAD`` e realize as seguintes ações:

1. Desabilite o usuário no Azure AD. Consulte [set-AzureADUser](/powershell/module/azuread/Set-AzureADUser).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. Revogue os tokens de atualização do Azure AD do usuário. Consulte [REVOKE-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. Desabilite os dispositivos do usuário. Consulte [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>Etapas opcionais

- [Apague dados corporativos de aplicativos gerenciados pelo Intune](/mem/intune/apps/apps-selective-wipe).

- [Limpar dispositivos corporativos deve redefinir o dispositivo para as configurações padrão de fábrica](/mem/intune/remote-actions/devices-wipe).

> [!NOTE]
> Os dados no dispositivo não podem ser recuperados após um apagamento.

## <a name="when-access-is-revoked"></a>Quando o acesso é revogado

Depois que os administradores tiverem adotado as etapas acima, o usuário não poderá obter novos tokens para nenhum aplicativo vinculado a Azure Active Directory. O tempo decorrido entre a revogação e o usuário que está perdendo o acesso depende de como o aplicativo está concedendo acesso:

- Para **aplicativos que usam tokens de acesso**, o usuário perde o acesso quando o token de acesso expira.

- Para **aplicativos que usam tokens de sessão**, as sessões existentes são encerradas assim que o token expira. Se o estado desabilitado do usuário for sincronizado com o aplicativo, o aplicativo poderá revogar automaticamente as sessões existentes do usuário se ele estiver configurado para fazer isso.  O tempo necessário depende da frequência de sincronização entre o aplicativo e o Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [Práticas de acesso seguro para administradores do Azure AD](../roles/security-planning.md)
- [Adicionar ou atualizar informações de perfil do usuário](../fundamentals/active-directory-users-profile-azure-portal.md)
