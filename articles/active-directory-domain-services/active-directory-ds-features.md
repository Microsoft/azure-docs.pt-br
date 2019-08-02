---
title: 'Azure Active Directory Domain Services: Recursos | Microsoft Docs'
description: Recursos dos Serviços de Domínio do Active Directory do Azure
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: e0f38ed8367f076c9f9c4da77440bb3e9603d514
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234205"
---
# <a name="features-provided-by-azure-active-directory-domain-services"></a>Recursos fornecidos pelo Azure Active Directory Domain Services

Os seguintes recursos estão disponíveis nos domínios gerenciados dos Serviços de Domínio do Azure AD.

* **Experiência de implantação simples:** com apenas alguns cliques, você pode habilitar o Azure AD Domain Services para seu diretório do Azure AD. Seu domínio gerenciado inclui contas de usuário somente em nuvem e contas de usuário sincronizadas de um diretório local.
* **Suporte para ingresso no domínio:** você pode facilmente ingressar computadores no domínio na rede virtual do Azure em que seu domínio gerenciado está disponível. A experiência de ingresso em sistemas operacionais cliente e servidor do Windows funciona perfeitamente nos domínios atendidos pelos Serviços de Domínio do Azure AD. Você também pode usar as ferramentas do ingresso no domínio automatizado com relação a esses domínios.
* **Instância de um domínio por diretório do Azure AD:** você pode criar um único domínio do Active Directory para cada diretório do Azure AD.
* **Crie domínios com nomes personalizados:** você pode criar domínios com nomes personalizados (por exemplo, 'contoso100.com') usando o Azure AD Domain Services. Você pode usar qualquer nome de domínio verificado ou não. Opcionalmente, você também pode criar um domínio com o sufixo de domínio interno (ou seja, * .onmicrosoft.com) oferecido pelo seu diretório do Azure AD.
* **Integrado ao Azure AD:** não é necessário configurar nem gerenciar a replicação para o Azure AD Domain Services. As contas de usuário, as associações de grupo e as credenciais de usuário (senhas) do seu diretório do Azure AD estão automaticamente disponíveis nos Azure AD Domain Services. Os novos usuários, os grupos ou as alterações a atributos em seu locatário do Azure AD ou em seu diretório local são automaticamente sincronizados com os Azure AD Domain Services.
* **Autenticação Kerberos e NTLM:** com suporte para autenticação NTLM e Kerberos, é possível implantar aplicativos que usam a Autenticação Integrada do Windows.
* **Use suas senhas/credenciais corporativas:** as senhas de usuários em seu locatário do Azure AD funcionam com o Azure AD Domain Services. Os usuários podem usar suas credenciais corporativas para computadores de ingresso no domínio, entrar interativamente ou pela área de trabalho remota e autenticar no domínio gerenciado.
* **Associação LDAP e suporte à leitura de LDAP:** você pode usar aplicativos que dependem de associações LDAP para autenticar usuários em domínios atendidos pelos Azure AD Domain Services. Além disso, os aplicativos que usam operações de leitura LDAP para consultar atributos de usuário/computador a partir do diretório também podem funcionar nos Serviços de Domínio do AD do Azure.
* **LDAPS (LDAP Seguro):** você pode habilitar o acesso ao diretório sobre LDAPS (LDAP Seguro). Acesso por LDAP seguro está disponível na rede virtual por padrão. No entanto, você também pode habilitar o acesso por LDAP seguro pela Internet.
* **Política de grupo:** você pode usar um único GPO para cada um dos usuários e contêineres de computadores a fim de impor a conformidade com políticas de segurança necessárias para contas de usuário e para computadores ingressados no domínio. Você também pode criar seus próprios GPOs personalizados e atribuí-los às unidades organizacionais personalizadas para [gerenciar a política de grupo](manage-group-policy.md).
* **Gerencie DNS:** membros do grupo 'Administradores do controlador de domínio do AAD' podem gerenciar o DNS para seu domínio gerenciado usando ferramentas de administração do DNS familiares, como o snap-in do MMC de administração do DNS.
* **Crie UOs (unidades organizacionais) personalizadas:** os membros do grupo "Administradores de controlador de domínio do AAD" podem criar UOs personalizadas no domínio gerenciado. Esses usuários recebem privilégios administrativos totais sobre UOs personalizadas, de modo que podem adicionar ou remover contas de serviço, computadores, grupos etc. dessas OUs personalizadas.
* **Disponível em muitas regiões globais do Azure:** Confira a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do Azure AD estão disponíveis.
* **Alta disponibilidade:** o Azure AD Domain Services oferece alta disponibilidade para seu domínio. Esse recurso oferece a garantia de maior tempo de atividade de serviço e resiliência a falhas. O monitoramento da integridade interna oferece a correção automatizada de falhas gerando novas instâncias para substituir instâncias com falha e fornecer um serviço contínuo para seu domínio.
* **Proteção de bloqueio de conta do AD:** as contas de usuário são bloqueadas por 30 minutos quando cinco senhas inválidas são inseridas em um período de dois minutos. As contas são desbloqueadas automaticamente depois de 30 minutos.
* **Use ferramentas de gerenciamento conhecidas:** você pode usar ferramentas de gerenciamento conhecidas do Active Directory do Windows Server, como o Centro Administrativo do Active Directory ou o Active Directory PowerShell, para administrar domínios gerenciados.
