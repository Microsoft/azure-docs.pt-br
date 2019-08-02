---
title: Estatísticas em tempo real na CDN do Azure | Microsoft Docs
description: As estatísticas em tempo real fornecem dados em tempo real sobre o desempenho da CDN do Azure ao fornecer conteúdo para os clientes.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d56007e5a196a0857f3b69ac51f5e3b5a88c4f6e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593502"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Estatísticas em tempo real na CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Visão geral
Este documento explica as estatísticas em tempo real na CDN do Microsoft Azure.  Essa funcionalidade fornece dados em tempo real, como a largura de banda, status do cache e conexões simultâneas, para seu perfil da CDN ao fornecer conteúdo para os clientes. Isso permite o monitoramento contínuo da integridade do serviço a qualquer momento, incluindo eventos de ativação.

Os grafos a seguir estão disponíveis:

* [Largura de banda](#bandwidth)
* [Códigos de status](#status-codes)
* [Status do Cache](#cache-statuses)
* [Conexões](#connections)

## <a name="accessing-real-time-stats"></a>Acessando as estatísticas em tempo real
1. No [Portal do Azure](https://portal.azure.com), navegue para seu perfil CDN.
   
    ![Folha Perfil CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Na folha do perfil do CDN, clique no botão **Gerenciar** .
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
3. Passe o mouse sobre a guia **Análise**, em seguida, sobre o submenu **statísticas em Tempo Real**.  Clique em **Objeto grande de HTTP**.
   
    ![Portal de gerenciamento da CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Os grafos de estatísticas em tempo real são exibidos.

Cada um dos grafos exibe estatísticas em tempo real para o período de tempo selecionado, iniciando quando a página carrega.  Os grafos são atualizados automaticamente em alguns segundos.  O botão **Atualizar Grafo**, se presente, limpará o gráfico, depois exibirá apenas os dados selecionados.

## <a name="bandwidth"></a>Largura de banda
![Grafo da largura de banda](./media/cdn-real-time-stats/cdn-bandwidth.png)

O grafo **Largura de Banda** exibe a quantidade de largura de banda usada para a plataforma atual durante um período de tempo selecionado. A parte sombreada do grafo indica o uso de largura de banda. A quantidade exata de largura de banda usada atualmente é exibida diretamente abaixo do grafo de linha.

## <a name="status-codes"></a>Códigos de status
![Grafo do código de status](./media/cdn-real-time-stats/cdn-status-codes.png)

O grafo **Códigos de Status** indica com que frequência determinados códigos de resposta HTTP estão ocorrendo no período de tempo selecionado.

> [!TIP]
> Para obter uma descrição de cada opção do código de status HTTP, confira [Códigos de Status HTTP da CDN do Azure](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

É exibida uma lista dos códigos de status HTTP diretamente acima do grafo. Essa lista indica cada código de status que pode ser incluído no grafo de linha e o número de ocorrências por segundo para esse código de status. Por padrão, uma linha é exibida para cada um destes códigos de status no grafo. No entanto, você pode optar por monitorar apenas os códigos de status que têm um significado especial para a sua configuração de CDN. Para fazer isso, verifique os códigos de status desejados, desmarque todas as outras opções e clique em **Atualizar Grafo**. 

Você pode ocultar temporariamente os dados registrados para um código de status específico.  Na legenda diretamente abaixo do grafo, clique no código de status que você deseja ocultar. O código de status será imediatamente ocultado no grafo. Clicar novamente no código de status fará com que essa opção seja exibida mais uma vez.

## <a name="cache-statuses"></a>Status do Cache
![Grafos dos Status do Cache](./media/cdn-real-time-stats/cdn-cache-status.png)

O grafo **Status do Cache** indica com que frequência determinados tipos de status do cache estão ocorrendo no período de tempo selecionado. 

> [!TIP]
> Para obter uma descrição de cada opção do código de status do cache, consulte [Códigos de Status do Cache da CDN do Azure](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

É exibida uma lista dos códigos de status do cache diretamente acima do grafo. Essa lista indica cada código de status que pode ser incluído no grafo de linha e o número de ocorrências por segundo para esse código de status. Por padrão, uma linha é exibida para cada um destes códigos de status no grafo. No entanto, você pode optar por monitorar apenas os códigos de status que têm um significado especial para a sua configuração de CDN. Para fazer isso, verifique os códigos de status desejados, desmarque todas as outras opções e clique em **Atualizar Grafo**. 

Você pode ocultar temporariamente os dados registrados para um código de status específico.  Na legenda diretamente abaixo do grafo, clique no código de status que você deseja ocultar. O código de status será imediatamente ocultado no grafo. Clicar novamente no código de status fará com que essa opção seja exibida mais uma vez.

## <a name="connections"></a>Conexões
![Grafo das Conexões](./media/cdn-real-time-stats/cdn-connections.png)

Esse grafo indica quantas conexões foram estabelecidas para seus servidores de borda. Cada solicitação de um ativo que passa por nossos resultados da CDN em uma conexão.

## <a name="next-steps"></a>Próximas etapas
* Receba uma notificação com [Alertas em tempo real no Azure CDN](cdn-real-time-alerts.md)
* Saiba mais com os [relatórios HTTP avançados](cdn-advanced-http-reports.md)
* Analisar os [padrões de uso](cdn-analyze-usage-patterns.md)

