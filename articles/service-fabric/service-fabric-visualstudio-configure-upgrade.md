---
title: Configurar a atualização de um aplicativo do Service Fabric | Microsoft Docs
description: Saiba como definir as configurações para atualizar um aplicativo do Service Fabric usando o Microsoft Visual Studio.
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 79120371ca2a62e5ef9f2bf38476635db12e9fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082822"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurar a atualização de um aplicativo do Service Fabric no Visual Studio
As ferramentas do Visual Studio para o Service Fabric do Azure dão suporte à atualização da publicação em clusters locais ou remotos. Há três cenários onde você deseja atualizar seu aplicativo para uma versão mais recente em vez de substituir o aplicativo durante o teste e a depuração:

* Os dados de aplicativos não serão perdidos durante a atualização.
* A disponibilidade continua alta para que não haja nenhuma interrupção do serviço durante a atualização, se houver instâncias de serviço suficientes distribuídas entre os domínios de atualização.
* Os testes podem ser executados em um aplicativo enquanto ele está sendo atualizado.

## <a name="parameters-needed-to-upgrade"></a>Parâmetros necessários para atualizar
Você pode escolher entre dois tipos de implantação: normal ou atualização. Uma implantação normal apaga todas as informações e dados sobre a implantação anterior do cluster, enquanto uma implantação de atualização preserva-as. Ao atualizar um aplicativo do Service Fabric no Visual Studio, você precisa fornecer políticas de verificação de integridade e parâmetros de atualização de aplicativo. Os parâmetros de atualização de aplicativo ajudam a controlar a atualização, enquanto as políticas de verificação de integridade determinam se a atualização foi bem-sucedida ou não. Veja [Atualização do aplicativo Service Fabric: atualizar parâmetros](service-fabric-application-upgrade-parameters.md) para obter mais detalhes.

Há três modos de atualização: *Monitorado*, *UnmonitoredAuto*, e *UnmonitoredManual*.

* Uma atualização Monitorada automatiza a atualização e a verificação de integridade do aplicativo.
* Uma atualização Não monitorada/Automática automatiza a atualização, mas ignora a verificação de integridade do aplicativo.
* Ao fazer uma atualização Não monitorada/Manual, é necessário atualizar manualmente cada domínio de atualização.

Cada modo de atualização requer diferentes conjuntos de parâmetros. Veja [Parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) para saber mais sobre as opções de atualização disponíveis.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Atualizar um aplicativo do Service Fabric no Visual Studio
Se você está usando as ferramentas do Service Fabric no Visual Studio para atualizar um aplicativo do Service Fabric, você pode especificar um processo de publicação como uma atualização em vez de uma implantação normal, marcando a caixa de seleção **Atualizar o aplicativo** .

### <a name="to-configure-the-upgrade-parameters"></a>Para configurar os parâmetros de atualização
1. Clique no botão **Configurações** próximo à caixa de seleção. A caixa de diálogo **Editar Parâmetros de Atualização** é exibida. A caixa de diálogo **Editar Parâmetros de Atualização** dá suporte aos modos de atualização Monitorada, Não monitorada/Automática e Não monitorada/Manual.
2. Selecione o modo de atualização que você deseja usar e preencha a grade de parâmetros.

    Cada parâmetro tem valores padrão. O parâmetro opcional *DefaultServiceTypeHealthPolicy* tem uma entrada de tabela de hash. Aqui está um exemplo do formato de entrada de tabela de hash para *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* é outro parâmetro opcional que utiliza uma entrada de tabela de hash no seguinte formato:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Aqui está um exemplo da vida real:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Se selecionar o modo de atualização Não monitorada/Manual, você precisará iniciar manualmente um console do PowerShell para continuar e concluir o processo de atualização. Consulte [Atualização de aplicativos do Service Fabric: tópicos avançados](service-fabric-application-upgrade-advanced.md) para saber como a atualização manual funciona.

## <a name="upgrade-an-application-by-using-powershell"></a>Atualizar um aplicativo usando o PowerShell
Você pode usar os cmdlets do PowerShell para atualizar um aplicativo do Service Fabric. Confira [Tutorial da atualização de aplicativos do Service Fabric](service-fabric-application-upgrade-tutorial.md) e [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) para obter informações detalhadas.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Especifique uma política de verificação de integridade no arquivo de manifesto do aplicativo
Cada serviço em um aplicativo do Service Fabric pode ter seus próprios parâmetros de política de integridade, que substituem os valores padrão. Você pode fornecer esses valores de parâmetro no arquivo de manifesto do aplicativo.

O exemplo a seguir mostra como aplicar uma política de verificação de integridade exclusiva para cada serviço no manifesto do aplicativo.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como atualizar um aplicativo, consulte [Atualizar um aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md).
