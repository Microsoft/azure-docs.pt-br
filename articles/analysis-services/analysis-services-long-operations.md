---
title: Práticas recomendadas para operações de longa execução no Azure Analysis Services | Microsoft Docs
description: Este artigo descreve as práticas recomendadas para operações de longa execução.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 4e069effae0cb7f834b2c3dac696d05304d841a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92014827"
---
# <a name="best-practices-for-long-running-operations"></a>Melhores práticas para operações de execução prolongada

No Azure Analysis Services, um *nó* representa uma máquina virtual do host onde um recurso de servidor está em execução. Algumas operações como consultas de longa execução, operações de atualização e sincronização de expansão de consulta poderão falhar se um recurso de servidor for movido para um nó diferente. As mensagens de erro comuns nesse cenário incluem:

- "Ocorreu um erro ao tentar localizar uma solicitação XMLA de execução longa. A solicitação pode ter sido interrompida pela atualização do serviço ou pelo reinício do servidor. "
- "O trabalho com ID ' <guid> para o modelo ' <database> ' foi cancelado devido a um erro de serviço (inatividade) com a mensagem ' cancelando a solicitação de atualização desde que ela foi bloqueada sem nenhuma atualização. Esse é um problema de serviço interno. Envie o trabalho novamente ou registre um tíquete para obter ajuda se esse problema ocorrer repetidamente. "

Há muitas razões pelas quais as operações de longa execução podem ser interrompidas. Por exemplo, atualizações no Azure, como: 
- Patches do sistema operacional 
- Atualizações de segurança
- Atualizações de serviço Azure Analysis Services
- Service Fabric atualizações. Service Fabric é um componente de plataforma usado por vários serviços de nuvem da Microsoft, incluindo Azure Analysis Services.

Além das atualizações que ocorrem no serviço, há uma movimentação natural de serviços entre nós devido ao balanceamento de carga. Os movimentos do nó são uma parte esperada de um serviço de nuvem. Azure Analysis Services tenta minimizar os impactos dos movimentos do nó, mas é impossível eliminá-los inteiramente. 

Além dos movimentos do nó, há outras falhas que podem ocorrer. Por exemplo, um sistema de banco de dados de fonte de dado pode estar offline ou a conectividade de rede é perdida. Se, durante a atualização, uma partição tiver 10 milhões linhas e uma falha ocorrer na linha de 9 milionésimos, não será possível reiniciar a atualização no ponto de falha. O serviço precisa iniciar novamente desde o início. 

## <a name="refresh-rest-api"></a>Atualizar API REST

As interrupções de serviço podem ser desafiadoras para operações de longa execução, como atualização de dados. O Azure Analysis Services inclui uma API REST para ajudar a reduzir os impactos negativos das interrupções de serviço. Para saber mais, consulte [atualização assíncrona com a API REST](analysis-services-async-refresh.md).
 
Além da API REST, há outras abordagens que você pode usar para minimizar possíveis problemas durante operações de atualização de longa execução. A principal meta é evitar a reinicialização da operação de atualização desde o início e, em vez disso, realizar atualizações em lotes menores que podem ser confirmadas em estágios. 
 
A API REST permite essa reinicialização, mas não permite total flexibilidade de criação e exclusão de partição. Se um cenário exigir operações complexas de gerenciamento de dados, sua solução deverá incluir alguma forma de envio em lote em sua lógica. Por exemplo, ao usar transações para processar dados em vários, os lotes separados permitem uma falha para não exigir reinicialização desde o início, mas em vez de um ponto de verificação intermediário. 
 
## <a name="scale-out-query-replicas"></a>Réplicas de consulta de expansão

Se estiver usando a lógica REST ou personalizada, as consultas de aplicativo cliente ainda poderão retornar resultados inconsistentes ou intermediários enquanto os lotes estão sendo processados. Se os dados consistentes retornados pelas consultas de aplicativo cliente forem necessários durante o processamento e os dados do modelo estiverem em um estado intermediário, você poderá usar a [escala](analysis-services-scale-out.md) horizontal com réplicas de consulta somente leitura.

Usando réplicas de consulta somente leitura, enquanto as atualizações estão sendo executadas em lotes, os usuários do aplicativo cliente podem continuar a consultar o instantâneo antigo de dados nas réplicas somente leitura. Depois que as atualizações forem concluídas, uma operação de sincronização poderá ser executada para colocar as réplicas somente leitura atualizadas.


## <a name="next-steps"></a>Próximas etapas

[Atualização assíncrona com a API REST](analysis-services-async-refresh.md)  
[Expansão do Azure Analysis Services](analysis-services-scale-out.md)  
[Alta disponibilidade do Analysis Services](analysis-services-bcdr.md)  
[Diretrizes de repetição para serviços do Azure](/azure/architecture/best-practices/retry-service-specific)