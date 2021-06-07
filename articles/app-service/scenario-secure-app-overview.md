---
title: Tutorial – Criar um aplicativo Web seguro no Serviço de Aplicativo do Azure | Azure
description: Neste tutorial, você aprenderá a criar um aplicativo Web usando o Serviço de Aplicativo do Azure, a habilitar a autenticação, a chamar o armazenamento do Azure e a chamar o Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 5b94000ce59b3a115e53ecdcd0849b97aae552c5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221833"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Tutorial: Habilitar a autenticação no Serviço de Aplicativo, no armazenamento de acesso e no Microsoft Graph

Este tutorial descreve um cenário de aplicativo comum em que você aprenderá a:

- [Configurar a autenticação para um aplicativo Web](scenario-secure-app-authentication-app-service.md) e limitar o acesso para usuários em sua organização. Confira A no diagrama.
- [Acessar com segurança o Armazenamento do Azure](scenario-secure-app-access-storage.md) para o aplicativo Web usando identidades gerenciadas. Confira B no diagrama.
- Acessar dados no Microsoft Graph [para o usuário conectado](scenario-secure-app-access-microsoft-graph-as-user.md) ou [para o aplicativo Web](scenario-secure-app-access-microsoft-graph-as-app.md) usando identidades gerenciadas. Confira C no diagrama.
- [Limpe os recursos](scenario-secure-app-clean-up-resources.md) que você criou para esse tutorial.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Diagrama que mostra cenários de aplicativo na plataforma de identidade da Microsoft." border="false":::

Para começar, saiba como habilitar a autenticação para um aplicativo Web.

> [!div class="nextstepaction"]
> [Configurar a autenticação para um aplicativo Web](scenario-secure-app-authentication-app-service.md)
