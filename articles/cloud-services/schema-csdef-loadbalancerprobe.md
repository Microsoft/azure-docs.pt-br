---
title: Def. dos Serviços de Nuvem do Azure Esquema LoadBalancerProbe | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 6f82406772f650b4565f2c9240efe580545dcad9
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360611"
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

|Atributo|Tipo|Descrição|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Obrigatória. O nome da sonda do balanceador de carga. O nome deve ser exclusivo.|
| `protocol`          | `string` | Obrigatória. Especifica o protocolo do ponto de extremidade. Os valores possíveis são `http` ou `tcp`. Se `tcp` for especificado, será necessário um ACK recebido para que a sonda tenha êxito. Se `http` for especificado, uma resposta 200 OK do URI especificado será necessária para que a sonda tenha êxito.|
| `path`              | `string` | O URI usado para solicitar o status de integridade da VM. `path` será necessário se `protocol` for definido como `http`. Caso contrário, não será permitido.<br /><br /> Não há nenhum valor padrão.|
| `port`              | `integer` | Opcional. A porta para se comunicar com a sonda. Isso é opcional para qualquer ponto de extremidade, uma vez que a mesma porta será usada para a sonda. É possível configurar uma porta diferente para sua investigação também. Os valores possíveis variam de 1 a 65535, inclusive.<br /><br /> O valor padrão é definido pelo ponto de extremidade.|
| `intervalInSeconds` | `integer` | Opcional. O intervalo, em segundos, para a frequência de investigação do status de integridade no ponto de extremidade. Normalmente, o intervalo é ligeiramente menor do que a metade do período de tempo limite alocado (em segundos) que permite duas sondas completas antes de tirar a instância de rotação.<br /><br /> O valor padrão é 15, o valor mínimo é 5.|
| `timeoutInSeconds`  | `integer` | Opcional. O período de tempo limite, em segundos, aplicado à sonda em que nenhuma resposta resultará na interrupção adicional da entrega do tráfego ao ponto de extremidade. Este valor permite que pontos de extremidade sejam tirados de rotação mais rapidamente ou mais lentamente do que os tempos normais usados no Azure (os padrões).<br /><br /> O valor padrão é 31, o valor mínimo é 11.|

## <a name="see-also"></a>Consulte também
[Esquema de definição do Serviço de Nuvem (clássico)](schema-csdef-file.md)