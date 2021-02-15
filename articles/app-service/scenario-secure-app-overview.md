---
title: Tutorial – Criar um aplicativo Web seguro no Serviço de Aplicativo do Azure | Azure
description: Neste tutorial, você aprenderá a criar um aplicativo Web usando o Serviço de Aplicativo do Azure, a habilitar a autenticação, a chamar o armazenamento do Azure e a chamar o Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 17931ee92072059d94cd950e9ce8ef6588f7e891
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905495"
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
