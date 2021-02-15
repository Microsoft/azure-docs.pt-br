---
title: Solucionar problemas do servidor de processo do Azure Site Recovery
description: Este artigo descreve como solucionar problemas no servidor de processo do Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: ad1bec66edaa3fcc6049f4911684f6e6d6c3e366
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999172"
---
# <a name="troubleshoot-the-process-server"></a>Solucionar problemas do servidor de processo

O servidor de processo do [Site Recovery](site-recovery-overview.md) é usado quando você configura a recuperação de desastres para o Azure para VMs VMware locais e servidores físicos. Este artigo descreve como solucionar problemas no servidor de processo, incluindo problemas de replicação e conectividade.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre o servidor de processo.

## <a name="before-you-start"></a>Antes de começar

Antes de iniciar a solução de problemas:

1. Verifique se você entendeu como [monitorar os servidores de processo](vmware-physical-azure-monitor-process-server.md).
2. Revise as práticas recomendadas abaixo.
3. Siga as [considerações sobre capacidade](site-recovery-plan-capacity-vmware.md#capacity-considerations) e use as diretrizes de dimensionamento para o [servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) ou os [servidores de processo autônomo](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Práticas recomendadas para a implantação do servidor de processo

Para ter um desempenho ideal dos servidores de processo, resumimos várias práticas recomendadas gerais.

**Prática recomendada** | **Detalhes**
--- |---
**Usage** | Verifique se o servidor de configuração/servidor de processo autônomo só é usado para a finalidade pretendida. Não execute nada mais no computador.
**Endereço IP** | Verifique se o servidor de processo tem um endereço IPv4 estático e se o NAT não está configurado.
**Controlar a memória/uso da CPU** |Mantenha o uso da CPU e da memória em 70%.
**Garantir espaço livre** | Espaço livre refere-se ao espaço em disco de cache no servidor de processo. Os dados de replicação são armazenados no cache antes de serem carregados no Azure.<br/><br/> Mantenha o espaço livre acima de 25%. Se ficar abaixo de 20%, a replicação será limitada para os computadores replicados associados ao servidor de processo.

## <a name="check-process-server-health"></a>Verificar integridade do servidor de processo

A primeira etapa da solução de problemas é verificar a integridade e o status do servidor de processo. Para fazer isso, examine todos os alertas, verifique se os serviços necessários estão em execução e se há uma pulsação no servidor de processo. Essas etapas são resumidas no gráfico a seguir, seguidas por procedimentos para ajudar a executá-las.

![Solucionar problemas de integridade do servidor de processo](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Etapa 1: Solucionar problemas de alertas da integridade do servidor de processo

O servidor de processo gera vários alertas de integridade. Esses alertas e as ações recomendadas são resumidos na tabela a seguir.

**Tipo de alerta** | **Erro** | **Solucionar problemas**
--- | --- | --- 
![Healthy][green] | Nenhum  | O servidor de processo está conectado e íntegro.
![Aviso][yellow] | Os serviços especificados não estão em execução. | 1. Verifique se os serviços estão em execução.<br/> 2. Se os serviços estiverem sendo executados conforme o esperado, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Aviso][yellow]  | Utilização da CPU > 80% nos últimos 15 minutos. | 1. Não adicione novos computadores.<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado com os [limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations) e considere configurar um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  Utilização da CPU > 95% nos últimos 15 minutos. | 1. Não adicione novos computadores.<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado com os [limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations) e considere configurar um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [Planejador de Implantações](./site-recovery-deployment-planner.md) para a replicação do VMware/servidor físico.
![Aviso][yellow] | Uso de memória > 80% nos últimos 15 minutos. |  1. Não adicione novos computadores.<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado com os [limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations) e considere configurar um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções associadas ao aviso.<br/> 4. Se o problema persistir, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] | Uso de memória > 95% nos últimos 15 minutos. | 1. Não adicione novos computadores e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/> 2. Siga as instruções associadas ao aviso.<br/> 3. 4. Se o problema continuar, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [Planejador de Implantações](./site-recovery-deployment-planner.md) para os problemas de replicação do VMware/servidor físico.
![Aviso][yellow] | Espaço livre na pasta de cache < 30% nos últimos 15 minutos. | 1. Não adicione novos computadores e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>2. Verifique se o número de VMs que usam o servidor de processo se alinha com as [diretrizes](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  Espaço livre < 25% nos últimos 15 minutos | 1. Siga as instruções associadas ao aviso para resolver o problema.<br/> 2. 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 3. Se o problema persistir, execute o [Planejador de Implantações](./site-recovery-deployment-planner.md) para a replicação do VMware/servidor físico.
![Crítico][red] | Não há pulsação no servidor de processo por 15 minutos ou mais. O serviço tmansvs não está comunicando com o servidor de configuração. | 1) Verifique se o servidor de processo está em execução.<br/> 2. Verifique se o tmassvc está em execução no servidor de processo.<br/> 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).


![Chave da tabela](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Etapa 2: Verificar serviços do servidor de processo

Os serviços que devem estar em execução no servidor de processo são resumidos na tabela a seguir. Há pequenas diferenças nos serviços, dependendo de como o servidor de processo é implantado. 

Para todos os serviços, exceto o obengine (Agente de Serviços de Recuperação do Microsoft Azure), verifique se o StartType está definido para **Automático** ou **Automático (Início Atrasado)** .
 
**Implantação** | **Serviços em execução**
--- | ---
**Servidor de processo no servidor de configuração** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Serviço de Upload de Log (LogUpload); Serviço de Aplicativo do InMage Scout; Agente de Serviços de Recuperação do Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; Serviço de Publicação na World Wide Web (W3SVC); MySQL; Serviço do Microsoft Azure Site Recovery (dra)
**Servidor de processo em execução como um servidor autônomo** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Serviço de Upload de Log (LogUpload); Serviço de Aplicativo do InMage Scout; Agente de Serviços de Recuperação do Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc.
**Servidor de processo implantado no Azure para failback** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Serviço de Upload de Log (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Etapa 3: Verificar pulsação do servidor de processo

Se não houver pulsação no servidor de processo (código de erro 806), faça o seguinte:

1. Verifique se a VM do servidor de processo está em execução.
2. Verifique esses logs em busca de erros.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Verificar conectividade e replicação

 As falhas de replicação inicial e contínua geralmente são causadas por problemas de conectividade entre os computadores de origem e o servidor de processo, ou entre o servidor de processo e o Azure. Essas etapas são resumidas no gráfico a seguir, seguidas por procedimentos para ajudar a executá-las.

![Fluxograma mostrando etapas para solucionar problemas de conectividade e replicação.](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Etapa 4: Verificar sincronização do tempo no computador de origem

Verifique se a data/hora do sistema do computador replicado está em sincronia. [Saiba mais](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Etapa 5: Verificar software antivírus no computador de origem

Verifique se nenhum software antivírus no computador replicado está bloqueando o Site Recovery. Se você precisar excluir o Site Recovery de programas antivírus, veja [este artigo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Etapa 6: Verificar conectividade do computador de origem


1. Instale o [cliente Telnet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10))no computador de origem, se necessário. Não use Ping.
2. No computador de origem, execute ping no servidor de processo na porta HTTPS com Telnet. Por padrão, 9443 é a porta HTTPS para o tráfego de replicação.

    `telnet <process server IP address> <port>`

3. Verifique se a conexão foi bem-sucedida.


**Conectividade** | **Detalhes** | **Ação**
--- | --- | ---
**Bem-sucedida** | O Telnet mostra uma tela em branco e o servidor de processo pode ser acessado. | Nenhuma ação adicional é necessária.
**Sem êxito** | Você não consegue conectar | Verifique se a porta de entrada 9443 é permitida no servidor de processo. Por exemplo, se você tiver uma rede de perímetro ou uma sub-rede filtrada. Verifique a conectividade novamente.
**Parcialmente bem-sucedida** | Você consegue conectar, mas o computador de origem relata que o servidor de processo não pode ser acessado. | Continue com o próximo procedimento de solução de problemas.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Etapa 7: Solucionar problemas em um servidor de processo inacessível

Se o servidor de processo não estiver acessível a partir do computador de origem, o erro 78186 será exibido. Se não for resolvido, esse problema fará com que os pontos de recuperação consistentes do aplicativo e com falhas não sejam gerados conforme o esperado.

Solucione problemas verificando se o computador de origem pode acessar o endereço IP do servidor de processo e execute a ferramenta cxpsclient nesse computador para verificar a conexão de ponta a ponta.


### <a name="check-the-ip-connection-on-the-process-server"></a>Verificar conexão IP no servidor de processo

Se o Telnet for bem-sucedido, mas o computador de origem relatar que o servidor de processo não pode ser acessado, verifique se você pode acessar o endereço IP do servidor de processo.

1. Em um navegador da Web, tente acessar o endereço https://<PS_IP>:<PS_Data_Port>/.
2. Se essa verificação mostra um erro de certificado HTTPS, isso é normal. Se você ignorar o erro, verá a mensagem: 400 - Solicitação Incorreta. Isso significa que o servidor não pode atender à solicitação do navegador e a conexão HTTPS padrão está correta.
3. Se essa verificação não funcionar, anote a mensagem de erro do navegador. Por exemplo, um erro 407 indicará um problema com a autenticação de proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Verificar conexão com cxpsclient

Além disso, você pode executar a ferramenta cxpsclient para verificar a conexão de ponta a ponta.

1. Execute a ferramenta da seguinte maneira:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. No servidor de processo, verifique os logs gerados nestas pastas:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Verificar logs da VM de origem para obter falhas de upload (erro 78028)

O problema com os uploads de dados bloqueados dos computadores de origem para o serviço de processo pode resultar em pontos de recuperação consistentes com falhas e do aplicativo não sendo gerados. 

1. Para solucionar as falhas de upload da rede, você pode procurar erros neste log:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Usar o restante dos procedimentos neste artigo pode ajudá-lo a resolver os problemas de upload dos dados.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Etapa 8: Verificar se o servidor de processo está enviando dados por push

Verifique se o servidor de processo está enviando dados por push ativamente para o Azure.

  1. No servidor de processo, abra o Gerenciador de Tarefas (pressione Ctrl+Shift+Esc).
  2. Selecione a guia **Desempenho** > **Abrir Monitor de Recursos**.
  3. Na página **Monitor de Recursos**, selecione a guia **Rede**. Em **Processos com Atividade de Rede**, verifique se o cbengine.exe está enviando ativamente grandes volumes de dados.

       ![Captura de tela mostrando um grande número de volumes em processos com atividade de rede.](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se cbengine.exe não estiver enviando um grande volume de dados, conclua as etapas nas seções a seguir.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Etapa 9: Verificar conexão do servidor de processo com o armazenamento de blobs do Azure

1. Em Monitor de Recursos, selecione **cbengine. exe**.
2. Em **Conexões TCP**, verifique se há conectividade do servidor de processo com o armazenamento do Azure.

  ![Captura de tela mostrando a conectividade entre cbengine.exe e a URL de armazenamento de BLOBs do Azure.](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Verificar serviços

Se não houver conectividade do servidor de processo com a URL do armazenamento de blobs do Azure, verifique se esses serviços estão em execução.

1. No Painel de Controle, selecione **Serviços**.
2. Verifique se os seguintes serviços estão em execução:

    - cxprocessserver
    - Agente do InMage Scout VX – Sentinel/Outpost
    - Agente de Serviços dos Serviços de Recuperação do Microsoft Azure
    - Serviço do Microsoft Azure Site Recovery
    - tmansvc

3. Iniciar ou reiniciar qualquer serviço que não está em execução.
4. Verifique se o servidor está conectado e acessível. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Etapa 10: verificar conexão do servidor de processo com o endereço IP público do Azure

1. No serviço de processo, em **%programfiles%\Agente de Serviços de Recuperação do Microsoft Azure\Temp**, abra o arquivo CBEngineCurr.errlog mais recente.
2. No arquivo, procure **443** ou a cadeia de caracteres **falha na tentativa de conexão**.

  ![Logs de erros na pasta Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se você tiver problemas, encontre o endereço IP público do Azure no arquivo CBEngineCurr.currLog usando a porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na linha de comando no servidor de processo, use o Telnet para executar ping no seu endereço IP público do Azure.
6. Se não conseguir conectar, siga o procedimento a seguir.

## <a name="step-11-check-process-server-firewall-settings"></a>Etapa 11: Verifique as configurações de firewall do servidor de processo. 

Verifique se o firewall baseado em endereço IP no servidor de processo bloqueia o acesso.

1. Para as regras de firewall baseadas em endereço IP:

    a) Baixe a lista completa de [intervalos de IP do datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Adicione intervalos de endereços IP à sua configuração de firewall para garantir que o firewall permita a comunicação com o Azure (e com a porta HTTPS padrão, 443).

    c) Permita os intervalos de endereço IP para a região do Azure de sua assinatura e a região Oeste dos EUA do Azure (usados para o controle de acesso e o gerenciamento de identidades).

2. Para os firewalls baseados em URL, adicione as URLs listadas na tabela a seguir à configuração do firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Etapa 12: Verificar configurações de proxy do servidor de processo 

1. Se você estiver usando um servidor proxy, verifique se o nome do servidor proxy é resolvido pelo servidor DNS. Verifique o valor fornecido ao definir o servidor de configuração na chave do registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Em seguida, verifique se as mesmas configurações estão sendo usadas pelo agente do Azure Site Recovery para enviar dados.

    a) Procure o **Backup do Microsoft Azure**.

    b) Abra o **Backup do Microsoft Azure** e selecione **Ação** > **Alterar Propriedades**.

    c) Na guia **Configuração do Proxy**, o endereço de proxy deve ser igual ao endereço de proxy exibido nas configurações do registro. Caso contrário, altere-a para o mesmo endereço.

## <a name="step-13-check-bandwidth"></a>Etapa 13: Verificar largura de banda

Aumente a largura de banda entre o servidor de processo e o Azure, depois verifique se o problema ainda ocorre.


## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste sua pergunta na [página de Perguntas e Respostas da Microsoft para o Azure Site Recovery](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png