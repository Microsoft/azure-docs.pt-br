---
title: Colocar os microsserviços do Azure Service Fabric em contêineres no Windows
description: Como colocar em contêineres seus serviços de Reliable Services e Reliable Actors do Service Fabric no Windows.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 1210b34590484379ae487ad1b87e76a433e4582a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621816"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Colocar em contêineres seus Reliable Services e Reliable Actors do Service Fabric no Windows

O Service Fabric oferece suporte a colocação em contêineres de microsserviços do Service Fabric (serviços baseados em Reliable Services e Reliable Actors). Para saber mais, confira [contêineres do Service Fabric](service-fabric-containers-overview.md).

Este documento fornece diretrizes para colocar o serviço em execução dentro de um contêiner do Windows.

> [!NOTE]
> Atualmente, esse recurso só funciona para Windows. Para executar contêineres, o cluster deve estar em execução no Windows Server 2016 com Contêineres.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Etapas para colocar seu aplicativo do Service Fabric em contêineres

1. Abra seu aplicativo do Service Fabric no Visual Studio.

2. Adicione a classe [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) ao seu projeto. O código nessa classe é um auxiliar para carregar corretamente os binários de tempo de execução do Service Fabric dentro de seu aplicativo durante a execução dentro de um contêiner.

3. Para cada pacote de código que você quer colocar em contêineres, inicialize o carregador no ponto de entrada do programa. Adicione o construtor estático mostrado no snippet de código a seguir ao seu arquivo de ponto de entrada do programa.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Compilar e [empacotar](service-fabric-package-apps.md#Package-App) seu projeto. Para compilar e criar um pacote, clique com o botão direito do mouse no projeto de aplicativo no Gerenciador de Soluções e escolha o comando **Empacotar**.

5. Para cada pacote de códigos que você precisa colocar em contêineres, execute o script do PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). A utilização é o seguinte:

    .NET completo
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      O script cria uma pasta com artefatos do Docker em $dockerPackageOutputDirectoryPath. Modifique o Dockerfile gerado para `expose` quaisquer portas, executar scripts de configuração e assim por diante. com base em suas necessidades.

6. Em seguida você precisa [compilar](service-fabric-get-started-containers.md#Build-Containers) e [enviar por push](service-fabric-get-started-containers.md#Push-Containers) seu pacote de contêineres do Docker ao seu repositório.

7. Modifique o ApplicationManifest.xml e ServiceManifest.xml a fim de adicionar a imagem de contêiner, informações do repositório, autenticação do registro e mapeamento de porta ao host. Para modificar os manifestos, confira [Criar um aplicativo de contêiner do Azure Service Fabric](service-fabric-get-started-containers.md). A definição de pacote de códigos no manifesto do serviço precisa ser substituída pela imagem de contêiner correspondente. Altere o Ponto de Entrada para um tipo de ContainerHost.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Adicione o mapeamento de porta ao host para seu replicador e ponto de extremidade de serviço. Como essas duas portas são atribuídas em tempo de execução pelo Service Fabric, o ContainerPort é definido como zero para usar a porta atribuída para mapeamento.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Para configurar o modo de isolamento de contêiner, veja [Configurar o modo de isolamento]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). O Windows dá suporte a dois modos de isolamento para contêineres: processo e Hyper-V. O snippet a seguir mostra como o modo de isolamento é especificado no arquivo de manifesto do aplicativo.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Por padrão, os aplicativos do Service Fabric têm acesso ao tempo de execução do Service Fabric, na forma de um ponto de extremidade aceitando solicitações específicas do aplicativo. Considere desabilitar esse acesso quando o aplicativo hospeda o código não confiável. Para obter mais informações, consulte [práticas recomendadas de segurança no Service Fabric](service-fabric-best-practices-security.md#platform-isolation). Para desabilitar o acesso ao tempo de execução do Service Fabric, adicione a seguinte configuração na seção de políticas do manifesto do aplicativo que corresponde ao manifesto do serviço importado, da seguinte maneira:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Para testar esse aplicativo, você precisa implantá-lo em um cluster que esteja executando a versão 5.7 ou superior. Para a versão 6.1 do tempo de execução ou versões inferiores, você precisa editar e atualizar as configurações de cluster para habilitar essa versão prévia do recurso. Execute as etapas neste [artigo](service-fabric-cluster-fabric-settings.md) para adicionar a configuração mostrada a seguir.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. Depois, [implante](service-fabric-deploy-remove-applications.md) o pacote de aplicativos editado nesse cluster.

Agora você deve ter um aplicativo do Service Fabric em contêineres executando seu cluster.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como executar [contêineres no Service Fabric](service-fabric-get-started-containers.md).
* Leia mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) do Service Fabric.
