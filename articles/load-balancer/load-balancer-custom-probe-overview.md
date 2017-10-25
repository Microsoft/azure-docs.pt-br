---
title: "Investigações personalizadas do Balanceador de Carga e monitoramento do status da integridade | Microsoft Docs"
description: "Saiba como usar investigações personalizadas para o Balanceador de Carga do Azure a fim de monitorar instâncias por trás de um Balanceador de Carga"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 102c07ff0994b3b411f2a13d7a43c5398d5dfd42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-load-balancer-probes"></a>Entender as investigações do balanceador de carga

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

O Balanceador de Carga do Azure oferece a capacidade de monitorar a integridade das instâncias do servidor usando investigações. Quando uma investigação não responde, o Balanceador de Carga interrompe o envio de novas conexões para as instâncias não íntegras. As conexões existentes não são afetadas e novas conexões são enviadas para instâncias íntegras.

Funções do serviço de nuvem (funções de trabalho e da Web) usam um agente convidado para o monitoramento de investigação. As investigações personalizadas de TCP ou HTTP devem ser configuradas quando você usa máquinas virtuais por trás de um Balanceador de Carga.

## <a name="understand-probe-count-and-timeout"></a>Noções básicas sobre contagem da investigação e tempo limite

O comportamento de investigação depende do seguinte:

* Do número de investigações bem-sucedidas que permitem a uma instância ser rotulada como em execução.
* Do número de investigações com falha que fazem com que uma instância seja rotulada como inoperante.

O valor de tempo limite e de frequência definido em SuccessFailCount determina se uma instância foi confirmada como em execução ou não. Para o Portal do Azure, o tempo limite é definido como duas vezes o valor da frequência.

A configuração da investigação de todas as instâncias com balanceamento de carga para um ponto de extremidade (ou seja, um conjunto com balanceamento de carga) deve ser a mesma. Isso significa que você não pode ter uma configuração de investigação diferente para cada instância de função ou máquina virtual no mesmo serviço hospedado para uma combinação de ponto de extremidade específica. Por exemplo, cada instância deve ter portas locais e tempos limite idênticos.

> [!IMPORTANT]
> A investigação de um Balanceador de Carga usa um endereço IP 168.63.129.16. Esse endereço IP público facilita a comunicação com recursos internos de plataforma para o cenário de Rede Virtual do Azure com seu próprio IP. O endereço IP público 168.63.129.16 virtual é usado em todas as regiões e não será alterado. Recomendamos que você permita esse endereço IP em todas as políticas de firewall local. Ele não deve ser considerado um risco de segurança, pois somente a plataforma interna do Azure pode originar uma mensagem a partir dele. Se você não fizer isso, haverá em um comportamento inesperado em vários cenários, como a configuração do mesmo intervalo de endereços IP como 168.63.129.16 e endereços IP duplicados.

## <a name="learn-about-the-types-of-probes"></a>Saiba mais sobre os tipos de investigações

### <a name="guest-agent-probe"></a>Investigação do agente convidado

Essa investigação só está disponível para os Serviços de Nuvem do Azure. O Balanceador de Carga utiliza o agente convidado dentro da máquina virtual e, então, escuta e responde com uma resposta HTTP 200 OK somente quando a instância estiver no estado Pronto (ou seja, não em outro estado como Ocupado, Reciclando ou Parando).

Para saber mais, consulte [Configuring the service definition file (csdef) for health probes (Configurando o arquivo de definição de serviço (csdef) para investigações de integridade)](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [Get started creating an Internet-facing load balancer for cloud services (Introdução à criação de um balanceador de carga para Internet para serviços de nuvem)](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>O que faz uma investigação de agente convidado marcar uma instância como não íntegra?

Se o agente convidado não responder com HTTP 200 OK, o Balanceador de Carga marcará a instância como sem resposta e interromperá o envio de tráfego para essa instância. O balanceador de carga continuará executando o ping nessa instância. Se o agente convidado responder com um HTTP 200, o Balanceador de Carga enviará o tráfego para essa instância novamente.

Quando você usa uma função Web, o código do site geralmente é executado no w3wp.exe, que não é monitorado nem pela malha do Azure nem pelo agente convidado. Isso significa que as falhas no w3wp.exe (por exemplo, as respostas HTTP 500) não serão relatadas para o agente convidado, e o balanceador de carga não retirará essa instância da rotação.

### <a name="http-custom-probe"></a>Investigação personalizada do HTTP

A investigação personalizada do Balanceador de Carga HTTP substitui a investigação do agente convidado padrão, o que permite a criação de sua própria lógica personalizada para determinar a integridade da instância de função. Por padrão, o balanceador de carga investiga seu ponto de extremidade a cada 15 segundos. A instância será considerada em rotação do balanceador de carga se ela responder com um HTTP 200 dentro do período de tempo limite (31 segundos por padrão).

Isso pode ser útil ser você quiser implementar sua própria lógica para remover instâncias da rotação do balanceador de carga. Por exemplo, você pode decidir remover uma instância se ela estiver usando mais de 90% da CPU e retornar um status diferente de 200. Se houver funções Web que usam o w3wp.exe, também significará que seu site é monitorado automaticamente, pois as falhas no código do site retornarão um status diferente de 200 para a investigação do balanceador de carga.

> [!NOTE]
> A investigação personalizada HTTP só oferece suporte aos caminhos relativos e ao protocolo HTTP. Não há suporte para HTTPS.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>O que faz uma investigação personalizada HTTP marcar uma instância como não íntegra?

* O aplicativo HTTP retorna um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Essa é uma confirmação positiva de que a instância do aplicativo deve ser retirada do serviço imediatamente.
* O servidor HTTP não responde após o período de tempo limite. Dependendo do valor de tempo limite definido, várias solicitações de investigação podem ficar sem resposta antes de a investigação ser marcada como não estando em execução (ou seja, antes das investigações SuccessFailCount serem enviadas).
* O servidor fecha a conexão por meio de uma redefinição TCP.

### <a name="tcp-custom-probe"></a>Investigação personalizada do TCP

As investigações de TCP iniciam uma conexão executando um handshake de três vias na porta definida.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>O que faz uma investigação personalizada TCP marcar uma instância como não íntegra?

* O servidor TCP não responde após o período de tempo limite. A marcação da investigação como não estando em execução depende da configuração da quantidade de solicitações de investigação sem resposta.
* A investigação recebe uma redefinição de TCP da instância de função.

Para saber mais sobre como configurar uma investigação de integridade HTTP ou uma investigação de TCP, confira [Introdução à criação de um balanceador de carga para a Internet no Resource Manager usando o PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Adicionar instâncias íntegras de volta à rotação do balanceador de carga

As investigações de TCP e HTTP são consideradas íntegras e marcam a instância de função como íntegra quando:

* O balanceador de carga obtém uma investigação positiva na primeira vez em que a VM é iniciada.
* O número SuccessFailCount (descrito acima) define o valor de investigações bem-sucedidas necessárias para marcar a instância de função como íntegra. Se uma instância de função tiver sido removida, o número de investigações bem-sucedidas e sucessivas deverá ser igual ou exceder o valor de SuccessFailCount a fim de marcar a instância de função como em execução.

> [!NOTE]
> Se a integridade de uma instância de função for flutuante, o balanceador de carga aguardará antes de colocar a instância de função de volta ao estado íntegro. Isso é feito por meio de uma política para proteger o usuário e a infraestrutura.

## <a name="use-log-analytics-for-load-balancer"></a>Usar análise de log para o Balanceador de Carga

Você pode usar a [análise de logs para o Balanceador de Carga](load-balancer-monitor-log.md) para verificar o status da integridade da investigação e a contagem da investigação. O registro em log pode ser usado com o Power BI ou com o Azure Operational Insights para fornecer estatísticas sobre o status da integridade do Balanceador de Carga.
