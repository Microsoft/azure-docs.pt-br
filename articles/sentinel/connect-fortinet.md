---
title: Conectar-se a dados Fortinet para versão prévia do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados Fortinet a Sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: f3ab4861e874074e7de059c7c50064d53749748c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611294"
---
# <a name="connect-your-fortinet-appliance"></a>Conectar seu dispositivo Fortinet

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço. Não é recomendável para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [termos de uso complementares para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode conectar Sentinel do Azure para qualquer dispositivo Fortinet salvando os arquivos de log como Syslog eventos CEF (formato comum). Com a integração com o Azure Sentinel, você pode executar facilmente consultas e análises entre os dados do arquivo de log de Fortinet. Para obter mais informações sobre como o Azure Sentinel ingere dados CEF, consulte [appliances conectar CEF](connect-common-event-format.md).

> [!NOTE]
> Dados são armazenados na localização geográfica do espaço de trabalho no qual você executa Sentinel do Azure.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Etapa 1: Conectar seu dispositivo Fortinet por meio de um agente

Para conectar seu dispositivo Fortinet Sentinel do Azure, implante um agente em uma VM dedicada ou máquina local para dar suporte a comunicação entre o dispositivo e o Azure Sentinel. Você pode implantar o agente manualmente ou automaticamente. Implantação automática só estará disponível se a sua máquina dedicada é uma nova VM que você cria no Azure.

Você também pode implantar o agente manualmente em uma VM do Azure existente em uma VM em outra nuvem ou em um computador local.

Para ver um diagrama de rede de ambas as opções, consulte [conectar fontes de dados](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Implantar o agente no Azure

1. No portal do Azure Sentinel, selecione **conectores de dados** e selecione o tipo de dispositivo.

1. Sob **configuração do agente de Syslog do Linux**:
   - Escolher **implantação automática** se você deseja criar uma nova máquina que já vem pré-instalado com o agente do Azure Sentinel e inclui toda a configuração necessária, conforme descrita anteriormente. Selecione **implantação automática** > **implantação automática do agente**. A página de compra para uma VM dedicada que é conectada automaticamente ao seu espaço de trabalho é exibida. A VM é uma **v3 de D2s standard (2 vCPUs, 8 GB de memória)** e tem um endereço IP público.
      1. Sobre o **implantação personalizada** página, fornecer seus detalhes, digite um nome de usuário e uma senha e se você concordar com os termos e condições, a VM de compra.
      1. Configure seu dispositivo para enviar logs usando as configurações listadas na página de conexão. Para o conector do formato comum de evento genérico, use estas configurações:
         - Protocolo = UDP
         - Porta = 514
         - Recurso = 4 Local
         - Formato = CEF
   - Escolher **implantação Manual** se você quiser usar uma VM existente como o computador Linux dedicado para o qual o agente do Azure Sentinel está instalado. 
      1. Sob **Baixe e instale o agente do Syslog**, selecione **máquina virtual Linux do Azure**. 
      1. Sobre o **máquinas virtuais** tela, selecione a máquina que você deseja usar e selecione **Connect**.
      1. Na tela de conector, sob **configurar e Syslog de encaminhamento**, defina se o daemon do Syslog está **rsyslog.d** ou **syslog-ng**. 
      1. Copie estes comandos e executá-los em seu dispositivo:
          - Se você selecionou rsyslog.d:
              
            1. Informe o daemon do Syslog para escutar em local_4 de recurso e enviar as mensagens do Syslog para o agente do Azure Sentinel usando a porta 25226. Use este comando: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. Use este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pelo seu GUID do espaço de trabalho.
            1. Reinicie o daemon do syslog usando este comando: `sudo service rsyslog restart`
             
          - Se você selecionou syslog-ng:

              1. Informe o daemon do Syslog para escutar em local_4 de recurso e enviar as mensagens do Syslog para o agente do Azure Sentinel usando a porta 25226. Use este comando: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              1. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. Use este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pelo seu GUID do espaço de trabalho.
              1. Reinicie o daemon do syslog usando este comando: `sudo service syslog-ng restart`
      1. Reinicie o agente do Syslog usando este comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirme que há não há erros no log do agente executando este comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Implantar o agente em um servidor Linux de local

Se você não estiver usando o Azure, implante manualmente o agente de sentinela do Azure para ser executado em um servidor dedicado do Linux.

1. No portal do Azure Sentinel, selecione **conectores de dados** e selecione o tipo de dispositivo.
1. Para criar uma VM do Linux dedicado, sob **configuração do agente de Syslog do Linux** selecionar **implantação Manual**.

    1. Sob **Baixe e instale o agente do Syslog**, selecione **computador não Azure Linux**.
    1. No **agente direto** tela que é aberta, selecione **Agent para Linux** para baixar o agente ou execute este comando para baixá-lo em seu computador Linux: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`

       1. Na tela de conector, sob **configurar e Syslog de encaminhamento**, defina se o daemon do Syslog está **rsyslog.d** ou **syslog-ng**.
       1. Copie estes comandos e executá-los em seu dispositivo:

          - Se você selecionou rsyslog:

            1. Informe o daemon do Syslog para escutar em local_4 de recurso e enviar as mensagens do Syslog para o agente do Azure Sentinel usando a porta 25226. Use este comando: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. Use este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pelo seu GUID do espaço de trabalho.
            1. Reinicie o daemon do syslog usando este comando: `sudo service rsyslog restart`

          - Se você selecionou syslog-ng:

            1. Informe o daemon do Syslog para escutar em local_4 de recurso e enviar as mensagens do Syslog para o agente do Azure Sentinel usando a porta 25226. Use este comando: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            1. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente de Syslog para escutar na porta 25226. Use este comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` onde {0} deve ser substituído pelo seu GUID do espaço de trabalho.
            1. Reinicie o daemon do syslog usando este comando: `sudo service syslog-ng restart`

      1. Reinicie o agente do Syslog usando este comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirme que há não há erros no log do agente executando este comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Etapa 2: Encaminhar logs Fortinet para o agente do Syslog

Configure Fortinet para encaminhar mensagens do Syslog no formato CEF para seu espaço de trabalho do Azure através do agente do Syslog.

1. Abra a CLI no seu dispositivo Fortinet e execute os seguintes comandos:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Substituir o servidor **endereço ip** com o endereço IP do agente.
    - Definir **facility_name** para usar o recurso que você configurou no agente. Por padrão, o agente define isso para local4.
    - Defina as **porta do syslog** para **514** ou a porta configurada no agente.
    - Para habilitar o formato CEF em versões anteriores do FortiOS, talvez você precise executar o conjunto de comandos **csv desabilitar**.
 
   > [!NOTE] 
   > Para obter mais informações, vá para o [biblioteca de documentos Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Selecione sua versão e use o **Handbook** e **referência de mensagens de Log**.

 Para usar o esquema relevante no Log Analytics do Azure Monitor para os eventos Fortinet, procure `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Etapa 3: Validar a conectividade

Pode levar até 20 minutos até que seus logs comecem a aparecer no Log Analytics. 

1. Verifique se que você usar o recurso correto. O recurso deve ser o mesmo no seu dispositivo e no Azure Sentinel. Você pode verificar qual arquivo de recurso, você está usando no Azure Sentinel e modificá-lo no arquivo `security-config-omsagent.conf`. 

2. Certifique-se de que os logs estão obtendo à porta à direita no agente do Syslog. Execute este comando no computador do agente do Syslog: `tcpdump -A -ni any  port 514 -vv`. Este comando mostra os logs de fluxo do dispositivo para a máquina de Syslog. Certifique-se de que os logs estão sendo recebidos do dispositivo de origem na porta direita e o recurso correto.

3. Certifique-se de que os logs que você enviar obedecer [RFC 5424](https://tools.ietf.org/html/rfc542).

4. No computador que executa o agente do Syslog, verifique se as portas 514 e 25226 são abertos e está ouvindo, usando o comando `netstat -a -n:`. Para obter mais informações sobre como usar esse comando, consulte [netstat(8) - página do manual Linux](https://linux.die.net/man/8/netstat). Se ele está escutando corretamente, você verá:

   ![Portas de sentinela do Azure](./media/connect-cef/ports.png) 

5. Verifique se que o daemon está definido para escutar na porta 514, no qual você está enviando logs.
    - Para rsyslog:<br>Certifique-se de que o arquivo `/etc/rsyslog.conf` inclui essa configuração:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Para obter mais informações, consulte [imudp: Módulo de entrada de Syslog de UDP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) e [imtcp: Módulo de entrada de Syslog de TCP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - Para syslog-ng:<br>Certifique-se de que o arquivo `/etc/syslog-ng/syslog-ng.conf` inclui essa configuração:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Para obter mais informações, consulte [imudp: Módulo de entrada de Syslog de UDP](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) e [edição do código-fonte aberto syslog-ng 3.16 - guia de administração](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Verifique se há comunicação entre o daemon do Syslog e o agente. Execute este comando no computador do agente do Syslog: `tcpdump -A -ni any  port 25226 -vv`. Este comando mostra os logs de fluxo do dispositivo para a máquina de Syslog. Certifique-se de que os logs também estão sendo recebidos no agente.

6. Se ambos os comandos fornecidos resultados bem-sucedidos, verifique o Log Analytics para ver se os logs chegam. Todos os eventos que são transmitidos desses dispositivos são exibidos em formato bruto no Log Analytics em `CommonSecurityLog` tipo.

7. Para verificar se há erros ou se os logs não são recebidos, procure no `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Se ele diz que há erros de incompatibilidade de formato de log, vá para `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` e examine o arquivo `security_events.conf`. Certifique-se de que seus logs de correspondem o formato de regex que você vê neste arquivo.

8. Certifique-se de que seu tamanho de padrão de mensagem do Syslog é limitado a 2048 bytes (2 KB). Se os logs são muito longos, atualize security_events usando este comando: `message_length_limit 4096`

10. Se seus logs Fortinet não estão sendo recebidas pelo agente, execute este comando, dependendo do tipo de daemon do Syslog que você estiver usando, para definir o recurso e os logs para procurar a palavra Fortinet nos logs:
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Reinicie o daemon do Syslog usando este comando: `sudo service rsyslog restart`
       - syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Reinicie o daemon do Syslog usando este comando: `sudo service syslog-ng restart`

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como conectar dispositivos Fortinet ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

