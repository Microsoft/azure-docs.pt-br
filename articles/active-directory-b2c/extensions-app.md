---
title: Aplicativo de extensões no Azure Active Directory B2C | Microsoft Docs
description: Restaurando o b2c-extensions-app.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc536fa4292d794e8d89a2564ad10a3c10dd0a3d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560846"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: aplicativo de extensões

Quando um diretório do Azure AD B2C é criado, um aplicativo chamado `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` é criado automaticamente no novo diretório. Esse aplicativo, conhecido como **b2c-extensions-app** , está visível em *Registros de aplicativo*. Ele é usado pelo serviço Azure AD B2C para armazenar informações sobre usuários e atributos personalizados. Se o aplicativo for excluído, o Azure AD B2C não funcionará corretamente e seu ambiente de produção será afetado.

> [!IMPORTANT]
> Não exclua o b2c-extensions-app, a menos que esteja planejando excluir seu locatário imediatamente. Se o aplicativo permanecer excluído por mais de 30 dias, as informações do usuário serão permanentemente perdidas.

## <a name="verifying-that-the-extensions-app-is-present"></a>Como verificar se o aplicativo de extensões está presente

Para verificar se o b2c-extensions-app está presente:

1. No locatário Azure AD B2C, clique em **Todos os serviços** no menu de navegação esquerdo.
1. Procure e abra **Registros de aplicativo**.
1. Procure um aplicativo que comece com **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Recuperar o aplicativo de extensões

Se você excluiu acidentalmente o b2c-extensions-app, é possível recuperá-lo em até 30 dias. Você pode restaurar o aplicativo usando a API do Graph:

1. Navegue até [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Faça logon no site como um administrador global do diretório Azure AD B2C para o qual você quer restaurar o aplicativo excluído. Esse administrador global deve ter um endereço de email semelhante ao seguinte: `username@{yourTenant}.onmicrosoft.com`.
1. Emita um HTTP GET em relação à URL `https://graph.windows.net/myorganization/deletedApplications` com api-version=1.6. Essa operação listará todos os aplicativos que foram excluídos nos últimos 30 dias.
1. Localize o aplicativo na lista em que o nome começa com ' B2C-Extensions-app ' e copie seu `objectid` valor de propriedade.
1. Emita um HTTP POST em relação à URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Substitua a parte `{OBJECTID}` da URL por `objectid` da etapa anterior.

Agora, deve ser possível [ver o aplicativo restaurado](#verifying-that-the-extensions-app-is-present) no portal do Azure.

> [!NOTE]
> Um aplicativo só poderá ser restaurado se ele tiver sido excluído nos últimos 30 dias. Se tiverem se passado mais de 30 dias, os dados serão permanentemente perdidos. Para obter mais ajuda, abra um tíquete de suporte.
