---
title: Sincronização LDAP com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de sincronização LDAP com Azure Active Directory.
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
ms.openlocfilehash: 1f34e734b315c7c05ce77f5e168a452fc1c1c547
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168671"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Sincronização LDAP com Azure Active Directory

O Lightweight Directory Access Protocol (LDAP) é um protocolo de serviço de diretório executado na pilha TCP/IP. Ele fornece um mecanismo usado para se conectar, Pesquisar e modificar diretórios da Internet. O serviço de diretório LDAP é baseado em um modelo cliente-servidor e sua função é habilitar o acesso a um diretório existente. Muitas empresas dependem de servidores LDAP locais para armazenar usuários e grupos para seus aplicativos de negócios críticos. 

Azure Active Directory (AD do Azure) pode substituir a sincronização LDAP por Azure AD Connect. O serviço de sincronização de Azure AD Connect executa todas as operações relacionadas à sincronização de dados de identidade entre seus ambientes locais e o Azure AD. 

## <a name="use-when"></a>Usar quando

Você precisa sincronizar dados de identidade entre seus diretórios LDAP v3 locais e o Azure AD. 

![diagrama arquitetônico](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Usuário**: acessa um aplicativo que depende do uso de um diretório LDAP V3 para classificar usuários e senhas.

* **Navegador da Web**: o componente com o qual o usuário interage para acessar a URL externa do aplicativo

* **Aplicativo Web**: aplicativo com dependências em diretórios LDAP v3.

* **Azure ad**: o Azure ad sincroniza informações de identidade (usuários, grupos, senhas) dos diretórios LDAP locais da organização por meio de Azure ad Connect. 

* **Azure ad Connect**: é uma ferramenta para se conectar a infraestruturas de identidade locais para Microsoft Azure AD. O assistente e as experiências guiadas ajudam a implantar e configurar pré-requisitos e componentes necessários para a conexão. 

* **Conector personalizado**: um conector LDAP genérico permite que você integre o serviço de sincronização Azure ad Connect com um servidor LDAP v3. Ele fica em Azure AD Connect.

* **Active Directory**: Active Directory é um serviço de diretório incluído na maioria dos sistemas operacionais Windows Server. Os servidores que executam Active Directory serviços de diretório são chamados de controladores de domínio e autenticam e autorizam todos os usuários e computadores em um domínio do Windows.

* **Servidor LDAP v3**: diretório compatível com protocolo LDAP que armazena usuários corporativos e senhas usados para autenticação de serviços de diretório.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Implementar a sincronização LDAP com o Azure AD

* [Ferramentas de integração de diretório de identidade híbrida](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Roteiro de instalação do Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md) 

* [Visão geral e criação de um conector LDAP](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > Os conectores LDAP são uma configuração avançada que requer alguma familiaridade com o Forefront Identity Manager e/ou Microsoft Identity Manager. Se usado na produção, aconselhamos que as perguntas sobre essa configuração devem passar por [suporte Premier](https://support.microsoft.com/premier) ou Microsoft Partner Network.

