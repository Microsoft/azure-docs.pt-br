---
title: 'Azure Active Directory Domain Services: Habilitar a delegação restrita do kerberos | Microsoft Docs'
description: Habilitar a delegação restrita de kerberos nos domínios gerenciados do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: f4252fcd70ff5aa9c2056b72add7c79283ce7fcf
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473443"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Configurar a KCD (delegação Restrita de Kerberos) em um domínio gerenciado
Muitos aplicativos precisam acessar recursos no contexto do usuário. O Active Directory oferece suporte a um mecanismo chamado delegação de Kerberos, que permite que esse caso de uso. Além disso, você pode restringir a delegação para que somente recursos específicos possam ser acessados no contexto do usuário. Os domínios gerenciados do Azure AD Domain Services são diferentes dos domínios tradicionais do Active Directory, pois estão bloqueados com mais segurança.

Este artigo mostra como configurar a delegação restrita de Kerberos em um domínio gerenciado do Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>KCD (delegação restrita de Kerberos)
A delegação de Kerberos permite que uma conta represente outra entidade de segurança (como um usuário) para acessar recursos. Considere um aplicativo Web que acessa uma API da Web de back-end no contexto de um usuário. Neste exemplo, o aplicativo Web (em execução no contexto de uma conta de serviço ou uma conta de computador/máquina) representa o usuário ao acessar o recurso (API da Web de back-end). A delegação Kerberos não é segura, pois não restringir os recursos que podem ser acessados pela conta de representação no contexto do usuário.

A KCD (Delegação restrita de Kerberos) restringe os serviços/recursos aos quais o servidor especificado pode agir em nome de um usuário. A KCD tradicional exige privilégios de administrador de domínio para configurar uma conta de domínio para um serviço, e restringe a conta em um domínio único.

A KCD tradicional também tem alguns problemas associados. Em sistemas operacionais mais antigos, se o administrador de domínio tiver configurado a KCD com base na conta para o serviço, o administrador do serviço não tinha como saber quais serviços de front-end eram delegados aos serviços de recursos que eles possuíam. E qualquer serviço front-end que pudesse ser delegado a um serviço de recurso representava um possível ponto de ataque. Se um servidor que hospedava um serviço front-end fosse comprometido, e tivesse sido configurado para delegar aos serviços de recursos, os serviços de recurso também poderiam ser comprometidos.

> [!NOTE]
> Em um domínio gerenciado do Azure AD Domain Services, você não tem privilégios de administrador de domínio. Portanto, **não é possível configurar um KCD tradicional baseado em conta em um domínio gerenciado**. Use o KCD baseado em recursos conforme descrito neste artigo. Esse mecanismo também é mais seguro.
>
>

## <a name="resource-based-kcd"></a>KCD baseado em recursos
Do Windows Server 2012 em diante, os administradores de serviço ganham a capacidade de configurar a delegação restrita para seus serviços. Neste modelo, o administrador do serviço de back-end pode permitir ou negar que serviços específicos de front-end utilizem a KCD. Esse modelo é conhecido como **KCD com base em recursos**.

A KCD Com base em recursos é configurado usando o PowerShell. Use os cmdlets `Set-ADComputer` ou `Set-ADUser`, dependendo se a conta de representação é uma conta de computador ou uma conta de serviço/conta de usuário.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Configurar a KCD baseada em recursos para uma conta de computador em um domínio gerenciado
Vamos supor que você tenha um aplicativo Web em execução no computador 'contoso100-webapp.contoso100.com'. Ele precisa acessar o recurso (uma API da Web em execução no 'contoso100-api.contoso100.com') no contexto de usuários do domínio. Veja como você pode configurar a KCD baseada em recursos para esse cenário:

1. [Criar uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar esta UO personalizada para usuários dentro do domínio gerenciado.
2. Una as duas máquinas virtuais (a que está executando o aplicativo da Web e a que está executando a API da Web) no domínio gerenciado. Crie essas contas de computador na UO personalizada.
3. Agora, configure o KCD com base em recursos usando o seguinte comando do PowerShell:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> As contas de computador para o aplicativo da Web e a API da Web precisam estar em uma UO personalizada onde você tem permissões para configurar o KCD com base em recursos. Você não pode configurar o KCD com base em recursos para uma conta de computador no contêiner 'AAD DC Computers' interno.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Configurar a KCD baseada em recursos para uma conta de usuário em um domínio gerenciado
Vamos supor que você tenha um aplicativo web em execução como uma conta de serviço 'appsvc' e ele precisa acessar o recurso (uma API da Web em execução como uma conta de serviço - 'backendsvc') no contexto de usuários do domínio. Veja como você pode configurar a KCD baseada em recursos para esse cenário.

1. [Criar uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar esta UO personalizada para usuários dentro do domínio gerenciado.
2. Una a máquina virtual executando o recurso/API da Web de back-end para o domínio gerenciado. Crie sua conta de computador dentro da OU personalizada.
3. Crie a conta de serviço (por exemplo, 'appsvc') usada para executar o aplicativo da Web dentro da OU personalizada.
4. Agora, configure o KCD com base em recursos usando o seguinte comando do PowerShell:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Tanto a conta de computador para a API da Web back-end quanto a conta do serviço precisam estar em uma UO personalizada onde você tenha permissões para configurar o KCD com base em recursos. Você não pode configurar o KCD com base em recursos para uma conta de computador no contêiner 'AAD DC Computers' interno ou para contas de usuário no contêiner 'AAD DC Users' interno. Portanto, você não pode usar contas de usuário sincronizadas do Azure AD para configurar o KCD com base em recursos.
>

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](create-instance.md)
* [Visão geral da Delegação Restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
