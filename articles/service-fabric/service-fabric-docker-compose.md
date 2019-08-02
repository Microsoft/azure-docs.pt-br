---
title: Versão prévia da implantação do Docker Compose no Azure Service Fabric
description: O Azure Service Fabric aceita o formato do Docker Compose para facilitar a orquestração dos contêineres existentes usando o Service Fabric. No momento, esse suporte está na versão prévia.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 6e8cbf30044f95c2514a3a1af15cb58868957a16
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620730"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Suporte à implantação do Docker Compose no Azure Service Fabric (Versão prévia)

O docker usa o arquivo [docker-compose.yml](https://docs.docker.com/compose) para definir aplicativos de vários contêineres. A fim de facilitar para os clientes familiarizados com o Docker orquestrarem aplicativos de contêiner existentes no Azure Service Fabric, incluímos o suporte da versão prévia para a implantação do Docker Compose nativamente na plataforma. O Service Fabric pode aceitar a versão 3 e posteriores de arquivos `docker-compose.yml`. 

Como esse suporte está na versão prévia, há suporte para apenas um subconjunto de diretivas do Compose. Por exemplo, não há suporte para atualizações de aplicativo. No entanto, você sempre pode remover e implantar aplicativos em vez de atualizá-los.

Para usar essa versão prévia, crie o cluster com a versão 5.7 ou superior do tempo de execução do Service Fabric por meio do portal do Azure junto com o SDK correspondente. 

> [!NOTE]
> Esse recurso está na versão prévia e não tem suporte.
> Os exemplos abaixo baseiam-se no tempo de execução versão 6.0 e no SDK versão 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Implantar um arquivo do Docker Compose no Service Fabric

Os seguintes comandos criam um aplicativo do Service Fabric (nomeado `fabric:/TestContainerApp`), que você poderá monitorar e gerenciar, como qualquer outro aplicativo do Service Fabric. Você pode usar o nome do aplicativo especificado para consultas de integridade.
O Service Fabric reconhece "DeploymentName" como o identificador da implantação do Compose.

### <a name="use-powershell"></a>Usar o PowerShell

Criar uma implantação do Compose do Service Fabric a partir de um arquivo docker-compose.yml executando o seguinte comando no PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` e `RegistryPassword` se referem ao nome de usuário e senha de registro de contêiner. Após concluir a implantação, você poderá verificar o status utilizando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Para excluir a implantação do Compose através de PowerShell, utilize o seguinte comando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Para iniciar uma atualização da implantação do Compose por meio do PowerShell, utilize o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Para reverter uma atualização da implantação do Compose por meio do PowerShell, utilize o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Depois que a atualização é aceita, o andamento da atualização pode ser controlado usando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Usar a CLI do Azure Service Fabric (sfctl)

Como alternativa, você pode usar o seguinte comando da CLI do Service Fabric:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Após ter criado a implantação, será possível verificar seu status usando o comando a seguir:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Para excluir a implantação do Compose, use o comando a seguir:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Para iniciar uma atualização de implantação do Compose, use o seguinte comando:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Para reverter uma atualização da implantação do Compose, utilize o seguinte comando:

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Depois que a atualização é aceita, o andamento da atualização pode ser controlado usando o seguinte comando:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Diretivas do Compose com suporte

Esta versão prévia dá suporte a um subconjunto das opções de configuração do formato da versão 3 do Compose, incluindo os primitivos a seguir:

* Serviços > Implantar > Réplicas
* Serviços > Implantar > Posicionamento > Restrições
* Serviços > Implantar > Recursos > Limites
    * -cpu-shares
    * -memory
    * -memory-swap
* Serviços > Comandos
* Serviços > Ambiente
* Serviços > Portas
* Serviços > Imagem
* Serviços > Isolamento (somente para Windows)
* Serviços > Registro em log > Driver
* Serviços > Registro em log > Driver > Opções
* Volume e Implantar > Volume

Configure o cluster para impor limites de recursos, conforme descrito na [Governança de recursos do Service Fabric](service-fabric-resource-governance.md). Todas as outras diretivas do Docker Compose não têm suporte nessa versão prévia.

### <a name="ports-section"></a>Seção Portas

Especifique o protocolo http ou https na seção Portas que será usada pelo ouvinte de serviço do Service Fabric. Isso garantirá que o protocolo de ponto de extremidade seja publicado corretamente com o serviço de nomenclatura para permitir que o proxy inverso encaminhe as solicitações:
* Para rotear para serviços do Service Fabric Compose não seguros, especifique **/http**. Por exemplo, – **“80:80/http”** .
* Para rotear para serviços do Service Fabric Compose seguros, especifique **/https**. Por exemplo, – **“443:443/https”** .

> [!NOTE]
> A sintaxe de seção das Portas /http e /https é específica para o Service Fabric registrar a URL correta do ouvinte do Service Fabric.  Se a sintaxe do arquivo do Docker Compose for validada programaticamente, poderá provocar um erro de validação.

## <a name="servicednsname-computation"></a>Computação de ServiceDnsName

Se o nome do serviço que você especifica em um arquivo do Compose é um nome de domínio totalmente qualificado (isto é, contem um ponto, [.]), o nome DNS registrado pelo Service Fabric será `<ServiceName>` (incluindo o ponto). Caso contrário, cada segmento de caminho no nome de aplicativo se tornará um rótulo de domínio no nome DNS do serviço com o primeiro segmento de caminho se tornando o rótulo de domínio primário.

Por exemplo, se o nome especificado do aplicativo for `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` seria o nome DNS registrado.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Implantação do Compose (definição da instância) versus modelo de aplicativo do Service Fabric (definição do tipo)

Um arquivo docker-compose.yml descreve um conjunto de contêineres implantável, incluindo suas propriedades e configurações.
Por exemplo, o arquivo pode conter variáveis de ambiente e portas. Você também pode especificar parâmetros de implantação, como restrições de posicionamento, limites de recursos e nomes DNS, no arquivo docker-compose.yml.

O [modelo de aplicativo do Service Fabric](service-fabric-application-model.md) usa tipos de serviço e tipos de aplicativo, em que é possível ter várias instâncias de aplicativo do mesmo tipo. Por exemplo, você pode ter uma instância de aplicativo por cliente. Esse modelo baseado em tipo dá suporte a várias versões do mesmo tipo de aplicativo que é registrado com o tempo de execução.

Por exemplo, o cliente A pode ter um aplicativo instanciado com tipo 1.0 do AppTypeA e o cliente B pode ter outro aplicativo instanciado com o mesmo tipo e versão. Você define os tipos de aplicativos nos manifestos do aplicativo e especifica os parâmetros de nome e implantação do aplicativo ao criar o aplicativo.

Embora esse modelo ofereça flexibilidade, também estamos planejando dar suporte a um modelo de implantação baseado em instâncias mais simples em que os tipos são implícitos no arquivo de manifesto. Nesse modelo, cada aplicativo obtém seu próprio manifesto independente. Estamos fazendo a versão prévia desse esforço adicionando suporte para o docker-compose.yml, que é um formato de implantação baseado em instância.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre o [modelo de aplicativo do Service Fabric](service-fabric-application-model.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
