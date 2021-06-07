---
title: Autenticação baseada em cabeçalho com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de autenticação baseada em cabeçalho com o Azure Active Directory.
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
ms.openlocfilehash: 69e87fc919893a544f5d0b1b615a110f25486e57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168739"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Autenticação baseada em cabeçalho com Azure Active Directory

Aplicativos herdados normalmente usam autenticação baseada em cabeçalho. Nesse cenário, um usuário (ou originador de mensagem) é autenticado em uma solução de identidade intermediária. A solução intermediária autentica o usuário e propaga os cabeçalhos HTTP (Hypertext Transfer Protocol) necessários para o serviço Web de destino. O Azure Active Directory (AD) dá suporte a esse padrão por meio de seu serviço de proxy de aplicativo e integra-se a outras soluções de controlador de rede. 

Em nossa solução, o proxy de aplicativo fornece acesso remoto ao aplicativo, autentica o usuário e passa os cabeçalhos exigidos pelo aplicativo. 

## <a name="use-when"></a>Usar quando

Os usuários remotos precisam de logon único (SSO) com segurança para aplicativos locais que exigem autenticação baseada em cabeçalho.

![Autenticação baseada em cabeçalho de imagem de arquitetura](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Usuário**: acessa aplicativos herdados servidos pelo proxy de aplicativo.

* **Navegador da Web**: o componente com o qual o usuário interage para acessar a URL externa do aplicativo.

* **AD do Azure**: autentica o usuário. 

* **Serviço de proxy de aplicativo**: atua como proxy reverso para enviar solicitação do usuário para o aplicativo local. Ele reside no Azure AD e também pode impor qualquer política de acesso condicional.

* **Conector de proxy de aplicativo**: instalado no local em servidores Windows para fornecer conectividade aos aplicativos. Ele usa apenas conexões de saída. Retorna a resposta ao Azure AD.

* **Aplicativos herdados**: aplicativos que recebem solicitações de usuário do proxy de aplicativo. O aplicativo herdado recebe os cabeçalhos HTTP necessários para configurar uma sessão e retornar uma resposta. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Implementar a autenticação baseada em cabeçalho com o Azure AD

* [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure AD](../manage-apps/application-proxy-add-on-premises-application.md)  

* [Autenticação baseada em cabeçalho para logon único com Proxy de Aplicativo e PingAccess](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) 

* [Proteger aplicativos herdados com redes e controladores de entrega de aplicativos](../manage-apps/secure-hybrid-access.md)