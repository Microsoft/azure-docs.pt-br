---
title: "Conectores de Proxy de aplicativo do portal clássico do Azure AD | Microsoft Docs"
description: Aborda como criar e gerenciar grupos de conectores no Proxy de Aplicativo do Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: b283796a-9679-4c79-b703-802bb850f65d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: fc65c4053c45d9c16c62ee0fe65924133a4bb94a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicativos em redes e locais separados usando grupos de conectores
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portal clássico do Azure](active-directory-application-proxy-connectors.md)
>
>

Os grupos de conectores são úteis para diversos cenários, incluindo:

* Sites com vários datacenters interconectados. Nesses casos, você deseja manter o tráfego dentro do datacenter o máximo possível, porque links entre datacenters são caros e lentos. Você pode implantar conectores em cada datacenter para servir apenas os aplicativos que residem dentro do datacenter. Essa abordagem minimiza os links entre datacenters e fornece uma experiência totalmente transparente para os usuários.
* Gerenciar aplicativos instalados em redes isoladas que não fazem parte da rede corporativa principal. Você pode usar grupos de conectores para instalar conectores dedicados em redes isoladas para isolar também os aplicativos para a rede.
* Para aplicativos instalados no IaaS para acesso à nuvem, os grupos de conector fornecem um serviço comum para proteger o acesso a todos os aplicativos. Os grupos de conectores não criam dependência adicional em sua rede corporativa nem fragmentam a experiência de aplicativo. Conectores podem ser instalados em cada datacenter na nuvem e servir apenas os aplicativos que residem nessa rede. Você pode instalar vários conectores para obter alta disponibilidade.
* Suporte para ambientes de várias florestas em que conectores específicos podem ser implantados por floresta e definidos para servir aplicativos específicos.
* Grupos de Conectores podem ser usados em sites de Recuperação de desastres para detectar failover ou como backup do site principal.
* Grupos de Conectores também podem ser usados para atender a várias empresas de um único locatário.

## <a name="prerequisite-create-your-connectors"></a>Pré-requisito: criar seus conectores
Para agrupar seus conectores, [instale vários conectores](active-directory-application-proxy-enable.md), nomeie e os agrupe. Por fim, você precisa atribuí-los a aplicativos específicos.

## <a name="step-1-create-connector-groups"></a>Etapa 1: Criar grupos de conectores
Você pode criar quantos grupos de conectores desejar. A criação de grupo de Conectores é feita no portal clássico do Azure.

1. Escolha o diretório e clique em **Configurar**.  
    ![Captura de tela da configuração do proxy de aplicativo - clique em gerenciar grupos de conectores](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)
2. Em Proxy de Aplicativo, clique em **Gerenciar Grupos de Conectores** e crie um grupo de conectores fornecendo um nome ao grupo.  
    ![Captura de tela dos grupos de conectores do proxy de aplicativo - nomeie o novo grupo](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Etapa 2: Atribuir conectores aos seus grupos
Após a criação dos grupos de conectores, mova os conectores para o grupo apropriado.

1. Em **Proxy de Aplicativo**, clique em **Gerenciar Conectores**.
2. Em **Grupo**, escolha o grupo desejado para cada conector. Podem ser necessários até 10 minutos para que os conectores fiquem ativos no novo grupo.  
    ![Captura de tela dos conectores do proxy de aplicativo - selecione o grupo no menu suspenso](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Etapa 3: Atribuir aplicativos aos grupos de conectores
A última etapa é atribuir cada aplicativo ao grupo de conectores que o atende.

1. No Portal Clássico do Azure, no seu diretório, escolha o Aplicativo que você deseja atribuir ao grupo e clique em **Configurar**.
2. Em **Grupo de conectores**, selecione o grupo que deseja usar. A alteração é aplicada imediatamente.  
    ![Captura de tela do grupo de conectores do proxy de aplicativo - selecione o grupo no menu suspenso](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)

## <a name="see-also"></a>Confira também
* [Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)
* [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
* [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)
