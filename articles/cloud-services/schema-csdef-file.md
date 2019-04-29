---
title: Esquema de definição (arquivo .csdef) dos Serviços de Nuvem do Azure | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: 42
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 4e018af7df64c9ed8050a3c618cf2645d5509cdd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613261"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Esquema de definição (arquivo .csdef) dos Serviços de Nuvem do Azure
O arquivo de definição de serviço define o modelo de serviço para um aplicativo. O arquivo contém as definições das funções disponíveis para um serviço de nuvem, especifica os pontos de extremidade de serviço e estabelece as definições de configuração para o serviço. Os valores de definição de configuração são definidos no arquivo de configuração de serviço, conforme descrito pelo [Esquema de configuração do Serviço de Nuvem (clássico)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Por padrão, o arquivo de esquema de configuração do Diagnóstico do Azure é instalado no diretório `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`. Substitua `<version>` pela versão instalada do [SDK do Azure](https://www.windowsazure.com/develop/downloads/).

A extensão padrão do arquivo de definição de serviço é .csdef.

## <a name="basic-service-definition-schema"></a>Esquema básico de definição de serviço
O arquivo de definição de serviço deve conter um elemento `ServiceDefinition`. A definição de serviço deve conter pelo menos um elemento de função (`WebRole` ou `WorkerRole`). Ele pode conter até 25 funções definidas em uma única definição e é possível combinar tipos de função. A definição de serviço também contém o elemento `NetworkTrafficRules` opcional que restringe quais funções podem se comunicar com pontos de extremidade internos especificados. A definição de serviço também contém o elemento `LoadBalancerProbes` opcional que contém as sondas de pontos de extremidade de integridade definidas pelo cliente.

O formato básico do arquivo de definição de serviço é o seguinte.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Definições do esquema
Os tópicos a seguir descrevem o esquema:

- [Esquema LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Esquema WebRole](schema-csdef-webrole.md)
- [Esquema WorkerRole](schema-csdef-workerrole.md)
- [Esquema NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> Elemento ServiceDefinition
O elemento `ServiceDefinition` é o elemento de nível superior do arquivo de definição de serviço.

A tabela a seguir descreve os atributos do elemento `ServiceDefinition`.

| Atributo               | DESCRIÇÃO |
| ----------------------- | ----------- |
| Nome                    |Obrigatório. O nome do serviço. O nome deve ser exclusivo dentro da conta de serviço.|
| topologyChangeDiscovery | Opcional. Especifica o tipo de notificação de alteração de topologia. Os valores possíveis são:<br /><br /> -   `Blast` – Envia a atualização assim que possível para todas as instâncias de função. Se você escolher uma opção, a função deverá ser capaz de lidar com a atualização da topologia sem ser reiniciada.<br />-   `UpgradeDomainWalk` – Envia a atualização para cada instância de função de maneira sequencial depois que a instância anterior tiver aceitado a atualização com sucesso.|
| schemaVersion           | Opcional. Especifica a versão do esquema de definição de serviço. A versão do esquema permitirá que o Visual Studio selecione as ferramentas do SDK corretas para usar para a validação de esquema se mais de uma versão do SDK for instalada lado a lado.|
| upgradeDomainCount      | Opcional. Especifica o número de domínios de atualização entre quais funções neste serviço são alocadas. As instâncias de função são alocadas para um domínio de atualização quando o serviço é implantado. Para obter mais informações, consulte [Update a cloud service role or deployment](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment) (Atualizar uma implantação ou função de serviço de nuvem).<br /><br /> É possível especificar até 20 domínios de atualização. Se não especificado, o número padrão de domínios de atualização é 5.|
