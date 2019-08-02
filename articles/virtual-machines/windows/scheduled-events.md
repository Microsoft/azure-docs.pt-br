---
title: Eventos agendados para VMs do Windows no Azure | Microsoft Docs
description: Agendado eventos usando o serviço de metadados do Azure para em suas máquinas virtuais do Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: ddd34147848ecb3a964eac3d618b452f5eb43f19
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710296"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Serviço de Metadados do Azure: Eventos Agendados para VMs do Windows

Os eventos agendados são um serviço de metadados do Azure que dá ao aplicativo tempo para se preparar para a manutenção de máquinas virtuais. Ele fornece informações sobre eventos de manutenção futuros (por exemplo, reinicializar) para que seu aplicativo possa se preparar e limitar as interrupções. Ele está disponível para todos os tipos de Máquina Virtual do Azure, incluindo PaaS e IaaS no Windows e no Linux. 

Para saber mais sobre os Eventos Agendados no Linux, confira [Eventos agendados para VMs do Linux](../linux/scheduled-events.md).

> [!Note] 
> Eventos Agendados estão disponível geralmente em todas as regiões do Azure. Consulte [Disponibilidade de Versão e Região](#version-and-region-availability) para obter informações sobre a versão mais recente.

## <a name="why-scheduled-events"></a>Por que Eventos Agendados?

Muitos aplicativos podem se beneficiar do tempo para se preparar para a manutenção de máquinas virtuais. O tempo pode ser usado para executar tarefas específicas de aplicativo que melhoram a disponibilidade, a confiabilidade e a capacidade de manutenção, incluindo: 

- Ponto de verificação e restauração
- Descarregamento de conexão
- Failover de réplica primária 
- Remoção do pool do balanceador de carga
- Log de eventos
- Desligamento normal 

Usando eventos agendados, seu aplicativo pode descobrir quando a manutenção ocorrerá e acionará tarefas para limitar seu impacto. Habilitar eventos agendados, oferece sua máquina virtual uma quantidade mínima de tempo antes que a atividade de manutenção é realizada. Consulte a seção de agendamento do evento abaixo para obter detalhes.

Os eventos agendados fornecem eventos nos seguintes casos de uso:
- [Manutenção iniciada na plataforma](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (por exemplo, VM reinicialização, migração ao vivo ou atualizações para o host de preservação da memória)
- Hardware degradado
- Manutenção iniciada pelo usuário (por exemplo, o usuário reinicia ou reimplanta uma VM)
- [Remoção de VMs de baixa prioridade](https://azure.microsoft.com/blog/low-priority-scale-sets) conjuntos de dimensionamento

## <a name="the-basics"></a>Noções básicas  

O Serviço de Metadados do Azure expõe informações sobre a execução de Máquinas Virtuais usando um Ponto de Extremidade REST acessível de dentro da VM. As informações estão disponíveis por meio de um IP não roteável para que ele não seja exposto fora da VM.

### <a name="endpoint-discovery"></a>Descoberta do ponto de extremidade
Para VMs habilitadas para VNET, o serviço de metadados está disponível de um IP não roteável estático, `169.254.169.254`. O ponto de extremidade completo para a versão mais recente dos eventos agendados é: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Se a Máquina Virtual não for criada em uma Rede Virtual, casos padrão para serviços de nuvem e VMs clássicas, uma lógica adicional será necessária para descobrir o endereço IP a ser utilizado. Consulte esse exemplo para saber como [descobrir o ponto de extremidade do host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Disponibilidade de Versão e Região
O serviço de eventos agendados tem controle de versão. As versões são obrigatórias e a versão atual é `2017-11-01`.

| Versão | Tipo de Versão | Regions | Notas de versão | 
| - | - | - | - |
| 2017-11-01 | Disponibilidade geral | Todos | <li> Adicionado suporte para remoção de VMs de baixa prioridade EventType 'Preempt'<br> | 
| 2017-08-01 | Disponibilidade geral | Todos | <li> Removido o sublinhado inicial dos nomes de recursos para as VMs de IaaS<br><li>Requisito de cabeçalho de metadados imposto para todas as solicitações | 
| 2017-03-01 | Visualizar | Todos |<li>Versão inicial

> [!NOTE] 
> Versões de visualização anteriores de eventos agendados compatíveis {mais recentes} como a api-version. Esse formato não é mais suportado e será substituído no futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Habilitar e Desabilitar Eventos Agendados
Eventos Agendados são habilitados para o serviço na primeira vez em que você faz uma solicitação para eventos. Você deve esperar um atraso na resposta em sua primeira chamada de até dois minutos. Você deve consultar o ponto de extremidade periodicamente para detectar eventos de manutenção futura, bem como o status das atividades de manutenção que estão sendo executadas.

Eventos Agendados são desabilitados para o serviço se ele não fizer uma solicitação por 24 horas.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo usuário
A manutenção de máquinas virtuais iniciada pelo usuário pelo portal do Azure, API, CLI ou PowerShell resulta em um evento agendado. Isso permite que você teste a lógica de preparação de manutenção em seu aplicativo e permite que seu aplicativo se prepare para manutenção iniciada pelo usuário.

Reiniciar uma máquina virtual agendará um evento com tipo `Reboot`. Reimplantar uma máquina virtual agendará um evento com tipo `Redeploy`.

## <a name="using-the-api"></a>Usando a API

### <a name="headers"></a>Cabeçalhos
Ao consultar o Serviço de Metadados você deverá fornecer o cabeçalho `Metadata:true` para garantir que a solicitação não foi redirecionada de forma involuntária. O cabeçalho `Metadata:true` é necessário para todas as solicitações de eventos programados. A não inclusão do cabeçalho na solicitação resultará em uma resposta de solicitação incorreta do Serviço de Metadados.

### <a name="query-for-events"></a>Consulta de eventos
Você pode consultar Eventos agendados realizando a chamada a seguir:

#### <a name="powershell"></a>Powershell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01 -H @{"Metadata"="true"}
```

Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que não há eventos agendados no momento.
No caso de haver eventos agendados, a resposta contém uma matriz de eventos: 
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
O DocumentIncarnation é uma ETag e fornece uma maneira fácil de inspecionar se a carga de eventos foi alterada desde a última consulta.

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  DESCRIÇÃO |
| - | - |
| EventId | Identificador global exclusivo para esse evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto desse evento. <br><br> Valores: <br><ul><li> `Freeze`: A máquina Virtual está agendada para pausar por alguns segundos. Conectividade de rede e CPU pode ser suspenso, mas não há nenhum impacto na memória ou em arquivos abertos. <li>`Reboot`: A Máquina Virtual está agendada para ser reinicializada (a memória não persistente é perdida). <li>`Redeploy`: A Máquina Virtual está agendada para ser movida para outro nó (os discos efêmeros são perdidos). <li>`Preempt`: A máquina Virtual de baixa prioridade está sendo excluída (discos efêmeros são perdidos).|
| ResourceType | Tipo de recurso que esse evento impacta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que esse evento impacta. Isso é garantido para conter máquinas de no máximo um [Domínio de Atualização](manage-availability.md), mas pode não conter todas as máquinas no UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Status do evento | Status desse evento. <br><br> Valores: <ul><li>`Scheduled`: Esse evento está agendado para ser iniciado após o tempo especificado na propriedade `NotBefore`.<li>`Started`: Esse evento foi iniciado.</ul> Não `Completed` status semelhante já foi fornecido; o evento não será mais retornado quando o evento for concluído.
| NotBefore| Tempo após o qual esse evento poderá começar. <br><br> Exemplo: <br><ul><li> Segunda-feira, 19 de setembro de 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Agendamento do evento
Cada evento é agendado uma quantidade mínima de tempo no futuro com base no tipo de evento. Esse tempo é refletido na propriedades `NotBefore` de um evento. 

|EventType  | Aviso mínimo |
| - | - |
| Congelamento| 15 minutos |
| Reboot | 15 minutos |
| Reimplantar | 10 minutos |
| Antecipe | 30 segundos |

### <a name="event-scope"></a>Escopo do Evento     
Os eventos agendados são entregues a:
 - Máquinas virtuais autônomas
 - Todas as Máquinas Virtuais em um Serviço de Nuvem      
 - Todas as máquinas virtuais em um conjunto de disponibilidade      
 - Todas as máquinas virtuais em um Grupo de Posicionamento do Conjunto de Escala.         

Como resultado, você deve verificar o campo `Resources` no evento para identificar quais VMs serão afetadas. 

### <a name="starting-an-event"></a>Como iniciar um evento 

Após a descoberta de um evento futuro e concluído sua lógica de desligamento normal, você poderá aprovar o evento pendente fazendo uma chamada `POST` para o serviço de metadados com `EventId`. Isso indica para o Azure que ele pode encurtar o tempo mínimo de notificação (quando possível). 

A seguir vemos o json esperado no corpo da solicitação `POST`. A solicitação deve conter uma lista de `StartRequests`. Cada `StartRequest` contém o `EventId` para o evento que você deseja agilizar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>Powershell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Reconhecer um evento permite que o evento prossiga para todos `Resources` no evento, e não apenas a máquina virtual que reconhece o evento. Portanto, é possível eleger um líder para coordenar o reconhecimento, que pode ser tão simples como a primeira máquina no campo `Resources`.


## <a name="powershell-sample"></a>Exemplo do PowerShell 

O exemplo a seguir consulta o serviço de metadados para eventos agendados e aprova cada evento pendente.

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-11-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Próximas etapas 

- Inspecionar uma [Demonstração de Eventos Agendados](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) no Azure Friday. 
- Examine os exemplos de código de Eventos Agendados no [Repositório GitHub de Eventos Agendados dos Metadados de Instância do Azure](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Leia mais sobre as API disponíveis no [serviço Metadados da Instância](instance-metadata-service.md).
- Saiba mais sobre a [manutenção planejada para máquinas virtuais do Windows no Azure](planned-maintenance.md).
