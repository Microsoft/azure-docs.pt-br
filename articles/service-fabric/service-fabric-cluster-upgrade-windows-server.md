---
title: Atualizar a versão de um cluster autônomo do Azure Service Fabric | Microsoft Docs
description: Atualize o código do Azure Service Fabric que executa um cluster do Service Fabric autônomo.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 29d034be5999d0bc3f0a244cfa7a5658a4ecce32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711350"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Atualize a versão do Service Fabric que é executada em seu cluster 

Para qualquer sistema moderno, a capacidade de atualização é fundamental para o sucesso de seu produto a longo prazo. Um cluster do Azure Service Fabric é um recurso que pertence a você. Este artigo descreve como atualizar a versão do Service Fabric em execução no seu cluster autônomo.

> [!NOTE]
> Certifique-se de que o cluster sempre esteja executando uma versão do Service Fabric com suporte. Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior é programada para encerrar sua vida útil após um mínimo de 60 dias a partir da data do comunicado. Novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nesse momento, a nova versão está disponível para escolha.
>
>

Será possível atualizar o cluster para a nova versão apenas se você estiver usando uma configuração de nó de estilo de produção, em que cada nó do Service Fabric será alocado em uma máquina virtual ou física separada. Se você tiver um cluster de desenvolvimento no qual exista mais de um nó do Service Fabric em uma única máquina virtual ou física, você deverá recriar o cluster com a nova versão.

Dois fluxos de trabalho distintos podem atualizar o cluster para a versão mais recente ou uma versão do Service Fabric com suporte. Um fluxo de trabalho é para clusters com conectividade para baixar a versão mais recente automaticamente. O outro fluxo de trabalho é para clusters sem conectividade para baixar a versão mais recente do Service Fabric automaticamente.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Ativar a atualização automática da versão do Service Fabric do cluster
Para configurar seu cluster para fazer o download das atualizações do Service Fabric quando a Microsoft lança uma nova versão, defina a configuração do cluster `fabricClusterAutoupgradeEnabled` como *verdadeiro*. Para selecionar manualmente uma versão suportada do Service Fabric na qual você deseja que seu cluster esteja, defina a configuração do cluster `fabricClusterAutoupgradeEnabled` como *falso*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters com conectividade para baixar o código e a configuração mais recentes
Use estas etapas para atualizar seu cluster para uma versão com suporte se os nós de cluster tiverem conectividade com a Internet para acesso ao [Centro de Download da Microsoft](https://download.microsoft.com).

Para clusters que tenham conectividade com o [Centro de Download da Microsoft](https://download.microsoft.com), a Microsoft verifica periodicamente a disponibilidade de novas versões do Service Fabric.

Quando uma nova versão do Service Fabric estiver disponível, o pacote será baixado localmente para o cluster e provisionado para atualização. Além de informar o cliente sobre essa nova versão, o sistema colocará um aviso explícito de integridade do cluster semelhante ao seguinte:

"O suporte à versão atual do cluster, [version#], termina em [data]."

Depois que o cluster estiver executando a versão mais recente, o aviso será removido.

Quando você vir o aviso de integridade do cluster, atualize o cluster:

1. Conecte-se ao cluster de qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obtenha a lista de versões do Service Fabric para as quais você pode atualizar.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Você deverá receber uma saída semelhante a esta:

    ![Obter versões do Service Fabric][getfabversions]
3. Inicie uma atualização de cluster para uma versão disponível usando o comando [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) do Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Você pode monitorar o progresso da atualização no Service Fabric Explorer ou executando o comando do PowerShell a seguir:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Para especificar as políticas de integridade personalizadas para o comando Start-ServiceFabricClusterUpgrade, consulte a documentação para [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas descritas anteriormente.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters *sem conectividade* para baixar o código e a configuração mais recentes
Use estas etapas para atualizar seu cluster para uma versão com suporte, se os nós de cluster não tiverem conexão com a Internet para acessar o [Centro de Download da Microsoft](https://download.microsoft.com).

> [!NOTE]
> Se você estiver executando um cluster que não esteja conectado à Internet, será necessário monitorar o [blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para saber mais sobre novos lançamentos. O sistema não mostra nenhum aviso de integridade do cluster para alertá-lo sobre novos lançamentos.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Provisionamento automático vs provisionamento manual
Para permitir o download automático e o registro para a versão mais recente do código, configure o Serviço de Atualização do Service Fabric. Para obter instruções, consulte *Tools \ ServiceFabricUpdateService.zip \ Readme_InstructionsAndHowTos.txt* no [pacote autônomo](service-fabric-cluster-standalone-package-contents.md).

Para o processo manual, siga estas instruções.

Modifique a configuração do cluster para definir a propriedade a seguir como *false* antes de iniciar uma atualização de configuração:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Para obter detalhes de uso, consulte o [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) comando do PowerShell. Certifique-se de atualizar “clusterConfigurationVersion” no JSON antes de iniciar a atualização de configuração.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização do cluster

1. Execute [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) de um de nós no cluster e observe o *TargetCodeVersion*.

2. Execute o seguinte de uma máquina conectada à internet para listar todas as versões compatíveis com atualização com a versão atual e baixar o pacote correspondente dos links de download associados:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Conecte-se ao cluster de qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Copie o pacote baixado para o repositório de imagens do cluster.

5. Registre o pacote copiado.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Inicie uma atualização do cluster para uma versão disponível.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Você pode monitorar o progresso da atualização no Service Fabric Explorer ou executando o comando do PowerShell a seguir:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Para especificar as políticas de integridade personalizadas para o comando Start-ServiceFabricClusterUpgrade, consulte a documentação para [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas descritas anteriormente.

## <a name="next-steps"></a>Próximas etapas
* [Atualize a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md)
* Personalize algumas configurações de cluster do [Service Fabric](service-fabric-cluster-fabric-settings.md).
* [Dimensionar o cluster e escalar horizontalmente](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
