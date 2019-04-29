---
title: Visão geral do protocolo .NET do Azure AD | Microsoft Docs
description: Como usar mensagens HTTP para autorizar o acesso a aplicativos Web e a APIs da Web em seu locatário usando o Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: d97aa34f8e76a1ea49841f26534b6a5403e6b5d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251630"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrar seu aplicativo no seu locatário do AD
Primeiro, você precisa registrar seu aplicativo em seu locatário do Azure Active Directory (Microsoft Azure Active Directory). Isso dará a você uma ID do Aplicativo para seu aplicativo e permitirá que ele receba tokens.

* Entre no [Portal do Azure](https://portal.azure.com).
* Escolha seu locatário do Microsoft Azure Active Directory clicando em sua conta no canto superior direito da página, seguido de clicar na **Mudar Diretório** navegação e, em seguida, selecione o locatário apropriada. 
  * Ignore esta etapa, se você tiver apenas um locatário do Microsoft Azure Active Directory em sua conta ou se você já selecionou apropriado locatário do Azure AD.
* No painel de navegação à esquerda, clique em **Azure Active Directory**.
* Clique em **Registros do Aplicativo** e depois em **Novo registro do aplicativo**.
* Siga os avisos e crie um novo aplicativo. Para este tutorial, não importa se é um aplicativo Web ou um aplicativo nativo, mas se você quiser exemplos específicos para aplicativos Web ou para aplicativos nativos, confira nossos [guias de início rápido](../articles/active-directory/develop/v1-overview.md).
  * Para os Aplicativos Web, forneça a **URL de Entrada**, que é a URL base do seu aplicativo, onde os usuários podem entrar como `http://localhost:12345`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Para Aplicativos Nativos, forneça um **URI de redirecionamento**, que será usado pelo Azure Active Directory para retornar respostas de token. Insira um valor específico para seu aplicativo, por exemplo, `http://MyFirstAADApp`
* Depois de concluir o registro, o Azure Active Directory atribuirá ao seu aplicativo um identificador de cliente único, a **ID do Aplicativo**. Você precisará desse valor nas próximas seções, então copie-o da página do aplicativo.
* Para localizar seu aplicativo no portal do Azure, clique em **Registros do aplicativo** e, em seguida, clique em **Exibir todos os aplicativos**.
