---
title: Gerenciar o estado nos serviços do Service Fabric do Azure | Microsoft Docs
description: Saiba como definir e gerenciar o estado do serviço nos serviços do Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e3ab36def2d210bd763f3ce2dc5df155e37e2dba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870886"
---
# <a name="service-state"></a>Estado do serviço
**Estado do serviço** refere-se aos dados em disco ou na memória que um serviço requer para funcionar. Inclui, por exemplo, as estruturas de dados e variáveis de membro que o serviço lê e grava para realizar trabalhos. Dependendo de como o serviço é estruturado, também pode incluir arquivos ou outros recursos armazenados em disco. Por exemplo, os arquivos que um banco de dados usaria para armazenar dados e logs de transação.

Como um exemplo de serviço, vamos considerar uma calculadora. Um serviço de calculadora básica pega dois números e retorna sua soma. Executar este cálculo não envolve nenhuma variável de membro ou outras informações.

Agora, considere a mesma calculadora, mas com um método adicional para armazenar e retornar a última soma computada. Agora, trata-se de um serviço com estado. Com estado significa que ele contém algum estado no qual grava quando calcula uma nova soma e do qual lê quando você solicita que ele retorne a última soma calculada.

No Service Fabric do Azure, o primeiro serviço é chamado de serviço sem estado. O segundo serviço é chamado de serviço com estado.

## <a name="storing-service-state"></a>Armazenando o estado do serviço
O estado pode ser externalizado ou localizado em conjunto com o código que está manipulando o estado. Normalmente, a externalização do estado é feita usando um banco de dados externo ou outro armazenamento de dados que é executado em máquinas diferentes na rede ou fora do processo no mesmo computador. Em nosso exemplo da calculadora, o armazenamento de dados poderia ser uma um banco de dados SQL ou uma instância do Armazenamento de Tabela do Azure. Cada solicitação para calcular a soma executa uma atualização desses dados e solicitações para o serviço retornar o valor fazem com que o valor atual seja buscado no repositório. 

O estado também pode ser localizado com o código que manipula o estado. Serviços com estado no Service Fabric normalmente são criados usando esse modelo. O Service Fabric fornece a infraestrutura para garantir que esse estado seja altamente disponível, consistente e durável e que serviços criados dessa maneira possam ser dimensionados com facilidade.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os conceitos do Service Fabric, confira os seguintes artigos:

* [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)
* [Escalabilidade de serviços da Malha do Serviço](service-fabric-concepts-scalability.md)
* [Particionando serviços da Malha do Serviço](service-fabric-concepts-partitioning.md)
* [Reliable Services do Service Fabric](service-fabric-reliable-services-introduction.md)
