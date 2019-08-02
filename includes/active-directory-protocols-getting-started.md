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
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172095"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrar seu aplicativo no seu locatário do AD
Primeiro, você precisa registrar seu aplicativo em seu locatário do Azure Active Directory (Microsoft Azure Active Directory). Isso dará a você uma ID do Aplicativo para seu aplicativo e permitirá que ele receba tokens.

* Entre no [Portal do Azure](https://portal.azure.com).
* Escolha seu locatário do Microsoft Azure Active Directory clicando em sua conta no canto superior direito da página, seguido de clicar na **Mudar Diretório** navegação e, em seguida, selecione o locatário apropriada. 
  * Ignore esta etapa, se você tiver apenas um locatário do Microsoft Azure Active Directory em sua conta ou se você já selecionou apropriado locatário do Azure AD.
* No painel de navegação à esquerda, clique em **Azure Active Directory**.
* Clique em **registros de aplicativo** e clique em **novo registro**.
* Siga os avisos e crie um novo aplicativo. Não importa se ele for um aplicativo web ou um aplicativo de cliente público (dispositivos móveis e da área de trabalho) para este tutorial, mas se você quiser exemplos específicos para aplicativos web ou aplicativos de cliente público, Confira nossos [guias de início rápido](../articles/active-directory/develop/v1-overview.md).
  * **Nome** é o nome do aplicativo e descreve o seu aplicativo aos usuários finais.
  * Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
  * Forneça a **URI de redirecionamento**. Para aplicativos Web, isso é a URL base do seu aplicativo em que os usuários possam entrar.  Por exemplo: `http://localhost:12345`. Para um cliente público (dispositivos móvel e da área de trabalho), o Azure AD o usa para retornar respostas de token. Insira um valor específico para seu aplicativo.  Por exemplo: `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* Depois de concluir o registro, o Azure AD atribuirá seu aplicativo um identificador de cliente exclusivo (o **ID do aplicativo**). Você precisará desse valor nas próximas seções, então copie-o da página do aplicativo.
* Para localizar seu aplicativo no portal do Azure, clique em **Registros do aplicativo** e, em seguida, clique em **Exibir todos os aplicativos**.
