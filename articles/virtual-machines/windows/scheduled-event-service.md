---
title: Monitorar eventos agendados para suas VMs no Azure
description: Saiba como monitorar máquinas virtuais do Azure para eventos agendados.
author: mysarn
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 866522da162d22621bd37bf9d2f2fa6838206e17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674694"
---
# <a name="monitor-scheduled-events-for-your-azure-vms"></a>Monitorar eventos agendados para suas VMs do Azure

As atualizações são aplicadas a diferentes partes do Azure todos os dias, para manter os serviços em execução neles seguros e atualizados. Além das atualizações planejadas, eventos não planejados talvez ocorram também. Por exemplo, se qualquer degradação ou falha de hardware for detectada, talvez os serviços do Azure precisem executar manutenção não planejada. Na maioria dos casos, eventos como o uso de migração ao vivo, atualizações que preservam a memória e a geralmente a manutenção de uma barra estrita sobre o impacto das atualizações são quase transparentes para os clientes e não têm nenhum impacto ou, no máximo, fazem a máquina virtual ficar congelada por alguns segundos. No entanto, para alguns aplicativos, mesmo alguns segundos de congelamento da máquina virtual poderiam causar um impacto. Saber com antecedência sobre a futura manutenção do Azure é importante para você ter a melhor experiência para esses aplicativos. O [serviço Eventos Agendados](scheduled-events.md) fornece uma interface programática para você ser notificado sobre manutenção futura e permite que você a manipule normalmente. 

Neste artigo, mostraremos como você pode usar eventos agendados para ser notificado de eventos de manutenção que talvez estejam afetando suas VMs e pode criar uma automação básica que possa ajudar no monitoramento e na análise.


## <a name="routing-scheduled-events-to-log-analytics"></a>Rotear eventos agendados para o Log Analytics

Os Eventos Agendados estão disponíveis como parte do [Serviço de Metadados de Instância do Azure](instance-metadata-service.md), que está disponível em todas as máquinas virtuais do Azure. Os clientes podem escrever a automação para consultar o ponto de extremidade das máquinas virtuais deles a fim de encontrar notificações de manutenção agendada e realizar mitigações, como salvar o estado e tirar a máquina virtual de rotação. É recomendável criar a automação para registrar os Eventos Agendados para que você possa ter um log de auditoria dos eventos de manutenção do Azure. 

Neste artigo, vamos explicar como capturar os Eventos Agendados de manutenção para o Log Analytics. Em seguida, vamos disparar algumas ações básicas de notificação, como enviar um email para sua equipe e obter uma exibição histórica de todos os eventos que afetaram suas máquinas virtuais. Para a agregação e a automação de eventos, vamos usar o [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md), mas você pode usar qualquer solução de monitoramento para coletar esses logs e disparar a automação.

![Diagrama mostrando o ciclo de vida do evento](./media/notifications/events.png)

## <a name="prerequisites"></a>Pré-requisitos

Para este exemplo, você vai precisar criar uma [máquina virtual do Windows em um conjunto de disponibilidade](tutorial-availability-sets.md). Os Eventos Agendados fornecem notificações sobre as alterações que podem afetar qualquer uma das máquinas virtuais em seu conjunto de disponibilidade, Serviço de Nuvem, Conjunto de Dimensionamento de Máquinas Virtuais ou VMs autônomas. Vamos executar um [serviço](https://github.com/microsoft/AzureScheduledEventsService) que sonda se há eventos agendados em uma das VMs que vão funcionar como um coletor, para obter eventos para todas as outras VMs no conjunto de disponibilidade.    

Não exclua o grupo de recursos do grupo ao final do tutorial.

Você também vai precisar [criar um workspace do Log Analytics](../../azure-monitor/logs/quick-create-workspace.md) a ser usado para agregar informações das VMs no conjunto de disponibilidade.

## <a name="set-up-the-environment"></a>Configurar o ambiente

Agora você deve ter duas VMs iniciais em um conjunto de disponibilidade. Agora, precisamos criar uma 3ª VM, chamada `myCollectorVM` , no mesmo conjunto de disponibilidade. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Baixe o arquivo .zip de instalação do projeto do [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Conecte-se a **myCollectorVM** e copie o arquivo .zip para a máquina virtual e extraia todos os arquivos. Em sua VM, abra um prompt do PowerShell. Mova o prompt para a pasta que contém `SchService.ps1`, por exemplo `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`, e configure o serviço.

```powershell
.\SchService.ps1 -Setup
```

Inicie o serviço.

```powershell
.\SchService.ps1 -Start
```

Agora o serviço começará a sondar, a cada 10 segundos, se há eventos agendados e os aprovará para agilizar a manutenção.  Congelar, Reinicializar, Reimplantar e Apropriar são os eventos capturados por Eventos de agendamento.   Observe que você pode estender o script para disparar algumas mitigações antes de aprovar o evento.

Valide o status do serviço e verifique se ele está em execução.

```powershell
.\SchService.ps1 -status  
```

Isso deve retornar `Running`.

Agora o serviço começará a sondar, a cada 10 segundos, se há eventos agendados e os aprovará para agilizar a manutenção.  Congelar, Reinicializar, Reimplantar e Apropriar são os eventos capturados por Eventos de agendamento. Você pode estender o script para disparar algumas mitigações antes de aprovar o evento.

Quando qualquer um dos eventos acima for capturado pelo serviço Eventos Agendados, ele será carregado no Status do Evento do Log de Eventos do Aplicativo, Tipo de Evento, Recursos (nomes de máquina virtual) e NotBefore (período de notificação mínimo). É possível localizar os eventos com a ID 1234 no Log de Eventos do Aplicativo.

Depois que o serviço for configurado e iniciado, ele registrará eventos nos logs de aplicativos do Windows.   Para verificar se isso funciona, reinicie uma das máquinas virtuais no conjunto de disponibilidade e você deverá ver um evento sendo carregado no Visualizador de eventos nos Logs do Windows > Log de aplicativo mostrando a VM reiniciada. 

![Captura de tela do visualizador de eventos.](./media/notifications/event-viewer.png)

Quando eventos são capturados pelo serviço Eventos Agendados, ele será registrado no log de eventos do aplicativo com Status do Evento, Tipo de Evento, Recursos (nome da VM) e NotBefore (período mínimo de notificação). É possível localizar os eventos com a ID 1234 no Log de Eventos do Aplicativo.

> [!NOTE] 
> Neste exemplo, as máquinas virtuais estão em um conjunto de disponibilidade, que nos permitia designar uma única máquina virtual como o coletor para escutar e rotear eventos agendados para nosso workspace do Log Analytics. Se você tiver máquinas virtuais autônomas, poderá executar o serviço em cada máquina virtual e, em seguida, conectá-las individualmente ao seu workspace do Log Analytics.
>
> Para nossa configuração, escolhemos o Windows, mas você pode criar uma solução semelhante no Linux.

A qualquer momento, é possível parar/remover o serviço Evento Agendado usando as opções `–stop` e `–remove`.

## <a name="connect-to-the-workspace"></a>Conectar-se ao workspace


Agora queremos conectar um workspace do Log Analytics à VM do coletor. O workspace do Log Analytics atua como um repositório e vamos configurar a coleta de log de eventos para capturar os logs de aplicativo da VM do coletor. 

 Para rotear os Eventos Agendados para o Log de Eventos, que será salvo como o Log do aplicativo pelo nosso serviço, você precisará conectar sua máquina virtual ao workspace do Log Analytics.  
 
1. Abra a página do workspace criada.
1. Em **Conectar-se a uma fonte de dados**, selecione **VMs (máquinas virtuais) do Azure**.

    ![Conectar-se a uma VM como uma fonte de dados](./media/notifications/connect-to-data-source.png)

1. Pesquise e selecione **myCollectorVM**. 
1. Na nova página para **myCollectorVM**, selecione **Conectar**.

Isso instalará o [Microsoft Monitoring Agent](../extensions/oms-windows.md) em sua máquina virtual. Levará alguns minutos para conectar sua VM ao workspace e instalar a extensão. 

## <a name="configure-the-workspace"></a>Configurar o workspace

1. Abra a página do seu workspace e selecione **Configurações avançadas**.
1. Selecione **Dados** no menu esquerdo e selecione **Logs de Eventos do Windows**.
1. Em **Coletar dos seguintes logs de eventos**, comece a digitar *aplicativo* e, em seguida, selecione **Aplicativo** na lista.

    ![Selecione Configurações avançadas](./media/notifications/advanced.png)

1. Deixe **ERRO**, **AVISO** e **INFORMAÇÕES** selecionados e, em seguida, selecione **Salvar** para salvar as configurações.


> [!NOTE]
> Haverá algum atraso e poderá levar até 10 minutos para que o log esteja disponível. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Criar uma regra de alerta com o Azure Monitor 


Depois que os eventos forem enviados por push para o Log Analytics, você poderá executar a [consulta](../../azure-monitor/logs/log-analytics-tutorial.md) a seguir para procurar os eventos agendados.

1. Na parte superior da página, selecione **Logs** e cole o seguinte na caixa de texto:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Selecione **salvar** e, em seguida `ogQuery` , digite para o nome, deixe **consulta** como o tipo, digite `VMLogs` como a **categoria** e, em seguida, selecione **salvar**. 

    ![Salvar a consulta](./media/notifications/save-query.png)

1. Selecione **Nova regra de alerta**. 
1. Na página **Criar regra**, deixe `collectorworkspace` como o **Recurso**.
1. Em **Condição**, selecione a entrada *Sempre que a pesquisa de logs do cliente for <login undefined>* . A página **Configurar lógica de sinal** será aberta.
1. Em **Valor do limite**, insira *0* e, em seguida, selecione **Concluído**.
1. Em **Ações**, selecione **Criar grupo de ações**. A página **Adicionar grupo de ações** será aberta.
1. Em **Nome do grupo de ações**, digite *myActionGroup*.
1. Em **Nome curto**, digite *myActionGroup*.
1. Em **Grupo de recursos**, selecione **myResourceGroupAvailability**.
1. Em Ações, em **NOME DA AÇÃO**, digite **Email** e, em seguida, selecione **Email/SMS/Push/Voz**. A página **Email/SMS/Push/Voz** será aberta.
1. Selecione **Email**, digite seu endereço de email e, em seguida, selecione **OK**.
1. Na página **Adicionar grupo de ações**, selecione **OK**. 
1. Na página **Criar regra**, em **DETALHES DO ALERTA**, digite *myAlert* para o **Nome da regra de alerta** e digite *Regra de alerta de email* para a **Descrição**.
1. Quando terminar, selecione **Criar regra de alerta**.
1. Reinicie todas as VMs no conjunto de disponibilidade. Em alguns minutos, você deve receber um email de que o alerta foi disparado.

Para gerenciar suas regras de alerta, acesse o grupo de recursos, selecione **Alertas** no menu esquerdo e, em seguida, selecione **Gerenciar regras de alerta** na parte superior da página.

     
## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira a página [Serviço de eventos agendados](https://github.com/microsoft/AzureScheduledEventsService) no GitHub.