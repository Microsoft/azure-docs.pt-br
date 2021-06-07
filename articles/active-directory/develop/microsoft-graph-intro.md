---
title: API do Microsoft Graph
description: A API de Microsoft Graph é uma API Web RESTful que permite que você acesse Microsoft Cloud recursos de serviço.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: a799d7c32ad632303931adf838ca8e2e715b2adc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860177"
---
# <a name="microsoft-graph-api"></a>API do Microsoft Graph

A API de Microsoft Graph é uma API Web RESTful que permite que você acesse Microsoft Cloud recursos de serviço. Depois de registrar seu aplicativo e obter tokens de autenticação para um usuário ou serviço, você pode fazer solicitações para a API de Microsoft Graph. Para obter mais informações, consulte [visão geral do Microsoft Graph](/graph/overview).

O Microsoft Graph expõe as bibliotecas de cliente e APIs REST para acessar dados nos seguintes serviços de Microsoft 365:
- Serviços Microsoft 365s: Delve, Excel, Microsoft Books, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner e SharePoint
- Serviços de segurança e mobilidade corporativa: análise avançada de ameaças, proteção avançada contra ameaças, Azure Active Directory, Identity Manager e Intune
- Serviços do Windows 10: atividades, dispositivos, notificações
- Dynamics 365 Business Central

## <a name="versions"></a>Versões

O Microsoft Graph atualmente dá suporte a duas versões: v 1.0 e beta. A versão v 1.0 inclui APIs geralmente disponíveis. Use a versão v 1.0 para todos os aplicativos de produção. A versão beta inclui APIs que estão atualmente em visualização. Como podemos introduzir alterações recentes em nossas APIs beta, recomendamos que você use a versão beta somente para testar os aplicativos que estão em desenvolvimento; Não use APIs beta em seus aplicativos de produção. Para obter mais informações, consulte [controle de versão, suporte e políticas de alteração significativas para Microsoft Graph](/graph/versioning-and-support).

Para começar a usar as APIs beta, consulte [referência de ponto de extremidade do Microsoft Graph beta](/graph/api/overview?view=graph-rest-beta)

Para começar a usar as APIs v 1.0, confira [Microsoft Graph referência da API REST v 1.0](/graph/api/overview)

## <a name="get-started"></a>Introdução

Para ler ou gravar em um recurso como um usuário ou uma mensagem de email, você constrói uma solicitação semelhante à seguinte:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Para obter mais informações sobre os elementos da solicitação construída, consulte [usar a API de Microsoft Graph](/graph/use-the-api)

Exemplos de início rápido estão disponíveis para mostrar como acessar a potência da API de Microsoft Graph. Os exemplos que estão disponíveis acessam dois serviços com uma autenticação: conta Microsoft e o Outlook. Cada início rápido acessa informações de conta Microsoft perfis de usuários e exibe eventos de seu calendário.
Os guias de início rápido envolvem quatro etapas:
- Selecione sua plataforma
- Obter sua ID do aplicativo (ID do cliente)
- Compilar o exemplo
- Entrar e exibir eventos em seu calendário

Quando você concluir o início rápido, terá um aplicativo que está pronto para ser executado. Para obter mais informações, consulte as [perguntas frequentes sobre o Microsoft Graph início rápido](/graph/quick-start-faq). Para começar a usar os exemplos, consulte [Microsoft Graph início rápido](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Ferramentas

O Microsoft Graph Explorer é uma ferramenta baseada na Web que você pode usar para compilar e testar solicitações usando APIs Microsoft Graph. Você pode acessar o Microsoft Graph Explorer em: `https://developer.microsoft.com/graph/graph-explorer` .

O postmaster é uma ferramenta que você também pode usar para compilar e testar solicitações usando as APIs de Microsoft Graph. Você pode baixar o postmaster em: `https://www.getpostman.com/` . Para interagir com Microsoft Graph no postmaster, use a coleção Microsoft Graph no postmaster. Para obter mais informações, consulte [usar o postmaster com a API de Microsoft Graph](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).
