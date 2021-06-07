---
title: Configurações de cookie do Proxy de Aplicativo – Azure Active Directory | Microsoft Docs
description: O Azure AD (Azure Active Directory) tem cookies de sessão e de acesso para acessar aplicativos locais por meio do Proxy de Aplicativo. Neste artigo, você descobrirá como usar e definir as configurações de cookie.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62afe97b44f45bc0b7aa12b33b6a65dd94ecf095
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99252195"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Configurações de cookie para acessar aplicativos locais no Azure Active Directory

O Azure AD (Azure Active Directory) tem cookies de sessão e de acesso para acessar aplicativos locais por meio do Proxy de Aplicativo. Saiba como usar as configurações de cookie do Proxy de Aplicativo. 

## <a name="what-are-the-cookie-settings"></a>O que são as configurações de cookie?

O [Proxy de Aplicativo](application-proxy.md) utiliza as configurações de cookie de sessão e de acesso a seguir.

| Configuração de cookies | Padrão | Descrição | Recomendações |
| -------------- | ------- | ----------- | --------------- |
| Usar Cookie Somente HTTP | **Não** | **Sim** permite que o Proxy de Aplicativo inclua o sinalizador HTTPOnly nos cabeçalhos de resposta HTTP. Esse sinalizador oferece benefícios de segurança adicionais, por exemplo, impede que o script CSS (do lado do cliente) copie ou modifique os cookies.<br></br><br></br>Antes que tenhamos suporte a configuração de HTTP-Only, o proxy de aplicativo criptografou e transmitiu cookies em um canal TLS protegido para proteção contra modificação. | Use **Sim** para ter os benefícios de segurança adicionais.<br></br><br></br>Use **Não** para clientes ou agentes de usuário que exijam acesso ao cookie de sessão. Por exemplo, use **Não** para um cliente RDP ou MTSC que se conecta a um servidor de Gateway de Área de Trabalho Remota por meio do Proxy de Aplicativo.|
| Usar um Cookie Seguro | **Não** | **Sim** permite que o Proxy de Aplicativo inclua o sinalizador Secure nos cabeçalhos de resposta HTTP. Os cookies seguros melhoram a segurança ao transmitir cookies em um canal TLS seguro, como HTTPS. Isso impede que os cookies sejam observados por partes não autorizadas devido à transmissão do cookie em texto não criptografado. | Use **Sim** para ter os benefícios de segurança adicionais.|
| Usar cookie persistente | **Não** | **Sim** permite que o Proxy de Aplicativo defina seus cookies de acesso para não expirarem quando o navegador da Web for fechado. A persistência dura até que o token de acesso expire ou até que o usuário exclua manualmente os cookies persistentes. | Use **Não** por causa do risco de segurança associado à autenticação contínua dos usuários.<br></br><br></br>Sugerimos usar **Sim** apenas para aplicativos mais antigos que não podem compartilhar cookies entre processos. É melhor atualizar seu aplicativo para lidar com o compartilhamento de cookies entre processos em vez de usar cookies persistentes. Por exemplo, você pode precisar de cookies persistentes para permitir que um usuário abra documentos do Office no modo de exibição de gerenciador em um site do SharePoint. Sem cookies persistentes, essa operação poderá falhar se os cookies de acesso não forem compartilhados entre o navegador, o processo do gerenciador e o processo do Office. |

## <a name="samesite-cookies"></a>SameSite cookies
A partir da versão Chrome 80 e, eventualmente, em navegadores que aproveitam Chromium, os cookies que não especificam o atributo [SameSite](https://web.dev/samesite-cookies-explained) serão tratados como se estivessem definidos como **SameSite = LAX**. O atributo SameSite declara como os cookies devem ser restritos a um contexto de mesmo site. Quando definido como LAX, o cookie é enviado apenas para solicitações de mesmo site ou navegação de nível superior. No entanto, o proxy de aplicativo exige que esses cookies sejam preservados no contexto de terceiros para manter os usuários conectados corretamente durante a sessão. Devido a isso, estamos fazendo atualizações para o acesso ao proxy de aplicativo e cookies de sessão para evitar o impacto adverso dessa alteração. As atualizações incluem:

* Definindo o atributo **SameSite** como **None**. Isso permite que os cookies de acesso e sessões do proxy de aplicativo sejam enviados corretamente no contexto de terceiros.
* Definir a configuração **usar cookie seguro** para usar **Sim** como o padrão. O Chrome também exige que os cookies especifiquem o sinalizador de segurança ou que serão rejeitados. Essa alteração será aplicada a todos os aplicativos existentes publicados por meio do proxy de aplicativo. Observe que os cookies de acesso ao proxy de aplicativo sempre foram definidos como seguros e transmitidos somente por HTTPS. Essa alteração será aplicada somente aos cookies de sessão.

Essas alterações nos cookies de proxy de aplicativo serão implantadas ao longo das próximas semanas antes da data de lançamento do Chrome 80.

Além disso, se o aplicativo de back-end tiver cookies que precisam estar disponíveis em um contexto de terceiros, você deverá optar explicitamente por alterar seu aplicativo para usar SameSite = None para esses cookies. O proxy de aplicativo traduz o cabeçalho Set-Cookie para suas URLS e respeitará as configurações para esses cookies definidos pelo aplicativo de back-end.



## <a name="set-the-cookie-settings---azure-portal"></a>Definir as configurações de cookie – portal do Azure
Para definir as configurações de cookie usando o portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Navegue até **Azure Active Directory**   >  **aplicativos empresariais**   >  **todos os aplicativos**.
3. Selecione o aplicativo para o qual você deseja habilitar a configuração de um cookie.
4. Clique em **proxy de aplicativo**.
5. Em **Configurações Adicionais**, defina a configuração do cookie como **Sim** ou **Não**.
6. Clique em **Salvar** para aplicar suas alterações. 

## <a name="view-current-cookie-settings---powershell"></a>Exibir configurações de cookie atuais-PowerShell

Para ver as configurações de cookie atuais para o aplicativo, use este comando do PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Definir configurações de cookie-PowerShell

Nos comandos do PowerShell a seguir, ```<ObjectId>``` é o ObjectID do aplicativo. 

**Cookie somente http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Cookie seguro**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Cookies persistentes**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
