---
title: Eventos Agendados para VMs Linux no Azure
description: Agendar eventos usando o Serviço de Metadados do Azure para nas máquinas virtuais do Linux.
author: EricRadzikowskiMSFT
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviewer: mimckitt
ms.openlocfilehash: 8b4f8b064ab19a578ce5854697a1ed9bb0195759
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505385"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Serviço de Metadados do Azure: Eventos Agendados para VMs do Linux

Os eventos agendados são um serviço de metadados do Azure que dá ao aplicativo tempo para se preparar para a manutenção de máquinas virtuais (VM). Ele fornece informações sobre eventos de manutenção futuros (por exemplo, reinicializar) para que seu aplicativo possa se preparar e limitar a interrupção. Ele está disponível para todos os tipos de Máquinas Virtuais do Azure, incluindo PaaS e IaaS no Windows e no Linux. 

Para saber mais sobre os eventos agendados no Windows, veja [Eventos Agendados para VMs do Windows](../windows/scheduled-events.md).

> [!Note] 
> Eventos Agendados estão disponível geralmente em todas as regiões do Azure. Consulte [Disponibilidade de Versão e Região](#version-and-region-availability) para obter informações sobre a versão mais recente.

## <a name="why-use-scheduled-events"></a>Por que usar Eventos Agendados?

Muitos aplicativos podem se beneficiar do tempo para se preparar para a manutenção de VMs. O tempo pode ser usado para executar tarefas específicas de aplicativo que melhoram a disponibilidade, a confiabilidade e a capacidade de manutenção, incluindo: 

- Ponto de verificação e restauração.
- Descarregamento de conexão.
- Failover de réplica primária.
- Remoção de um pool de balanceador de carga.
- Log de eventos.
- Desligamento normal.

Com Eventos Agendados, seu aplicativo pode descobrir quando a manutenção ocorrerá e acionará tarefas para limitar seu impacto.  

Os eventos agendados fornecem eventos nos seguintes casos de uso:

- [Manutenção iniciada pela plataforma](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json) (por exemplo, reinicialização de VM, migração ao vivo ou atualizações de preservação de memória para host)
- A máquina virtual está em execução no [hardware de host degradado](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) que tem previsão para falhar em breve
- Manutenção iniciada pelo usuário (por exemplo, um usuário reinicia ou reimplanta uma VM)
- Remoção de instâncias [VM spot](../spot-vms.md) e [Conjunto de dimensionamento spot](../../virtual-machine-scale-sets/use-spot.md).

## <a name="the-basics"></a>Noções básicas  

  O Serviço de Metadados do Azure expõe informações sobre a execução de VMs usando um ponto de extremidade REST acessível de dentro da VM. As informações estão disponíveis por meio de um IP não roteável para que ele não seja exposto fora da VM.

### <a name="scope"></a>Escopo
Os eventos agendados são entregues a:

- Máquinas virtuais autônomas.
- Todas as VMs em um serviço de nuvem.
- Todas as VMs em um conjunto de disponibilidade.
- Todas as VMs em uma zona de disponibilidade. 
- Todas as VMs em um grupo de posicionamento do conjunto de dimensionamento. 

> [!NOTE]
> Específico para VMs em uma zona de disponibilidade, os eventos agendados vão para VMs únicas em uma zona.
> Por exemplo, se você tiver 100 VMs em um conjunto de disponibilidade e houver uma atualização para uma delas, o evento agendado vai para todos os 100, enquanto que, se houver 100 VMs únicas em uma zona, o evento será acessado apenas para a VM que está sendo afetada.

Como resultado, verifique o campo `Resources` no evento para identificar quais VMs são afetadas.

### <a name="endpoint-discovery"></a>Descoberta do ponto de extremidade
Para VMs habilitadas para VNET, o serviço de metadados está disponível de um IP não roteável estático, `169.254.169.254`. O ponto de extremidade completo para a versão mais recente dos eventos agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

Se a VM não for criada em uma Rede Virtual, os casos padrão para serviços de nuvem e VMs clássicas, uma lógica adicional será necessária para descobrir o endereço IP a ser utilizado. Consulte esse exemplo para saber como [descobrir o ponto de extremidade do host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Disponibilidade de Versão e Região
O serviço de Eventos Agendados tem controle de versão. As versões são obrigatórias; a versão atual é `2019-08-01`.

| Versão | Tipo de Versão | Regiões | Notas de versão | 
| - | - | - | - | 
| 01-08-2019 | Disponibilidade geral | Todos | <li> Adição de suporte para o EventSource |
| 01-04-2019 | Disponibilidade geral | Todos | <li> Adição de suporte para a Descrição do Evento |
| 2019-01-01 | Disponibilidade geral | Todos | <li> Suporte adicionado para conjuntos de dimensionamento de máquinas virtuais EventType “Terminate” |
| 2017-11-01 | Disponibilidade geral | Todos | <li> Suporte adicionado para o EventType de remoção de VM spot “Preempt”<br> | 
| 2017-08-01 | Disponibilidade geral | Todos | <li> Removido o sublinhado inicial dos nomes de recursos para as VMs de IaaS<br><li>Requisito de cabeçalho de metadados imposto para todas as solicitações | 
| 2017-03-01 | Visualização | Todos | <li>Versão inicial |


> [!NOTE] 
> Versões prévias anteriores de Eventos Agendados compatíveis {mais recentes} como a api-version. Esse formato não é mais suportado e será substituído no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Habilitar e Desabilitar Eventos Agendados
Eventos Agendados são habilitados para o serviço na primeira vez em que você faz uma solicitação para eventos. Você deve esperar um atraso na resposta em sua primeira chamada de até dois minutos.

Eventos Agendados são desabilitados para o serviço se ele não fizer uma solicitação por 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo usuário
A manutenção de VMs iniciada pelo usuário pelo Portal do Azure, API, CLI ou PowerShell resulta em um evento agendado. Você pode então testar a lógica de preparação de manutenção em seu aplicativo e permite que seu aplicativo se prepare para manutenção iniciada pelo usuário.

Se você reinicia uma VM, um evento com o tipo `Reboot` é agendado. Se você reimplanta uma VM, um evento com o tipo `Redeploy` é agendado.

## <a name="use-the-api"></a>Usar a API

### <a name="headers"></a>headers
Ao consultar o Serviço de Metadados você deverá fornecer o cabeçalho `Metadata:true` para garantir que a solicitação não foi redirecionada de forma involuntária. O cabeçalho `Metadata:true` é necessário para todas as solicitações de eventos programados. A não inclusão do cabeçalho na solicitação resulta em uma resposta de "Solicitação Incorreta" do Serviço de Metadados.

### <a name="query-for-events"></a>Consulta de eventos
Você pode consultar Eventos Agendados realizando a chamada a seguir:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que não há eventos agendados no momento.
No caso de haver eventos agendados, a resposta contém uma matriz de eventos. 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},       
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  Descrição |
| - | - |
| EventId | Identificador global exclusivo para esse evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto desse evento. <br><br> Valores: <br><ul><li> `Freeze`: A máquina virtual está agendada para ser colocada em pausa por alguns segundos. A conectividade de CPU e rede pode ser suspensa, mas não há nenhum impacto na memória ou arquivos abertos.<li>`Reboot`: A Máquina Virtual está agendada para ser reinicializada (a memória não persistente é perdida). <li>`Redeploy`: A Máquina Virtual está agendada para ser movida para outro nó (os discos efêmeros são perdidos). <li>`Preempt`: A máquina virtual spot está sendo excluída (discos efêmeros são perdidos). <li> `Terminate`: a máquina virtual está agendada para ser excluída. |
| ResourceType | O tipo de recurso que esse evento afeta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| A lista de recursos que esse evento afeta. É garantido que a lista contém máquinas de no máximo um [domínio de atualização](../availability.md), mas pode não conter todas as máquinas no UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Status desse evento. <br><br> Valores: <ul><li>`Scheduled`: Esse evento está agendado para ser iniciado após o tempo especificado na propriedade `NotBefore`.<li>`Started`: Esse evento foi iniciado.</ul> Nenhum `Completed` ou status semelhante é fornecido em nenhum momento. O evento não é mais retornado quando é concluído.
| NotBefore| Tempo após o qual esse evento pode começar. <br><br> Exemplo: <br><ul><li> Segunda-feira, 19 de setembro de 2016 18:29:47 GMT  |
| Descrição | A descrição deste evento. <br><br> Exemplo: <br><ul><li> O servidor host está passando por manutenção. |
| EventSource | Iniciador do evento. <br><br> Exemplo: <br><ul><li> `Platform`: Esse evento é iniciado pela plataforma. <li>`User`: esse evento é iniciado pelo usuário. |

### <a name="event-scheduling"></a>Agendamento do evento
Cada evento é agendado uma quantidade mínima de tempo no futuro com base no tipo de evento. Esse tempo é refletido na propriedades `NotBefore` de um evento. 

|EventType  | Aviso mínimo |
| - | - |
| Congelamento| 15 minutos |
| Reboot | 15 minutos |
| Reimplantar | 10 minutos |
| Preempt | 30 segundos |
| Terminate | [Configurável pelo usuário](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): De 5 a 15 minutos |

> [!NOTE] 
> Em alguns casos, o Azure é capaz de prever a falha do host devido a hardware degradado e agendará uma migração para tentar reduzir a interrupção em seu serviço. As máquinas virtuais afetadas receberão um evento agendado com um `NotBefore`, que normalmente é de alguns dias no futuro. O tempo real depende da avaliação de risco da falha prevista. O Azure tenta avisar com sete dias de antecedência, quando possível, mas o tempo real varia e poderá ser menor se houver uma previsão de grande chance de falha de software iminente. Para minimizar o risco para o serviço caso o hardware falhe antes da migração iniciada pelo sistema, recomendamos que você reimplante sua máquina virtual assim que possível.

### <a name="polling-frequency"></a>Frequência de sondagem

Você pode sondar o ponto de extremidade para atualizações com frequência ou com pouca frequência como desejar. No entanto, quanto maior o tempo entre as solicitações, mais tempo você pode perder para reagir a um evento futuro. A maioria dos eventos tem de 5 a 15 minutos de aviso prévio, embora, em alguns casos, o aviso de antecedência possa ser de até 30 segundos. Para garantir que você tenha o máximo de tempo possível para realizar ações de mitigação, recomendamos que você monitore o serviço uma vez por segundo.

### <a name="start-an-event"></a>Iniciar um evento 

Após a descoberta de um evento futuro e concluído sua lógica de desligamento normal, você poderá aprovar o evento pendente fazendo uma chamada `POST` para o Serviço de Metadados com `EventId`. Essa chamada indica para o Azure que ele pode encurtar o tempo mínimo de notificação (quando possível). 

A seguir vemos o exemplo de JSON esperado no corpo da solicitação `POST`. A solicitação deve conter uma lista de `StartRequests`. Cada `StartRequest` contém o `EventId` para o evento que você deseja agilizar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Exemplo de bash
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Reconhecer um evento permite que o evento prossiga para todos `Resources` no evento, e não apenas a VM que reconhece o evento. Portanto, é possível eleger um líder para coordenar o reconhecimento, que pode ser tão simples como a primeira máquina no campo `Resources`.

## <a name="python-sample"></a>Exemplo de Python 

O exemplo a seguir consulta o Serviço de Metadados para eventos agendados e aprova cada evento pendente:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Próximas etapas 
- Inspecione [Eventos Agendados no Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) para ver uma demonstração. 
- Examine os Exemplos de código de Eventos Agendados no [Repositório GitHub de Eventos Agendados de Metadados de Instância do Azure](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Leia mais sobre as APIs disponíveis no [Serviço de Metadados de Instância](instance-metadata-service.md).
- Saiba mais sobre a [manutenção planejada para máquinas virtuais do Linux no Azure ](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json).
