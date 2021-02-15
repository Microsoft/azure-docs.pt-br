---
title: Políticas de redefinição de senha por autoatendimento – Azure Active Directory
description: Saiba mais sobre as diferentes opções de política de redefinição de senha de autoatendimento Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 80be5ca22f3dfb673f09327108e66fccc9de6ddd
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918036"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Políticas de senha e restrições de conta no Azure Active Directory

No Azure Active Directory (AD do Azure), há uma política de senha que define as configurações como a complexidade, o comprimento ou a duração da senha. Também há uma política que define os caracteres aceitáveis e o comprimento dos nomes de os.

Quando a redefinição de senha de autoatendimento (SSPR) é usada para alterar ou redefinir uma senha no Azure AD, a política de senha é verificada. Se a senha não atender aos requisitos da política, o usuário será solicitado a tentar novamente. Os administradores do Azure têm algumas restrições sobre o uso de SSPR que são diferentes para contas de usuário regulares.

Este artigo descreve as configurações de política de senha e os requisitos de complexidade associados às contas de usuário em seu locatário do Azure AD e como você pode usar o PowerShell para verificar ou definir as configurações de expiração de senha.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas de nome de usuário

Cada conta que entra no Azure AD deve ter um valor de atributo UPN (nome principal de usuário) exclusivo associado à sua conta. Em ambientes híbridos com um ambiente de Active Directory Domain Services (AD DS) local sincronizado com o AD do Azure usando Azure AD Connect, por padrão, o UPN do Azure AD é definido como o UPN local.

A tabela a seguir descreve as políticas de nome de usuário que se aplicam a contas de AD DS locais que são sincronizadas com o Azure AD e para contas de usuário somente em nuvem criadas diretamente no Azure AD:

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caracteres não permitidos |<ul> <li>Qualquer caractere "\@\" que não esteja separando o nome de usuário do domínio.</li> <li>Não pode conter um caractere de ponto "." imediatamente antes do símbolo "\@\"</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>Pode haver até 64 caracteres antes do símbolo de "\@\"</li><li>Pode haver até 48 caracteres após o símbolo de "\@\"</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Políticas de senha do Azure AD

Uma política de senha é aplicada a todas as contas de usuário que são criadas e gerenciadas diretamente no Azure AD. Algumas dessas configurações de política de senha não podem ser modificadas, embora você possa [Configurar senhas excluídas personalizadas para a proteção de senha do Azure ad](tutorial-configure-custom-password-protection.md) ou parâmetros de bloqueio de conta.

Por padrão, uma conta é bloqueada após 10 tentativas de entrada malsucedidas com a senha incorreta. O usuário é bloqueado por um minuto. Quanto mais tentativas de conexão com senha incorreta, por mais tempo o usuário ficará bloqueado. O [bloqueio inteligente](howto-password-smart-lockout.md) rastreia os três últimos hashes de senha inválidos para evitar incrementar o contador de bloqueios para a mesma senha. Se alguém inserir a mesma senha inválida várias vezes, esse comportamento não fará com que a conta seja bloqueada. Você pode definir o limite e a duração do bloqueio inteligente.

A política de senha do Azure AD não se aplica a contas de usuário sincronizadas de um ambiente de AD DS local usando Azure AD Connect, a menos que você habilite *EnforceCloudPasswordPolicyForPasswordSyncedUsers*.

As opções de política de senha do Azure AD a seguir estão definidas. A menos que indicado, você não pode alterar essas configurações:

| Propriedade | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [] {} &#124; \: ',. ? / \` ~ " ( ) ;</li> <li>espaço em branco</li></ul> |
| Caracteres não permitidos | Caracteres Unicode. |
| Restrições de senha |<ul><li>Um mínimo de 8 caracteres e um máximo de 256 caracteres.</li><li>Requer três de quatro dos seguintes itens:<ul><li>Caracteres minúsculos.</li><li>Caracteres maiúsculos.</li><li>Números (0-9).</li><li>Símbolos (veja as restrições de senha acima).</li></ul></li></ul> |
| Duração da expiração da senha (duração máxima da senha) |<ul><li>Valor padrão: **90** dias.</li><li>O valor é configurável usando o cmdlet `Set-MsolPasswordPolicy` do Módulo do Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificação de expiração de senha (quando os usuários são notificados da expiração da senha) |<ul><li>Valor padrão: **14** dias (antes de a senha expirar).</li><li>O valor é configurável usando o cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Expiração de senha (permitir que as senhas nunca expirem) |<ul><li>Valor padrão: **false** (indica que a senha tem uma data de validade).</li><li>O valor pode ser configurado para contas de usuário individuais usando o cmdlet `Set-MsolUser`.</li></ul> |
| Histórico de alteração de senha | A última senha *não pode* ser usada novamente quando o usuário alterar uma senha. |
| Histórico de redefinição de senha | A última senha *pode* ser usada novamente quando o usuário redefine uma senha esquecida. |

## <a name="administrator-reset-policy-differences"></a>Diferenças da política de redefinição de senha do administrador

Por padrão, as contas de administrador são habilitadas para redefinição de senha de autoatendimento e uma política de redefinição de senha de *duas portão* padrão forte é imposta. Essa política pode ser diferente da que você definiu para os usuários e essa política não pode ser alterada. Sempre teste a funcionalidade de redefinição de senha como um usuário sem funções de administrador do Azure atribuídas.

Com uma política de dois portões, os administradores não podem usar perguntas de segurança.

A política de duas portas requer dois tipos de dados de autenticação, como um endereço de email, aplicativo autenticador ou um número de telefone. Uma política de duas portas aplica-se nas seguintes circunstâncias:

* Todas as seguintes funções de administrador do Azure são afetadas:
  * Administrador de assistência técnica
  * Administrador de suporte a serviço
  * Administrador de cobrança
  * Suporte de camada 1 do parceiro
  * Suporte de camada 2 do parceiro
  * Administradores do Exchange
  * Administrador de caixa de correio
  * Administrador do Skype for Business
  * Administrador de usuários
  * Gravadores de diretório
  * Administrador global ou administrador da empresa
  * Administrador do SharePoint
  * Administrador de conformidade
  * Administrador de aplicativos
  * Administrador de segurança
  * Administrador de função com privilégios
  * Administrador do Intune
  * Administrador local do dispositivo ingressado no Azure AD
  * Administrador de serviços de Proxy do aplicativo
  * Administrador do Dynamics 365
  * Administrador de serviços do Power BI
  * Administrador de autenticação
  * Administrador de senha
  * Administrador de autenticação privilegiada

* Caso tenham se passado 30 dias decorridos de uma assinatura de avaliação; ou
* Um domínio personalizado foi configurado para seu locatário do Azure AD, como *contoso.com*; or
* O Azure AD Connect está sincronizando identidades do seu diretório local

Você pode desabilitar o uso de SSPR para contas de administrador usando o cmdlet do PowerShell [set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings) . O `-SelfServePasswordResetEnabled $False` parâmetro DESABILITA SSPR para administradores.

### <a name="exceptions"></a>Exceções

Uma política de duas portas requer um tipo de dados de autenticação, como um endereço de email ou um número de telefone. Uma política de uma porta aplica-se nas seguintes circunstâncias:

* Ela esteja dentro dos primeiros 30 dias de uma assinatura; ou
* Um domínio personalizado não foi configurado para seu locatário do Azure AD, portanto, está usando o padrão **. onmicrosoft.com*. O domínio padrão **. onmicrosoft.com* não é recomendado para uso em produção; e
* O Azure AD Connect não está sincronizando identidades

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Políticas de expiração de senha

Um administrador *global* ou *administrador de usuários* pode usar o [módulo Microsoft Azure ad para Windows PowerShell](/powershell/module/Azuread/) para definir senhas de usuário para não expirar.

Você também pode usar os cmdlets do PowerShell para remover a configuração nunca expira ou para ver quais senhas de usuário estão definidas para nunca expirar.

Essa orientação se aplica a outros provedores, como o Intune e Microsoft 365, que também dependem do Azure AD para serviços de identidade e diretório. A expiração da senha é a única parte da política que pode ser alterada.

> [!NOTE]
> Somente as senhas para contas de usuário que não são sincronizadas por meio do Azure AD Connect podem ser configuradas para não expirar. Para saber mais sobre a sincronização de diretório, confira [Conectar AD ao Azure AD](../hybrid/whatis-hybrid-identity.md).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Definir ou verificar políticas de senha usando o PowerShell

Para começar, [Baixe e instale o módulo do PowerShell do Azure ad](/powershell/module/Azuread/) e [Conecte-o ao seu locatário do Azure ad](/powershell/module/azuread/connect-azuread#examples).

Depois que o módulo for instalado, use as etapas a seguir para concluir cada tarefa, conforme necessário.

### <a name="check-the-expiration-policy-for-a-password"></a>Verificar a política de expiração de uma senha

1. Abra um prompt do PowerShell e [Conecte-se ao seu locatário do Azure ad](/powershell/module/azuread/connect-azuread#examples) usando uma conta de administrador *global* ou de *administrador de usuário* .
1. Execute um dos seguintes comandos para um usuário individual ou para todos os usuários:

   * Para ver se a senha de um único usuário está definida para nunca expirar, execute o cmdlet a seguir. Substitua `<user ID>` pela ID de usuário do usuário que você deseja verificar, como *driley \@ contoso.onmicrosoft.com*:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Para ver a configuração **Senha nunca expira** para todos os usuários, execute o seguinte cmdlet: 

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Definir uma senha para expirar

1. Abra um prompt do PowerShell e [Conecte-se ao seu locatário do Azure ad](/powershell/module/azuread/connect-azuread#examples) usando uma conta de administrador *global* ou de *administrador de usuário* .
1. Execute um dos seguintes comandos para um usuário individual ou para todos os usuários:

   * Para definir a senha de um usuário para que a senha expire, execute o cmdlet a seguir. Substitua `<user ID>` pela ID de usuário do usuário que você deseja verificar, como *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Para definir as senhas de todos os usuários da organização para que elas expirem, use o seguinte cmdlet: 

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Definir uma senha para nunca expirar

1. Abra um prompt do PowerShell e [Conecte-se ao seu locatário do Azure ad](/powershell/module/azuread/connect-azuread#examples) usando uma conta de administrador *global* ou de *administrador de usuário* .
1. Execute um dos seguintes comandos para um usuário individual ou para todos os usuários:

   * Para definir a senha de um usuário para nunca expirar, execute o cmdlet a seguir. Substitua `<user ID>` pela ID de usuário do usuário que você deseja verificar, como *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Para definir as senhas de todos os usuários de uma organização para nunca expirar, execute o seguinte cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > Senhas definidas como `-PasswordPolicies DisablePasswordExpiration` ainda expiram com base no atributo `pwdLastSet`. Com base no atributo `pwdLastSet`, se você alterar a expiração para `-PasswordPolicies None`, todas as senhas que tenham um `pwdLastSet` com idade acima de 90 dias exigirão que o usuário as altere na próxima vez que entrarem. Essa alteração pode afetar um grande número de usuários.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o SSPR, consulte [tutorial: permitir que os usuários desbloqueiem sua conta ou redefinam senhas usando Azure Active Directory redefinição de senha de autoatendimento](tutorial-enable-sspr.md).

Se você ou os usuários tiverem problemas com o SSPR, consulte [solucionar problemas de redefinição de senha de autoatendimento](./troubleshoot-sspr.md)
