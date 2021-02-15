---
title: Controle de simultaneidade-Azure Marketplace
description: Estratégias de controle de simultaneidade para as APIs de publicação do Portal do Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: e468898daffe8fc42250575d3efa42f99279c410
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88031641"
---
# <a name="concurrency-control"></a>Controle de simultaneidade

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue funcionando após a transição para o Partner Center. As APIs de CPP só devem ser usadas para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

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
