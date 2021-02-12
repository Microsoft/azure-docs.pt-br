---
title: Preencher previamente as informações de contato para redefinição de senha de autoatendimento-Azure Active Directory
description: Saiba como preencher previamente as informações de contato para usuários de Azure Active Directory SSPR (redefinição de senha de autoatendimento) para que possam usar o recurso sem concluir um processo de registro.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861146"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Preencher previamente as informações de contato de autenticação de usuário para Azure Active Directory redefinição de senha de autoatendimento (SSPR)

Para usar a SSPR (redefinição de senha de autoatendimento) do Azure AD (Azure Active Directory), as informações de contato de autenticação para um usuário devem estar presentes. Algumas organizações têm usuários que registram seus próprios dados de autenticação. Outras organizações preferem sincronizar a partir de dados de autenticação que já existem no Active Directory Domain Services (AD DS). Esses dados sincronizados são disponibilizados para o Azure AD e o SSPR sem a necessidade de interação do usuário. Quando os usuários precisam alterar ou redefinir sua senha, eles podem fazer isso, mesmo que eles não tenham registrado suas informações de contato anteriormente.

Você pode preencher previamente as informações de contato de autenticação se atender aos seguintes requisitos:

* Você formatou corretamente os dados em seu diretório local.
* Você configurou [Azure ad Connect](../hybrid/how-to-connect-install-express.md) para seu locatário do Azure AD.

Os números de telefone devem estar no formato *+ CountryCode PhoneNumber*, como *+ 1 4251234567*.

> [!NOTE]
> Deve haver um espaço entre o código do país e o número de telefone.
>
> A redefinição de senha não dá suporte a ramais telefônicos. Mesmo no formato *+1 4251234567X12345*, as extensões são removidas antes que a chamada seja completada.

## <a name="fields-populated"></a>Campos populados

Se você usar as configurações padrão no Azure AD Connect, os seguintes mapeamentos serão feitos para popular as informações de contato de autenticação para SSPR:

| Active Directory local | AD do Azure     |
|------------------------------|--------------|
| telephoneNumber              | Telefone comercial |
| Serviço Móvel                       | Telefone celular |

Depois que um usuário verifica seu número de telefone celular, o campo *telefone* em **autenticação informações de contato** no Azure ad também é preenchido com esse número.

## <a name="authentication-contact-info"></a>Informações de contato de autenticação

Na página **métodos de autenticação** de um usuário do Azure AD na portal do Azure, um administrador global pode definir manualmente as informações de contato de autenticação. Você pode examinar os métodos existentes na seção *métodos de autenticação utilizáveis* ou **+ Adicionar métodos de autenticação**, conforme mostrado no seguinte exemplo de captura de tela:

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Gerenciar métodos de autenticação do portal do Azure":::

As seguintes considerações se aplicam a essas informações de contato de autenticação:

* Se o campo *telefone* for preenchido e o *telefone celular* estiver habilitado na política SSPR, o usuário verá esse número na página de registro de redefinição de senha e durante o fluxo de trabalho de redefinição de senha.
* Se o campo de *email* for preenchido e o *email* estiver habilitado na política SSPR, o usuário verá esse email na página de registro de redefinição de senha e durante o fluxo de trabalho de redefinição de senha.

## <a name="security-questions-and-answers"></a>Perguntas e respostas de segurança

As perguntas e respostas de segurança são armazenadas em seu locatário do Azure AD e podem ser acessadas somente por usuários pelo [Portal de registro do SSPR](https://aka.ms/ssprsetup). Os administradores não podem ver, configurar nem modificar o conteúdo das perguntas e respostas dos outros usuários.

## <a name="what-happens-when-a-user-registers"></a>O que acontece quando um usuário se registra

Quando um usuário se registra, a página de registro define os seguintes campos:

* **Telefone de autenticação**
* **E-mail de autenticação**
* **Perguntas de segurança e respostas**

Se você forneceu um valor *Mobile phone* para celular ou *email alternativo*, os usuários poderão usar esses valores imediatamente para redefinir suas senhas, mesmo que não tenham se registrado no serviço.

Os usuários também veem esses valores quando se registram pela primeira vez e podem modificá-los se desejarem. Depois de registrados com êxito, esses valores são persistidos nos campos *telefone de autenticação* e *email de autenticação* , respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Definir e ler os dados de autenticação usando o PowerShell

Os campos a seguir podem ser definidos usando o PowerShell:

* *Email alternativo*
* *Celular*
* *Telefone comercial*
    * Só poderá ser definido se você não estiver sincronizando com um diretório local.

> [!IMPORTANT]
> Há uma falta de paridade conhecida em recursos de comando entre o PowerShell v1 e o PowerShell v2. A [API REST do Microsoft Graph (beta) para métodos de autenticação](/graph/api/resources/authenticationmethods-overview) é o foco da engenharia atual para fornecer interação moderna.

### <a name="use-powershell-version-1"></a>Usar o PowerShell versão 1

Para começar, [Baixe e instale o módulo do PowerShell do Azure ad](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule). Depois de instalado, use as etapas a seguir para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Definir dados de autenticação com o PowerShell versão 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Ler dados de autenticação com o PowerShell versão 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Ler as opções de Telefone de Autenticação e Email de Autenticação

Para ler o **Telefone de Autenticação** e **Email de Autenticação** quando você usa o PowerShell versão 1, use os seguintes comandos:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Usar o PowerShell versão 2

Para começar, [Baixe e instale o módulo PowerShell da versão 2 do Azure ad](/powershell/module/azuread/).

Para instalar rapidamente de versões recentes do PowerShell com suporte `Install-Module` , execute os comandos a seguir. A primeira linha verifica se o módulo já está instalado:

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

Depois que o módulo for instalado, use as etapas a seguir para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Definir dados de autenticação com o PowerShell versão 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Ler dados de autenticação com o PowerShell versão 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Próximas etapas

Depois que as informações de contato de autenticação forem preenchidas previamente para os usuários, conclua o tutorial a seguir para habilitar a redefinição de senha de autoatendimento:

> [!div class="nextstepaction"]
> [Habilitar a redefinição de senha self-service do Azure AD](tutorial-enable-sspr.md)
