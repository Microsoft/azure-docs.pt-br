---
title: Ciclo de vida do aplicativo no Service Fabric | Microsoft Docs
description: "Descreve o desenvolvimento, implantação, testes, atualização, manutenção e remoção de aplicativos da Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/29/2017
ms.author: ryanwi
ms.openlocfilehash: d892c5979d0dec54767fe5a9ef84b05739683604
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida do aplicativo Service Fabric
Semelhante a outras plataformas, um aplicativo no Azure Service Fabric geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. O Service Fabric dá um excelente suporte ao ciclo de vida completo dos aplicativos em nuvem, desde o desenvolvimento até a implantação, gerenciamento diário, manutenção e possível encerramento. O modelo de serviço permite que várias funções diferentes participem do ciclo de vida do aplicativo de forma independente. Este artigo fornece uma visão geral das APIs e como elas são usadas pelas diferentes funções em todas as fases do ciclo de vida de um aplicativo da Malha do Serviço.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

O vídeo da Microsoft Virtual Academy a seguir descreve como gerenciar o ciclo de vida do aplicativo:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Funções do modelo de serviço
As funções do modelo de serviço são:

* **Desenvolvedor de serviço**: desenvolve serviços modulares e genéricos que podem ser redefinidos e usados em vários aplicativos do mesmo tipo ou de tipos diferentes. Por exemplo, um serviço de fila pode ser usado para criar um aplicativo de emissão de tíquetes (assistência técnica) ou um aplicativo de comércio eletrônico (carrinho de compras).
* **Desenvolvedor de aplicativos**: cria aplicativos integrando uma coleção de serviços para atender a determinados requisitos ou cenários. Por exemplo, um site de comércio eletrônico pode integrar o “Serviço de Front-End JSON sem Monitoração de Estado”, "Serviço de Monitoração de Estado do Leilão" e "Serviço de Monitoração de Estado da Fila" para criar uma solução de leilão.
* **Administrador do aplicativo**: toma decisões sobre a configuração do aplicativo (preenchendo os parâmetros de configuração do modelo), implantação (mapeamento os recursos disponíveis) e qualidade do serviço. Por exemplo, um administrador do aplicativo decide a localidade do idioma do aplicativo (inglês para os EUA ou japonês para o Japão, por exemplo). Outro aplicativo implantado pode ter configurações diferentes.
* **Operador**: implanta os aplicativos baseados na configuração do aplicativo e nos requisitos especificados pelo administrador do aplicativo. Por exemplo, um operador provisiona, implanta o aplicativo e verifica se ele está em execução no Azure. Os operadores de monitoram as informações de integridade e de desempenho do aplicativo e mantêm a infra-estrutura física conforme necessário.

## <a name="develop"></a>Desenvolver
1. Um *desenvolvedor de serviço* desenvolve tipos diferentes de serviços usando o modelo de programação [Reliable Actors](service-fabric-reliable-actors-introduction.md) ou [Reliable Services](service-fabric-reliable-services-introduction.md).
2. Um *desenvolvedor de serviço* descreve declarativamente os tipos de serviço desenvolvidos em um arquivo de manifesto do serviço que consiste em um ou mais pacotes de código, de configuração e de dados.
3. Um *desenvolvedor de aplicativos* cria um aplicativo usando diferentes tipos de serviço.
4. Um *desenvolvedor de aplicativos* descreve declarativamente o tipo de aplicativo em um manifesto de aplicativo referenciando os manifestos do serviço dos serviços membros e substituindo e parametrizando adequadamente as diferentes configurações de implantação e configurações dos serviços membros.

Confira [Introdução aos Reliable Actors](service-fabric-reliable-actors-get-started.md) e [Introdução aos Reliable Services](service-fabric-reliable-services-quick-start.md) para obter exemplos.

## <a name="deploy"></a>Implantar
1. Um *administrador do aplicativo* personaliza o tipo de aplicativo para um aplicativo específico a ser implantado para um cluster Service Fabric especificando os parâmetros apropriados do elemento **ApplicationType** no manifesto do aplicativo.
2. Um *operador* carrega o pacote de aplicativos para o armazenamento de imagens do cluster usando o [**método CopyApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) ou o cmdlet [**Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de aplicativo contém o manifesto do aplicativo e a coleção de pacotes de serviço. O Service Fabric implanta os aplicativos do pacote de aplicativos colocado no armazenamento de imagens, que pode ser um armazenamento de blobs do Azure ou o serviço do sistema Service Fabric.
3. O *operador*, então, configura o tipo de aplicativo no cluster de destino do pacote de aplicativos carregado usando o [**método ProvisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**Register-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) ou a operação REST [**Provisionar um aplicativo**](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Após a configuração do aplicativo, um *operador* inicia o aplicativo com os parâmetros fornecidos pelo *administrador do aplicativo* usando o [**método CreateApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CreateApplicationAsync_System_Fabric_Description_ApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**New-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication) ou a operação REST [**Criar Aplicativo**](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Depois de o aplicativo ser implantado, um *operador* usa o método [**CreateServiceAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_CreateServiceAsync_System_Fabric_Description_ServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**New-ServiceFabricService**](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice) ou a operação REST [**Criar Serviço para criar novas instâncias de serviço**](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) para o aplicativo com base nos tipos de serviço disponíveis.
6. O aplicativo está sendo executado no cluster da Malha do Serviço.

Confira [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="test"></a>Teste
1. Depois de implantar o cluster de desenvolvimento local ou um cluster de teste, um *desenvolvedor de serviço* executa o cenário de teste de failover interno usando as classes [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters#System_Fabric_Testability_Scenario_FailoverTestScenarioParameters) e [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario#System_Fabric_Testability_Scenario_FailoverTestScenario) ou o cmdlet [**Invoke-ServiceFabricFailoverTestScenario**](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). O cenário de teste de failover executa um determinado serviço através de transições e failovers importantes para garantir que ainda estará disponível e funcionando.
2. O *desenvolvedor de serviço* executa o cenário de teste de caos interno usando as classes [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters#System_Fabric_Testability_Scenario_ChaosTestScenarioParameters) e [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) ou o cmdlet [**Invoke-ServiceFabricChaosTestScenario**](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). O cenário de teste caos induz vários nós, pacote de código e falhas de réplica aleatoriamente no cluster.
3. O *desenvolvedor de serviço* [testa a comunicação entre serviços](service-fabric-testability-scenarios-service-communication.md) por meio da criação de cenários de teste que movem réplicas primárias pelo cluster.

Consulte [Introduction to the Fault Analysis Service (Introdução ao Fault Analysis Service)](service-fabric-testability-overview.md) para obter mais informações.

## <a name="upgrade"></a>Atualizar
1. Um *desenvolvedor de serviço* atualiza os serviços membros do aplicativo instanciado e/ou corrige erros e fornece uma nova versão do manifesto do serviço.
2. Um *desenvolvedor de aplicativos* substitui e parametriza as configurações de implantação e configuração dos serviços membros e fornece uma nova versão do manifesto do aplicativo. O desenvolvedor do aplicativo incorpora as novas versões dos manifestos do serviço no aplicativo e fornece uma nova versão do tipo de aplicativo em um pacote de aplicativo atualizado.
3. Um *administrador do aplicativo* incorpora a nova versão do tipo de aplicativo no aplicativo de destino atualizando os parâmetros apropriados.
4. Um *operador* carrega o pacote de aplicativos atualizado no armazenamento de imagens de cluster usando o método [**CopyApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) ou o cmdlet [**Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). O pacote de aplicativo contém o manifesto do aplicativo e a coleção de pacotes de serviço.
5. Um *operador* configura a nova versão do aplicativo no cluster de destino usando o método [**ProvisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**Register-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) ou a operação REST [**Configurar um Aplicativo**](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. Um *operador* atualiza o aplicativo de destino para a nova versão usando o método [**UpgradeApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpgradeApplicationAsync_System_Fabric_Description_ApplicationUpgradeDescription_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**Start-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade) ou a operação REST [**Atualizar um Aplicativo**](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. Um *operador* verifica o progresso de atualização usando o método [**GetApplicationUpgradeProgressAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_GetApplicationUpgradeProgressAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) ou a operação REST [**Obter Progresso de Atualização do Aplicativo**](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Se necessário, o *operador* modifica e reaplica os parâmetros da atualização do aplicativo atual usando o método [**UpdateApplicationUpgradeAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpdateApplicationUpgradeAsync_System_Fabric_Description_ApplicationUpgradeUpdateDescription_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**Update-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/update-servicefabricapplicationupgrade) ou a operação REST [**Fazer Atualização do Aplicativo**](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Se necessário, o *operador* reverte a atualização do aplicativo atual usando o método [**RollbackApplicationUpgradeAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RollbackApplicationUpgradeAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**Start-ServiceFabricApplicationRollback**](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationrollback) ou a operação REST [**Reverter Atualização do Aplicativo**](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. A Malha do Serviço atualiza o aplicativo de destino em execução no cluster sem perder a disponibilidade de qualquer um dos seus serviços membros.

Consulte [Tutorial sobre a atualização do aplicativo](service-fabric-application-upgrade-tutorial.md) para obter exemplos.

## <a name="maintain"></a>Manter
1. Para patches e atualizações do sistema operacional, a Malha do Serviço faz a interface com a infraestrutura do Azure para garantir a disponibilidade de todos os aplicativos executados no cluster.
2. Para ver as atualizações e as correções para a plataforma Service Fabric, o Service Fabric se atualiza automaticamente sem perder a disponibilidade de qualquer aplicativo em execução no cluster.
3. Um *administrador do aplicativo* aprova a adição ou remoção de nós de um cluster depois de analisar os dados do histórico de utilização da capacidade e demanda futura projetada.
4. Um *operador* adiciona e remove nós especificados pelo *administrador do aplicativo*.
5. Quando novos nós são adicionados ou os nós existentes são removidos do cluster, o Service Fabric balanceia a carga automaticamente dos aplicativos em execução em todos os nós do cluster para obter o desempenho ideal.

## <a name="remove"></a>Remover
1. Um *operador* pode excluir uma instância específica de um serviço em execução no cluster sem remover o aplicativo inteiro usando o método [**DeleteServiceAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_DeleteServiceAsync_System_Fabric_Description_DeleteServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**Remove-ServiceFabricService**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricservice) ou a operação REST [**Excluir Serviço**](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Um *operador* também pode excluir uma instância do aplicativo e todos os serviços usando o método [**DeleteApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_DeleteApplicationAsync_System_Fabric_Description_DeleteApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**Remove-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication) ou a operação REST [**Excluir Aplicativo**](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Depois de os aplicativos e serviços serem interrompidos, o *operador* pode desconfigurar o tipo de aplicativo usando o método [**UnprovisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UnprovisionApplicationAsync_System_String_System_String_System_TimeSpan_System_Threading_CancellationToken_), o cmdlet [**Unregister-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) ou a operação REST [**Desconfigurar um Aplicativo**](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Desconfigurar o tipo de aplicativo não remove o pacote de aplicativos do ImageStore. Você deve remover manualmente o pacote de aplicativos.
4. Um *operador* remove o pacote de aplicativos do ImageStore usando o método [**RemoveApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RemoveApplicationPackage_System_String_System_String_) ou o cmdlet [**Remove-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Confira [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o desenvolvimento, o teste e o gerenciamento de aplicativos e serviços da Malha do Serviço, confira:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)
* [Atualização do aplicativo](service-fabric-application-upgrade.md)
* [Visão geral da Possibilidade de Teste](service-fabric-testability-overview.md)
