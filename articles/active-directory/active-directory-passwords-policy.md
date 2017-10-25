---
title: "Política: SSPR do Azure AD | Microsoft Docs"
description: "Opções de política de autoatendimento de redefinição de senha do Azure AD"
services: active-directory
keywords: "Gerenciamento de senha do Active Directory, gerenciamento de senha, autoatendimento de redefinição de senha do Azure AD"
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
ms.openlocfilehash: f03c01158bf1a95ec03454b092bbe0314c4ea6be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas e restrições de senha do Active Directory do Azure

Este artigo descreve as políticas de senha e os requisitos de complexidade associados a contas de usuário armazenadas no locatário do Azure AD.

## <a name="administrator-password-policy-differences"></a>Diferenças de política de senha do administrador

A Microsoft impõe uma política padrão rígida de redefinição de senha de **duas entradas** para qualquer função de administrador do Azure (exemplo: Administrador Global, Administrador de Assistência Técnica, Administrador de Senha etc.)

Isso impede administradores de usar perguntas de segurança e impõe o seguinte.

Duas políticas de entrada, exigindo duas partes dos dados de autenticação (endereço de email **e** número de telefone), aplicam-se nas seguintes circunstâncias

* Todas as funções de administrador do Azure
  * Administrador de assistência técnica
  * Administrador de suporte a serviço
  * Administrador de cobrança
  * Suporte de camada 1 do parceiro
  * Suporte de camada 2 do parceiro
  * Administrador de serviços do Exchange
  * Administrador de serviços do Lync
  * Administrador da conta de usuário
  * Gravadores de diretório
  * Administrador de empresa/administrador global
  * Administrador de serviços do SharePoint
  * Administrador de conformidade
  * Administrador de aplicativos
  * Administrador de segurança
  * Administrador de função com privilégios
  * Administrador de serviços do Intune
  * Administrador de serviços de Proxy de Aplicativo
  * Administrador de serviços do CRM
  * Administrador de serviços do Power BI
  
* Trinta dias decorridos de uma avaliação **OU**
* O domínio personalizado está presente (contoso.com) **OU**
* O Azure AD Connect está sincronizando identidades do seu diretório local

### <a name="exceptions"></a>Exceções
Uma política de entrada, exigindo uma parte dos dados de autenticação (endereço de email **ou** número de telefone), aplicam-se nas seguintes circunstâncias

* Primeiros 30 dias de uma avaliação **OU**
* O domínio personalizado não está presente (*.onmicrosoft.com) **E** o Azure AD Connect não está sincronizando identidades


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas UserPrincipalName que se aplicam a todas as contas de usuário

Cada conta de usuário que precisa entrar no Azure AD deve ter um valor de atributo UPN (nome principal do usuário) exclusivo associado à respectiva conta. A tabela abaixo descreve as políticas que se aplicam a contas de usuário do Active Directory locais sincronizadas com a nuvem e a contas de usuário somente em nuvem.

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caracteres não permitidos |<ul> <li>Qualquer caractere '@' que não esteja separando o nome de usuário do domínio.</li> <li>Não pode conter um caractere de ponto '.' imediatamente antes do símbolo '@'</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>64 caracteres antes do símbolo '@'</li><li>48 caracteres depois do símbolo '@'</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Políticas de senha que se aplicam somente a contas de usuário na nuvem

A tabela a seguir descreve as configurações de política de senha disponíveis que podem ser aplicadas a contas de usuário que são criadas e gerenciadas no AD do Azure.

| Propriedade | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caracteres não permitidos |<ul><li>Caracteres Unicode</li><li>Espaços</li><li> **Somente senhas fortes**: não pode conter um caractere de ponto '.' imediatamente antes do símbolo '@'</li></ul> |
| Restrições de senha |<ul><li>Mínimo de 8 caracteres e no máximo 16 caracteres</li><li>**Somente senhas fortes**: Requer 3 de 4 dos seguintes:<ul><li>Caracteres minúsculos</li><li>Caracteres maiúsculos</li><li>Números (0-9)</li><li>Símbolos (veja as restrições de senha acima)</li></ul></li></ul> |
| Tempo de expiração da senha |<ul><li>Valor padrão: **90** dias </li><li>O valor é configurável usando o cmdlet Set-MsolPasswordPolicy do Módulo do Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificação de expiração de senha |<ul><li>Valor padrão: **14** dias (antes do vencimento de senha)</li><li>O valor é configurável usando o cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Expiração de senha |<ul><li>Valor padrão: **false** dias (indica que a expiração de senha está habilitada) </li><li>O valor pode ser configurado para contas de usuário individuais usando o cmdlet Set-MsolUser. </li></ul> |
| Histórico de **alteração** de senha |A última senha **não pode** ser usada novamente ao **alterar** uma senha. |
| Histórico de **redefinição** de senha | A última senha **pode** ser usada novamente ao **redefinir** uma senha esquecida. |
| Bloqueio de conta |Após 10 tentativas malsucedidas de entrar (senha incorreta), o usuário será bloqueado por um minuto. Quanto mais tentativas de entrada com senha incorreta, por mais tempo o usuário ficará bloqueado. |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Definir políticas de expiração de senha no Active Directory do Azure

Um administrador global de um serviço de nuvem da Microsoft pode usar o Módulo Microsoft Azure Active Directory para Windows PowerShell de modo a configurar senhas de usuário para não expirarem. Você também pode usar os cmdlets do Windows PowerShell para remover as configurações que nunca expiram ou para ver quais senhas de usuário são configuradas para não expirar. Esta instrução se aplica a outros provedores, como o Microsoft Intune e o Office 365, que também dependem do Microsoft Azure Active Directory para serviços de identidade e diretório.

> [!NOTE]
> Apenas as senhas de contas de usuário que não são sincronizadas por meio da sincronização de diretório podem ser configuradas para não expirar. Para saber mais sobre a sincronização de diretório, confira [Conectar AD ao Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>Definir ou verificar políticas de senha usando o PowerShell

Para começar, primeiramente é preciso [baixar e instalar o módulo PowerShell do Azure AD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Depois que você tiver instalado, pode seguir as etapas abaixo para configurar cada campo.

### <a name="how-to-check-expiration-policy-for-a-password"></a>Como verificar a política de expiração de senha
1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.
2. Execute um dos seguintes comandos:

   * Para ver se a senha de um único usuário está definida para nunca expirar, execute o seguinte cmdlet usando o nome UPN (por exemplo, aprilr@contoso.onmicrosoft.com) ou a ID do usuário que você deseja verificar: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Para ver a configuração "Senha nunca expira" para todos os usuários, execute o seguinte cmdlet: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Definir uma senha para expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.
2. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para que ela expire, execute o seguinte cmdlet usando o nome UPN ou a ID do usuário: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Para definir as senhas de todos os usuários da organização para que elas expirem, use o seguinte cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Definir uma senha para nunca expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.
2. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para que ela nunca expire, execute o seguinte cmdlet usando o nome UPN ou a ID do usuário: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Para definir as senhas de todos os usuários em uma organização para nunca expirar, execute o seguinte cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Início Rápido**](active-directory-passwords-getting-started.md): comece agora mesmo a usar o gerenciamento de autoatendimento de senhas do Azure AD 
* [**Licenciamento**](active-directory-passwords-licensing.md): configure o licenciamento do Azure AD
* [**Dados**](active-directory-passwords-data.md): entenda os dados que são necessários e como eles são usados para o gerenciamento de senhas
* [**Distribuição**](active-directory-passwords-best-practices.md): planeje e implante o SSPR para seus usuários usando as diretrizes descritas aqui
* [**Personalizar**](active-directory-passwords-customize.md): personalize a aparência da experiência do SSPR em sua empresa.
* [**Relatórios**](active-directory-passwords-reporting.md): descubra se, quando e onde os usuários estão acessando a funcionalidade SSPR
* [**Detalhamento Técnico**](active-directory-passwords-how-it-works.md): veja os bastidores para entender como o recurso funciona
* [**Perguntas frequentes**](active-directory-passwords-faq.md): como? Por quê? O quê? Onde? Quem? Quando? – respostas para perguntas que você sempre quis fazer
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - Saiba como resolver problemas comuns que vemos com a SSPR
