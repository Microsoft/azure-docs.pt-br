---
title: CI/CD para o Azure Spring Cloud
description: CI/CD para o Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 79d9b2a0d706dd2d9861d068de0e4671db1c5158
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089104"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD para o Azure Spring Cloud

As ferramentas de integração contínua e entrega contínua permitem que você implante rapidamente atualizações em aplicativos existentes com esforço mínimo e risco. O Azure DevOps ajuda a organizar e controlar esses trabalhos importantes. Atualmente, o Azure Spring Cloud não oferece um plug-in do Azure DevOps específico.  No entanto, você pode integrar seus aplicativos Spring Cloud ao DevOps usando uma [tarefa CLI do Azure](/azure/devops/pipelines/tasks/deploy/azure-cli?preserve-view=true&view=azure-devops).

Este artigo mostra como usar uma tarefa de CLI do Azure com o Azure Spring Cloud para integrar com o Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Criar uma conexão de serviço Azure Resource Manager

Leia [Este artigo](/azure/devops/pipelines/library/connect-to-azure?preserve-view=true&view=azure-devops) para saber como criar uma conexão de serviço Azure Resource Manager para seu projeto DevOps do Azure. Certifique-se de selecionar a mesma assinatura que você está usando para sua instância do serviço de nuvem do Azure Spring.

## <a name="azure-cli-task-templates"></a>Modelos de tarefa CLI do Azure
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Implantar artefatos

Você pode criar e implantar seus projetos usando uma série de `tasks` . Esse trecho de código define as variáveis, uma tarefa do .NET Core para compilar o aplicativo e uma CLI do Azure tarefa para implantar o arquivo *. zip* .

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --is-public true
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Implantar artefatos

Você pode criar e implantar seus projetos usando uma série de `tasks` . Esse trecho de código define primeiro uma tarefa Maven para compilar o aplicativo, seguido por uma segunda tarefa que implanta o arquivo JAR usando a extensão de CLI do Azure de nuvem do Azure Spring.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Implantar da origem

É possível implantar diretamente no Azure sem uma etapa de compilação separada.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Implantar seu primeiro aplicativo do Azure Spring Cloud](spring-cloud-quickstart.md)