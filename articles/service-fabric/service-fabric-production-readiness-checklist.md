---
title: Lista de verificação de preparação da produção do Microsoft Azure Service Fabric| Microsoft Docs
description: Prepare o aplicativo do Service Fabric e o cluster prontos para produção seguindo as melhores práticas.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: aljo
ms.openlocfilehash: e94280f9df1d4ac59856a73f6f6c2b7f7a0b9cc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726580"
---
# <a name="production-readiness-checklist"></a>Lista de verificação de preparação para produção

O aplicativo e o cluster estão prontos para receber tráfego de produção? Executar e testar o aplicativo e o cluster não significa necessariamente que estão prontos para entrar em produção. Mantenha o aplicativo e o cluster em execução correta, analisando a lista de verificação a seguir. É altamente recomendável que todos esses itens sejam verificados. Obviamente, é possível optar por usar soluções alternativas para um item de linha específico (por exemplo, suas próprias estruturas de diagnóstico).


## <a name="pre-requisites-for-production"></a>Pré-requisitos para produção
1. As [melhores práticas de segurança do Azure Service Fabric](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices) são: 
1. Usar certificados X.509
1. Configurar políticas de segurança
1. Configurar o SSL para o Azure Service Fabric
1. Usar segurança e isolamento de rede com o Azure Service Fabric
1. Configurar o Azure Key Vault para segurança
1. Microsoft.Network/loadBalancersAtribuir usuários a funções
1. Implementar a configuração de segurança dos Reliable Actors se o modelo de programação de Atores estiver sendo usado
1. Para clusters com mais de 20 núcleos ou 10 nós, crie um tipo de nó primário dedicado para serviços do sistema. Adicione [restrições de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para reservar o tipo de nó primário para serviços do sistema.
1. Use D2v2 ou SKU superior para o tipo de nó primário. É recomendável separar uma SKU com pelo menos 50 GB de capacidade de disco rígido.
1. Os clusters de produção devem ser [seguros](service-fabric-cluster-security.md). Para um exemplo de configuração de um cluster seguro, consulte este [modelo de cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Use nomes comuns para certificados e evite usar certificados autoassinados.
1. Adicione [restrições de recursos em contêineres e serviços](service-fabric-resource-governance.md), de modo que não consumam mais de 75% dos recursos do nó. 
1. Reconheça e defina o [nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). É recomendável o nível de durabilidade prata ou superior para tipos de nó que executam cargas de trabalho com estado. O tipo de nó primário deve ter um nível de durabilidade definido como Prata ou superior.
1. Reconheça e escolha o [nível de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) do tipo de nó. É recomendável confiabilidade prata ou superior.
1. Carregue e dimensione o teste das cargas de trabalho para identificar os [requisitos de capacidades](service-fabric-cluster-capacity.md) para o cluster. 
1. Os serviços e aplicativos são monitorados e os logs dos aplicativos estão sendo gerados e armazenados, com alertas. Por exemplo, consulte [Adicionar log ao seu aplicativo do Service Fabric](service-fabric-how-to-diagnostics-log.md) e [monitorar contêineres com os logs do Azure Monitor](service-fabric-diagnostics-oms-containers.md).
1. O cluster é monitorado com o alerta (por exemplo, com [registra em log do Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)). 
1. A infraestrutura de conjunto de dimensionamento de máquina virtual subjacente é monitorada com o alerta (por exemplo, com [registra em log do Azure Monitor](service-fabric-diagnostics-oms-agent.md).
1. O cluster tem [certificados primários e secundários](service-fabric-cluster-security-update-certs-azure.md) sempre (para que você não seja bloqueado).
1. Manter clusters separados para desenvolvimento, preparação e produção. 
1. [Upgrades de aplicativos](service-fabric-application-upgrade.md) e [upgrades de cluster](service-fabric-tutorial-upgrade-cluster.md) primeiro são testados em clusters de preparo e desenvolvimento. 
1. Desative upgrades automáticos em clusters de produção e ative-o para clusters de processo de preparo e desenvolvimento (reversão, conforme necessário). 
1. Estabeleça um RPO (Objetivo de Ponto de Recuperação) para o serviço e configure um [processo de recuperação de desastre](service-fabric-disaster-recovery.md) e teste-o.
1. Planeje a [colocação em escala](service-fabric-cluster-scaling.md) do cluster manualmente ou programaticamente.
1. Planeje a [aplicação de patch](service-fabric-patch-orchestration-application.md) dos nós do cluster. 
1. Estabeleça um pipeline de CI/CD para que as alterações mais recentes sejam testadas continuamente. Por exemplo, usando [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) ou [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Teste os clusters de preparo e desenvolvimento sob carga com o [Serviço de Análise de Falhas](service-fabric-testability-overview.md) e induza [caos](service-fabric-controlled-chaos.md) controlado. 
1. Planeje a [colocação em escala](service-fabric-concepts-scalability.md) dos aplicativos. 


Se estiver usando o modelo de programação Reliable Actors ou Reliable Services do Service Fabric, os seguintes itens devem ser verificados:
1. Faça upgrade dos aplicativos durante o desenvolvimento local para verificar se o código de serviço está respeitando o token de cancelamento no método `RunAsync` e fechando ouvintes de comunicação personalizados.
1. Evite [armadilhas comuns](service-fabric-work-with-reliable-collections.md) ao usar Reliable Collections.
1. Monitore os contadores de desempenho de memória de CLR do .NET ao executar testes de carga e verifique as altas taxas de Coleta de Lixo ou crescimento de heap sem controle.
1. Mantenha o backup de [Reliable Services e Reliable Actors](service-fabric-reliable-services-backup-restore.md) offline e teste o processo de restauração.
1. Sua contagem de instâncias de máquina virtual NodeType primária deve idealmente ser igual ao mínimo para sua camada de Confiabilidade de Clusters; quando apropriado, as condições para exceder o mínimo de camada incluem: temporariamente ao escalar verticalmente seu SKU de conjunto de dimensionamento de máquinas virtuais NodeTypes.

## <a name="optional-best-practices"></a>Melhores práticas opcionais

Embora as listas acima sejam pré-requisitos para entrar em produção, os seguintes itens também devem ser considerados:
1. Conecte o [modelo de integridade do Service Fabric](service-fabric-health-introduction.md) para estender a avaliação e os relatórios de integridade internos.
1. Implante um watchdog personalizado que está monitorando o aplicativo e [carregue](service-fabric-cluster-resource-manager-metrics.md) relatórios para [balanceamento de recursos](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Próximas etapas
* [Implantar um cluster do Windows do Service Fabric](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Implantar um cluster do Linux do Service Fabric](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Leia mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) do Service Fabric.
