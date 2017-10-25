---
title: "Práticas recomendadas para o Azure Functions | Microsoft Docs"
description: "Aprenda as práticas recomendadas e padrões para o Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, padrões, práticas recomendadas, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/13/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e0d4a29f59e48cac675e567fd84384b1b60d35f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Melhore o desempenho e a confiabilidade do Azure Functions

Este artigo fornece orientações para melhorar o desempenho e confiabilidade de seus aplicativos de funções. 


## <a name="avoid-long-running-functions"></a>Evite funções grandes de longa duração

As funções grandes de longa duração podem causar problemas de tempo limite inesperados. Uma função pode ser grande devido a muitas dependências de Node.js. A importação dessas dependências pode causar aumento no tempo de carregamento resultando em tempos limite inesperados. As dependências são carregadas explícita e implicitamente. Um único módulo carregado pelo seu código pode carregar seus próprios módulos adicionais.  

Sempre que possível, refatore funções grandes em conjuntos menores de funções que funcionem juntos e retornem respostas rápidas. Por exemplo, um webhook ou a função do gatilho HTTP pode exigir uma resposta de confirmação dentro de um certo limite de tempo; é comum de webhooks exigirem uma resposta imediata. Você pode passar o conteúdo do gatilho HTTP para uma fila para ser processado por uma função de gatilho de fila. Essa abordagem permite adiar o trabalho real e retornar uma resposta imediata.


## <a name="cross-function-communication"></a>Comunicação entre funções

Ao integrar várias funções, geralmente é uma prática recomendada usar filas de armazenamento para comunicação entre funções.  O principal motivo é que as filas de armazenamento são mais baratas e mais fáceis de provisionar. 

Mensagens individuais em uma fila de armazenamento estão limitadas ao tamanho de 64 KB. Se você precisar passar mensagens maiores entre as funções, uma fila de barramento de serviço do Azure pode ser usada para oferecer suporte a mensagens com tamanhos de até 256 KB.

Tópicos de barramento de serviço são úteis se você precisar de filtragem de mensagens antes do processamento.

Hubs de eventos são úteis para oferecer suporte a comunicações de alto volume.


## <a name="write-functions-to-be-stateless"></a>Grave funções para serem sem estado 

As funções devem ser sem estado e idempotentes se possível. Associe quaisquer informações de estado necessárias a seus dados. Por exemplo, um pedido sendo processado provavelmente teria um membro `state` associado. Uma função pode processar um pedido com base no estado enquanto a função em si permanece sem estado. 

Funções de idempotentes são recomendadas especialmente com gatilhos de timer. Por exemplo, se você tiver algo que absolutamente deve ser executado uma vez por dia, grave-o para que possa ser executado a qualquer momento durante o dia com os mesmos resultados. A função pode ser encerrada quando não houver nenhum trabalho para um dia específico. Também se uma execução anterior tiver falhado ao concluir, a próxima execução deve continuar de onde a anterior parou.


## <a name="write-defensive-functions"></a>Grave funções defensivas

Suponha que sua função pode encontrar uma exceção a qualquer momento. Projete suas funções com a capacidade de continuar de um ponto de falha anterior durante a próxima execução. Considere um cenário que requeira as seguintes ações:

1. Consulta de 10.000 linhas em um banco de dados.
2. Crie uma mensagem de fila para cada uma das linhas para processar ainda mais adiante na linha.
 
Dependendo de quão complexo é o sistema, você pode ter: serviços posteriores envolvidos se comportando mal, interrupções de rede ou limites de cota alcançados, etc. Tudo isso pode afetar sua função a qualquer momento. Você precisa para projetar suas funções para estarem preparadas para isso.

Como o seu código reage se ocorrer uma falha após a inserção de 5.000 desses itens em uma fila para processamento? Controle itens em um conjunto concluído. Caso contrário, você pode inseri-los de novo posteriormente. Isso pode ter um sério impacto em seu fluxo de trabalho. 

Se um item da fila já tiver sido processado, permita que sua função seja no-op.

Tire proveito de medidas defensivas já fornecidas para componentes usados na plataforma Azure Functions. Por exemplo, consulte **Tratamento de mensagens suspeitas na fila** na documentação de [gatilhos de fila de armazenamento do Azure](functions-bindings-storage-queue.md#trigger).
 

## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Não misture códigos de teste e de produção no mesmo aplicativo de funções

As funções em um aplicativo de funções compartilham recursos. Por exemplo, a memória é compartilhada. Se você estiver usando um aplicativo de funções em produção, não adicione recursos e funções de teste nele. Ele pode causar sobrecarga inesperada durante a execução de código de produção.

Cuidado com o que você carrega em seus aplicativos de funções de produção. A memória é dividida igualmente entre cada função no aplicativo.

Se você tiver um assembly compartilhado referenciado em várias funções .Net, coloque-o em uma pasta compartilhada comum. Referencie o assembly com uma instrução semelhante ao exemplo a seguir: 

    #r "..\Shared\MyAssembly.dll". 

Caso contrário, é fácil de implantar acidentalmente várias versões de teste do mesmo binário que se comportam diferentemente entre as funções.

Não use o log detalhado no código de produção. Ele tem um impacto negativo no desempenho.



## <a name="use-async-code-but-avoid-blocking-calls"></a>Usar o código assíncrono, mas evitar chamadas de bloqueio

A programação assíncrona é uma prática recomendada. No entanto, sempre evite fazer referência à propriedade `Result` ou chamar o método `Wait` em um instância `Task`. Essa abordagem pode levar ao esgotamento de thread.


[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

Como Azure Functions usa o serviço de aplicativo do Azure, você deve estar ciente das diretrizes de serviço de aplicativo.
* [Otimizações de desempenho HTTP para padrões e práticas](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)

