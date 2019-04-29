---
title: Controle de simultaneidade | Microsoft Docs
description: Estratégias de controle de simultaneidade para as APIs de publicação do Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ecf0bb6ac7fc77e804c9fc8d62aba52810de5640
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60624999"
---
<a name="concurrency-control"></a>Controle de simultaneidade
===================

Cada chamada às APIs de publicação do Portal do Cloud Partner deve especificar explicitamente qual estratégia de controle de simultaneidade usar. A falha em fornecer o cabeçalho **If-Match** resultará em uma resposta de erro HTTP 400. Duas estratégias para controle de simultaneidade são ofertadas.

-   **Otimista** - O cliente que executa a atualização verifica se os dados foram alterados desde a última vez que leu os dados.
-   **Último vence** - O cliente atualiza os dados diretamente, independentemente de outro aplicativo tê-lo modificado desde o último horário de leitura.

<a name="optimistic-concurrency-workflow"></a>Fluxo de trabalho de simultaneidade otimista
-------------------------------

É recomendável usar a estratégia de simultaneidade otimista com o fluxo de trabalho a seguir para garantir que nenhuma edição inesperada seja feita nos recursos.

1.  Recuperar uma entidade usando as APIs. A resposta inclui um valor de ETag que identifica a versão atualmente armazenada da entidade (no momento da resposta).
2.  No momento da atualização, inclua esse mesmo valor de ETag no cabeçalho de solicitação **If-Match**.
3.  A API compara o valor de ETag recebido na solicitação com o valor de ETag atual da entidade em uma transação atômica.
    *   Se os valores de ETag forem diferentes, a API retornará uma resposta HTTP `412 Precondition Failed`. Esse erro indica que outro processo atualizou a entidade desde que o cliente a recuperou pela última vez ou que o valor de ETag especificado na solicitação está incorreto.
    *  Se os valores de ETag forem iguais ou o cabeçalho **If-Match** header contiver o caractere asterisco curinga (`*`), a API executará a operação solicitada. A operação da API também atualiza o valor de ETag armazenado da entidade.


> [!NOTE]
> Especificar o caractere curinga (*) no cabeçalho **If-Match** resulta na API usando a estratégia de simultaneidade Último vence. Nesse caso, a comparação de ETag não ocorre e o recurso é atualizado sem nenhuma verificação. 
