---
title: Requisitos de dados SSPR do Azure AD | Microsoft Docs
description: "Requisitos de dados para autoatendimento de redefinição de senha do Azure AD e como atendê-los"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f8e78030182b93259238a4f154de11f616915ec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implantar redefinição de senha sem exigir registro do usuário final

A implantação do SSPR (Autoatendimento de Redefinição de Senha) exige que os dados de autenticação estejam presentes. Algumas organizações precisam que seus próprios usuários insiram os respectivos dados de autenticação, mas muitas organizações preferem sincronizar com dados existentes no Active Directory. Se você tiver formatado corretamente os dados no diretório local e configurar [Azure AD Connect usando as configurações expressas](./connect/active-directory-aadconnect-get-started-express.md), esses dados serão disponibilizados para o Azure AD e o SSPR sem exigir qualquer interação do usuário.

Todos os números de telefone devem estar no formato +CountryCode PhoneNumber (por exemplo: +1 4255551234) para que funcione corretamente.

> [!NOTE]
> A redefinição de senha não dá suporte a ramais telefônicos. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes que a chamada seja completada.

## <a name="fields-populated"></a>Campos populados

Se você usar as configurações padrão no Azure AD Connect, serão realizados os seguintes mapeamentos.

| AD local | AD do Azure | Informações de contato para Autenticação do Azure AD |
| --- | --- | --- |
| telephoneNumber | Telefone comercial | Telefone alternativo |
| Serviço Móvel | Telefone celular | Telefone |


## <a name="security-questions-and-answers"></a>Perguntas e respostas de segurança

As perguntas e respostas de segurança são armazenadas em seu locatário do Azure AD e podem ser acessadas somente por usuários pelo [Portal de registro do SSPR](https://aka.ms/ssprsetup). Os administradores não podem ver nem modificar o conteúdo das perguntas e respostas dos outros usuários.

### <a name="what-happens-when-a-user-registers"></a>O que acontece quando um usuário se registra

Quando um usuário se registra, a página de registro define os seguintes campos:

* Telefone de autenticação
* E-mail de autenticação
* Perguntas de segurança e respostas

Se você forneceu um valor para **Celular** ou **Email Alternativo**, os usuários poderão usar esses valores imediatamente para redefinir as senhas, mesmo que não tenham se registrado no serviço. Além disso, os usuários visualizam esses valores ao se registrarem pela primeira vez, e os modificam, se for desejado. Após registro bem-sucedido, esses valores serão persistidos nos campos **Telefone de Autenticação** e **Email de Autenticação**, respectivamente.

## <a name="set-and-read-authentication-data-using-powershell"></a>Definir e ler os dados de autenticação usando o PowerShell

Os campos a seguir podem ser definidos usando o PowerShell

* Email alternativo
* Telefone celular
* Telefone comercial – só poderá ser definido se não for sincronizar com um diretório local

### <a name="using-powershell-v1"></a>Usando o PowerShell V1

Para começar, primeiramente é preciso [baixar e instalar o módulo PowerShell do Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

#### <a name="set-authentication-data-with-powershell-v1"></a>Definir dados de autenticação com o PowerShell V1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>Ler dados de autenticação com o PowerShell V1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>O Telefone de Autenticação e o Email de Autenticação só podem ser lidos usando o PowerShell V1 com os comandos abaixo

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>Usando o PowerShell V2

Para começar, primeiramente é preciso [baixar e instalar o módulo PowerShell V2 do Azure AD](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

Para instalar rapidamente de versões recentes do PowerShell que suportam Install-Module, execute estes comandos (a primeira linha apenas verifica se ele já está instalado):

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>Definir Dados de Autenticação com o PowerShell V2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>Ler Dados de Autenticação com o PowerShell V2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Início Rápido**](active-directory-passwords-getting-started.md): comece agora mesmo a usar o gerenciamento de autoatendimento de senhas do Azure AD 
* [**Licenciamento**](active-directory-passwords-licensing.md): configure o licenciamento do Azure AD
* [**Distribuição**](active-directory-passwords-best-practices.md): planeje e implante o SSPR para seus usuários usando as diretrizes descritas aqui
* [**Personalizar**](active-directory-passwords-customize.md): personalize a aparência da experiência do SSPR em sua empresa.
* [**Política** ](active-directory-passwords-policy.md) - Como entender e definir políticas de senha do Azure AD
* [**Relatórios**](active-directory-passwords-reporting.md): descubra se, quando e onde os usuários estão acessando a funcionalidade SSPR
* [**Detalhamento Técnico**](active-directory-passwords-how-it-works.md): veja os bastidores para entender como o recurso funciona
* [**Perguntas frequentes**](active-directory-passwords-faq.md): como? Por quê? O quê? Onde? Quem? Quando? – respostas para perguntas que você sempre quis fazer
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - Saiba como resolver problemas comuns que vemos com a SSPR
