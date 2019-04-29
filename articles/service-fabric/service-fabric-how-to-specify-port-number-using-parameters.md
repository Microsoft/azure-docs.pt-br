---
title: Como especificar o número da porta de um serviço usando parâmetros no Azure Service Fabric | Microsoft Docs
description: Mostra como usar parâmetros para especificar a porta para um aplicativo no Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: d69e02126564388bf045693b9960e6e574307641
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720239"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Como especificar o número da porta de um serviço usando parâmetros no Service Fabric

Este artigo mostra como especificar o número da porta de um serviço usando os parâmetros no Service Fabric usando o Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedimento para especificar o número da porta de um serviço usando parâmetros

Neste exemplo, você poderá definir o número da porta para a API Web do asp.net core usando um parâmetro.

1. Abra o Visual Studio e crie um novo aplicativo do Service Fabric.
1. Escolha o modelo do ASP.NET Core sem monitoração de estado.
1. Escolha a API Web.
1. Abra o arquivo ServiceManifest.xml.
1. Observe o nome do ponto de extremidade especificado para o serviço. O padrão é `ServiceEndpoint`.
1. Abra o arquivo ApplicationManifest.xml
1. No elemento `ServiceManifestImport`, adicione um novo elemento `RessourceOverrides` com uma referência para o ponto de extremidade no arquivo ServiceManifest.xml.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. No elemento `Endpoint`, agora você pode substituir qualquer atributo usando um parâmetro. Neste exemplo, você deverá especificar `Port` e defini-lo como um nome de parâmetro usando colchetes – por exemplo,`[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Ainda no arquivo ApplicationManifest.xml, especifique o parâmetro no elemento `Parameters`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. E defina um `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Abra a pasta ApplicationParameters e o arquivo `Cloud.xml`
1. Para especificar uma porta diferente a ser usada ao publicar em um cluster remoto, adicione o parâmetro com o número da porta a esse arquivo.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Ao publicar seu aplicativo do Visual Studio usando o perfil de publicação Cloud.xml, o serviço será configurado para usar a porta 80. Se você implantar o aplicativo sem especificar o parâmetro MyWebAPI_PortNumber, o serviço usará a porta 8080.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre alguns dos principais conceitos que são discutidos neste artigo, veja os artigos sobre [Gerenciar aplicativos de vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

Para obter informações sobre outras funcionalidades de gerenciamento de aplicativo disponíveis no Visual Studio, confira [Gerenciar seus aplicativos do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
