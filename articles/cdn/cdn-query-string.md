---
title: Controlando o comportamento de cache da CDN do Azure com cadeias de consulta | Microsoft Docs
description: "O cache da cadeia de caracteres de consulta da CDN do Azure controla como os arquivos devem ser armazenados em cache quando contêm cadeias de caracteres de consulta."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 8d79626fa8516f226a82d3dac693c2033904c91d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings"></a>Controlando o comportamento de cache da CDN do Azure com cadeias de consulta
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN Premium do Azure da Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Visão geral
O cache da cadeia de caracteres de consulta controla como os arquivos devem ser armazenados em cache quando contêm cadeias de caracteres de consulta.

> [!IMPORTANT]
> Os produtos CDN Standard e Premium fornecem a mesma funcionalidade de cache de cadeia de consulta, mas a interface do usuário varia.  Este documento descreve a interface da **CDN Standard do Azure da Akamai** e da **CDN Standard do Azure da Verizon**.  Para saber mais sobre o armazenamento em cache de cadeias de caracteres de consulta com a **CDN Premium do Azure da Verizon**, confira [Controlando o comportamento do cache de solicitações de CDN com cadeias de caracteres de consulta - Premium](cdn-query-string-premium.md).
> 
> 

Existem três modos disponíveis:

* **Ignorar cadeias de consulta**: esse é o modo padrão.  O nó de borda CDN passará a cadeia de caracteres de consulta do solicitante para a origem na primeira solicitação e deixará o ativo em cache.  Todas as solicitações subsequentes para esse ativo que são atendidas a partir do nó de borda ignorarão a cadeia de caracteres de consulta até que o ativo em cache expire.
* **Ignorar o cache da URL com cadeias de consulta**: nesse modo, as solicitações com cadeias de consulta não são armazenadas em cache no nó de borda da CDN.  O nó de borda recupera o ativo diretamente da origem e passa-o para o solicitante com cada solicitação.
* **Armazenar em cache cada URL exclusiva**: esse modo trata cada solicitação com uma cadeia de consulta como um ativo exclusivo com seu próprio cache.  Por exemplo, a resposta da origem de uma solicitação de *foo.ashx?q=bar* seria armazenada em cache no nó de borda e retornada para os caches subsequentes com a mesma cadeia de caracteres de consulta.  Uma solicitação de *foo.ashx?q=somethingelse* será armazenada em cache como um ativo separado com seu próprio tempo de vida.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterando configurações de cache de cadeia de consulta para perfis de CDN padrão
1. Na folha Perfil CDN, clique no ponto de extremidade da CDN que deseja gerenciar.
   
    ![Pontos de extremidade da folha Perfil CDN](./media/cdn-query-string/cdn-endpoints.png)
   
    A folha do ponto de extremidade da CDN se abre.
2. Clique no botão **Configurar** .
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-query-string/cdn-config-btn.png)
   
    A folha Configuração da CDN se abre.
3. Selecione uma configuração da lista suspensa **Comportamento do cache da cadeia de consulta** .
   
    ![Opções de cache de cadeia de caracteres de consulta CDN](./media/cdn-query-string/cdn-query-string.png)
4. Após fazer suas seleções, clique no botão **Salvar** .

> [!IMPORTANT]
> As mudanças de configuração podem não estar visíveis imediatamente, pois o registro demora um pouco para se propagar pela CDN.  Para <b>Azure CDN do Akamai</b> , a propagação geralmente é concluída em um minuto.  Para perfis da <b>CDN do Azure da Verizon</b>, a propagação geralmente é concluída em 90 minutos, mas em alguns casos pode levar mais tempo.
> 
> 

