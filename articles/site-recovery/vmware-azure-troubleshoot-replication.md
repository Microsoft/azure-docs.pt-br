---
title: Solucionar problemas de replicação para recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações sobre a solução de problemas comuns de replicação durante a recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 1aaf13f01c7e7197001f3099fabd4b8be8545f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565019"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Solução de problemas de replicação para VMs VMware e servidores físicos

Você pode ver uma mensagem de erro específica ao proteger suas máquinas virtuais VMware ou servidores físicos usando o Azure Site Recovery. Este artigo descreve alguns problemas comuns que você pode encontrar ao replicar VMs VMware locais e servidores físicos no Azure utilizando o [Site Recovery](site-recovery-overview.md).

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>Monitorar a integridade do Servidor de Processo para evitar problemas de replicação

É recomendável monitorar a Integridade do PS (Servidor de Processo) no portal para garantir que a replicação esteja em andamento para os computadores de origem associados. No cofre, vá para Gerenciar > Infraestrutura do Site Recovery > Servidores de Configuração. Na folha Servidor de Configuração, clique no Servidor de Processo em Servidores Associados. A folha Servidor de Processo é aberta com as estatísticas de integridade. É possível acompanhar a utilização da CPU, o uso de memória, o status dos serviços de PS necessários para replicação, a data de validade do certificado e o espaço livre disponível. O status de todas as estatísticas deve estar na cor verde. 

**É recomendável ter memória e uso da CPU abaixo de 70% e espaço livre acima de 25%**. Espaço livre refere-se ao espaço em disco do cache no Servidor de Processo, o qual será usado para armazenar os dados de replicação dos computadores de origem antes de carregar para o Azure. Se reduzir para menos de 20%, a replicação será limitada para todos os computadores de origem associados. Siga as [diretrizes de capacidade](./site-recovery-plan-capacity-vmware.md#capacity-considerations) para reconhecer a configuração necessária para replicar os computadores de origem.

Assegure-se de que os serviços a seguir estão em execução no computador de PS. Iniciar ou reiniciar qualquer serviço que não está em execução.

**Servidor de Processo Interno**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Serviço de Upload de Log (LogUpload)
* Serviço de Aplicativo InMage Scout
* Agente dos Serviços de Recuperação do Microsoft Azure (obengine)
* Agente do InMage Scout VX - Sentinel/Outpost (svagents)
* tmansvc
* Serviço de Publicação na World Wide Web (W3SVC)
* MySQL
* Serviço do Microsoft Azure Site Recovery (dra)

**Expandir Servidor de Processo**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Serviço de Upload de Log (LogUpload)
* Serviço de Aplicativo InMage Scout
* Agente dos Serviços de Recuperação do Microsoft Azure (obengine)
* Agente do InMage Scout VX - Sentinel/Outpost (svagents)
* tmansvc

**Servidor de Processo no Azure para failback**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Serviço de Upload de Log (LogUpload)

Certifique-se de que o StartType de todos os serviços esteja definido como **Automático ou Automático (Atraso na Inicialização)**. O agente dos Serviços de Recuperação do Microsoft Azure (obengine) não precisa ter o conjunto StartType, conforme acima.

## <a name="replication-issues"></a>Problemas de replicação

Falhas de replicação inicial e contínua geralmente são causadas por problemas de conectividade entre o servidor de origem e o servidor de processo ou entre o servidor de processo e o Azure. Na maioria dos casos, você pode solucionar esses problemas seguindo as etapas nas próximas seções.

>[!Note]
>Verifique se:
>1. O sistema de data hora para o item protegido está em sincronizado.
>2. Nenhum software antivírus está bloqueando o Azure Site Recovery. Saiba mais [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) em exclusões de pastas necessárias para o Azure Site Recovery.

### <a name="check-the-source-machine-for-connectivity-issues"></a>Verifique o computador de origem para problemas de conectividade

As seguintes lista mostra maneiras você pode verificar o computador de origem.

*  Na linha de comando do servidor de origem, use Telnet para executar ping no servidor de processo por meio da porta HTTPS, executando o comando a seguir. A Porta HTTPS 9443 é o padrão usado pelo Servidor de Processo para enviar e receber tráfego de replicação. É possível modificar essa porta no momento do registro. O comando a seguir verifica problemas de conectividade de rede e problemas que bloqueiam a porta de firewall.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Use Telnet para testar a conectividade. Não use `ping`. Se o Telnet não estiver instalado, conclua as etapas listadas em [instalar o cliente Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Se o Telnet conectar-se com êxito à porta de PS, uma tela em branco será exibida.

   Se você não pode se conectar ao servidor de processo, permita tráfego pela porta de entrada 9443 no servidor de processo. Por exemplo, talvez seja necessário permitir tráfego pela porta de entrada 9443 no servidor de processo, se sua rede tiver uma rede de perímetro ou sub-rede filtrada. Em seguida, verifique se o problema ainda ocorre.

*  Se o Telnet tiver êxito e o computador de origem relatar que o PS não está acessível, abra o navegador da Web no computador de origem e verifique se o endereço https://<PS_IP>:<PS_Data_Port>/ está acessível.

    Erro de certificado HTTPS é esperado ao atingir esse endereço. Ignorar o erro de certificado e continuar deverá encerrar em 400 – Solicitação Incorreta, o que significa que o servidor não pode atender à solicitação do navegador e que a conexão HTTPS padrão com o servidor está funcionando bem e corretamente.

    Se isso não der certo, os detalhes da mensagem de erro no navegador fornecerão orientações. Por exemplo, se a autenticação do proxy estiver incorreta, o servidor proxy retornará 407 – Autenticação de Proxy Obrigatória juntamente com as ações necessárias na mensagem de erro. 

*  Verifique os seguintes logs na VM de origem quanto a erros relacionados às falhas de upload da rede:

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server-for-connectivity-issues"></a>Verifique o servidor de processo para problemas de conectividade

A lista a seguir mostra maneiras para você verificar o servidor de processo:

> [!NOTE]
> O Servidor de Processo deve ter um endereço IPv4 estático e não deve ter o IP de NAT configurado nele.

* **Verifique a conectividade entre os computadores de origem e o Servidor de Processo**
* Caso tenha resposta para Telnet do computador de origem e ainda assim o PS não puder ser acessado a partir da origem, verifique a conexão ponta a ponta com cxprocessserver da VM de origem, executando a ferramenta cxpsclient na VM de origem:

      <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

   Verifique os logs gerados no PS nos diretórios a seguir para obter detalhes sobre os erros correspondentes:

      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
      and
      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
* Verifique os seguintes logs sobre o PS caso não haja nenhuma pulsação do PS. Isso é identificado por **código de erro 806** no portal.

      C:\ProgramData\ASR\home\svsystems\eventmanager*.log
      and
      C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

* **Verifique se o servidor de processo está enviando dados por push ativamente para o Azure**.

  1. No servidor de processo, abra o Gerenciador de Tarefas (pressione Ctrl+Shift+Esc).
  2. Selecione a guia **Desempenho** e clique no link **Abrir o Monitor de Recursos**. 
  3. Na página **Monitor de Recursos**, selecione a guia **Rede**. Em **Processos com Atividade de Rede**, verifique se o **cbengine.exe** está enviando ativamente grandes volumes de dados.

       ![Captura de tela que mostra os volumes em processos com atividade de rede](./media/vmware-azure-troubleshoot-replication/cbengine.png)

  Se cbengine.exe não estiver enviando um grande volume de dados, conclua as etapas nas seções a seguir.

* **Verifique se o servidor de processo pode se conectar ao armazenamento de Blobs do Azure**.

  Selecione **cbengine.exe**. Em **Conexões TCP**, verifique se há conectividade do servidor de processo à URL do Armazenamento de Blobs do Azure.

  ![Captura de tela que mostra a conectividade entre cbengine.exe e a URL do Armazenamento de Blobs do Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

  Se não houver nenhuma conectividade do servidor de processo à URL do Armazenamento de Blobs do Azure, no Painel de Controle, selecione **Serviços**. Verifique se os seguintes serviços estão em execução:

  *  cxprocessserver
  *  Agente do InMage Scout VX – Sentinel/Outpost
  *  Agente de Serviços dos Serviços de Recuperação do Microsoft Azure
  *  Serviço do Microsoft Azure Site Recovery
  *  tmansvc

  Iniciar ou reiniciar qualquer serviço que não está em execução. Verifique se o problema ainda ocorre.

* **Verifique se o servidor de processo pode se conectar ao endereço IP público do Azure usando a porta 443**.

  Em %programfiles%\Microsoft Azure Recovery Services Agent\Temp, abra o arquivo CBEngineCurr.errlog mais recente. No arquivo, procure **443** ou a cadeia de caracteres **Falha na tentativa de conexão**.

  ![Captura de tela que mostra os logs de erros na pasta Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

  Se problemas forem exibidos na linha de comando no servidor de processo, use o Telnet para o endereço IP público do Azure (o endereço IP é mascarado na imagem anterior). Você pode encontrar o endereço IP público do Azure no arquivo CBEngineCurr.currLog usando a porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

  Se não for possível conectar-se, verifique se o problema de acesso é devido às configurações de firewall ou de proxy, conforme descrito na próxima etapa.

* **Verifique se o firewall baseado em endereço IP no servidor de processo bloqueia o acesso**.

  Se você usar regras de firewall baseadas em endereço IP no servidor, baixe a lista completa dos [intervalos de IP de datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adicione intervalos de endereços IP à sua configuração de firewall para garantir que o firewall permita a comunicação com o Azure (e com a porta HTTPS padrão, 443). Permita os intervalos de endereço IP para a região do Azure da sua assinatura e para a região do Azure Oeste dos EUA (usados para controle de acesso e gerenciamento de identidade).

* **Verifique se um firewall baseado em URL no servidor de processo bloqueia o acesso**.

  Se você usar uma regra de firewall baseada em URL no servidor, adicione as URLs listadas na tabela a seguir à configuração do firewall:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Verifique se as configurações de proxy no servidor de processo bloqueiam o acesso**.

   Se você estiver usando um servidor proxy, verifique se o nome do servidor proxy é resolvido pelo servidor DNS. Para verificar o valor que você forneceu ao configurar o servidor de configuração, vá para a chave do Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.

   Em seguida, verifique se as mesmas configurações estão sendo usadas pelo agente do Azure Site Recovery para enviar dados: 
      
   1. Procure o **Backup do Microsoft Azure**. 
   2. Abra **Backup do Microsoft Azure** e, em seguida, selecione **Ação** > **Alterar Propriedades**. 
   3. Na guia **Configuração de Proxy**, você deve ver o endereço de proxy. O endereço de proxy deve ser o mesmo que o endereço de proxy que é exibido nas configurações do Registro. Caso contrário, altere-a para o mesmo endereço.

*  **Verifique se a largura de banda de limitação está ou não restrita no servidor de processo**.

   Aumente a largura de banda e verifique se o problema ainda ocorre.


## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>O computador de origem não está listado no portal do Azure

Ao tentar selecionar o computador de origem para habilitar a replicação por meio do Azure Site Recovery, o computador pode não estar disponível para você por um dos seguintes motivos:

* **Duas máquinas virtuais com o mesmo UUID de instância**: Se houver duas máquinas virtuais no vCenter com o mesmo UUID de instância, a primeira máquina virtual descoberta pelo servidor de configuração será mostrada no portal do Azure. Para resolver esse problema, assegure que não haja duas máquinas virtuais com o mesmo UUID de instância. Esse cenário é geralmente visto em instâncias em que uma VM de backup se torna ativa e é conectada aos nossos registros de descoberta. Consulte [Azure Site Recovery – VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) para resolver esse problema.
* **Credenciais incorretas de usuário do vCenter**: Verifique se você adicionou as credenciais corretas do vCenter durante a configuração do servidor de configuração, usando o modelo OVF ou a configuração unificada. Para verificar as credenciais que você adicionou durante a instalação, consulte [Modificar credenciais para a descoberta automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Privilégios insuficientes do vCenter**: Se as permissões fornecidas para acessar o vCenter não têm as permissões necessárias, pode ocorrer falha ao descobrir máquinas virtuais. Verifique se as permissões descritas em [Preparar uma conta para a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) foram adicionadas à conta de usuário do vCenter.
* **Servidores de gerenciamento do Azure Site Recovery**: Se a máquina virtual for usada como o servidor de gerenciamento em uma ou mais das seguintes funções – servidor de Configuração/servidor de processo de expansão/servidor de destino Mestre, você não poderá escolher a máquina virtual no portal. Os servidores de gerenciamento não podem ser replicados.
* **Já protegida/com failover por meio dos serviços do Azure Site Recovery**: Se a máquina virtual já estiver protegida ou com failover por meio do Site Recovery, essa máquina virtual não estará disponível para seleção de proteção no portal. Verifique se a máquina virtual que você está procurando no portal já não está protegida por algum outro usuário ou sob uma assinatura diferente.
* **vCenter não conectado**: Verifique se o vCenter está no estado conectado. Para verificar, acesse o cofre dos Serviços de Recuperação > Infraestrutura do Site Recovery > Servidores de Configuração > clique no respectivo servidor de configuração > uma folha será aberta à direita com os detalhes dos servidores associados. Verifique se o vCenter está conectado. Se ele estiver no estado "Não Conectado", resolva o problema e, em seguida [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **ESXi desligado**: Se o host ESXi no qual a máquina virtual reside estiver no estado desligado, a máquina virtual não será listada ou não será selecionável no portal do Azure. Ligue o host ESXi e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **Reinicialização pendente**: Se houver uma reinicialização pendente na máquina virtual, você não poderá selecioná-la no portal do Azure. Conclua as atividades de reinicialização pendente e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.
* **IP não encontrado**: Se a máquina virtual não tiver um endereço IP válido associado a ela, você não poderá selecioná-la no portal do Azure. Atribua um endereço IP válido à máquina virtual e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>As máquinas virtuais protegidas estão em cinza no portal

As máquinas virtuais que são replicadas no Site Recovery não estão disponíveis no portal do Azure se há entradas duplicadas no sistema. Para saber como excluir entradas obsoletas e resolver o problema, consulte [Azure Site Recovery – VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-recommended-steps-for-resolution"></a>Etapas para resolução de erros comuns e recomendados

### <a name="initial-replication-issues-error-78169"></a>[Erro 78169] de problemas de replicação inicial

Ao longo de um acima garantindo que há nenhuma conectividade, largura de banda ou tempo sincronizar questões relacionadas, certifique-se de que:

- Nenhum software antivírus está bloqueando o Azure Site Recovery. Saiba mais [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) em exclusões de pastas necessárias para o Azure Site Recovery.

### <a name="application-consistency-recovery-point-missing-error-78144"></a>Ponto de recuperação de consistência do aplicativo ausente [Erro 78144]

 Isso ocorre devido a problemas com cópia de sombra de Volume Service (VSS). Para resolver esse erro: 
 
- Verifique se a versão instalada do agente do Azure Site Recovery é pelo menos 9.22.2. 
- Verifique se o provedor VSS é instalado como um serviço nos serviços do Windows e verifique também se o serviço de componentes do MMC para verificar se o Azure Site Recovery VSS Provider está listado.
- Se o provedor do VSS não está instalado, consulte o [Falha na instalação do artigo de solução de problemas](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Se estiver desabilitado VSS,
    - Verificar se o tipo de inicialização do serviço do provedor do VSS está definido como **automática**.
    - Reinicie os serviços a seguir:
        - Serviço VSS
        - Provedor de VSS do Azure Site Recovery
        - Serviço VDS

### <a name="high-churn-on-source-machine-error-78188"></a>Alta rotatividade no computador de origem [Erro 78188]

Possíveis causas:
- A taxa de alteração de dados (bytes de gravação/s) nos discos listados da máquina virtual é mais do que o [limites com suporte do Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) para o tipo de conta de armazenamento de destino de replicação.
- Há um aumento repentino na taxa de rotatividade devido à qual alta quantidade de dados está pendente para upload.

Como resolver o problema:
- Certifique-se de que o tipo de conta de armazenamento de destino (Standard ou Premium) é provisionado, de acordo com o requisito de taxa de variação na origem.
- Se a variação observada é temporária, aguarde algumas horas para que o carregamento de dados pendentes para acompanhar e criar pontos de recuperação.
- Se o problema persistir, use o ASR [Planejador de implantação](site-recovery-deployment-planner.md#overview) para ajudar a planejar a replicação.

### <a name="no-heartbeat-from-source-machine-error-78174"></a>Nenhuma pulsação da máquina de origem [Erro 78174]

Isso acontece quando o agente de mobilidade do Azure Site Recovery na máquina de origem não está se comunicando com o servidor de configuração (CS).

Para resolver o problema, use as etapas a seguir para verificar a conectividade de rede da VM de origem para o servidor de configuração:

1. Verifique se que o computador de origem está em execução.
2. Entrar no computador de origem usando uma conta que tenha privilégios de administrador.
3. Verificar se os seguintes serviços estão em execução e se não reiniciar os serviços:
   - Svagents (InMage Scout VX Agent)
   - Serviço de Aplicativo InMage Scout
4. No computador de origem, examine os logs no local para obter detalhes do erro:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="no-heartbeat-from-process-server-error-806"></a>Nenhuma pulsação do servidor de processo [Erro 806]
Caso não haja nenhuma pulsação do servidor de processo (PS), verifique se:
1. PS VM está em execução
2. Verifique o seguinte logon do PS para detalhes do erro:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="no-heartbeat-from-master-target-error-78022"></a>Não houve pulsação do destino mestre [Erro 78022]

Isso acontece quando o agente de mobilidade do Azure Site Recovery no destino mestre não está se comunicando com o servidor de configuração.

Para resolver o problema, use as etapas a seguir para verificar o status do serviço:

1. Verifique se que a VM de destino mestre está em execução.
2. Entrar para a VM de destino mestre usando uma conta que tenha privilégios de administrador.
    - Verifique se o serviço svagents está em execução. Se estiver em execução, reinicie o serviço
    - Verifique os logs no local para obter detalhes do erro:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-is-not-reachable-from-the-source-machine-error-78186"></a>Servidor de processo não está acessível da máquina de origem [Erro 78186]

Esse erro resulta em pontos consistentes de aplicativo e falha não seja gerados se não for resolvido. Para resolver o problema, siga os links de solução de problemas abaixo:
1. Certifique-se de que [PS serviços estão em execução](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Verifique os problemas de conectividade de máquina de origem](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Verifique os problemas de conectividade do servidor de processo](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) e siga as orientações fornecidas para:
    - Verificando a conectividade com o código-fonte
    - Problemas de firewall e proxy

### <a name="data-upload-blocked-from-source-machine-to-process-server-error-78028"></a>Carregamento de dados bloqueado da máquina de origem para o servidor de processo [Erro 78028]

Esse erro resulta em pontos consistentes de aplicativo e falha não seja gerados se não for resolvido. Para resolver o problema, siga os links de solução de problemas abaixo:

1. Certifique-se de que [PS serviços estão em execução](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Verifique os problemas de conectividade de máquina de origem](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Verifique os problemas de conectividade do servidor de processo](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) e siga as orientações fornecidas para:
    - Verificando a conectividade com o código-fonte
    - Problemas de firewall e proxy

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Temos uma comunidade ativa e um dos nossos engenheiros poderá ajudá-lo.
