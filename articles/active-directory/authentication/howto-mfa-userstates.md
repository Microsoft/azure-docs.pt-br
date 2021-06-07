---
title: Habilitar a Autenticação Multifator individual – Azure Active Directory
description: Saiba como habilitar a autenticação multifator do Azure AD por usuário alterando o estado do usuário
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 749829f641119273813d3c8ca826daf8b4dc4d11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742656"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>Habilitar a autenticação multifator do Azure AD por usuário para proteger eventos de entrada

Para proteger eventos de entrada do usuário no Azure AD, você pode exigir a MFA (autenticação multifator). Habilitar a autenticação multifator do Azure AD usando políticas de acesso condicional é a abordagem recomendada para proteger os usuários. O acesso condicional é um recurso Azure AD Premium P1 ou P2 que permite aplicar regras para exigir MFA conforme necessário em determinados cenários. Para começar a usar o acesso condicional, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).

Para locatários gratuitos do Azure AD sem acesso condicional, você pode [usar os padrões de segurança para proteger os usuários](../fundamentals/concept-fundamentals-security-defaults.md). Os usuários são solicitados a receber a MFA conforme necessário, mas você não pode definir suas próprias regras para controlar o comportamento.

Se necessário, você pode habilitar cada conta para a autenticação multifator do Azure AD por usuário. Quando os usuários são habilitados individualmente, eles executam a autenticação multifator cada vez que se conectam (com algumas exceções, como quando eles entram em endereços IP confiáveis ou quando o recurso _lembrar MFA em dispositivos confiáveis_ está ativado).

Não é recomendável alterar os Estados do usuário, a menos que suas licenças do Azure AD não incluam o acesso condicional e você não queira usar os padrões de segurança. Para obter mais informações sobre as diferentes maneiras de habilitar a MFA, consulte [recursos e licenças para a autenticação multifator do Azure ad](concept-mfa-licensing.md).

> [!IMPORTANT]
>
> Este artigo fornece detalhes sobre como exibir e alterar o status da autenticação multifator do Azure AD por usuário. Se você usar o acesso condicional ou os padrões de segurança, não examine ou habilite as contas de usuário usando estas etapas.
>
> Habilitar a autenticação multifator do Azure AD por meio de uma política de acesso condicional não altera o estado do usuário. Não se assuste se os usuários parecem desabilitados. O Acesso Condicional não altera o estado.
>
> **Não habilite ou aplique a autenticação multifator do Azure AD por usuário se você usar políticas de acesso condicional.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Estados do usuário da autenticação multifator do Azure AD

O estado de um usuário reflete se um administrador o registrou na autenticação multifator do Azure AD por usuário. As contas de usuário na autenticação multifator do Azure AD têm os três Estados distintos a seguir:

| Estado | Descrição | Autenticação herdada afetada | Aplicativos que usam o navegador afetados | Autenticação moderna afetada |
|:---:| --- |:---:|:--:|:--:|
| Desabilitado | O estado padrão de um usuário não registrado na autenticação multifator do Azure AD por usuário. | Não | Não | Não |
| habilitado | O usuário está registrado na autenticação multifator do Azure AD por usuário, mas ainda pode usar sua senha para autenticação herdada. Se o usuário ainda não tiver registrado os métodos de autenticação MFA, eles receberão uma solicitação para registrar-se na próxima vez que entrarem usando a autenticação moderna (como por meio de um navegador da Web). | Não. A autenticação herdada continuará funcionando até que o processo de registro seja concluído. | Sim. Depois que a sessão expirar, o registro da autenticação multifator do Azure AD será necessário.| Sim. Depois que o token de acesso expira, o registro da autenticação multifator do Azure AD é necessário. |
| Imposto | O usuário está registrado por usuário na autenticação multifator do Azure AD. Se o usuário ainda não tiver registrado os métodos de autenticação, eles receberão uma solicitação para registrar-se na próxima vez que entrarem usando a autenticação moderna (por exemplo, por meio de um navegador da Web). Os usuários que concluírem o registro enquanto estiverem no estado *habilitado* serão automaticamente movidos para o estado *imposto* . | Sim. Os aplicativos exigem senhas de aplicativo. | Sim. A autenticação multifator do Azure AD é necessária na entrada. | Sim. A autenticação multifator do Azure AD é necessária na entrada. |

Todos os usuários começam com o status *Desabilitado*. Quando você registra os usuários na autenticação multifator do Azure AD por usuário, seu estado muda para *habilitado*. Quando usuários habilitados entram e concluem o processo de registro, seu estado é alterado para *Imposto*. Os administradores podem mover os usuários entre os Estados, incluindo de *imposto* para *habilitado* ou *desabilitado*.

> [!NOTE]
> Se a MFA por usuário for reabilitada em um usuário e o usuário não fizer o novo registro, seu estado de MFA não passará de *habilitado* para *imposto* na interface do usuário de gerenciamento do MFA. O administrador deve mover o usuário diretamente para *imposto*.

## <a name="view-the-status-for-a-user"></a>Exibir o status de um usuário

Para exibir e gerenciar Estados do usuário, conclua as seguintes etapas para acessar a página de portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
1. Pesquise e selecione *Azure Active Directory* e, em seguida, selecione **Usuários** > **Todos os usuários**.
1. Selecione **Autenticação Multifator**. Role para a direita para ver esta opção de menu. Selecione a captura de tela de exemplo abaixo para ver a janela portal do Azure completa e o local do menu: [ ![ selecione autenticação multifator na janela usuários no Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Uma nova página abre e exibe o estado do usuário, conforme mostrado no exemplo a seguir.
   ![Captura de tela que mostra informações de estado do usuário de exemplo para a autenticação multifator do Azure AD](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Alterar o status de um usuário

Para alterar o estado da autenticação multifator do Azure AD por usuário para um usuário, conclua as seguintes etapas:

1. Use as etapas anteriores para [Exibir o status de um usuário](#view-the-status-for-a-user) para acessar a página **usuários** da autenticação multifator do Azure AD.
1. Localize o usuário que você deseja habilitar para a autenticação multifator do Azure AD por usuário. Altere o modo de exibição na parte superior para **usuários**, se necessário.
   ![Selecione o usuário desejado na guia de usuários e altere o seu estado](./media/howto-mfa-userstates/enable1.png)
1. Marque a caixa ao lado do nome de um usuário para alterar o seu estado.
1. No lado direito, em **etapas rápidas**, escolha **Habilitar** ou **Desabilitar**. No exemplo a seguir, o usuário *John Smith* tem uma marca de seleção ao lado do seu nome e está habilitado para uso: ![Habilitar o usuário selecionado clicando em Habilitar no menu etapas rápidas](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Os usuários *habilitados* são automaticamente alternados para *imposto* quando se registram para a autenticação multifator do Azure AD. Não altere manualmente o estado do usuário para *imposto* , a menos que o usuário já esteja registrado ou se for aceitável para o usuário experimentar a interrupção em conexões com protocolos de autenticação herdados.

1. Confirme sua seleção na janela pop-up que é aberta.

Depois de habilitar os usuários, notifique-os por email. Informe aos usuários que um aviso é exibido para pedir que eles se registrem no próximo acesso. Além disso, se sua organização usar aplicativos sem navegador que não dão suporte à autenticação moderna, eles precisam criar senhas de aplicativo. Para obter mais informações, consulte o [Guia do usuário final da autenticação multifator do Azure ad](../user-help/multi-factor-authentication-end-user-first-time.md) para ajudá-los a começar.

## <a name="change-state-using-powershell"></a>Alteração do estado usando o PowerShell

Para alterar o estado do usuário usando o [PowerShell do Azure AD](/powershell/azure/), altere o parâmetro `$st.State` para uma conta de usuário. Há três opções de estado possíveis para uma conta de usuário:

* *Enabled*
* *Imposto*
* *Desabilitado*  

Em geral, não mova os usuários diretamente para o estado *imposto* , a menos que eles já estejam registrados para MFA. Se você fizer isso, os aplicativos de autenticação herdados deixarão de funcionar porque o usuário não passou pelo registro da autenticação multifator do Azure AD e obteve uma [senha de aplicativo](howto-mfa-app-passwords.md). Em alguns casos, esse comportamento pode ser desejado, mas afeta a experiência do usuário até que o usuário se registre.

Para começar, instale o módulo *MSOnline* usando [Install-Module](/powershell/module/powershellget/install-module) conforme a seguir:

```PowerShell
Install-Module MSOnline
```

Em seguida, conecte-se usando [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Este exemplo de script do PowerShell habilita a MFA para um usuário individual chamado *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Usar o PowerShell é uma boa opção quando você precisa habilitar usuários em massa. O script a seguir executa um loop em uma lista de usuários e habilita a MFA em suas contas. Defina as contas de usuário na primeira linha para `$users` da seguinte maneira:

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Para desabilitar a MFA, o exemplo a seguir obtém um usuário com [Get-MsolUser](/powershell/module/msonline/get-msoluser) e, em seguida, remove qualquer *StrongAuthenticationRequirements* configurado para o usuário definido usando [Set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Você também pode desabilitar diretamente a MFA para um usuário usando [Set-MsolUser](/powershell/module/msonline/set-msoluser) da seguinte maneira:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>Converter usuários de MFA por usuário para acesso condicional

O PowerShell a seguir pode ajudá-lo a fazer a conversão para o acesso condicional com base na autenticação multifator do Azure AD.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Se a MFA for reabilitada em um usuário e o usuário não fizer o novo registro, seu estado de MFA não passará de *habilitado* para *imposto* na interface do usuário de gerenciamento do MFA. Nesse caso, o administrador deve mover o usuário diretamente para *imposto*.

## <a name="next-steps"></a>Próximas etapas

Para definir as configurações de autenticação multifator do Azure AD, consulte  [definir configurações de autenticação multifator do Azure ad](howto-mfa-mfasettings.md).

Para gerenciar as configurações de usuário da autenticação multifator do Azure AD, consulte [gerenciar configurações de usuário com a autenticação multifator do Azure ad](howto-mfa-userdevicesettings.md).

Para entender por que um usuário foi solicitado ou não solicitado a executar a MFA, consulte [relatórios de autenticação multifator do Azure ad](howto-mfa-reporting.md).
