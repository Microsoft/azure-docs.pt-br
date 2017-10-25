---
title: Coletar e analisar mensagens do Syslog no Log Analytics do OMS | Microsoft Docs
description: "O Syslog é um protocolo de registro de eventos em log que é comum para o Linux. Este artigo descreve como configurar a coleta de mensagens do Syslog no Log Analytics e os detalhes dos registros que eles criam no repositório do OMS."
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
ms.date: 07/12/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 7513f405d5c7c05a8e6e2b7b0e6313f23a319c84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="syslog-data-sources-in-log-analytics"></a>Fontes de dados de Syslog no Log Analytics
O Syslog é um protocolo de registro de eventos em log que é comum para o Linux.  Os aplicativos enviarão mensagens que podem ser armazenadas no computador local ou entregues a um coletor de Syslog.  Quando o agente do OMS para Linux é instalado, ele configura o daemon do Syslog local para encaminhar mensagens para o agente.  O agente então envia a mensagem para o Log Analytics, no qual um registro correspondente é criado no repositório OMS.  

> [!NOTE]
> O Log Analytics dá suporte à coleta de mensagens enviadas por rsyslog ou syslog-ng, em que rsyslog é o daemon padrão. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o [daemon rsyslog](http://rsyslog.com) deverá ser instalado e configurado para substituir sysklog.
>
>

![Coleção do Syslog](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Configurando Syslog
O Agente do OMS para Linux coletará apenas os eventos com os recursos e severidades especificadas em sua configuração.  Você pode configurar o Syslog por meio do portal do OMS ou gerenciando arquivos de configuração em seus agentes do Linux.

### <a name="configure-syslog-in-the-oms-portal"></a>Configurar o Syslog no portal do OMS
Configure o Syslog do menu [Dados nas Configurações do Log Analytics](log-analytics-data-sources.md#configuring-data-sources).  Essa configuração é entregue ao arquivo de configuração em cada agente do Linux.

Você pode adicionar um novo recurso, digitando seu nome e clicando em **+**.  Para cada recurso, somente mensagens com as severidades selecionadas serão coletados.  Marque as severidades para o recurso específico que você deseja coletar.  Você não pode fornecer quaisquer critérios adicionais para filtrar mensagens.

![Configurar Syslog](media/log-analytics-data-sources-syslog/configure.png)

Por padrão, todas as alterações de configuração são automaticamente envidas por push para todos os agentes.  Se você quiser configurar o Syslog manualmente em cada agente do Linux, desmarque a caixa *Aplicar as configurações abaixo aos computadores Linux*.

### <a name="configure-syslog-on-linux-agent"></a>Configurar o Syslog no agente do Linux
Quando o [agente do OMS é instalado em um cliente Linux](log-analytics-linux-agents.md), ele instala um arquivo de configuração de Syslog padrão, que define o recurso e a severidade das mensagens que são coletadas.  Você pode modificar esse arquivo para alterar a configuração.  O arquivo de configuração é diferente, dependendo do daemon do Syslog que o cliente tem instalado.

> [!NOTE]
> Se você editar a configuração de syslog, deverá reiniciar o daemon syslog para que as alterações entrem em vigor.
>
>

#### <a name="rsyslog"></a>rsyslog
O arquivo de configuração para rsyslog está localizado em **/etc/rsyslog.d/95-omsagent.conf**.  Seu conteúdo padrão é mostrado abaixo.  Isso coleta mensagens do syslog enviadas do agente local para todos os recursos com um nível de aviso ou superior.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Você pode remover um recurso removendo sua seção do arquivo de configuração.  Você pode limitar as severidades coletadas para um recurso específico, modificando a entrada desse recurso.  Por exemplo, para limitar o recurso do usuário a mensagens com uma severidade de erro ou superior, você modificaria essa linha do arquivo de configuração para o seguinte:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
O arquivo de configuração para syslog-ng é a localização em **/etc/syslog-ng/syslog-ng.conf**.  Seu conteúdo padrão é mostrado abaixo.  Isso coleta mensagens do syslog enviadas do agente local para todos os recursos e todas as severidades.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Você pode remover um recurso removendo sua seção do arquivo de configuração.  Você pode limitar as severidades coletadas para um recurso específico, removendo-as de sua lista.  Por exemplo, para limitar o recurso exclusivamente a mensagens críticas e de alerta, você modificaria essa seção do arquivo de configuração para o seguinte:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Coletar dados de portas de Syslog adicionais
O agente do OMS escuta as mensagens do Syslog no cliente local na porta 25224.  Quando o agente é instalado, uma configuração de syslog padrão é aplicada e encontra o seguinte local:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Você pode alterar o número da porta criando dois arquivos de configuração: um arquivo de configuração FluentD, e um arquivo rsyslog-or-syslog-ng, dependendo do daemon do Syslog que você instalou.  

* O arquivo de configuração FluentD deve ser um novo arquivo localizado em: `/etc/opt/microsoft/omsagent/conf/omsagent.d` e substituir o valor na entrada da **porta** por seu número da porta personalizado.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Para rsyslog, você deve criar um novo arquivo de configuração localizado em: `/etc/rsyslog.d/` e substituir o valor % SYSLOG_PORT % pelo número da porta personalizada.  

    > [!NOTE]
    > Se você modificar esse valor no arquivo de configuração `95-omsagent.conf`, ele será substituído quando o agente aplicar a uma configuração padrão.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* A configuração de syslog-ng deve ser modificada por meio da cópia da configuração de exemplo mostrada abaixo e adicionando as configurações modificadas personalizadas ao final do arquivo de configuração syslog ng.conf localizado em `/etc/syslog-ng/`.  **Não** use o rótulo padrão **% WORKSPACE_ID %_oms** ou **% WORKSPACE_ID_OMS**, defina um rótulo personalizado para ajudar a distinguir as alterações.  

    > [!NOTE]
    > Se você modificar os valores padrão no arquivo de configuração, eles serão substituídos quando o agente aplicar uma configuração padrão.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Depois de concluir as alterações, o Syslog e o serviço do agente do OMS precisarão ser reiniciados para garantir que as alterações de configuração entrem em vigor.   

## <a name="syslog-record-properties"></a>Propriedades de registro do syslog
Os registros do syslog têm um tipo de **Syslog** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador do qual o evento foi coletado. |
| Recurso |Define a parte do sistema que gerou a mensagem. |
| HostIP |Endereço IP do sistema que envia a mensagem. |
| HostName |Nome do sistema enviando a mensagem. |
| SeverityLevel |Nível de severidade do evento. |
| SyslogMessage |Texto da mensagem. |
| ProcessID |A ID do processo que gerou a mensagem. |
| EventTime |Data e hora em que o alerta foi gerado. |

## <a name="log-queries-with-syslog-records"></a>Consultas do log com registros do Syslog
A tabela a seguir fornece diferentes exemplos de consultas de log que recuperam registros do Syslog.

| Consultar | Descrição |
|:--- |:--- |
| Type=Syslog |Todos os Syslogs. |
| Type=Syslog SeverityLevel=error |Todos os registros do Syslog com a severidade de erro. |
| Type=Syslog &#124; measure count() by Computer |Contagem de registros do Syslog por computador. |
| Type=Syslog &#124; measure count() by Facility |Contagem de registros do Syslog por recurso. |

>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), as consultas acima seriam alteradas para o demonstrado a seguir.

> | Consultar | Descrição |
|:--- |:--- |
| syslog |Todos os Syslogs. |
| Syslog &#124; where SeverityLevel == "error" |Todos os registros do Syslog com a severidade de erro. |
| Syslog &#124; summarize AggregatedValue = count() by Computer |Contagem de registros do Syslog por computador. |
| Syslog &#124; summarize AggregatedValue = count() by Facility |Contagem de registros do Syslog por recurso. |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.
* Use [campos personalizados](log-analytics-custom-fields.md) para analisar dados dos registros do syslog em campos individuais.
* [Configure Agentes do Linux](log-analytics-linux-agents.md) para coletar outros tipos de dados.
