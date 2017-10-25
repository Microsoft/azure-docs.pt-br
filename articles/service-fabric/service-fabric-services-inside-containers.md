---
title: "Como colocar em contêineres os microsserviços do Azure Service Fabric (versão prévia)"
description: "O Azure Service Fabric adicionou novas funcionalidades para colocar em contêineres seus microsserviços do Service Fabric. Esse recurso está atualmente na visualização."
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: 6f8ad0bad8d1ae861e6b72f7e1a32ab0675813c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Como colocar em contêineres seus Reliable Services e Reliable Actors do Service Fabric (versão prévia)

O Service Fabric oferece suporte a colocação em contêineres de microsserviços do Service Fabric (serviços baseados em Reliable Services e Reliable Actors). Para saber mais, confira [contêineres do Service Fabric](service-fabric-containers-overview.md).


 Este recurso está em versão prévia, e este artigo fornece as várias etapas para executar seu serviço dentro de um contêiner.  

> [!NOTE]
> Esse recurso está na versão prévia e não tem suporte. Atualmente, esse recurso só funciona para Windows.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Etapas para colocar seu aplicativo do Service Fabric em contêineres

1. Abra seu aplicativo do Service Fabric no Visual Studio.

2. Adicione a classe [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) ao seu projeto. O código nessa classe é um auxiliar para carregar corretamente os binários de tempo de execução do Service Fabric dentro de seu aplicativo durante a execução dentro de um contêiner.

3. Para cada pacote de código que você quer colocar em contêineres, inicialize o carregador no ponto de entrada do programa. Adicione o construtor estático mostrado no trecho de código a seguir ao seu arquivo de ponto de entrada do programa.

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
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  O script cria uma pasta com artefatos do Docker em $dockerPackageOutputDirectoryPath. Modifique o Dockerfile gerado para expor as portas, executar scripts de configuração etc. com base em suas necessidades.

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

9. Para testar esse aplicativo, você precisa implantá-lo em um cluster que esteja executando a versão 5.7 ou superior. Além disso, você precisa editar e atualizar as configurações de cluster para habilitar esse recurso de visualização. Execute as etapas neste [artigo](service-fabric-cluster-fabric-settings.md) para adicionar a configuração mostrada a seguir.
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
10. Depois, [implante](service-fabric-deploy-remove-applications.md) o pacote de aplicativos editado nesse cluster.

Agora você deve ter um aplicativo do Service Fabric em contêineres executando seu cluster.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como executar [contêineres no Service Fabric](service-fabric-get-started-containers.md).
* Leia mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) do Service Fabric.
