---
title: Exemplos do PowerShell para o Proxy de Aplicativo do Azure AD
description: Use estes exemplos do PowerShell para o Proxy de Aplicativo do Azure AD para obter informações sobre os aplicativos e conectores do Proxy de Aplicativo em seu diretório, atribuir usuários e grupos a aplicativos e obter informações de certificado.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5189df623325578bb3b579f5c4c3456067986ff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709473"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Exemplos do PowerShell do Azure AD para o Proxy de Aplicativo do Azure AD

A tabela a seguir inclui links para exemplos de scripts do PowerShell para o Proxy de Aplicativo do Azure AD. Esses exemplos exigem o [Módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2) ou a [Versão prévia do módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), salvo indicação em contrário.


Para obter mais informações sobre os cmdlets usados nesses exemplos, confira [Gerenciamento de aplicativos do Proxy de Aplicativo](/powershell/module/azuread/#application_proxy_application_management) e [Gerenciamento de conectores do Proxy de Aplicativo](/powershell/module/azuread/#application_proxy_connector_management).

| Link | Descrição |
|---|---|
|**Aplicativos do Proxy de Aplicativo**||
| [Listar informações básicas para todos os aplicativos do Proxy de Aplicativo](scripts/powershell-get-all-app-proxy-apps-basic.md) | Lista informações básicas (AppId, DisplayName, ObjId) sobre todos os aplicativos do Proxy de Aplicativo no seu diretório. |
| [Listar informações estendidas para todos os aplicativos do Proxy de Aplicativo](scripts/powershell-get-all-app-proxy-apps-extended.md) | Lista informações estendidas (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) sobre todos os aplicativos do Proxy de Aplicativo em seu diretório.  |
| [Listar todos os aplicativos do Proxy de Aplicativo por grupo de conectores](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Lista informações sobre todos os aplicativos do Proxy de Aplicativo em seu diretório e a quais grupos de conectores os aplicativos estão atribuídos. |
| [Obter todos os aplicativos do Proxy de Aplicativo com uma política de tempo de vida de token](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Lista todos os aplicativos do Proxy de Aplicativo em seu diretório com uma política de tempo de vida de token, bem como seus detalhes. Este exemplo requer a [Versão prévia do módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true). |
|**Grupos de conectores**||
| [Obter todos os grupos de conectores e conectores no diretório](scripts/powershell-get-all-connectors.md) | Lista todos os grupos de conectores e conectores em seu diretório. |
| [Mover todos os aplicativos atribuídos a um grupo de conectores para outro grupo de conectores](scripts/powershell-move-all-apps-to-connector-group.md) | Move todos os aplicativos atribuídos atualmente a um grupo de conectores para outro grupo de conectores. |
|**Usuários e grupos atribuídos**||
| [Exibir usuários e grupos atribuídos a um aplicativo do Proxy de Aplicativo](scripts/powershell-display-users-group-of-app.md) | Lista os usuários e grupos atribuídos a um aplicativo específico do Proxy de Aplicativo. |
| [Atribuir um usuário a um aplicativo](scripts/powershell-assign-user-to-app.md) | Atribui um usuário específico a um aplicativo. |
| [Atribuir um grupo a um aplicativo](scripts/powershell-assign-group-to-app.md) | Atribui um grupo específico a um aplicativo. |
|**Configuração de URL externa**||
| [Obter todos os aplicativos do Proxy de Aplicativo usando domínios padrão (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Lista todos os aplicativos do Proxy de Aplicativo usando domínios padrão (.msappproxy.net). |
| [Obter todos os aplicativos do Proxy de Aplicativo usando a publicação curinga](scripts/powershell-get-all-wildcard-apps.md) | Lista todos os aplicativos do Proxy de Aplicativo usando a publicação curinga. |
|**Configuração de domínio personalizado**||
| [Obter todos os aplicativos do Proxy de Aplicativo usando informações de certificado e domínios personalizados](scripts/powershell-get-all-custom-domains-and-certs.md) | Lista todos os aplicativos do Proxy de Aplicativo que estão usando domínios personalizados e informações de certificado associadas a eles. |
| [Obter todos os aplicativos do Proxy de Aplicativo do Azure AD publicados sem nenhum certificado carregado](scripts/powershell-get-all-custom-domain-no-cert.md) | Lista todos os aplicativos do Proxy de Aplicativo que estão usando domínios personalizados, mas que não têm um certificado TLS/SSL válido carregado. |
| [Obter todos os aplicativos do Proxy de Aplicativo do Azure AD publicados com o certificado idêntico](scripts/powershell-get-custom-domain-identical-cert.md) | Lista todos os aplicativos do Proxy de Aplicativo do Azure AD publicados com o certificado idêntico. |
| [Obter todos os aplicativos do Proxy do Azure AD publicados com o certificado idêntico e substituí-los](scripts/powershell-get-custom-domain-replace-cert.md) | Para aplicativos do Proxy do Azure AD publicados com um certificado idêntico, permite substituir o certificado em massa. |
