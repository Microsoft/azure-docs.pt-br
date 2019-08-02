---
title: Versões de Service Fabric do Azure
description: Notas de versão para os recursos e melhorias mais recentes em Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 829963cf13eb1dc1b1113b6a296fe77dadb63bc4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224945"
---
# <a name="service-fabric-releases"></a>Versões Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guias</a> 
|  
|  
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank"></a> <a href="https://github.com/Azure/service-fabric-issues" target="blank"></a><a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank"></a><a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank"></a>de solução de problemas questões de suporte acompanhamento opções de código de versões com suporte 
| 

Este artigo fornece mais informações sobre as versões e atualizações mais recentes para o Service Fabric Runtime e SDKs.

## <a name="whats-new-in-service-fabric"></a>O que há de novo no Service Fabric

### <a name="service-fabric-65"></a>Service Fabric 6,5

A versão mais recente do Service Fabric inclui melhorias de suporte, confiabilidade e desempenho, novos recursos, correções de bugs e aprimoramentos para facilitar o gerenciamento do ciclo de vida do cluster e do aplicativo.

> [!IMPORTANT]
> Service Fabric 6,5 é a versão final com suporte a ferramentas de Service Fabric no Visual Studio 2015. Os clientes são aconselhados a mudar para o [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) no futuro.

Aqui está o que há de novo no Service Fabric 6,5:

- Service Fabric Explorer inclui um [Visualizador de repositório de imagens](service-fabric-visualizing-your-cluster.md#image-store-viewer) para inspecionar aplicativos que você carregou no repositório de imagens.

- O [POA (patch Orchestration Application)](service-fabric-patch-orchestration-application.md) versão [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) inclui muitos aprimoramentos de autodiagnóstico. Os clientes do POA são recomendados para migrar para esta versão.

- O [serviço EventStore está habilitado por padrão](service-fabric-visualizing-your-cluster.md#event-store) para clusters Service Fabric 6,5, a menos que você tenha optado por você.

- [Eventos de ciclo de vida de réplica](service-fabric-diagnostics-event-generation-operational.md#replica-events) adicionados para serviços com estado.

- [Melhor visibilidade do status do nó de semente](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), incluindo avisos de nível de cluster se um nó de semente nãoestiver íntegro (inativo, *removido* ou *desconhecido*).

- [Service Fabric ferramenta de recuperação](https://github.com/Microsoft/Service-Fabric-AppDRTool) de desastres de aplicativos permite que Service Fabric serviços com estado se recuperem rapidamente quando o cluster primário encontra um desastre. Os dados do cluster primário são sincronizados continuamente no aplicativo em espera secundário usando backup e restauração periódicos.

- Suporte do Visual Studio para [publicação de aplicativos .NET Core em clusters baseados em Linux](service-fabric-how-to-publish-linux-app-vs.md).

- A [CLI do Service Fabric do Azure (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) será instalada automaticamente para o Service Fabric 6,5 (e versões posteriores) quando você atualizar ou criar um novo cluster do Linux no Azure.

- O [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) é instalado por padrão em clusters Onebox do MacOS/Linux.

Para obter mais detalhes, consulte as [notas de versão do Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Versões do Service Fabric 6,5

| Data de liberação | Versão | Mais informações |
|---|---|---|
| 11 de junho de 2019 | [Service Fabric do Azure 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 de julho de 2019 | [Versão de atualização do Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |

## <a name="previous-versions"></a>Versões anteriores

### <a name="service-fabric-64-releases"></a>Versões do Service Fabric 6,4

| Data de liberação | Versão | Mais informações |
|---|---|---|
| 30 de novembro de 2018 | [Service Fabric do Azure 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 de dezembro de 2018 | [Versão de atualização do Azure Service Fabric 6,4 para clusters do Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 de fevereiro de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 de março de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 de abril de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 de maio de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 de maio de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
