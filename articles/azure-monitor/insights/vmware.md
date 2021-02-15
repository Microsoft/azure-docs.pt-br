---
title: Monitoramento do VMware solução no Azure Monitor | Microsoft Docs
description: Saiba mais sobre como a solução de Monitoramento de VMware pode ajudar a gerenciar logs e monitorar hosts ESXi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: be50deb836082354db899e84ef24d75c4d403432
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450402"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Monitoramento do VMware (preterido) solução no Azure Monitor

![Símbolo de VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A solução de Monitoramento do VMware foi preterida.  Os clientes que já instalaram a solução podem continuar a usá-la, mas o Monitoramento do VMware não poderá ser adicionado a um novo workspace.

A solução Monitoramento do VMware no Azure Monitor é uma solução que ajuda a criar uma abordagem centralizada de registro em log e monitoramento para grandes logs do VMware. Este artigo descreve como você pode solucionar problemas, capturar e gerenciar os hosts ESXi em uma única localização usando a solução. Com a solução, você pode ver os dados detalhados de todos os seus hosts ESXi em uma única localização. Você pode ver os principais eventos contagens, status e tendências de VM e hosts ESXi fornecidos por meio dos logs de host ESXi. Você pode solucionar problemas exibindo e pesquisando logs de host ESXi centralizados. Além disso, você pode criar alertas com base em consultas de pesquisa de log.

A solução usa a funcionalidade de syslog nativo do host ESXi para enviar dados por push para um destino de VM, que tem o agente do Log Analytics. No entanto, a solução não grava arquivos no syslog dentro da VM de destino. O agente do Log Analytics abre a porta 1514 e escuta nela. Depois de receber os dados, o agente de Log Analytics envia os dados por push para Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Instale e configure a solução
Use as informações a seguir para instalar e configurar a solução.

* Adicionar a solução de monitoramento do VMware à sua assinatura usando o processo descrito em [instalar uma solução de monitoramento](./solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Hosts VMware ESXi com suporte
Host vSphere ESXi 5.5, 6.0 e 6.5

#### <a name="prepare-a-linux-server"></a>Preparar um servidor Linux
Crie um sistema operacional do Linux VM para receber todos os dados syslog de hosts ESXi. O [agente do Log Analytics para Linux](../learn/quick-collect-linux-computer.md) é o ponto de coleta para todos os dados syslog de hosts ESXi. Você pode usar vários hosts ESXi para encaminhar logs para um único servidor Linux, como no exemplo a seguir.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![fluxo syslog](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurar coleção syslog
1. Configure o encaminhamento syslog para VSphere. Para obter informações detalhadas ajudar a configurar o encaminhamento syslog, veja [Configurar syslog em ESXi 5.0 e superiores (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Vá para **configuração do host ESXi**  >  **software**  >  **Configurações avançadas**  >  **syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. No campo *Syslog.global.logHost*, adicione o servidor Linux e o número da porta *1514*. Por exemplo, `tcp://hostname:1514` ou `tcp://123.456.789.101:1514`
1. Abra o firewall do host ESXi para syslog. Configuração do host **ESXi**  >  **Software**  >  **Perfil**  >  de segurança **Firewall** e abra **Propriedades**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Marque o Console vSphere para confirmar que esse syslog está configurado corretamente. Confirme no host ESXI que a porta **1514** está configurada.
1. Baixe e instale o agente do Log Analytics para Linux no servidor Linux. Para saber mais, veja a [Documentação do Agente do Log Analytics para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Depois que o agente do Log Analytics para Linux estiver instalado, vá para o diretório /etc/opt/microsoft/omsagent/sysconf/omsagent.d e copie o arquivo vmware_esxi.conf para o diretório /etc/opt/microsoft/omsagent/conf/omsagent.d e altere o proprietário/grupo e as permissões do arquivo. Por exemplo:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Reinicie o agente do Log Analytics para Linux ao executar `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Teste a conectividade entre o servidor Linux e o host ESXi usando o comando `nc` no Host ESXi. Por exemplo:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. No portal do Azure, execute uma consulta de log para `VMware_CL` . Quando Azure Monitor coleta os dados do syslog, ele retém o formato syslog. No portal, alguns campos específicos são capturados, como *Hostname* e *ProcessName*.  

    ![Captura de tela mostra uma consulta de log para Type = VMware_CL com um resultado de carimbo de data/hora.](./media/vmware/type.png)  

    Se seus resultados da pesquisa de log de exibição forem semelhantes àqueles da imagem acima, você estará pronto para usar o painel de solução de Monitoramento do VMWare.  

## <a name="vmware-data-collection-details"></a>Detalhes da coleta de dados do VMware
A solução de Monitoramento do VMware coleta vários dados de log e métricas de desempenho de hosts ESXi usando os Agentes do Log Analytics para Linux que você tenha habilitado.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados.

| plataforma | Agente do Log Analytics para Linux | Agente do System Center Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |a cada 3 minutos |

A tabela a seguir mostram exemplos de campos de dados coletados pela solução de Monitoramento de VMware:

| nome do campo | descrição |
| --- | --- |
| Device_s |dispositivos de armazenamento do VMware |
| ESXIFailure_s |tipos de falha |
| EventTime_t |horário em que o evento ocorreu |
| HostName_s |nome de host ESXi |
| Operation_s |criar VM ou excluir VM |
| ProcessName_s |nome do evento |
| ResourceId_s |nome do host do VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Status de SCSI do VMware |
| SyslogMessage_s |Dados syslog |
| UserName_s |usuário que criou ou excluiu a VM |
| VMName_s |Nome da VM |
| Computador |computador host |
| TimeGenerated |horário em que os dados foram gerados |
| DataCenter_s |Datacenter do VMware |
| StorageLatency_s |latência de armazenamento (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Visão geral da solução de Monitoramento de VMware
O bloco de VMware aparece no espaço de trabalho do Log Analytics. Ele fornece uma visão ampla de eventuais falhas. Quando você clica no bloco, você entra em um modo de exibição de painel.

![Captura de tela mostra o bloco VMware, exibindo nove falhas.](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navegar pelo modo de exibição de painel
No modo de exibição de painel **VMware**, as folhas são organizadas por:

* Contagem de Status de Falha
* Contagens de hosts principais por evento
* Contagens de eventos principais
* Atividades da Máquina Virtual
* Eventos de Disco do Host ESXi

![solução1](./media/vmware/solutionview1-1.png)

![solução2](./media/vmware/solutionview1-2.png)

Clique em qualquer folha para abrir o painel de pesquisa do Log Analytics que mostra informações detalhadas específicas para essa folha.

A partir daqui, você pode editar a consulta de log para modificá-la para algo específico. Para obter detalhes sobre como criar consultas de log, consulte [Localizar dados usando consultas de log em Azure monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Encontrar eventos do host ESXi
Um único host ESXi gera vários logs, com base em seus processos. A solução de Monitoramento do VMware centraliza-os e resume as contagens de eventos. Essa exibição centralizada ajuda a entender qual host ESXi tem um alto volume de eventos e quais eventos ocorrem com mais frequência em seu ambiente.

![event](./media/vmware/events.png)

Você pode aprofundar-se ainda mais, clicando em um host ESXi ou um tipo de evento.

Quando você clica em um nome de host ESXi, as informações desse host são exibidas. Se você quiser restringir os resultados com o tipo de evento, deverá adicionar `“ProcessName_s=EVENT TYPE”` em sua consulta de pesquisa. Você pode selecionar **ProcessName** no filtro de pesquisa. Isso restringe as informações para você.

![Captura de tela do host ESXi por contagem de eventos e divisão por folhas de tipo de evento na exibição do painel Monitoramento do VMware.](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Localizar atividades altas de VM
Uma máquina virtual pode ser criada em e excluída de qualquer host ESXi. Isso é útil para um administrador identificar a quantidade de VMs criadas por um host ESXi. Isso, por sua vez, ajuda a entender o planejamento de capacidade e desempenho. Manter o controle dos eventos de atividade de VM é essencial ao gerenciar seu ambiente.

![Captura de tela da folha atividades de máquina virtual no painel Monitoramento do VMware, mostrando um grafo de criação e exclusão de VM pelo host ESXi.](./media/vmware/vmactivities1.png)

Se você quiser ver dados adicionais de criação de VMs de host ESXi, clique em um nome de host ESXi.

![Captura de tela de um painel do painel de Monitoramento do VMware mostrando uma tabela com uma linha de dados para cada criação de máquina virtual por um host ESXi.](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Consultas de log comuns
A solução inclui outras consultas úteis que podem ajudá-lo a gerenciar seus hosts ESXi, como espaço de armazenamento de alta latência de armazenamento e falha do caminho.

![A captura de tela mostra as pesquisas RECOMENDAdas, que são consultas armazenadas úteis.](./media/vmware/queries.png)


#### <a name="save-queries"></a>Salvar consultas
Salvar consultas de log é um recurso padrão no Azure Monitor e pode ajudá-lo a manter todas as consultas que você encontrou úteis. Depois de criar uma consulta você considerar útil, salve-a clicando em **Favoritos**. Uma consulta salva permite que você reutilize-a posteriormente com facilidade usando a página [Meu Painel](../learn/tutorial-logs-dashboards.md), na qual você pode criar seus próprios painéis personalizados.

![A captura de tela mostra parte de um painel personalizado rotulado pesquisa de logs com ícones para desfazer, exportar, alerta, salvar, favoritos e histórico.](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Criar alertas com base em consultas
Depois de criar as consultas, talvez você queira usá-las para alertá-lo quando eventos específicos ocorrem. Veja [Alertas no Log Analytics](../platform/alerts-overview.md) para obter informações sobre como criar alertas. Para obter exemplos de consultas de alertas e outros exemplos de consultas, veja a postagem de blog [Monitorar VMware usando o Log Analytics](/archive/blogs/msoms/monitor-vmware-using-oms-log-analytics).

## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>O que é necessário na configuração do host de ESXi? Que impacto terá no meu ambiente atual?
A solução usa o mecanismo de encaminhamento do Syslog de Host de ESXi nativo. Não é necessário qualquer software adicional da Microsoft no Host de ESXi para capturar os logs. Ele deve ter um impacto baixo em seu ambiente existente. No entanto, você precisa definir o encaminhamento de syslog, que é a funcionalidade de ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>É necessário reiniciar o meu host de ESXi?
Não. Esse processo não requer uma reinicialização. Às vezes, vSphere não atualiza corretamente o syslog. Nesse caso, faça logon para o host de ESXi e recarregue o syslog. Novamente, você não precisa reiniciar o host, então esse processo não é perturbador para seu ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Posso aumentar ou diminuir o volume de dados de log enviado ao Log Analytics?
Sim, pode. Você pode usar as configurações de nível de Log do Host de ESXi no vSphere. Coleta de log se baseia no nível de *informações*. Portanto, se você deseja auditar a criação da VM ou exclusão, você precisa manter o nível de *informações* em Hostd. Para obter mais informações, consulte a [Base de dados de conhecimento do VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Por que o Hostd não está fornecendo dados do Log Analytics? Minha configuração de log está definida como informações.
Havia um bug do host de ESXi para o carimbo de data/hora do syslog. Para obter mais informações, consulte a [Base de dados de conhecimento do VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Depois de aplicar a solução alternativa, Hostd deve funcionar normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Posso ter vários dados syslog de encaminhamento dos hosts de ESXi para uma única VM com omsagent?
Sim. Você pode ter vários encaminhamento dos hosts de ESXi para uma única VM com omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Por que não vejo dados que fluem para o Log Analytics?
Pode haver vários motivos:

* O host de ESXi não está corretamente enviando dados para a VM executando o omsagent. Para testar, execute as seguintes etapas:

  1. Para confirmar, faça logon no host de ESXi usando ssh e execute o seguinte comando: `nc -z ipaddressofVM 1514`

      Se não for bem-sucedido, as configurações do vSphere na Configuração Avançada não estão provavelmente corretas. Consulte [Configurar a coleta de syslog](#configure-syslog-collection) para obter informações sobre como configurar o host de ESXi para encaminhamento de syslog.
  1. Se a conectividade da porta de syslog foi bem-sucedida, mas os dados ainda não aparecem, recarregue o syslog no host de ESXi usando ssh para executar o comando a seguir: `esxcli system syslog reload`
* A VM com o agente do Log Analytics não está definida corretamente. Para corrigir isso, execute as seguintes etapas:

  1. O Log Analytics escuta na porta 1514. Use o seguinte comando para verificar se ele está em execução: `netstat -a | grep 1514`
  1. Você deve ver a porta `1514/tcp` abrir. Se você não fizer isso, verifique se o omsagent está instalado corretamente. Se você não ver as informações da porta, a porta de syslog não está aberta no VM.

    a. Verifique se o agente do Log Analytics está em execução usando `ps -ef | grep oms`. Se ele não está em execução, inicie o processo executando o comando `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Abra o arquivo `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` .

     c. Verifique se o usuário apropriado e a configuração do grupo são válidos, semelhante a: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Se o arquivo não existe ou o usuário e a configuração de grupo estão errados, tome uma ação corretiva ao [Preparar um servidor Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Próximas etapas
* Usar [consultas de log](../log-query/log-query-overview.md) no Log Analytics para exibir dados detalhados de hosts do VMware.
* [Criar seus próprios painéis](../learn/tutorial-logs-dashboards.md) mostrando os dados de host do VMware.
* [Criar alertas](../platform/alerts-overview.md) quando ocorrem eventos específicos de host do VMware.

