---
title: "Atualização de aplicativos do Service Fabric | Microsoft Docs"
description: "Este artigo fornece uma introdução à atualização de um aplicativo do Service Fabric, incluindo a escolha de modos de atualização e execução de verificações de integridade."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 43e1a66c3aca882f8f572d2bf71976d6b65a9c68
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-application-upgrade"></a>Atualização de aplicativos do Service Fabric
Um aplicativo do Azure Service Fabric é uma coleção de serviços. Durante uma atualização, a Malha do Serviço compara o novo [manifesto do aplicativo](service-fabric-application-model.md#describe-an-application) com a versão anterior e determina quais serviços as atualizações do aplicativo exigem. O Service Fabric compara os números de versão nos manifestos de serviço com os números de versão na versão anterior. Se um serviço não foi alterado, ele não foi atualizado.

## <a name="rolling-upgrades-overview"></a>Visão geral das atualizações sem interrupção
Em uma atualização do aplicativo sem interrupção, a atualização é executada em estágios. Em cada estágio, a atualização é aplicada a um subconjunto de nós no cluster, chamado de domínio de atualização. Como resultado, o aplicativo permanece disponível durante a atualização. Durante a atualização, o cluster pode conter uma combinação de versões antigas e novas.

Por esse motivo, as duas versões devem ser compatíveis uma com a outra. Se não forem compatíveis, o administrador do aplicativo é responsável por realizar uma atualização de várias fases para manter a disponibilidade. Em uma atualização multifásica, a primeira etapa é atualizar para uma versão intermediária do aplicativo que é compatível com a versão anterior. A segunda etapa é atualizar a versão final que quebra a compatibilidade com a versão anterior à atualização, mas é compatível com a versão intermediária.

Os domínios de atualização ficam especificados no manifesto do cluster quando este é configurado. Os domínios de atualização não recebem atualizações em uma ordem específica. Um domínio de atualização é uma unidade lógica de implantação para um aplicativo. Os domínios de atualização permitem que os serviços permaneçam com alta disponibilidade durante uma atualização.

Atualizações com interrupção são possíveis se a atualização for aplicada a todos os nós no cluster, que é o caso quando o aplicativo tem somente um domínio de atualização. Essa abordagem não é recomendável porque o serviço ficaria paralisado e não estaria disponível no momento da atualização. Além disso, o Azure não fornece qualquer garantia quando um cluster é configurado com apenas um domínio de atualização.

Após a conclusão da atualização, todos os serviços e réplicas (instâncias) permanecerão na mesma versão-ou seja, se a atualização for bem-sucedida, eles serão atualizados para a nova versão; se a atualização falhar e for revertida, eles serão revertidos para a versão antiga.

## <a name="health-checks-during-upgrades"></a>Verificações de integridade durante atualizações
Para uma atualização, as políticas de integridade precisam ser configuradas (ou valores padrão podem ser usados). Uma atualização é considerada bem-sucedida quando todos os domínios de atualização são atualizados dentro do tempo limite especificado e quando todos os domínios de atualização são considerados íntegros.  Um domínio de atualização íntegro significa que o domínio de atualização foi aprovado em todas as verificações de integridade especificadas na política de integridade. Por exemplo, uma política de integridade pode obrigar que todos os serviços em uma instância do aplicativo estejam *íntegros*, de acordo com a definição de integridade do Service Fabric.

As políticas e verificações de integridade durante a atualização feita pelo Service Fabric são independentes do serviço e do aplicativo. Ou seja, nenhum teste específico de serviço é realizado.  Por exemplo, o serviço pode ter um requisito de taxa de transferência, mas o Service Fabric não tem as informações para verificar a taxa de transferência. Confira os [artigos sobre integridade](service-fabric-health-introduction.md) para as verificações que serão executadas. As verificações que ocorrem durante uma atualização incluem testes para ver se o pacote de aplicativos foi copiado corretamente, se a instância foi iniciada e assim por diante.

A integridade do aplicativo é uma agregação das entidades filho do aplicativo. Em resumo, o Service Fabric avalia a integridade do aplicativo por meio da integridade reportada no aplicativo. Ele também avalia a integridade de todos os serviços do aplicativo dessa maneira. O Service Fabric ainda avalia a integridade dos serviços de aplicativo agregando a integridade de seus filhos, como a réplica do serviço. Quando a política de integridade do aplicativo for atendida, a atualização poderá continuar. Se a política de integridade for violada, a atualização do aplicativo falhará.

## <a name="upgrade-modes"></a>Modos de atualização
O modo recomendado de atualização do aplicativo é o modo monitorado, que é o comumente usado. O modo monitorado executa a atualização em um domínio de atualização, e se todas as verificações de integridade forem aprovadas (pela política especificada), ele passa para o próximo domínio de atualização automaticamente.  Se as verificações de integridade falharem e/ou o tempo limite for atingido, a atualização será revertida para o domínio de atualização ou o modo será alterado para manual não monitorado. Você pode configurar a atualização para escolher um desses dois modos para atualizações que falharam. 

O manual não monitorado precisaria de intervenção manual após cada atualização em um domínio de atualização, a fim de disparar a atualização no domínio de atualização seguinte. Nenhuma verificação de integridade do Service Fabric é executada. O administrador executa as verificações de integridade ou de status antes de iniciar a atualização no próximo domínio de atualização.

## <a name="upgrade-default-services"></a>Fazer upgrade dos serviços padrão
Os serviços padrão no aplicativo Service Fabric podem ser atualizados durante o processo de upgrade de um aplicativo. Os serviços padrão são definidos no [manifesto do aplicativo](service-fabric-application-model.md#describe-an-application). As regras padrão de upgrade dos serviços padrão são:

1. Os serviços padrão no novo [manifesto do aplicativo](service-fabric-application-model.md#describe-an-application) que não existem no cluster são criados.
> [!TIP]
> [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) precisa ser definido como true para habilitar as regras a seguir. Esse recurso é compatível com a v5.5.

2. Os serviços padrão existentes no [manifesto do aplicativo](service-fabric-application-model.md#describe-an-application) anterior e na nova versão são atualizados. As descrições de serviço na nova versão substituem aquelas que já estão no cluster. A atualização do aplicativo é revertida automaticamente na falha de atualização do serviço padrão.
3. Os serviços padrão no [manifesto do aplicativo](service-fabric-application-model.md#describe-an-application) anterior, mas não na nova versão, são excluídos. **Observe que essa exclusão de serviços padrão não pode ser revertida.**

Caso o upgrade de um aplicativo seja revertido, os serviços padrão serão revertidos para o status antes do início do upgrade. Mas os serviços excluídos nunca poderão ser criados.

## <a name="application-upgrade-flowchart"></a>Fluxograma de atualização de aplicativo
O fluxograma após este parágrafo pode ajudar na compreensão do processo de atualização de um aplicativo do Service Fabric. Mais especificamente, o fluxo descreve como os tempos limite, incluindo *HealthCheckStableDuration*, *HealthCheckRetryTimeout* e *UpgradeHealthCheckInterval*, ajudam a controlar quando a atualização em um domínio de atualização é considerada um êxito ou falha.

![O processo de atualização de um aplicativo a Malha do Serviço][image]

## <a name="next-steps"></a>Próximas etapas
[Atualização do aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante a atualização de aplicativo usando o Visual Studio.

[Atualização do aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

Controle como seu aplicativo é atualizado usando [Parâmetros de Atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [Serialização de Dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
