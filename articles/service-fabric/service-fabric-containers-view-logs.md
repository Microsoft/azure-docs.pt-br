---
title: Exibir logs de contêineres no Azure Service Fabric | Microsoft Docs
description: Descreve como exibir os logs de contêineres para serviços de contêiner do Service Fabric em execução usando o Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: aljo
ms.openlocfilehash: 0408010a49b8ec83aa02c74887139f663788ad80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881464"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Exibir logs para um serviço de contêiner do Service Fabric
O Microsoft Azure Service Fabric é um orquestrador de contêineres e dá suporte a [contêineres de Linux e Windows](service-fabric-containers-overview.md).  Este artigo descreve como exibir logs de contêiner de um serviço de contêiner em execução ou contêiner inativo para que você possa diagnosticar e solucionar problemas.

## <a name="access-the-logs-of-a-running-container"></a>Acessar os logs de um contêiner em execução
Logs de contêiner podem ser acessados usando [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Em um navegador da web, abra o Service Fabric Explorer do ponto de extremidade de gerenciamento do cluster navegando até [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Logs de contêiner estão localizados no nó de cluster que está executando a instância de serviço de contêiner. Por exemplo, obtenha os logs do contêiner do front-end da Web do [aplicativo de exemplo do Linux Voting](service-fabric-quickstart-containers-linux.md). No modo de exibição de árvore, expanda **Cluster**>**Aplicativos**>**VotingType**>**fabric:/Voting/azurevotefront**.  Em seguida, expanda a partição (d1aa737e-f22a-e347-be16-eec90be24bc1, neste exemplo) e veja que o contêiner está em execução no nó de cluster *_lnxvm_0*.

No modo de exibição de árvore, localize o pacote de código no nó *_lnxvm_0* expandindo **Nós**>**_lnxvm_0**>**fabric:/Voting** > **azurevotfrontPkg**>**Pacotes de códigos**>**código**.  Selecione a opção **Logs do Contêiner** para exibir os logs do contêiner.

![Plataforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Acessar os logs de um contêiner inativo ou com falha
Começando em v 6.2, você pode buscar também os logs para um contêiner inativo ou falha usando os comandos [APIs REST](/rest/api/servicefabric/sfclient-index) ou [CLI do Service Fabric (SFCTL)](service-fabric-cli.md).

### <a name="set-container-retention-policy"></a>Definir política de retenção de contêiner
Para ajudar a diagnosticar as falhas de inicialização do contêiner, o Service Fabric (versão 6.1 ou superiores) oferece suporte à retenção de contêineres que encerraram ou falharam na inicialização. Essa política pode ser definida no arquivo **ApplicationManifest.xml** conforme mostrado no snippet de código a seguir:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A configuração **ContainersRetentionCount** especifica o número de contêineres mantidos quando ocorre uma falha. Se um valor negativo for especificado, todos os contêineres com falha serão mantidos. Quando o atributo **ContainersRetentionCount** não for especificado, nenhum contêiner será retido. O atributo **ContainersRetentionCount** também oferece suporte a Parâmetros de Aplicativo para que os usuários possam especificar valores diferentes para clusters de teste e produção. Use restrições de posicionamento para direcionar o serviço de contêiner para um determinado nó quando esses recursos forem usados com a finalidade de impedir que o serviço de contêiner se mova para outros nós. Todos os contêineres retidos usando esse recurso devem ser removidos manualmente.

A configuração **RunInteractive** corresponde aos [sinalizadores](https://docs.docker.com/engine/reference/commandline/run/#options) `--interactive` e `tty` do Docker. Quando essa configuração é definida como verdadeira no arquivo de manifesto, esses sinalizadores são usados para iniciar o contêiner.  

### <a name="rest"></a>REST
Use a operação [Obter Logs de Contêiner Implantados no Nó](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) para obter os logs para um contêiner de falha. Especifique o nome do contêiner em execução no nó, nome do aplicativo, nome do manifesto de serviço e o nome do pacote de código.  Especifique `&Previous=true`. A resposta conterá os logs do contêiner para o contêiner inativo da instância do pacote de código.

A URI de solicitação tem a forma a seguir:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Solicitação de exemplo:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

Corpo da resposta 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Use o comando [sfctl serviço get-container-logs](service-fabric-sfctl-service.md) para buscar os logs para um contêiner com falha.  Especifique o nome do contêiner em execução no nó, nome do aplicativo, nome do manifesto de serviço e o nome do pacote de código. Especifique a `--previous` marca.  A resposta conterá os logs do contêiner para o contêiner inativo da instância do pacote de código.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Resposta:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Próximas etapas
- Navegue pelo [tutorial Criar um aplicativo de contêiner do Linux](service-fabric-tutorial-create-container-images.md).
- Saiba mais sobre o [Service Fabric e contêineres](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
