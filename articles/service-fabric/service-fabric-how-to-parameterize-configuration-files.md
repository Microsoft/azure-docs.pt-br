---
title: Parametrizar os arquivos de configuração no Azure Service Fabric | Microsoft Docs
description: Aprenda a parametrizar os arquivos de configuração no Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: dad497978de7187177998524db3b2f2ee448c717
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464774"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Como parametrizar os arquivos de configuração no Service Fabric

Este artigo mostra como parametrizar os arquivos de configuração no Service Fabric.  Caso ainda não esteja familiarizado com os conceitos básicos de gerenciamento de aplicativos para vários ambientes, leia [Gerenciar aplicativos para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedimento para parametrizar arquivos de configuração

Neste exemplo, você deve substituir um valor de configuração usando os parâmetros na implantação do aplicativo.

1. Abra o  *\<arquivo MyService > \PackageRoot\Config\Settings.xml* em seu projeto de serviço.
1. Defina um nome de parâmetro de configuração e valor, por exemplo, tamanho do cache igual a 25, ao adicionar o seguinte XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Salve e feche o arquivo.
1. Abra o  *\<arquivo MyApplication > \ApplicationPackageRoot\ApplicationManifest.xml*
1. No arquivo ApplicationManifest.xml, declare um parâmetro e um valor padrão no elemento `Parameters`.  É recomendado que o nome do parâmetro contenha o nome do serviço (por exemplo, "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Na seção do arquivo ApplicationManifest. xml, adicione um `ConfigOverrides` elemento e `ConfigOverride` , referenciando o pacote de configuração, a seção e o parâmetro. `ServiceManifestImport`

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> No caso em que você adicionar um ConfigOverride, o Service Fabric sempre escolherá os parâmetros do aplicativo ou o valor padrão especificado no manifesto do aplicativo.
>
>

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre outras funcionalidades de gerenciamento de aplicativo disponíveis no Visual Studio, confira [Gerenciar seus aplicativos do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
