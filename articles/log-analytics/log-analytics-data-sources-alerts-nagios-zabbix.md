---
title: Coletar alertas do Nagios e do Zabbix no OMS Log Analytics | Microsoft Docs
description: "O Nagios e o Zabbix são ferramentas de monitoramento de software livre. Você pode coletar alertas dessas ferramentas para o Log Analytics para analisá-los junto com os alertas de outras fontes.  Este artigo descreve como configurar o Agente do OMS para Linux para coletar alertas desses sistemas."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 0b64c32e1031e704d50aab0b38eaea41e27d134b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-oms-agent-for-linux"></a>Coletar alertas do Nagios e do Zabbix no Log Analytics do Agente do OMS para Linux 
O [Nagios](https://www.nagios.org/) e o [Zabbix](http://www.zabbix.com/) são ferramentas de monitoramento de software livre.  Você pode coletar alertas dessas ferramentas para o Log Analytics para analisá-los junto com os [alertas de outras fontes](log-analytics-alerts.md).  Este artigo descreve como configurar o Agente do OMS para Linux para coletar alertas desses sistemas.
 
## <a name="configure-alert-collection"></a>Configurar coleta de alertas

### <a name="configuring-nagios-alert-collection"></a>Configurar coleta de alertas do Nagios
Execute as etapas a seguir no servidor Nagios para coletar alertas.

1. Conceda ao usuário **omsagent** acesso de leitura ao arquivo de log do Nagios (ou seja, `/var/log/nagios/nagios.log`). Supondo que o arquivo nagios.log pertença ao grupo `nagios`, você poderá adicionar o usuário **omsagent** ao grupo **nagios**. 

    sudo usermod -a -G nagios omsagent

2.  Modificar o arquivo de configuração em (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Verifique se as entradas a seguir estão presentes e se não foram comentadas:  

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
Para obter alertas de um servidor do Zabbix, você precisa especificar um usuário e senha com *texto não criptografado*. Isso não é o ideal, mas recomendamos que você crie o usuário e conceda permissões somente para monitorar.

Execute as etapas a seguir no servidor Nagios para coletar alertas.

1. Modificar o arquivo de configuração em (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Verifique se as entradas a seguir estão presentes e se não foram comentadas.  Altere o nome de usuário e senha para valores para o seu ambiente do Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Reinicie o daemon omsagent

    sudo sh /opt/microsoft/omsagent/bin/service_control restart


## <a name="alert-records"></a>Registros de alerta
Você pode recuperar registros de alerta do Nagios e do Zabbix usando [pesquisas de logs](log-analytics-log-searches.md) no Log Analytics.

### <a name="nagios-alert-records"></a>Registros de alerta do Nagios

Registros de alerta coletados pelo Nagios têm **Type** definido como **Alert** e **SourceSystem** definido como **Nagios**.  Eles têm as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*Nagios* |
| AlertName |Nome do alerta. |
| AlertDescription | Descrição do alerta. |
| AlertState | Status do serviço ou do host.<br><br>OK<br>AVISO<br>PARA CIMA<br>PARA BAIXO |
| HostName | Nome do host que criou o alerta. |
| PriorityNumber | Nível de prioridade do alerta. |
| StateType | O tipo de estado do alerta.<br><br>SUAVE – problema que não foi verificado novamente.<br>GRAVE – problema que foi verificado novamente um número especificado de vezes.  |
| TimeGenerated |Data e hora em que o alerta foi criado. |


### <a name="zabbix-alert-records"></a>Registros de alerta do Zabbix
Registros de alerta coletados pelo Zabbix têm **Type** definido como **Alert** e **SourceSystem** definido como **Zabbix**.  Eles têm as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*Zabbix* |
| AlertName | Nome do alerta. |
| AlertPriority | Severidade do alerta.<br><br>não classificado<br>informações<br>Aviso<br>média<br>alto<br>desastre  |
| AlertState | Estado do alerta.<br><br>0 – estado está atualizado.<br>1 – o estado é desconhecido.  |
| AlertTypeNumber | Especifica se o alerta pode ou não gerar vários eventos de problema.<br><br>0 – estado está atualizado.<br>1 – o estado é desconhecido.    |
| Comentários | Comentários adicionais para o alerta. |
| HostName | Nome do host que criou o alerta. |
| PriorityNumber | Valor que indica a gravidade do alerta.<br><br>0 – não classificado<br>1 – informações<br>2 – aviso<br>3 – média<br>4 – alta<br>5 – desastre |
| TimeGenerated |Data e hora em que o alerta foi criado. |
| TimeLastModified |Data e hora em que o estado do alerta foi alterado pela última vez. |


## <a name="next-steps"></a>Próximas etapas
* Aprenda sobre [alertas](log-analytics-alerts.md) no Log Analytics.
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções. 
