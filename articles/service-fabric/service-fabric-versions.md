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
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716074"
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
| 6.4.* | 6.2.301.* |Menor ou igual a versão 3.3 |A versão atual, portanto, nenhuma data de término |

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

A tabela a seguir lista os sistemas operacionais com suporte para as versões com suporte do Service Fabric.

| Sistema operacional | Versão do Service Fabric mais antiga com suporte |
| --- | --- |
| Windows Server 2012 R2 | Todas as versões |
| Windows Server 2016 | Todas as versões |
| Windows Server 1709 | 6,0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6,0 |

