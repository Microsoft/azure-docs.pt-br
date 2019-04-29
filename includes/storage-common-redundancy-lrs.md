---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 32032f729283cb3f2a786412b563fdee88ba4c8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729597"
---
O armazenamento localmente redundante (LRS) fornece pelo menos 99,999999999% (11 noves) de durabilidade dos objetos em um determinado ano. O LRS fornece a durabilidade deste objeto, replicando seus dados para uma unidade de escala de armazenamento. Um datacenter, localizado na região onde você criou sua conta de armazenamento, hospeda a unidade de escala de armazenamento. Uma solicitação de gravação para uma conta de armazenamento LRS é retornada com êxito somente depois que os dados são gravados em todas as réplicas. Cada réplica reside em domínios de falha separados e atualiza domínios dentro de uma unidade de escala de armazenamento.

Uma unidade de escala de armazenamento é uma coleção de racks de nós de armazenamento. Um domínio de falha (FD) é um grupo de nós que representa uma unidade física de falha. Pense em um domínio de falha como nós pertencentes ao mesmo rack físico. Um domínio de atualização (UD) é um grupo de nós atualizados em conjunto durante o processo de atualização de um serviço (distribuição). As réplicas estão difundidas entre UDs e FDs em uma unidade de escala de armazenamento. Essa arquitetura garante que seus dados estejam disponíveis se uma falha de hardware afetar um único rack ou quando os nós forem atualizados durante um upgrade de serviço.

O LRS é a opção de replicação de menor custo e oferece a menor durabilidade em comparação com outras opções. Em caso de desastre no nível de datacenter (por exemplo, incêndio, inundação etc.), as réplicas podem ser perdidas ou ficar irrecuperáveis. Para reduzir esse risco, a Microsoft recomenda o uso de armazenamento com redundância de zona (ZRS) ou armazenamento com redundância geográfica (GRS).

* Se seu aplicativo armazenar dados que possam ser facilmente reconstruídos, você pode optar por LRS.
* Alguns aplicativos são restritos à replicação de dados somente em um país devido a requisitos de governança de dados. Em alguns casos, as regiões emparelhadas através das quais os dados são replicados para contas GRS podem estar em outro país. Para obter mais informações sobre pares de regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).
