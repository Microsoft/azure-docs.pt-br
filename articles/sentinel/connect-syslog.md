---
title: Conectar dados do syslog ao Azure Sentinel | Microsoft Docs
description: Conecte qualquer computador ou dispositivo que dê suporte a syslog para o Azure Sentinel usando um agente em um computador Linux entre o dispositivo e o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 35c8c2aa31887feb294b04b8a88bbe5478659e5e
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807896"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Coletar dados de fontes baseadas em Linux usando syslog

Você pode transmitir eventos de computadores com suporte do syslog, baseados em Linux ou dispositivos no Azure Sentinel, usando o agente de Log Analytics para Linux (anteriormente conhecido como agente do OMS). Você pode fazer isso para qualquer computador que permita instalar o agente de Log Analytics diretamente no computador. O daemon do syslog nativo da máquina coletará eventos locais dos tipos especificados e os encaminhará localmente para o agente, que os transmitirá para seu espaço de trabalho Log Analytics.

> [!NOTE]
> - Se seu dispositivo oferecer suporte a **CEF (formato de evento comum) no syslog**, um conjunto de dados mais completo será coletado e os dados serão analisados na coleção. Você deve escolher essa opção e seguir as instruções em [conectar sua solução externa usando CEF](connect-common-event-format.md).
>
> - Log Analytics dá suporte à coleta de mensagens enviadas pelos daemons **rsyslog** ou **syslog-ng** , em que rsyslog é o padrão. O daemon de syslog padrão na versão 5 de Red Hat Enterprise Linux (RHEL), CentOS e versão de Oracle Linux (**sysklog**) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog.

## <a name="how-it-works"></a>Como ele funciona

O **syslog** é um protocolo de log de eventos comum ao Linux. Quando o **agente de log Analytics para Linux** é instalado em sua VM ou dispositivo, a rotina de instalação configura o daemon do syslog local para encaminhar mensagens para o agente na porta TCP 25224. Em seguida, o agente envia a mensagem para seu espaço de trabalho Log Analytics por HTTPS, onde é analisado em uma entrada de log de eventos na tabela syslog no **Azure Sentinel > logs**.

Para obter mais informações, consulte [syslog Data Sources in Azure monitor](../azure-monitor/platform/data-sources-syslog.md).

## <a name="configure-syslog-collection"></a>Configurar coleção de syslog

### <a name="configure-your-linux-machine-or-appliance"></a>Configurar seu computador ou dispositivo Linux

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, selecione o conector **syslog** .

1. Na folha **syslog** , selecione **abrir a página conector**.

1. Instale o agente do Linux. Em **escolher onde instalar o agente:**
    
    **Para uma VM Linux do Azure:**
      
    1. Selecione **instalar agente na máquina virtual Linux do Azure**.
    
    1. Clique no link **baixar & instalar o agente para máquinas virtuais do Azure Linux >** . 
    
    1. Na folha **máquinas virtuais** , clique em uma máquina virtual para instalar o agente e, em seguida, clique em **conectar**. Repita essa etapa para cada VM que você deseja conectar.
    
    **Para qualquer outra máquina Linux:**

    1. Selecione **instalar agente em um computador Linux não Azure**

    1. Clique no link **baixar & instalar o agente para computadores Linux que não são do Azure >** . 

    1. Na folha **Gerenciamento de agentes** , clique na guia **servidores Linux** e, em seguida, copie o comando para **baixar e carregar o agente para Linux** e executá-lo em seu computador Linux. 
    
   > [!NOTE]
   > Certifique-se de definir as configurações de segurança para esses computadores de acordo com a política de segurança da sua organização. Por exemplo, você pode definir as configurações de rede para se alinhar com a política de segurança de rede da sua organização e alterar as portas e protocolos no daemon para se alinhar com os requisitos de segurança.

### <a name="configure-the-log-analytics-agent"></a>Configurar o agente de Log Analytics

1. Na parte inferior da folha conector de syslog, clique no link **abrir configurações avançadas de espaço de trabalho de configuração >** .

1. Na folha **Configurações avançadas** , selecione syslog de **dados**  >  . Em seguida, adicione os recursos para o conector coletar.
    
    - Adicione os recursos que seu dispositivo de syslog inclui em seus cabeçalhos de log. 
    
    - Se você quiser usar a detecção de logon de SSH anormal com os dados coletados, adicione **auth** e **authpriv**. Consulte a [seção a seguir](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) para obter detalhes adicionais.

1. Depois de adicionar todos os recursos que você deseja monitorar e ajustar as opções de severidade para cada um deles, marque a caixa de seleção **aplicar a configuração abaixo a meus computadores**.

1. Selecione **Salvar**. 

1. Em sua VM ou dispositivo, certifique-se de que você está enviando os recursos que você especificou.

1. Para consultar os dados de log do syslog em **logs**, digite `Syslog` na janela de consulta.

1. Você pode usar os parâmetros de consulta descritos em [usando funções no Azure monitor consultas de log](../azure-monitor/log-query/functions.md) para analisar suas mensagens de syslog. Em seguida, você pode salvar a consulta como uma nova função Log Analytics e usá-la como um novo tipo de dados.

> [!NOTE]
> **Usando o mesmo computador para encaminhar mensagens de syslog *e* CEF simples**
>
>
> Você pode usar seu [computador de encaminhador de log CEF](connect-cef-agent.md) existente para coletar e encaminhar logs de fontes de syslog simples também. No entanto, você deve executar as etapas a seguir para evitar o envio de eventos em ambos os formatos para o Azure Sentinel, pois isso resultará em duplicação de eventos.
>
>    Já configurou a [coleta de dados de suas fontes CEF](connect-common-event-format.md)e configurou o agente de log Analytics como acima:
>
> 1. Em cada computador que envia logs no formato CEF, você deve editar o arquivo de configuração do syslog para remover as instalações que estão sendo usadas para enviar mensagens CEF. Dessa forma, os recursos enviados no CEF também não serão enviados no syslog. Consulte [Configurar syslog no agente do Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) para obter instruções detalhadas sobre como fazer isso.
>
> 1. Você deve executar o comando a seguir nessas máquinas para desabilitar a sincronização do agente com a configuração de syslog no Azure Sentinel. Isso garante que a alteração de configuração feita na etapa anterior não seja substituída.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`


### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configurar o conector syslog para detecção de logon de SSH anormal

> [!IMPORTANT]
> A detecção de logon SSH anômala está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel pode aplicar o ML (aprendizado de máquina) aos dados do syslog para identificar a atividade de logon do SSH (Secure Shell anormal). Os cenários incluem:

- Viagem impossível – quando dois eventos de logon bem-sucedidos ocorrem em dois locais que são impossíveis de alcançar dentro do período de dois eventos de logon.
- Local inesperado – o local de onde ocorreu um evento de logon bem-sucedido é suspeito. Por exemplo, o local não foi visto recentemente.
 
Essa detecção requer uma configuração específica do conector de dados syslog: 

1. Para a etapa 5 no procedimento anterior, verifique se **auth** e **authpriv** estão selecionados como instalações a serem monitoradas. Mantenha as configurações padrão para as opções de gravidade, para que todas estejam selecionadas. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![Instalações necessárias para a detecção de logon de SSH anormal](./media/connect-syslog/facilities-ssh-detection.png)

2. Aguarde tempo suficiente para que as informações de syslog sejam coletadas. Em seguida, navegue até **Azure Sentinel-logs** e copie e cole a seguinte consulta:
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    Altere o **intervalo de tempo** , se necessário, e selecione **executar**.
    
    Se a contagem resultante for zero, confirme a configuração do conector e se os computadores monitorados têm atividade de logon bem-sucedida para o período de tempo especificado para a consulta.
    
    Se a contagem resultante for maior que zero, os dados do syslog serão adequados para a detecção de logon de SSH anormal. Você habilita essa detecção na   >     >  **detecção de logon do ssh anômala (versão prévia)** dos modelos de regra de análise.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar os dispositivos locais do syslog ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

