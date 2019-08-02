---
title: Acessar aplicativos de Proxy de Aplicativo Azure AD no Teams | Microsoft Docs
description: Use o Proxy de Aplicativo Azure AD para acessar seu aplicativo local por meio do Microsoft Teams.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037e005993a54e525560571a6d893197af99b6a0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807770"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Acessar seus aplicativos locais por meio do Microsoft Teams

O Proxy de Aplicativo do Azure Active Directory fornece logon único para aplicativos locais independentemente de onde você está. O Microsoft Teams simplifica seus esforços de colaboração em um único local. Integrar os dois significa que os usuários podem ser produtivos com seus colegas de equipe em qualquer situação.

Os usuários podem adicionar aplicativos de nuvem aos seus canais do Teams [usando guias](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), mas e os sites do SharePoint ou a ferramenta de planejamento que é hospedada localmente? O Proxy de Aplicativo é a solução. Eles podem adicionar aplicativos publicados por meio do Proxy de Aplicativo para seus canais usando as mesmas URLs externas que eles sempre usam para acessar os próprios aplicativos remotamente. E já que o Proxy de Aplicativo é autenticado pelo Azure Active Directory, seus usuários obtêm a mesma experiência de logon único.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instalar o conector de Proxy de Aplicativo e publicar o aplicativo

Se você ainda não o fez, [configure o Proxy de Aplicativo para seu locatário e instale o conector](application-proxy-add-on-premises-application.md). Em seguida, [publique seu aplicativo local](application-proxy-add-on-premises-application.md) para acesso remoto. Quando você estiver publicando o aplicativo, anote a URL externa porque ela é usada para adicionar o aplicativo ao Teams.

Se você já tem seus aplicativos publicados mas não se lembra de suas URLs externas, consulte-os no [portal do Azure](https://portal.azure.com). Entre, navegue até **Azure Active Directory** > **Aplicativos empresariais** > **Todos os aplicativos** > selecione seu aplicativo > **Proxy de aplicativo**.

## <a name="add-your-app-to-teams"></a>Adicionar seu aplicativo ao Teams

Depois de publicar o aplicativo pelo Proxy de Aplicativo, informe aos usuários que eles podem adicioná-lo como uma guia diretamente em seus canais do Teams e, em seguida, o aplicativo estará disponível para todos da equipe usarem. Faça com que eles sigam estas três etapas:

1. Navegue até o canal do Teams em que você deseja adicionar este aplicativo e selecione **+** para adicionar uma guia.

   ![Selecione + para adicionar uma guia no Teams](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Selecione **Site** entre as opções da guia.

   ![Selecione o site de adicionar uma tela de guia](./media/application-proxy-integrate-with-teams/website.png)

1. Nomeie a guia e defina a URL para a URL externa do Proxy de Aplicativo.

   ![Nomeie a guia e adicionar a URL externa](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Depois que um membro de uma equipe adiciona a guia, ela aparece para todos no canal. Quaisquer usuários que têm acesso ao aplicativo obtêm acesso de logon único com as credenciais que usam para o Microsoft Teams. Quaisquer usuários que não tenham acesso ao aplicativo verão a guia no Teams, mas serão bloqueados até que você conceda a permissão para o aplicativo local e a versão do aplicativo publicada no Portal do Azure.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [publicar sites do SharePoint local](application-proxy-integrate-with-sharepoint-server.md) com o Proxy de Aplicativo.
- Configure seus aplicativos para usar [domínios personalizados](application-proxy-configure-custom-domain.md) para a URL externa deles.
