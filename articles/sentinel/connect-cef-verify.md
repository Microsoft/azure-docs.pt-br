---
title: Validar a conectividade com o Azure Sentinel | Microsoft Docs
description: Valide a conectividade da solução de segurança para garantir que as mensagens CEF sejam encaminhadas para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: f9fb1c917a0719cb9d250b997329d3415b5872eb
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747467"
---
# <a name="step-3-validate-connectivity"></a>ETAPA 3: validar a conectividade

Depois de implantar o encaminhador de log (na etapa 1) e configurar sua solução de segurança para enviar mensagens de CEF de ti (na etapa 2), siga estas instruções para verificar a conectividade entre sua solução de segurança e o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões elevadas (sudo) em seu computador do encaminhador de log.

- Você deve ter o **python 2,7** ou **3** instalado em seu computador do encaminhador de log.<br>
Use o `python –version` comando para verificar.

- Talvez seja necessário a ID do espaço de trabalho e a chave primária do espaço de trabalho em algum momento nesse processo. Você pode encontrá-los no recurso de espaço de trabalho, em **Gerenciamento de agentes**.

## <a name="how-to-validate-connectivity"></a>Como validar a conectividade

1. No menu de navegação do Azure Sentinel, abra **logs**. Execute uma consulta usando o esquema **CommonSecurityLog** para ver se você está recebendo logs de sua solução de segurança.<br>
Lembre-se de que pode levar cerca de 20 minutos até que os logs comecem a aparecer na **log Analytics**. 

1. Se você não vir nenhum resultado da consulta, verifique se os eventos estão sendo gerados de sua solução de segurança ou tente gerar alguns e verifique se eles estão sendo encaminhados para o computador do Encaminhador do syslog que você designou. 

1. Execute o script a seguir no encaminhador de log (aplicando a ID do espaço de trabalho no lugar do espaço reservado) para verificar a conectividade entre sua solução de segurança, o encaminhador de log e o Azure Sentinel. Esse script verifica se o daemon está escutando nas portas corretas, se o encaminhamento está configurado corretamente e se nada está bloqueando a comunicação entre o daemon e o agente de Log Analytics. Ele também envia as mensagens de simulação ' TestCommonEventFormat ' para verificar a conectividade de ponta a ponta. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - Você pode receber uma mensagem direcionando para executar um comando para corrigir um problema com o **mapeamento do campo *computador***. Consulte a [explicação no script de validação](#mapping-command) para obter detalhes.

    - Você pode receber uma mensagem direcionando para executar um comando para corrigir um problema com a **análise dos logs do firewall do Cisco ASA**. Consulte a [explicação no script de validação](#parsing-command) para obter detalhes.

## <a name="validation-script-explained"></a>Script de validação explicado

O script de validação executa as seguintes verificações:

# <a name="rsyslog-daemon"></a>[daemon do rsyslog](#tab/rsyslog)

1. Verifica se o arquivo<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe e é válido.

1. Verifica se o arquivo inclui o seguinte texto:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Verifica se a análise dos eventos de firewall do Cisco ASA está configurada conforme o esperado, usando o seguinte comando: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Se houver um problema com a análise, o script produzirá uma mensagem de erro direcionando você para **executar manualmente o comando a seguir** (aplicando a ID do espaço de trabalho no lugar do espaço reservado). O comando garantirá a análise correta e reiniciará o agente.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Verifica se o campo *computador* na origem do syslog está corretamente mapeado no agente de log Analytics, usando o seguinte comando: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Se houver um problema com o mapeamento, o script produzirá uma mensagem de erro direcionando você para **executar manualmente o comando a seguir** (aplicando a ID do espaço de trabalho no lugar do espaço reservado). O comando garantirá o mapeamento correto e reiniciará o agente.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Verifica se existem aprimoramentos de segurança no computador que podem estar bloqueando o tráfego de rede (como um firewall de host).

1. Verifica se o daemon do syslog (rsyslog) está configurado corretamente para enviar mensagens (que ele identifica como CEF) para o agente de Log Analytics na porta TCP 25226:

    - Arquivo de configuração: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Reinicia o daemon de syslog e o agente de Log Analytics:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Verifica se as conexões necessárias são estabelecidas: TCP 514 para receber dados, TCP 25226 para comunicação interna entre o daemon de syslog e o agente de Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Verifica se o daemon do syslog está recebendo dados na porta 514 e se o agente está recebendo dados na porta 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Envia dados FICTÍCIOs para a porta 514 no localhost. Esses dados devem ser observáveis no espaço de trabalho do Azure Sentinel executando a seguinte consulta:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[daemon do syslog-ng](#tab/syslogng)

1. Verifica se o arquivo<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe e é válido.

1. Verifica se o arquivo inclui o seguinte texto:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Verifica se a análise dos eventos de firewall do Cisco ASA está configurada conforme o esperado, usando o seguinte comando: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Se houver um problema com a análise, o script produzirá uma mensagem de erro direcionando você para **executar manualmente o comando a seguir** (aplicando a ID do espaço de trabalho no lugar do espaço reservado). O comando garantirá a análise correta e reiniciará o agente.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Verifica se o campo *computador* na origem do syslog está corretamente mapeado no agente de log Analytics, usando o seguinte comando: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Se houver um problema com o mapeamento, o script produzirá uma mensagem de erro direcionando você para **executar manualmente o comando a seguir** (aplicando a ID do espaço de trabalho no lugar do espaço reservado). O comando garantirá o mapeamento correto e reiniciará o agente.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Verifica se existem aprimoramentos de segurança no computador que podem estar bloqueando o tráfego de rede (como um firewall de host).

1. Verifica se o daemon de syslog (syslog-ng) está configurado corretamente para enviar mensagens que ele identifica como CEF (usando um Regex) para o agente de Log Analytics na porta TCP 25226:

    - Arquivo de configuração: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Reinicia o daemon de syslog e o agente de Log Analytics:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Verifica se as conexões necessárias são estabelecidas: TCP 514 para receber dados, TCP 25226 para comunicação interna entre o daemon de syslog e o agente de Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Verifica se o daemon do syslog está recebendo dados na porta 514 e se o agente está recebendo dados na porta 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Envia dados FICTÍCIOs para a porta 514 no localhost. Esses dados devem ser observáveis no espaço de trabalho do Azure Sentinel executando a seguinte consulta:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar os dispositivos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
