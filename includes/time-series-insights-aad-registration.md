---
title: incluir arquivo
description: incluir arquivo
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 0ce9575f078058c821ffffe1b9fe45eed5a4ad94
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724159"
---
* Depois de selecionar a plataforma apropriada na etapa 4 de definir configurações de [plataforma](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#configure-platform-settings) , configure seus **URIs de redirecionamento** e **tokens de acesso** no painel lateral à direita da interface do usuário.

    * Os **URIs de Redirecionamento** devem corresponder ao endereço fornecido pela solicitação de autenticação:

        * Para aplicativos hospedados em um ambiente de desenvolvimento local, selecione **Cliente público (móvel e desktop)** . Lembre-se de definir **cliente público** como **Sim**.
        * Para aplicativos de página única hospedados no Serviço de Aplicativo do Azure, selecione **Web**.

    * Determine se uma **URL de logout** é apropriada.

    * Habilite o fluxo de concessão implícita marcando **Tokens de acesso** ou **Tokens de ID**.

    [![Criar URIs de Redirecionamento](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Clique em **Configurar** e **Salvar**.

* Associe o Azure Time Series Insights de aplicativo Azure Active Directory. Selecione **Permissões de API** > **Adicionar uma permissão** > **APIs usadas por minha organização**.

    [![Associar uma API ao seu aplicativo do Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Digite `Azure Time Series Insights` na barra em seguida e selecione `Azure Time Series Insights`.

* Em seguida, especifique o tipo de permissão de API exigido por seu aplicativo. Por padrão, **Permissões delegadas** estará realçado. Escolha um tipo de permissão e selecione **Adicionar permissões**.

    [![Especifique o tipo de permissão de API exigido por seu aplicativo](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* [Adicione credenciais](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#add-credentials) se o aplicativo for chamar as APIs do seu ambiente como ele mesmo. As credenciais permitem que seu aplicativo se autentique como ele mesmo, sem nenhuma interação com um usuário no runtime.