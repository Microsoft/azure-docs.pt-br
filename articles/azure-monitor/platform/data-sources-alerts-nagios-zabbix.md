---
title: Coletar alertas do Nagios e do Zabbix no Azure Monitor | Microsoft Docs
description: O Nagios e o Zabbix são ferramentas de monitoramento de software livre. Você pode coletar os alertas dessas ferramentas para o Azure Monitor para analisá-los junto com os alertas de outras fontes.  Este artigo descreve como configurar o agente do Log Analytics para Linux para coletar alertas desses sistemas.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 0ed6747573edf4c059eb29d28107a22706c52856
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759972"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>Coletar alertas do Nagios e do Zabbix no Azure Monitor do Agente do Log Analytics para Linux 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

O [Nagios](https://www.nagios.org/) e o [Zabbix](https://www.zabbix.com/) são ferramentas de monitoramento de software livre. Você pode coletar alertas dessas ferramentas para o Azure Monitor para analisá-los junto com os alertas de outras fontes.  Este artigo descreve como configurar o agente do Log Analytics para Linux para coletar alertas desses sistemas.


> [!NOTE]
> [Os alertas criados pelo Azure Monitor](alerts-overview.md) são armazenados separadamente dos dados de log e não podem ser acessados a partir de consultas de log.

 
## <a name="prerequisites"></a>Pré-requisitos
O agente do Log Analytics para Linux dá suporte para coletar alertas do Nagios até a versão 4.2.x e do Zabbix até a versão 2.x.

## <a name="configure-alert-collection"></a>Configurar coleta de alertas

### <a name="configuring-nagios-alert-collection"></a>Configurar coleta de alertas do Nagios
Para coletar alertas, execute as etapas a seguir no servidor do Nagios.

1. Conceda ao usuário **omsagent** acesso de leitura ao arquivo de log do Nagios `/var/log/nagios/nagios.log`. Supondo que o arquivo nagios.log pertença ao grupo `nagios`, você poderá adicionar o usuário **omsagent** ao grupo **nagios**. 

    sudo usermod -a -G nagios omsagent

2.  Modifique o arquivo de configuração em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Verifique se as entradas a seguir estão presentes e se não foram comentadas:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Reinicie o daemon omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Configurar coleta de alertas do Zabbix
Para obter alertas de um servidor do Zabbix, você precisa especificar um usuário e senha com *texto não criptografado*.  Embora não seja ideal, é recomendável que você crie um usuário Zabbix com permissões somente leitura para capturar alarmes relevantes.

Para coletar alertas no servidor do Nagios, execute as etapas a seguir.

1. Modifique o arquivo de configuração em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Verifique se as entradas a seguir estão presentes e se não foram comentadas.  Altere o nome de usuário e senha para valores para o seu ambiente do Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Reinicie o daemon omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Registros de alerta
Você pode recuperar registros de alerta do Nagios e do Zabbix usando [pesquisas de logs](../log-query/log-query-overview.md) no Azure Monitor.

### <a name="nagios-alert-records"></a>Registros de alerta do Nagios

Registros de alerta coletados pelo Nagios têm **Type** definido como **Alert** e **SourceSystem** definido como **Nagios**.  Eles têm as propriedades indicadas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| `Type` |*Alerta* |
| `SourceSystem` |*Nagios* |
| `AlertName` |Nome do alerta. |
| `AlertDescription` | Descrição do alerta. |
| `AlertState` | Status do serviço ou do host.<br><br>OK<br>AVISO<br>PARA CIMA<br>PARA BAIXO |
| `HostName` | Nome do host que criou o alerta. |
| `PriorityNumber` | Nível de prioridade do alerta. |
| `StateType` | O tipo de estado do alerta.<br><br>SUAVE – problema que não foi verificado novamente.<br>GRAVE – problema que foi verificado novamente um número especificado de vezes.  |
| `TimeGenerated` |Data e hora em que o alerta foi criado. |


### <a name="zabbix-alert-records"></a>Registros de alerta do Zabbix
Registros de alerta coletados pelo Zabbix têm **Type** definido como **Alert** e **SourceSystem** definido como **Zabbix**.  Eles têm as propriedades indicadas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| `Type` |*Alerta* |
| `SourceSystem` |*Zabbix* |
| `AlertName` | Nome do alerta. |
| `AlertPriority` | Severidade do alerta.<br><br>não classificado<br>informações<br>Aviso<br>média<br>alto<br>desastre  |
| `AlertState` | Estado do alerta.<br><br>0 - O estado está atualizado.<br>1 – o estado é desconhecido.  |
| `AlertTypeNumber` | Especifica se o alerta pode ou não gerar vários eventos de problema.<br><br>0 - O estado está atualizado.<br>1 – o estado é desconhecido.    |
| `Comments` | Comentários adicionais para o alerta. |
| `HostName` | Nome do host que criou o alerta. |
| `PriorityNumber` | Valor que indica a gravidade do alerta.<br><br>0 – não classificado<br>1 – informações<br>2 – aviso<br>3 – média<br>4 – alta<br>5 – desastre |
| `TimeGenerated` |Data e hora em que o alerta foi criado. |
| `TimeLastModified` |Data e hora em que o estado do alerta foi alterado pela última vez. |


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre os [alertas](alerts-overview.md) no Azure Monitor.
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 
