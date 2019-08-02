---
title: Controlar o comportamento de cache da CDN do Azure com cadeias de caracteres consulta - camada Standard | Microsoft Docs
description: O cache da cadeia de caracteres de consulta da CDN do Azure controla como os arquivos são armazenados em cache quando uma solicitação da Web contém uma cadeia de caracteres de consulta. Este artigo descreve o cache da cadeia de caracteres de consulta em produtos Standard da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2b9e56f8a0a023c8423426fee081a5a48ebda330
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593453"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Controlar o comportamento de cache da CDN do Azure com cadeias de caracteres consulta - camada Standard
> [!div class="op_single_selector"]
> * [Camada Standard](cdn-query-string.md)
> * [Camada Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Visão geral
Com a Rede de Distribuição de Conteúdo do Azure (CDN), você pode controlar como os arquivos são armazenados em cache para uma solicitação na Web que contenha uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares de chave-valor, no qual o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, a ordem não importa. 

> [!IMPORTANT]
> Os produtos Standard e Premium da CDN do Azure fornecem a mesma funcionalidade de cache da cadeia de caracteres de consulta, mas a interface do usuário é diferente. Este artigo descreve a interface da **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Akamai** e da **CDN Standard do Azure da Verizon**. Para cache da cadeia de caracteres de consulta com **CDN do Azure CDN Premium da Verizon**, consulte [Controlar o comportamento de cache da CDN do Azure com cadeias de caracteres de consulta - camada Premium](cdn-query-string-premium.md).

Estão disponíveis três modos de cadeia de caracteres de consulta:

- **Ignorar as cadeias de caracteres de consulta**: Modo padrão. Nesse modo, o nó POP (ponto de presença) da CDN transmite as cadeias de caracteres de consulta do solicitante para o servidor de origem na primeira solicitação e armazena em cache o ativo. Todas as solicitações subsequentes para esse ativo que forem atendidas a partir do POP ignoram a cadeia de caracteres de consulta até que o ativo em cache expire.

- **Ignorar o cache para cadeias de caracteres de consulta**: Nesse modo, as solicitações com cadeias de caracteres de consulta não estão em cache no nó POP da CDN. O nó POP recupera o ativo diretamente da origem e passa-o para o solicitante com cada solicitação.

- **Armazenar em cache todas as URLs exclusivas**: Nesse modo, cada solicitação com um URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta do servidor de origem para uma solicitação para example.ashx?q=test1 é armazenada em cache no nó POP e retornada para caches subsequentes com a mesma cadeia de caracteres de consulta. Uma solicitação para example.ashx?q=test2 é armazenada em cache como um ativo separado com sua própria configuração de vida útil.
   
    >[!IMPORTANT] 
    > Não use este modo quando a cadeia de caracteres de consulta contiver parâmetros que serão alterados em cada solicitação, como uma ID da sessão ou um nome de usuário, porque isso resultará em baixo índice de ocorrências no cache.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterando configurações de cache de cadeia de consulta para perfis de CDN padrão
1. Abra um perfil de CDN e, em seguida, selecione o ponto de extremidade da CDN que deseja gerenciar.
   
   ![Pontos de extremidade de perfil CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. No painel esquerdo em Configurações, clique em **Regras de cache**.
   
    ![Botão de regras de cache da CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Na lista **Comportamento de cache da cadeia de caracteres de consulta**, selecione um modo de cadeia de caracteres de consulta e, em seguida, clique em **Salvar**.
   
   ![Opções de cache de cadeia de caracteres de consulta CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Como demora para que o registro se propague pela CDN do Azure, as alterações nas configurações da cadeia de caracteres de cache podem não ficar visíveis imediatamente:
> - Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
> - Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
> - Para perfis da **CDN Standard do Azure da Verizon** e da **CDN Premium do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos. 



