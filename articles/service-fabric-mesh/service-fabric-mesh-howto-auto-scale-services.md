---
title: Dimensionar automaticamente um aplicativo em execução na Malha do Azure Service Fabric | Microsoft Docs
description: Saiba como configurar políticas de dimensionamento automático para os serviços de um aplicativo de Malha do Service Fabric.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2233dffabd7c76ca55cf215f8bc04e66134f5799
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583597"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Criar políticas de dimensionamento automático para um aplicativo de Malha do Service Fabric
Uma das principais vantagens da implantação de aplicativos na Malha do Service Fabric é a capacidade de dimensionar e reduzir seus serviços com facilidade. Isso é usado para manipulação de volumes variáveis de carga nos seus serviços ou para melhorar a disponibilidade. Você pode realizar manualmente a expansão ou redução horizontal de seus serviços ou definir políticas de dimensionamento automático.

O [dimensionamento automático](service-fabric-mesh-scalability.md#autoscaling-service-instances) permite que você dimensione dinamicamente o número de suas instâncias de serviço (dimensionamento horizontal). O dimensionamento automático fornece grande elasticidade e permite o provisionamento ou remoção de instâncias de serviço com base na utilização de CPU ou de memória.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opções para criar um mecanismo, um gatilho ou uma política de dimensionamento
Uma política de escala automática é definida para cada serviço que você deseja dimensionar. A política é definida no arquivo de recurso de serviço YAML ou no modelo de implantação de JSON. Cada política de dimensionamento consiste em duas partes: um gatilho e um mecanismo de dimensionamento.

O gatilho define quando uma política de dimensionamento automático é invocada.  Especifique o tipo de gatilho (carga média) e a métrica de monitor (CPU ou memória).  Limites de carga superiores e inferiores especificados como um percentual. O intervalo de escala define a frequência (em segundos) na qual verificar a utilização especificada (por exemplo, a carga média da CPU) em todas as instâncias de serviço atualmente implantadas.  O mecanismo é disparado quando a métrica monitorada fica abaixo do limite inferior ou acima do limite superior.  

O mecanismo de dimensionamento define como realizar a operação de dimensionamento quando a política é disparada.  Especifique o tipo de mecanismo (adicionar/remover réplica), as contas de réplica mínima e máxima conta (como números inteiros).  O número de réplicas de serviço nunca será dimensionado abaixo da contagem mínima ou acima da contagem máxima.  Especifique também o incremento de escala como um inteiro, que é o número de réplicas que serão adicionadas ou removidas em uma operação de dimensionamento.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definir um política de dimensionamento automático em um modelo JSON

O exemplo a seguir mostra uma política de dimensionamento automático em um modelo de implantação de JSON.  A política de dimensionamento automático é declarada em uma propriedade do serviço a ser dimensionado.  Neste exemplo, um gatilho de carga média da CPU é definido.  O mecanismo será disparado se a carga média da CPU de todas as instâncias implantadas chegar a ficar abaixo de 0,2 (20%) ou acima de 0,8 (80%).  A carga da CPU é verificada a cada 60 segundos.  O mecanismo de dimensionamento é definido para adicionar ou remover instâncias se a política é disparada.  Instâncias de serviço serão adicionadas ou removidas em incrementos de um.  Também é definida uma contagem mínima de instâncias de um e uma contagem máxima de instâncias de 40.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definir uma política de dimensionamento automático em um arquivo de recurso service.yaml
O exemplo a seguir mostra uma política de dimensionamento automático em um arquivo de recurso do serviço (YAML).  A política de dimensionamento automático é declarada como uma propriedade do serviço a ser dimensionado.  Neste exemplo, um gatilho de carga média da CPU é definido.  O mecanismo será disparado se a carga média da CPU de todas as instâncias implantadas chegar a ficar abaixo de 0,2 (20%) ou acima de 0,8 (80%).  A carga da CPU é verificada a cada 60 segundos.  O mecanismo de dimensionamento é definido para adicionar ou remover instâncias se a política é disparada.  Instâncias de serviço serão adicionadas ou removidas em incrementos de um.  Também é definida uma contagem mínima de instâncias de um e uma contagem máxima de instâncias de 40.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Próximas etapas
Saiba como [dimensionar manualmente um serviço](service-fabric-mesh-tutorial-template-scale-services.md)
