---
title: Registrar um aplicativo com o ponto de extremidade do Azure AD v2.0 usando o portal | Microsoft Docs
description: "Como registrar um aplicativo na Microsoft para habilitar a entrada e acessar os serviços da Microsoft usando o ponto de extremidade v2.0"
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: 
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: e6202aa8665c906382666fe08a561421e50e0a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Como registrar um aplicativo com o ponto de extremidade v2.0
Para criar um aplicativo que aceite entrada do AD do Azure e do MSA, você primeiro precisará registrar um aplicativo com a Microsoft.  Você não poderá usar nenhum dos seus aplicativos existentes com o Azure AD ou MSA, será necessário criar um novo.

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0.  Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="visit-the-microsoft-app-registration-portal"></a>Visite o Portal de Registro de Aplicativos da Microsoft
Primeiro as prioridades, navegue até [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Este é o novo portal de registro de aplicativos em que você pode gerenciar todos os seus aplicativos Microsoft.

Entrar com uma conta da Microsoft pessoal, profissional ou escolar.  Se você não tiver uma, inscreva-se para uma nova conta pessoal. Vá em frente, não vai demorar muito. Vamos aguardar aqui.

Pronto? Você deve agora estar olhando a lista de aplicativos da Microsoft, que provavelmente está vazia.  Vamos mudar isso.

Clique em **Adicionar um aplicativo**e dê um nome a ele.  O portal atribuirá ao seu aplicativo uma Id de aplicativo globalmente exclusiva que você usará posteriormente em seu código.  Caso seu aplicativo inclua um componente do servidor que precisa de tokens de acesso para chamar APIs (tais como: Office, Azure ou sua própria API Web), também convém criar um **Segredo de Aplicativo** aqui.

Em seguida, adicione as Plataformas que seu aplicativo usará.

* Para aplicativos baseados na Web, forneça um **URI de Redirecionamento** em que é possível enviar mensagens de entrada.
* Para aplicativos móveis, copie o URI de redirecionamento criado automaticamente para você.

Opcionalmente, você pode personalizar a aparência de sua página de entrada na Seção do perfil.  Certifique-se de clicar em **Salvar** antes de continuar.

> [!NOTE]
> Quando você cria um aplicativo usando [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ele será registrado no locatário inicial da conta que você usa para entrar no portal.  Isso significa que não é possível registrar um aplicativo no seu locatário do Azure AD usando uma conta pessoal da Microsoft.  Se você quiser registrar um aplicativo em um determinado locatário explicitamente, entre com uma conta criada originalmente no locatário em questão.
> 
> 

## <a name="build-a-quick-start-app"></a>Compilar um aplicativo de início rápido
Agora que você tem um aplicativo da Microsoft, poderá concluir um dos nossos tutoriais de início rápido da v2.0.  Aqui estão algumas recomendações:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

