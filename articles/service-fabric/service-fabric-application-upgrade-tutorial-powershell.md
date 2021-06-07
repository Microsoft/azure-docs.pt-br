---
title: Service Fabric a atualização do aplicativo usando o PowerShell
description: Este artigo descreve a experiência de implantação de um aplicativo do Service Fabric, de alteração do código e de distribuição de uma atualização usando o PowerShell.
ms.topic: conceptual
ms.date: 8/5/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b038f4a41ee02960a02c4445b65eb2360a75761
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98792024"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Atualização do aplicativo do Service Fabric usando o PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

O mais usado e o método de atualização recomendado é a atualização sem interrupção monitorada.  O Service Fabric do Azure monitora a integridade do aplicativo que está sendo atualizado com base em um conjunto de políticas de integridade. Quando um UD (domínio de atualização) for atualizado, o Service Fabric avaliará a integridade do aplicativo e prosseguirá para o próximo domínio de atualização ou retornará um erro para a atualização dependendo das políticas de integridade.

Uma atualização do aplicativo monitorada pode ser executada usando o gerenciado ou nativo APIs, PowerShell, CLI do Azure, Java ou REST. Para obter instruções sobre como executar uma atualização usando o Visual Studio, confira [Atualização do aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md).

Com as atualizações monitoradas sem interrupção do Service Fabric, o administrador de aplicativos pode configurar a política de avaliação de integridade que o Service Fabric usa para determinar se o aplicativo está íntegro. Além disso, o administrador pode configurar a ação a ser executada quando a avaliação de integridade falhar (por exemplo, fazendo uma reversão automática). Esta seção percorre uma atualização monitorada para um dos exemplos de SDK que usa o PowerShell. 

> [!NOTE]
> Os [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s não são preservados em uma atualização de aplicativo. Para preservar os parâmetros do aplicativo atual, o usuário deve primeiro obter os parâmetros e passá-los para a chamada à API de atualização, como a seguir:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Etapa 1: criar e implantar o exemplo de Objetos Visuais
Crie e publique o aplicativo clicando com o botão direito do mouse no projeto do aplicativo, **VisualObjectsApplication** e selecionando o comando **Publicar**.  Para obter mais informações, confira o [Tutorial de atualização de aplicativos do Service Fabric](service-fabric-application-upgrade-tutorial.md).  Se preferir, você pode usar o PowerShell para implantar o seu aplicativo.

> [!NOTE]
> Antes de qualquer um dos comandos do Service Fabric poder ser usado no PowerShell, você precisa primeiro conectar ao cluster usando o cmdlet `Connect-ServiceFabricCluster`. Da mesma forma, supõe-se que o Cluster já esteja configurado no computador local. Consulte o artigo [configurando o ambiente de desenvolvimento de Malha do serviço](service-fabric-get-started.md).
> 
> 

Depois de criar o projeto no Visual Studio, você pode usar o comando do PowerShell [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) para copiar o pacote de aplicativos para o ImageStore. Se quiser verificar o pacote do aplicativo localmente, use o cmdlet [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage). A próxima etapa é registrar o aplicativo no runtime do Service Fabric usando o cmdlet [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype). A próxima etapa é iniciar uma instância do aplicativo usando o cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication).  Estas três etapas são semelhantes a usar o item de menu **Implantar** no Visual Studio.  Após a conclusão do provisionamento, você deverá limpar o pacote de aplicativos copiado do armazenamento de imagem para reduzir os recursos consumidos.  Se um tipo de aplicativo não for mais necessário, seu registro deverá ser cancelado pelo mesmo motivo. Veja [Implantar e remover aplicativos usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) para saber mais.

Agora, você pode usar o [Gerenciador de Malha do serviço para exibir o cluster e o aplicativo](service-fabric-visualizing-your-cluster.md). O aplicativo tem um serviço Web que pode ser acessado no Internet Explorer, digitando `http://localhost:8081/visualobjects` na barra de endereços.  Você deve ver alguns objetos visuais flutuantes moverem-se na tela.  Além disso, você pode usar [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) para verificar o status do aplicativo.

## <a name="step-2-update-the-visual-objects-sample"></a>Etapa 2: atualizar o exemplo de Objetos Visuais
Você pode notar que a com a versão implantada na Etapa 1, os objetos visuais não giram. Vamos atualizar esse aplicativo para um onde os objetos visuais possam girar.

Selecione o projeto VisualObjects.ActorService dentro da solução VisualObjects e abra o arquivo StatefulVisualObjectActor.cs. Nesse arquivo, navegue até o método `MoveObject`, desmarque o comentário `this.State.Move()` e remova os comentários `this.State.Move(true)`. Essa alteração gira os objetos após a atualização do serviço.

Também precisamos atualizar o arquivo *ServiceManifest.xml* (em PackageRoot) do projeto **VisualObjects.ActorService**. Atualize o *CodePackage* e a versão de serviço para 2.0, bem como as linhas correspondentes no arquivo *ServiceManifest.xml*.
Você pode usar a opção *Editar Arquivos de Manifesto* do Visual Studio depois de clicar com o botão direito na solução para fazer as alterações no arquivo de manifesto.

Depois que as alterações forem feitas, o manifesto deverá se parecer com o seguinte (as partes realçadas mostram as alterações):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Agora, o arquivo *ApplicationManifest.xml* (localizado no projeto **VisualObjects** na solução **VisualObjects**) é atualizado para a versão 2.0 do projeto **VisualObjects.ActorService**. Além disso, a versão do aplicativo é atualizada para 2.0.0.0 de 1.0.0.0. O *ApplicationManifest.xml* deve se parecer com o seguinte snippet:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Agora, compile o projeto selecionando apenas o projeto **ActorService**, clicando com o botão direito do mouse e selecionando **Compilar** no Visual Studio. Se você selecionar **Recompilar todos**, deverá atualizar as versões de todos os projetos, pois o código pode ter sido alterado. Em seguida, vamos empacotar o aplicativo atualizado clicando com o botão direito do mouse em **_VisualObjectsApplication_*_, selecionando o menu Service Fabric e escolhendo _* Package**. Essa ação cria um pacote de aplicativos que pode ser implantado.  Seu aplicativo atualizado está pronto para ser implantado.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Etapa 3: decida sobre diretivas de integridade e parâmetros de atualização
Familiarize-se com os [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) e o [processo de atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, os tempos limite e o critério de integridade aplicados. Para este passo a passo, o critério de avaliação de integridade do serviço é definido como o padrão (e valores recomendados), o que significa que todos os serviços e as instâncias devem estar *íntegros* após a atualização.  

Vamos, no entanto, aumentar *HealthCheckStableDuration* para 180 segundos (de modo que os serviços estejam íntegros pelo menos 120 segundos antes que a atualização prossiga para o próximo domínio de atualização).  Vamos definir também *UpgradeDomainTimeout* para 1.200 segundos e *UpgradeTimeout* para 3.000 segundos.

Por fim, vamos definir também *UpgradeFailureAction* para reversão. Essa opção exige que o Service Fabric reverta o aplicativo para a versão anterior caso encontre problemas durante a atualização. Portanto, ao iniciar a atualização (na etapa 4), os seguintes parâmetros são especificados:

FailureAction = Rollback

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Etapa 4: preparar o aplicativo para atualização
Agora, o aplicativo está compilado e pronto para ser atualizado. Se você abrir uma janela do PowerShell como administrador e digitar [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication), será informado de que o aplicativo tipo 1.0.0.0 do **VisualObjects** foi implantado.  

O pacote de aplicativos é armazenado no seguinte caminho relativo onde você descompactou o SDK de Service Fabric - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Você deve localizar uma pasta "Pacote" nesse diretório, no qual o pacote de aplicativos está armazenado. Verifique os carimbos de data/hora para garantir que é o build mais recente (talvez seja necessário modificar os caminhos adequadamente também).

Agora vamos copiar o pacote de aplicativo atualizado para o ImageStore de Malha do serviço (onde os pacotes de aplicativos são armazenados pela Malha do serviço). O parâmetro *ApplicationPackagePathInImageStore* informa à Malha de serviço onde ela pode encontrar o pacote do aplicativo. Criamos o aplicativo atualizado no "VisualObjects\_V2" com o seguinte comando (pode ser que você tenha que modificar novamente os caminhos).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

A próxima etapa será registrar esse aplicativo no Service Fabric, o que pode ser feito usando o comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype):

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Caso o comando anterior não funcione, é provável que você precise recompilar todos os serviços. Conforme mencionado na Etapa 2, você terá que atualizar sua versão do serviço Web.

É recomendável que você remova o pacote de aplicativos depois que o aplicativo for registrado com êxito.  Excluir pacotes de aplicativos do repositório de imagens libera recursos do sistema.  Manter pacotes de aplicativos não utilizados consome o armazenamento em disco e leva a problemas de desempenho do aplicativo.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Etapa 5: iniciar a atualização do aplicativo
Agora podemos iniciar a atualização do aplicativo usando o comando [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


O nome do aplicativo é o mesmo descrito no arquivo *ApplicationManifest.xml* . A Malha de serviços usa esse nome para identificar qual aplicativo está sendo atualizado. Se você definir tempos limite muito curtos, poderá encontrar uma mensagem de erro informando o problema. Consulte a seção de solução de problemas ou aumente os tempos limite.

Agora, enquanto a atualização do aplicativo está sendo realizada, você pode monitorá-lo usando o Service Fabric Explorer ou usando o comando do PowerShell [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade): 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Em alguns minutos, o status recebido que usa o comando do PowerShell acima deve indicar que todos os domínios de atualização foram atualizados (concluído). E você deve notar que os objetos visuais na janela do navegador começaram a girar!

Você pode tentar atualizar da versão 2 para a versão 3 ou da versão 2 para a versão 1 como um exercício. Migrar da versão 2 para a versão 1 também é considerado uma atualização. Teste as possibilidades com políticas de integridade e tempos limite para ficar familiarizado com eles. Quando você estiver implantando um cluster do Azure, os parâmetros precisarão ser definidos corretamente. É bom definir os tempos limite de forma prudente.

## <a name="next-steps"></a>Próximas etapas
[Atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante uma atualização de aplicativo usando o Visual Studio.

Controle como seu aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).
