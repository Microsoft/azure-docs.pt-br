---
title: Solucionar problemas de disponibilidade do Azure Load Balancer Resource Health, frontend e back-end
description: Use as métricas disponíveis para diagnosticar seus Standard Load Balancer do Azure degradados ou indisponíveis.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 3acaaba86c9a546a0bd45b5386287908168d50d0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955613"
---
# <a name="troubleshoot-resource-health-and-inbound-availability-issues"></a>Solucionar problemas de integridade de recursos e de disponibilidade de entrada 

Este artigo é um guia para investigar problemas que afetam a disponibilidade do seu IP de front-end do balanceador de carga e dos recursos de backend. 

A verificação de Resource Health (RHC) para o Load Balancer é usada para determinar a integridade do balanceador de carga. Ele analisa a métrica de disponibilidade do caminho de dados em um intervalo de **2 minutos** para determinar se os pontos de extremidade de balanceamento de carga, as combinações de IP de front-end e portas de front-end com regras de balanceamento de carga, estão disponíveis.

A tabela abaixo descreve a lógica RHC usada para determinar o estado de integridade do balanceador de carga.

| Status de integridade de recurso | Description |
| --- | --- |
| Disponível | O recurso padrão do Load Balancer está íntegro e disponível. |
| Degradado | O balanceador de carga padrão tem eventos iniciados pela plataforma ou pelo usuário que afetam o desempenho. A métrica de disponibilidade do Datapath relatou menos de 90%, mas mais de 25% de integridade por pelo menos dois minutos. Você passará por um impacto de desempenho moderado a severo. 
| Indisponível | O recurso padrão do Load Balancer não está íntegro. A métrica de disponibilidade do caminho de dado relatou menos a integridade de 25% por pelo menos dois minutos. Você terá um impacto significativo no desempenho ou falta de disponibilidade para a conectividade de entrada. Pode haver eventos de usuário ou plataforma causando indisponibilidade. |
| Unknown | O status de integridade do recurso para o recurso de Load Balancer padrão ainda não foi atualizado ou não recebeu informações de disponibilidade do caminho de dados dos últimos 10 minutos. Esse estado deve ser transitório e reflete o status correto assim que os dados são recebidos. |


## <a name="about-the-metrics-well-use"></a>Sobre as métricas que usaremos
As duas métricas a serem usadas são o *status de investigação de integridade* e disponibilidade do caminho de *dados* e é importante entender seu significado para derivar informações corretas. 

## <a name="data-path-availability"></a>Disponibilidade do caminho de dados
A métrica de disponibilidade do caminho de dados é gerada por um ping TCP a cada 25 segundos em todas as portas de front-end com balanceamento de carga e regras NAT de entrada configuradas. Esse ping TCP será roteado para qualquer uma das instâncias de back-end íntegras (investigadas). Se o serviço receber uma resposta para o ping, ele será considerado como êxito e a soma da métrica será iterada uma vez, se falhar. A contagem dessa métrica é 1/100 do total de pings TCP por período de amostra. Portanto, queremos considerar a média, que mostrará a média da soma/contagem do período de tempo. A métrica de disponibilidade do caminho de dados agregada por média, portanto, nos dá uma taxa de sucesso percentual para pings TCP em sua porta IP: de front-end para cada uma das regras de NAT de entrada e de balanceamento de carga.

## <a name="health-probe-status"></a>Status de investigação de integridade
A métrica de status de investigação de integridade é gerada por um ping do protocolo definido na investigação de integridade. Esse ping é enviado para cada instância no pool de back-end e na porta definida na investigação de integridade. Para investigações HTTP e HTTPS, um ping bem-sucedido requer uma resposta HTTP 200 OK, enquanto com investigações TCP qualquer resposta é considerada bem-sucedida. Os sucessos consecutivos ou as falhas de cada investigação determinam se uma instância de back-end está íntegra e pode receber tráfego para as regras de balanceamento de carga às quais o pool de back-end é atribuído. Semelhante à disponibilidade de caminho de dados, usamos a agregação média, que nos informa a média de pings bem-sucedidos/totais durante o intervalo de amostragem. Esse valor de status de investigação de integridade indica a integridade de back-end em isolamento do balanceador de carga, investigando suas instâncias de back-end sem enviar tráfego pelo front-end.

>[!IMPORTANT]
>O status da investigação de integridade é amostrado em uma base de um minuto. Isso pode levar a flutuações secundárias em um valor de outro tipo constante. Por exemplo, se houver duas instâncias de back-end, uma investigada e uma investigação inativa, o serviço de investigação de integridade poderá capturar 7 amostras para a instância íntegra e 6 para a instância não íntegra. Isso levará a um valor anteriormente estável de 50 mostrado como 46,15 para um intervalo de um minuto. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Diagnosticar balanceadores de carga degradados e indisponíveis
Conforme descrito no [artigo Resource Health](load-balancer-standard-diagnostics.md#resource-health-status), um balanceador de carga degradado é aquele que mostra entre 25% e 90% de disponibilidade de caminho de dados, e um balanceador de carga indisponível é um com menos de 25% de disponibilidade de caminho de dados, em um período de dois minutos. Essas mesmas etapas podem ser executadas para investigar a falha que você vê em qualquer alerta de status de investigação de integridade ou de disponibilidade de caminho de dados que você configurou. Exploraremos o caso em que verificamos o nosso Resource Health e descobrimos que nosso balanceador de carga não está disponível com uma disponibilidade de caminho de dados de 0%-nosso serviço está inativo.

Primeiro, vamos para a exibição de métricas detalhada de nossa folha de informações do Load Balancer. Você pode fazer isso por meio da folha de recursos do balanceador de carga ou do link em sua mensagem do Resource Health.  Em seguida, navegamos até a guia disponibilidade de front-end e backend e revisamos uma janela de trinta minutos do período de tempo em que o estado degradado ou indisponível ocorreu. Se observarmos que nossa disponibilidade de caminho de dados foi 0%, sabemos que há um problema que impede o tráfego para todas as nossas regras NAT de balanceamento de carga e de entrada e pode ver quanto tempo esse impacto tem duração. 

O próximo lugar que precisamos ver é nossa métrica de status de investigação de integridade para determinar se o caminho de dados está indisponível porque não temos instâncias de back-end íntegras para atender ao tráfego. Se tivermos pelo menos uma instância de back-end íntegra para todas as nossas regras de balanceamento de carga e de entrada, sabemos que não é nossa configuração, fazendo com que os caminhos de dados não estejam disponíveis. Esse cenário indica um problema de plataforma do Azure, embora raro, não se preocupe se você encontrá-los como um alerta automatizado é enviado à nossa equipe para resolver rapidamente todos os problemas de plataforma.

## <a name="diagnose-health-probe-failures"></a>Diagnosticar falhas de investigação de integridade
Digamos que verifiquemos nosso status de investigação de integridade e descubramos que todas as instâncias estão sendo mostradas como não íntegras. Essa descoberta explica o motivo pelo qual o caminho de dados não está disponível, pois o tráfego está em qualquer lugar. Em seguida, passaremos pela seguinte lista de verificação para eliminar erros comuns de configuração:
* Verifique a utilização da CPU para seus recursos para verificar se suas instâncias estão de fato íntegras
  * Você pode verificar isso 
* Se estiver usando uma investigação HTTP ou HTTPS, verifique se o aplicativo está íntegro e respondendo
  * Validar isso é funcional acessando diretamente os aplicativos por meio do endereço IP privado ou endereço IP público em nível de instância associado à sua instância de back-end
* Examine os grupos de segurança de rede aplicados aos nossos recursos de back-end. Verifique se não há regras de prioridade mais alta do que a AllowAzureLoadBalancerInBound que bloqueará a investigação de integridade
  * Você pode fazer isso visitando a folha de rede de suas VMs de back-end ou conjuntos de dimensionamento de máquinas virtuais
  * Se você achar que esse problema de NSG é o caso, mova a regra de permissão existente ou crie uma nova regra de alta prioridade para permitir o tráfego de AzureLoadBalancer
* Verifique seu sistema operacional. Verifique se suas VMs estão escutando na porta de investigação e examine suas regras de firewall do so para garantir que elas não estejam bloqueando o tráfego de investigação proveniente do endereço IP 168.63.129.16
  * Você pode verificar portas de escuta executando netstat-a no prompt de comando do Windows ou netstat-l em um terminal do Linux
* Não coloque uma VM NVA de firewall no pool de back-end do balanceador de carga, use [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md#user-defined) para rotear o tráfego para instâncias de back-end por meio do firewall
* Verifique se você está usando o protocolo correto, se estiver usando HTTP para investigar uma porta que escuta um aplicativo não HTTP, a investigação falhará

Se você tiver feito essa lista de verificação e ainda estiver encontrando falhas de investigação de integridade, poderá haver problemas raros de plataforma afetando o serviço de investigação para suas instâncias. Nesse caso, o Azure tem seu retorno e um alerta automatizado é enviado à nossa equipe para resolver rapidamente todos os problemas de plataforma.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre a investigação de integridade Azure Load Balancer](load-balancer-custom-probe-overview.md)
* [Saiba mais sobre as métricas de Azure Load Balancer](load-balancer-standard-diagnostics.md)
