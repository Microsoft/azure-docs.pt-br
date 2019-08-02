---
title: Versões de cluster com suporte no Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre as versões de cluster no Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/03/2019
ms.author: aljo
ms.openlocfilehash: 030e57512d10f31ca03dff2f99e57c305de49e97
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565988"
---
# <a name="supported-service-fabric-versions"></a>Versões do Service Fabric com suporte

Verifique se que o cluster sempre está executando uma versão com suporte do Azure Service Fabric. Termina um mínimo de 60 dias depois que anunciamos o lançamento de uma nova versão do Service Fabric, suporte para a versão anterior. Você encontrará os anúncios sobre novos lançamentos a [blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consulte os documentos a seguir para obter detalhes sobre como manter o cluster executando uma versão do Service Fabric com suporte:

- [Atualizar um cluster do Service Fabric do Azure](service-fabric-cluster-upgrade.md)
- [Atualizar a versão de Service Fabric em execução no cluster do Windows Server autônomo](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versões com suporte

A tabela a seguir lista as versões do Service Fabric e suas datas de término do suporte.

| Tempo de execução do Service Fabric no cluster | Pode atualizar diretamente da versão do cluster |Versão compatível do pacote do SDK ou NuGet | Fim do suporte |
| --- | --- |--- | --- |
| Todas as versões anteriores a 5.3.121 do cluster | 5.1.158.* |Menor ou igual à versão 2.3 |20 de janeiro de 2017 |
| 5.3.* | 5.1.158.* |Menor ou igual à versão 2.3 |24 de fevereiro de 2017 |
| 5.4.* | 5.1.158.* |Menor ou igual à versão 2.4 |10 de maio de 2017       |
| 5.5.* | 5.4.164.* |Menor ou igual à versão 2.5 |10 de agosto de 2017    |
| 5.6.* | 5.4.164.* |Menor ou igual à versão 2.6 |13 de outubro de 2017   |
| 5.7.* | 5.4.164.* |Menor ou igual à versão 2.7 |15 de dezembro de 2017  |
| 6.0.* | 5.6.205.* |Menor ou igual à versão 2.8 |30 de março de 2018     |
| 6.1.* | 5.7.221.* |Menor ou igual à versão 3.0 |15 de julho de 2018      |
| 6.2.* | 6.0.232.* |Menor ou igual à versão 3.1 |26 de outubro de 2018   |
| 6.3.* | 6.1.480.* |Menor ou igual à versão 3.2 |31 de março de 2019  |
| 6.4.* | 6.2.301.* |Menor ou igual a versão 3.3 |15 de setembro de 2019 |
| 6.5.* | 6.4.617.* |Menor ou igual à versão 3.4 |A versão atual, portanto, nenhuma data de término |

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

A tabela a seguir lista os sistemas operacionais com suporte para as versões com suporte do Service Fabric.

| Sistema operacional | Versão do Service Fabric mais antiga com suporte |
| --- | --- |
| Windows Server 2012 R2 | Todas as versões |
| Windows Server 2016 | Todas as versões |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Nomes de versão com suporte

A tabela a seguir lista os nomes de versão do Service Fabric e seus números de versão correspondente.

| Nome da versão | Número de versão do Windows | Número de versão do Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | ND |
| 5.3 CU1 | 5.3.204.9494 | ND |
| 5.3 CU2 | 5.3.301.9590 | ND |
| 5.3 CU3 | 5.3.311.9590 | ND |
| 5.4 CU2 | 5.4.164.9494 | ND |
| 5.5 ATUALIZAÇÃO CUMULATIVA 1 PARA | 5.5.216.0    | ND |
| 5.5 CU2 | 5.5.219.0    | ND |
| 5.5 CU3 | 5.5.227.0    | ND |
| 5.5 CU4 | 5.5.232.0    | ND |
| 5.6 RTO | 5.6.204.9494 | ND |
| 5.6 CU2 | 5.6.210.9494 | ND |
| 5.6 CU3 | 5.6.220.9494 | ND |
| 5.7 RTO | 5.7.198.9494 | ND |
| 5.7 CU4 | 5.7.221.9494 | ND |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | ND |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | ND |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | ND |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | ND |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |