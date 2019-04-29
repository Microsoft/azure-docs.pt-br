---
title: Def. dos Serviços de Nuvem do Azure Esquema LoadBalancerProbe | Microsoft Docs
ms.custom: ''
origin.date: 04/14/2015
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: 14
author: thraka
ms.author: v-yiso
manager: timlt
ms.openlocfilehash: de365de7bf93c0a612f102b3ec2b25c79d1c3d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613871"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Esquema LoadBalancerProbe de definição dos Serviços de Nuvem do Azure
A sonda do balanceador de carga é uma sonda de integridade definida pelo cliente de pontos de extremidade UDP e pontos de extremidade em instâncias de função. O `LoadBalancerProbe` não é um elemento autônomo; ele é combinado com a função web ou a função de trabalho em um arquivo de definição de serviço. Um `LoadBalancerProbe` pode ser usado por mais de uma função.

A extensão padrão do arquivo de definição de serviço é .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>A função de uma sonda do balanceador de carga
O Azure Load Balancer é responsável pelo roteamento de tráfego de entrada para as instâncias de função. O balanceador de carga determina quais instâncias podem receber tráfego investigando regularmente cada instância a fim de determinar a integridade dessa instância. O balanceador de carga investiga cada instância várias vezes por minuto. Há duas opções diferentes para oferecer integridade de instância ao balanceador de carga – a sonda padrão do balanceador de carga ou uma sonda personalizada do balanceador de carga, implementada definindo o LoadBalancerProbe no arquivo .csdef.

O balanceador de carga padrão utiliza o agente convidado dentro da máquina virtual, que escutará e responderá com uma resposta HTTP 200 OK somente quando a instância está estiver no estado Pronto (como quando a instância não está nos estados Ocupado, Reciclando ou Parando, etc.). Se o agente convidado não responder com HTTP 200 OK, o Azure Load Balancer marcará a instância como sem resposta e interromperá o envio de tráfego para essa instância. O Azure Load Balancer continua executando ping na instância e se o agente convidado responder com um HTTP 200, o Azure Load Balancer enviará o tráfego para essa instância novamente. Ao usar uma função web, normalmente o código do seu site é executado no w3wp.exe, que não é monitorado pela malha do Azure ou pelo agente convidado, o que significa que as falhas no w3wp.exe (por exemplo, as respostas HTTP 500) não serão relatadas para o agente convidado e o balanceador de carga não saberá retirar essa instância da rotação.

A sonda personalizada do balanceador de carga substitui a sonda padrão do agente convidado e permite criar sua própria lógica personalizada para determinar a integridade da instância de função. O balanceador de carga investiga regularmente seu ponto de extremidade (a cada 15 segundos, por padrão) e a instância deverá ser considerada em rotação se ela responder com um ACK TCP ou HTTP 200 dentro do período de tempo limite (padrão de 31 segundos). Isso poderá ser útil para implementar sua própria lógica para remover instâncias da rotação do balanceador de carga, por exemplo, retornar um status diferente de 200 se a instância estiver acima de 90% da CPU. Para funções Web que usam o w3wp.exe, isso também significa que você obtém o monitoramento automático de seu site, uma vez que as falhas em seu código de site retornam um status diferente de 200 para a sonda do balanceador de carga. Se você não definir um LoadBalancerProbe no arquivo .csdef, então o comportamento do balanceador de carga padrão (conforme descrito anteriormente) deverá ser usado.

Se você usar uma sonda personalizada do balanceador de carga, será necessário garantir que sua lógica leve em consideração o método RoleEnvironment.OnStop. Ao usar a sonda padrão do balanceador de carga, a instância é tirada da rotação antes de o OnStop ser chamado, mas uma sonda personalizada do balanceador de carga pode continuar retornando um 200 OK durante o evento OnStop. Se você estiver usando o evento OnStop para limpar o cache, interromper o serviço ou, caso contrário, fazer alterações que possam afetar o comportamento do tempo de execução do seu serviço, então será necessário certificar-se de que a lógica da sua sonda personalizada do balanceador de carga remova a instância da rotação.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Esquema de definição de serviço básico para uma sonda do balanceador de carga
 O formato básico de um arquivo de definição de serviço que contém uma sonda de balanceador de carga é o seguinte.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
O elemento `LoadBalancerProbes` do arquivo de definição de serviço inclui os seguintes elementos:

- [Elemento LoadBalancerProbes](#LoadBalancerProbes)
- [Elemento LoadBalancerProbe](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> Elemento LoadBalancerProbes
O elemento `LoadBalancerProbes` descreve a coleção de sondas do balanceador de carga. Esse elemento é o pai do [Elemento LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a> Elemento LoadBalancerProbe
O elemento `LoadBalancerProbe` define a sonda de integridade de um modelo. É possível definir várias sondas do balanceador de carga. 

A tabela a seguir descreve os atributos do elemento `LoadBalancerProbe`:

|Atributo|Type|DESCRIÇÃO|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Obrigatório. O nome da sonda do balanceador de carga. O nome deve ser exclusivo.|
| `protocol`          | `string` | Obrigatório. Especifica o protocolo do ponto de extremidade. Os valores possíveis são `http` ou `tcp`. Se `tcp` for especificado, será necessário um ACK recebido para que a sonda tenha êxito. Se `http` for especificado, uma resposta 200 OK do URI especificado será necessária para que a sonda tenha êxito.|
| `path`              | `string` | O URI usado para solicitar o status de integridade da VM. `path` será necessário se `protocol` for definido como `http`. Caso contrário, não será permitido.<br /><br /> Não há nenhum valor padrão.|
| `port`              | `integer` | Opcional. A porta para se comunicar com a sonda. Isso é opcional para qualquer ponto de extremidade, uma vez que a mesma porta será usada para a sonda. É possível configurar uma porta diferente para sua investigação também. Os valores possíveis variam de 1 a 65535, inclusive.<br /><br /> O valor padrão é definido pelo ponto de extremidade.|
| `intervalInSeconds` | `integer` | Opcional. O intervalo, em segundos, para a frequência de investigação do status de integridade no ponto de extremidade. Normalmente, o intervalo é ligeiramente menor do que a metade do período de tempo limite alocado (em segundos) que permite duas sondas completas antes de tirar a instância de rotação.<br /><br /> O valor padrão é 15, o valor mínimo é 5.|
| `timeoutInSeconds`  | `integer` | Opcional. O período de tempo limite, em segundos, aplicado à sonda em que nenhuma resposta resultará na interrupção adicional da entrega do tráfego ao ponto de extremidade. Este valor permite que pontos de extremidade sejam tirados de rotação mais rapidamente ou mais lentamente do que os tempos normais usados no Azure (os padrões).<br /><br /> O valor padrão é 31, o valor mínimo é 11.|

## <a name="see-also"></a>Veja também
[Esquema de definição do Serviço de Nuvem (clássico)](schema-csdef-file.md)