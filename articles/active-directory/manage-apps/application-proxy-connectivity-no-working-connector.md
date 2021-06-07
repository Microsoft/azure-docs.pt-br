---
title: Nenhum grupo de conectores de trabalho encontrado para um aplicativo de proxy de aplicativo
description: Solucionar problemas que podem ocorrer quando não há nenhum trabalho conector em um grupo de conector para seu aplicativo com o Application Proxy do Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cec028dec2bf2e6295f59aae9c2e243da2209827
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257027"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nenhum grupo do conector de trabalho encontrado para um aplicativo de Application Proxy

Este artigo o ajuda a resolver os problemas mais comuns enfrentados quando não há um conector detectado para um aplicativo de Application Proxy integrado ao Azure Active Directory.

## <a name="overview-of-steps"></a>Visão geral das etapas
Se não houver nenhum conector funcionando em um grupo de conector para o seu aplicativo, haverá algumas maneiras de resolver o problema:

-   Se você tiver não conectores no grupo, você poderá:

    -   Baixar um novo conector à direita no servidor local e atribuí-lo a este grupo

    -   Mover um conector ativo para o grupo

-   Se você não tiver conectores ativos no grupo, você poderá:

    -   Identificar o motivo pelo qual que o conector está inativo e resolver

    -   Mover um conector ativo para o grupo

Para saber qual é o problema, abra o menu "Application Proxy" no seu aplicativo e examine a mensagem de aviso do grupo de conector. Se não houver nenhum conector no grupo, a mensagem de aviso especificará que o grupo precisa de pelo menos um Conector. Se você não tiver nenhum Conector ativo, a mensagem de aviso explicará isso. É comum ter Conectores inativos. 

   ![Seleção de grupo do conector no portal do Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Para obter detalhes sobre cada uma dessas opções, consulte a seção correspondente abaixo. As instruções pressupõem que você está começando na página de gerenciamento de Conector. Se você estiver procurando a mensagem de erro acima, você poderá ir para essa página clicando na mensagem de aviso. Você pode também acessar a página visitando **Azure Active Directory**, clicando em **aplicativos empresariais**, em seguida, **Application Proxy.**

   ![Gerenciamento de grupo do conector no portal do Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Baixar um novo conector

Para baixar um novo conector, use o botão "Baixar conector" na parte superior da página.

Instale o conector em um computador com a linha direta de visão para o aplicativo de back-end. Em geral, o conector está instalado no mesmo servidor que o aplicativo. Após o download, o conector deve aparecer no menu. Clique no conector e use a lista suspensa "Grupo do Conector" para certificar-se de que ele pertence ao grupo direito. Salve a alteração.

   ![Baixe o conector do portal do Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Mova um conector ativo

Se você tiver um conector ativo que deve pertencer ao grupo e tem metas claras para o aplicativo de back-end de destino, você poderá mover o conector para o grupo atribuído. Para fazer isso, clique no Conector. No campo "Conector de grupo", use a lista suspensa para selecionar o grupo correto e clique em Salvar.

## <a name="resolve-an-inactive-connector"></a>Resolver um conector inativo

Se os conectores apenas no grupo estão inativos, eles estão provavelmente em um computador que não tem todas as portas desbloqueadas.

consulte o documento de resolução de problemas das portas para obter detalhes sobre como investigar esse problema.

## <a name="next-steps"></a>Próximas etapas
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md)


