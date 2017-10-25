---
title: "Atualizar um cluster autônomo do Azure Service Fabric no Windows Server | Microsoft Docs"
description: "Atualize o código do Azure Service Fabric e/ou a configuração que executa um cluster do Service Fabric autônomo, incluindo a configuração de modo de atualização de cluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f141c3d22275ff04d7161415e9c9f879d85dbc08
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="upgrade-your-standalone-azure-service-fabric-on-windows-server-cluster"></a>Atualize seu cluster autônomo do Azure Service Fabric no Windows Server
> [!div class="op_single_selector"]
> * [Cluster do Azure](service-fabric-cluster-upgrade.md)
> * [Cluster Independente](service-fabric-cluster-upgrade-windows-server.md)
>
>

Para qualquer sistema moderno, a capacidade de atualização é uma chave para o sucesso de seu produto a longo prazo. Um cluster do Azure Service Fabric é um recurso que pertence a você. Este artigo descreve como garantir que o cluster sempre execute versões com suporte do código do Service Fabric e de configurações.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Controlar a versão do Service Fabric em execução no cluster
Para configurar o cluster para baixar atualizações do Service Fabric quando a Microsoft lançar uma nova versão, defina a configuração **fabricClusterAutoupgradeEnabled** do cluster para true. Para selecionar uma versão com suporte do Service Fabric em que você deseja que o seu cluster esteja, defina a configuração **fabricClusterAutoupgradeEnabled** do cluster para false.

> [!NOTE]
> Certifique-se de que o cluster sempre esteja executando uma versão do Service Fabric com suporte. Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior é programada para encerrar sua vida útil após um mínimo de 60 dias a partir da data do comunicado. Novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nesse momento, a nova versão está disponível para escolha.
>
>

Será possível atualizar o cluster para a nova versão apenas se você estiver usando uma configuração de nó de estilo de produção, em que cada nó do Service Fabric é alocado em uma máquina virtual ou física separada. Se você tiver um cluster de desenvolvimento no qual exista mais de um nó do Service Fabric em uma única máquina virtual ou física, você deverá recriar o cluster com a nova versão.

Dois fluxos de trabalho distintos podem atualizar o cluster para a versão mais recente ou uma versão do Service Fabric com suporte. Um fluxo de trabalho é para clusters com conectividade para baixar a versão mais recente automaticamente. O outro fluxo de trabalho é para clusters sem conectividade para baixar a versão mais recente do Service Fabric automaticamente.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters com conectividade para baixar o código e a configuração mais recentes
Use estas etapas para atualizar seu cluster para uma versão com suporte se os nós de cluster tiverem conectividade com a Internet para [http://download.microsoft.com](http://download.microsoft.com).

Para clusters que tenham conectividade com [http://download.microsoft.com](http://download.microsoft.com), a Microsoft verifica periodicamente a disponibilidade de novas versões do Service Fabric.

Quando uma nova versão do Service Fabric estiver disponível, o pacote será baixado localmente para o cluster e provisionado para atualização. Além de informar o cliente sobre essa nova versão, o sistema colocará um aviso explícito de integridade do cluster semelhante ao seguinte:

"O suporte à versão atual do cluster, [version#], termina em [Data]."

Depois que o cluster estiver executando a versão mais recente, o aviso será removido.

#### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização do cluster
Ao ver o aviso de integridade do cluster, você deve fazer o seguinte:

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

    ![obter versões de malha][getfabversions]
3. Inicie uma atualização de cluster para uma versão disponível usando o comando [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) do PowerShell.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Você pode monitorar o progresso da atualização no Service Fabric Explorer ou executando o comando do Windows PowerShell a seguir.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Para especificar as políticas de integridade personalizadas para o comando **Start-ServiceFabricClusterUpgrade**, consulte a documentação para [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas descritas anteriormente.

### <a name="upgrade-clusters-that-have-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters <U>sem conectividade</u> para baixar o código e a configuração mais recentes
Use estas etapas para atualizar seu cluster para uma versão com suporte se os nós de cluster não tiverem conectividade com a Internet para [http://download.microsoft.com](http://download.microsoft.com).

> [!NOTE]
> Se estiver executando um cluster que não esteja conectado à Internet, você precisará monitorar o blog da equipe do Service Fabric para saber sobre uma novo lançamento. O sistema não mostra nenhum aviso de integridade do cluster para alertá-lo a respeito.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Provisionamento automático vs provisionamento manual
Para permitir o download automático e o registro para a versão mais recente do código, configure o Serviço de Atualização do Service Fabric. Consulte Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt dentro do [Pacote Autônomo](service-fabric-cluster-standalone-package-contents.md) para obter instruções.
Para o processo manual, siga as instruções abaixo.

Modifique a configuração do cluster para definir a propriedade a seguir como false antes de iniciar uma atualização de configuração.

        "fabricClusterAutoupgradeEnabled": false,

Consulte [cmd do PS Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx) para obter detalhes de uso. Certifique-se de atualizar “clusterConfigurationVersion” no JSON antes de iniciar a atualização de configuração.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização do cluster

1. Execute Get-ServiceFabricClusterUpgrade em um de nós no cluster e observe o TargetCodeVersion.
2. Execute o seguinte de uma máquina conectada à internet para listar todas as versões compatíveis com atualização com a versão atual e baixar o pacote correspondente dos links de download associados.

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Conecte-se ao cluster de qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no cluster. O computador no qual este script é executado não precisa fazer parte do cluster

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
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Inicie uma atualização do cluster para uma versão disponível.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Você pode monitorar o progresso da atualização no Service Fabric Explorer ou executando o comando do PowerShell a seguir.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Para especificar as políticas de integridade personalizadas para o comando **Start-ServiceFabricClusterUpgrade**, consulte a documentação para [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas descritas anteriormente.


## <a name="upgrade-the-cluster-configuration"></a>Atualizar a configuração do cluster
Antes de iniciar a atualização de configuração, você pode testar seu novo json de configuração de cluster executando o script do powershell no pacote autônomo.

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
ou

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Algumas configurações não podem ser atualizadas, como pontos de extremidade, nome do cluster, nó IP, etc. Isso testará o json de configuração do cluster novo contra antigo e gerar erros na janela do Powershell, se houver qualquer problema.

Para atualizar a configuração do cluster, execute o comando **Start-ServiceFabricClusterConfigurationUpgrade**. A atualização de configuração é processada domínio de atualização por domínio de atualização.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Atualização da configuração do certificado do cluster  
O certificado do cluster é usado para autenticação entre nós de cluster, para que a distribuição do certificado seja executada com muito cuidado, pois a falha bloqueará a comunicação entre nós de cluster.  
Tecnicamente, há três opções com suporte:  

1. Atualização de um certificado: o caminho de atualização é 'Certificado A (Primário)-> Certificado B (Primário)-> Certificado C (Primário)->...'.   
2. Atualização de dois certificados: o caminho de atualização é 'Certificado A (Primário) - > Certificado A (Primário) e B (Secundário) -> Certificado B (Primário) -> Certificado B (Primário) e C (Secundário) -> Certificado C (Primário)->...'.
3. Atualização do tipo de certificado: configuração de certificados com base em CommonName configuração <-> com base em impressão digital do certificado. Por exemplo, impressão digital do certificado (primária) e a impressão digital B (secundários) -> certificado CommonName C.
4. Atualização da impressão digital do emissor do certificado: o caminho de atualização é 'Certificado CN=A,IssuerThumbprint=IT1 (Principal) -> Certificado CN=A,IssuerThumbprint=IT1,IT2 (Principal) -> Certificado CN=A,IssuerThumbprint=IT2 (Principal)'


## <a name="next-steps"></a>Próximas etapas
* Saiba como personalizar algumas das [configurações de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).
* Saiba como [reduzir e escalar horizontalmente seu cluster](service-fabric-cluster-scale-up-down.md).
* Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

