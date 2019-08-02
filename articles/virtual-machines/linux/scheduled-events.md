---
title: Eventos agendados para VMs do Linux no Azure | Microsoft Docs
description: Agendar eventos usando o Serviço de Metadados do Azure para nas máquinas virtuais do Linux.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: f7691bcd6f9f3137f48bdd52722c887c4777a32c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706546"
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

- [Manutenção iniciada na plataforma](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (por exemplo, VM reinicialização, migração ao vivo ou atualizações para o host de preservação da memória)
- Hardware degradado
- Manutenção iniciada pelo usuário (por exemplo, um usuário reinicia ou reimplanta uma VM)
- [Remoção de VMs de baixa prioridade](https://azure.microsoft.com/blog/low-priority-scale-sets) conjuntos de dimensionamento

## <a name="the-basics"></a>Noções básicas  

  O Serviço de Metadados do Azure expõe informações sobre a execução de VMs usando um ponto de extremidade REST acessível de dentro da VM. As informações estão disponíveis por meio de um IP não roteável para que ele não seja exposto fora da VM.

### <a name="scope"></a>Escopo
Os eventos agendados são entregues a:

- Máquinas virtuais de autônomas.
- Todas as VMs em um serviço de nuvem.
- Todas as VMs em um conjunto de disponibilidade.
- Todas as VMs em um grupo de posicionamento do conjunto de dimensionamento. 

Como resultado, verifique o campo `Resources` no evento para identificar quais VMs são afetadas.

### <a name="endpoint-discovery"></a>Descoberta do ponto de extremidade
Para VMs habilitadas para VNET, o serviço de metadados está disponível de um IP não roteável estático, `169.254.169.254`. O ponto de extremidade completo para a versão mais recente dos eventos agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Se a VM não for criada em uma Rede Virtual, os casos padrão para serviços de nuvem e VMs clássicas, uma lógica adicional será necessária para descobrir o endereço IP a ser utilizado. Consulte esse exemplo para saber como [descobrir o ponto de extremidade do host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Disponibilidade de Versão e Região
O serviço de Eventos Agendados tem controle de versão. As versões são obrigatórias; a versão atual é `2017-11-01`.

| Versão | Tipo de Versão | Regions | Notas de versão | 
| - | - | - | - | 
| 2017-11-01 | Disponibilidade geral | Todos | <li> Adicionado suporte para remoção de VMs de baixa prioridade EventType 'Preempt'<br> | 
| 2017-08-01 | Disponibilidade geral | Todos | <li> Removido o sublinhado inicial dos nomes de recursos para as VMs de IaaS<br><li>Requisito de cabeçalho de metadados imposto para todas as solicitações | 
| 2017-03-01 | Visualizar | Todos | <li>Versão inicial


> [!NOTE] 
> Versões prévias anteriores de Eventos Agendados compatíveis {mais recentes} como a api-version. Esse formato não é mais suportado e será substituído no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Habilitar e Desabilitar Eventos Agendados
Eventos Agendados são habilitados para o serviço na primeira vez em que você faz uma solicitação para eventos. Você deve esperar um atraso na resposta em sua primeira chamada de até dois minutos.

Eventos Agendados são desabilitados para o serviço se ele não fizer uma solicitação por 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo usuário
A manutenção de VMs iniciada pelo usuário pelo Portal do Azure, API, CLI ou PowerShell resulta em um evento agendado. Você pode então testar a lógica de preparação de manutenção em seu aplicativo e permite que seu aplicativo se prepare para manutenção iniciada pelo usuário.

Se você reinicia uma VM, um evento com o tipo `Reboot` é agendado. Se você reimplanta uma VM, um evento com o tipo `Redeploy` é agendado.

## <a name="use-the-api"></a>Usar a API

### <a name="headers"></a>Cabeçalhos
Ao consultar o Serviço de Metadados você deverá fornecer o cabeçalho `Metadata:true` para garantir que a solicitação não foi redirecionada de forma involuntária. O cabeçalho `Metadata:true` é necessário para todas as solicitações de eventos programados. A não inclusão do cabeçalho na solicitação resulta em uma resposta de "Solicitação Incorreta" do Serviço de Metadados.

### <a name="query-for-events"></a>Consulta de eventos
Você pode consultar Eventos Agendados realizando a chamada a seguir:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que não há eventos agendados no momento.
No caso de haver eventos agendados, a resposta contém uma matriz de eventos. 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  DESCRIÇÃO |
| - | - |
| EventId | Identificador global exclusivo para esse evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto desse evento. <br><br> Valores: <br><ul><li> `Freeze`: A máquina Virtual está agendada para pausar por alguns segundos. Conectividade de rede e CPU pode ser suspenso, mas não há nenhum impacto na memória ou em arquivos abertos.<li>`Reboot`: A Máquina Virtual está agendada para ser reinicializada (a memória não persistente é perdida). <li>`Redeploy`: A Máquina Virtual está agendada para ser movida para outro nó (os discos efêmeros são perdidos). <li>`Preempt`: A máquina Virtual de baixa prioridade está sendo excluída (discos efêmeros são perdidos).|
| ResourceType | O tipo de recurso que esse evento afeta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| A lista de recursos que esse evento afeta. É garantido que a lista contém máquinas de no máximo um [domínio de atualização](manage-availability.md), mas pode não conter todas as máquinas no UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Status desse evento. <br><br> Valores: <ul><li>`Scheduled`: Esse evento está agendado para ser iniciado após o tempo especificado na propriedade `NotBefore`.<li>`Started`: Esse evento foi iniciado.</ul> Nenhum `Completed` ou status semelhante é fornecido em nenhum momento. O evento não é mais retornado quando é concluído.
| NotBefore| Tempo após o qual esse evento pode começar. <br><br> Exemplo: <br><ul><li> Segunda-feira, 19 de setembro de 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento do evento
Cada evento é agendado uma quantidade mínima de tempo no futuro com base no tipo de evento. Esse tempo é refletido na propriedades `NotBefore` de um evento. 

|EventType  | Aviso mínimo |
| - | - |
| Congelamento| 15 minutos |
| Reboot | 15 minutos |
| Reimplantar | 10 minutos |
| Antecipe | 30 segundos |

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
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Reconhecer um evento permite que o evento prossiga para todos `Resources` no evento, e não apenas a VM que reconhece o evento. Portanto, é possível eleger um líder para coordenar o reconhecimento, que pode ser tão simples como a primeira máquina no campo `Resources`.

## <a name="python-sample"></a>Exemplo de Python 

O exemplo a seguir consulta o Serviço de Metadados para eventos agendados e aprova cada evento pendente:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01"
headers = "{Metadata:true}"
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
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Próximas etapas 
- Inspecione [Eventos Agendados no Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) para ver uma demonstração. 
- Examine os Exemplos de código de Eventos Agendados no [Repositório GitHub de Eventos Agendados de Metadados de Instância do Azure](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Leia mais sobre as APIs disponíveis no [Serviço de Metadados de Instância](instance-metadata-service.md).
- Saiba mais sobre a [manutenção planejada para máquinas virtuais do Linux no Azure ](planned-maintenance.md).
