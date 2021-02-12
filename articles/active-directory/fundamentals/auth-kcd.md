---
title: Delegação restrita de Kerberos com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de delegação restrita de Kerberos com Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c7aa4d2300a6dee44da067b122fc7af97f7aa9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172849"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Autenticação do Windows-delegação restrita de Kerberos com Azure Active Directory

A delegação restrita de Kerberos (KCD) fornece delegação restrita entre recursos e é baseada em nomes de entidade de serviço. Ele exige que os administradores de domínio criem as delegações e sejam limitados a um único domínio. A KCD baseada em recursos é geralmente usada como uma maneira de fornecer autenticação Kerberos para um aplicativo Web que tenha usuários em vários domínios dentro de uma floresta Active Directory.

Proxy de Aplicativo do Azure Active Directory pode fornecer SSO (logon único) e acesso remoto a aplicativos baseados em KCD que exigem um tíquete Kerberos para acesso e KCD (delegação restrita de Kerberos).

Habilite o SSO para seus aplicativos KCD locais que usam a IWA (autenticação integrada do Windows) fornecendo permissão aos conectores de proxy de aplicativo para representar usuários no Active Directory. O conector de proxy de aplicativo usa essa permissão para enviar e receber tokens em nome dos usuários.

## <a name="use-when"></a>Usar quando

Há a necessidade de fornecer acesso remoto, proteger com pré-autenticação e fornecer SSO para aplicativos IWA locais.

![Diagrama de arquitetura](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Usuário**: acessa o aplicativo herdado servido pelo proxy de aplicativo.

* **Navegador da Web**: o componente com o qual o usuário interage para acessar a URL externa do aplicativo.

* **AD do Azure**: autentica o usuário. 

* **Serviço de proxy de aplicativo**: atua como proxy reverso para enviar solicitação do usuário para o aplicativo local. Ele fica no Azure AD. O proxy de aplicativo também pode impor qualquer política de acesso condicional.

* **Conector de proxy de aplicativo**: instalado no local em servidores Windows para fornecer conectividade ao aplicativo. Retorna a resposta ao Azure AD. Executa a negociação KCD com Active Directory, representando o usuário para obter um token Kerberos para o aplicativo.

* **Active Directory**: envia o token Kerberos para o aplicativo para o conector de proxy de aplicativo.

* **Aplicativos herdados**: aplicativos que recebem solicitações de usuário do proxy de aplicativo. Os aplicativos herdados retornam a resposta para o conector de proxy de aplicativo.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Implementar a autenticação do Windows (KCD) com o Azure AD

* [Delegação restrita de Kerberos para logon único em seus aplicativos com o Proxy de Aplicativo](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) 

* [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md)

