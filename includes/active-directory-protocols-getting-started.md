---
title: "Visão geral do protocolo .NET do Azure AD | Microsoft Docs"
description: "Como usar mensagens HTTP para autorizar o acesso a aplicativos Web e a APIs da Web em seu locatário usando o Azure AD."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo
ms.openlocfilehash: 42ce8dfd30cda7d4085778954350550fd9fdf13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## Registrar seu aplicativo no seu locatário do AD
Primeiro, você precisará registrar seu aplicativo em seu locatário do Azure AD (Azure Active Directory). Isso dará a você uma ID do Aplicativo para seu aplicativo e permitirá que ele receba tokens.

* Entre no [Portal do Azure](https://portal.azure.com).
* Escolha seu locatário do Azure AD clicando em sua conta no canto superior direito da página.
* No painel de navegação à esquerda, clique em **Azure Active Directory**.
* Clique em **Registros do Aplicativo** e clique em **Adicionar**.
* Siga os avisos e crie um novo aplicativo. Para este tutorial, não importa se é um aplicativo Web ou um aplicativo nativo, mas se você quiser exemplos específicos para aplicativos Web ou para aplicativos nativos, confira nossos [guias de início rápido](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Para os Aplicativos Web, forneça a **URL de Entrada**, que é a URL base do seu aplicativo, onde os usuários podem entrar como `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Para os Aplicativos Nativos, forneça um **URI de redirecionamento**, que será usado pelo Azure AD para retornar respostas de token. Insira um valor específico para seu aplicativo, por exemplo, `http://MyFirstAADApp`
* Depois de concluir o registro, o Azure AD atribuirá a seu aplicativo um identificador de cliente único, a ID do Aplicativo. Você precisará desse valor nas próximas seções, então copie-o da página do aplicativo.
