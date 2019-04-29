---
title: Atualizar um cluster autônomo do Fabric Service do Azure|Microsoft Docs
description: Aprenda sobre como atualizar a versão ou a configuração de um cluster autônomo do Azure Service Fabric.  T
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 1d96a2e81917af5e80bb847ea25610ccb71ad70f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711043"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Atualizando e atualizando um cluster independente do Service Fabric

Para qualquer sistema moderno, oferecer a possibilidade de atualização é fundamental para o sucesso duradouro de seu produto. Um cluster independente do Azure Service Fabric é um recurso que você possui. Este artigo descreve o que pode ser atualizado ou atualizado.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão de malha que é executada em seu cluster
Certifique-se de que o cluster sempre esteja executando uma [versão do Service Fabric com suporte](service-fabric-versions.md). Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior é programada para encerrar sua vida útil após um mínimo de 60 dias a partir da data do comunicado. Novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nesse momento, a nova versão está disponível para escolha.

Você pode configurar seu cluster para receber upgrades automáticos de fabric à medida que eles são liberados pela Microsoft ou você pode selecionar manualmente uma versão de estrutura suportada na qual deseja que seu cluster esteja. Para obter mais informações, leia [Atualize a versão do Service Fabric que é executada em seu cluster](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Personalizar as definições de configuração

Muitas [definições de configuração](service-fabric-cluster-manifest.md) diferentes podem ser configuradas no arquivo *ClusterConfig.json*, como o nível de confiabilidade das propriedades do cluster e do nó.  Para saber mais, leia [Atualize a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md).  Muitas outras configurações mais avançadas também podem ser personalizadas.  Para obter mais informações, leia as [ configurações da malha do cluster do Service Fabric ](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definir propriedades de nó
Às vezes, você pode querer garantir que determinadas cargas de trabalho sejam executadas apenas em determinados tipos de nós no cluster. Por exemplo, algumas cargas de trabalho podem exigir GPUs ou SSDs, enquanto outras, não. Para cada um dos tipos de nós em um cluster, você pode adicionar propriedades de nós customizados aos nós do cluster. As restrições de posicionamento são as instruções anexadas a serviços individuais que selecionam uma ou mais propriedades do nó. Restrições de posicionamento definem onde os serviços devem ser executados.

Para obter detalhes sobre o uso de restrições de posicionamento, propriedades do nó e como defini-las, leia [propriedades do nó e restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Adicionar métricas de capacidade
Para cada um dos tipos de nó, é possível adicionar métricas de capacidade personalizadas que você deseja usar em seus aplicativos para relatar a carga. Para obter detalhes sobre o uso de métricas de capacidade para relatar carga, confira os documentos do Gerenciador de Recursos do cluster do Service Fabric em [Descrevendo seu cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [Métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Corrigir o sistema operacional nos nós do cluster
O aplicativo de orquestração de patch (POA) é um aplicativo do Service Fabric que automatiza a correção do sistema operacional em um cluster do Service Fabric sem tempo de inatividade. O [Aplicativo de Orquestração de Patches para Windows](service-fabric-patch-orchestration-application.md) pode ser implantado em seu cluster para instalar os patches de maneira orquestrada, mantendo os serviços disponíveis o tempo todo. 


## <a name="next-steps"></a>Próximas etapas
* Saiba como personalizar algumas das [configurações de malha do cluster do Service Fabric](service-fabric-cluster-fabric-settings.md)
* Saiba como [reduzir e escalar horizontalmente seu cluster](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
