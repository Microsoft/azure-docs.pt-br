---
title: Solucionar problemas de recuperação de desastres do Hyper-V to Azure com o Azure Site Recovery | Microsoft Docs
description: Descreve como solucionar problemas de recuperação de desastres com a replicação do Hyper-V para o Azure usando o Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 8bb790571e1499bd45fb8bee27f4f1896046cbc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749049"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Solucionar problemas de Hyper-V para replicação e failover do Azure

Este artigo descreve problemas comuns que você pode encontrar ao replicar VMs do Hyper-V locais para o Azure, usando [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Problemas ao habilitar a proteção

Se você enfrentar problemas ao habilitar a proteção para VMs do Hyper-V, verifique as seguintes recomendações:

1. Verifique se os hosts Hyper-V e VMs satisfazer todas as [requisitos e pré-requisitos](hyper-v-azure-support-matrix.md).
2. Se os servidores Hyper-V estiverem localizados nas nuvens do System Center Virtual Machine Manager (VMM), verifique se você preparou o [servidor VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Verifique se o serviço de gerenciamento de máquina Virtual do Hyper-V está em execução em hosts Hyper-V.
4. Verifique se há problemas que aparecem na entrada Hyper-V-Hyper-v-vmms\admin na VM. Esse log está localizado em **Logs de Aplicativos e Serviços** > **Microsoft** > **Windows**.
5. Na VM convidada, verifique se o WMI está habilitado e acessível.
   - [Saiba mais sobre](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) teste básico de WMI.
   - [Solucione problemas](https://aka.ms/WMiTshooting) de WMI.
   - [Solucionar problemas de](https://technet.microsoft.com/library/ff406382.aspx#H22) problemas com scripts do WMI e serviços.
6. Na VM convidada, certifique-se de que a versão mais recente do Integration Services está em execução.
    - [Verifique](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) se você tem a versão mais recente.
    - [Mantenha](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) o Integration Services atualizado.
    
## <a name="replication-issues"></a>Problemas de replicação

Solucione problemas com a replicação inicial e contínua da seguinte maneira:

1. Verifique se você está executando a [versão mais recente](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) do Site Recovery.
2. Verifique se a replicação está em pausa:
   - Verifique o status de integridade da VM no console do Gerenciador do Hyper-V.
   - Se for crítico, clique com o botão direito na VM > **Replicação** > **Exibir integridade da replicação**.
   - Se a replicação estiver em pausa, clique em **Retomar replicação**.
3. Verifique se todos os serviços necessários estão em execução. Se não estiverem, reinicie-os.
    - Se você estiver replicando o Hyper-V sem o VMM, verifique se esses serviços estão em execução no host Hyper-V:
        - Serviço Gerenciamento de máquinas virtuais
        - Serviço Agente dos Serviços de Recuperação do Microsoft Azure
        - Serviço do Microsoft Azure Site Recovery
        - Serviço de Host do Provedor de WMI
    - Se você estiver replicando com o VMM no ambiente, verifique se estes serviços estão em execução:
        - No host do Hyper-V, verifique se o serviço de Gerenciamento de máquina virtual, o agente de Serviços de Recuperação do Microsoft Azure e o serviço de Host do provedor de WMI estão em execução.
        - No servidor do VMM, certifique-se de que o serviço do System Center Virtual Machine Manager está em execução.
4. Verifique a conectividade entre o servidor Hyper-V e o Azure. Para verificar a conectividade, abra o Gerenciador de Tarefas no host Hyper V. Na guia **Desempenho**, clique em **Abrir o Monitor de Recursos**. Na guia **Rede**> **Processar com Atividade de Rede**, verifique se o cbengine.exe está enviando ativamente grandes volumes (Mb) de dados.
5. Verifique se os hosts Hyper-V podem se conectar à URL de blob de armazenamento do Azure. Para verificar se os hosts podem se conectar, selecione e marque **cbengine.exe**. Exiba **Conexões TCP** para verificar a conectividade do host para o blob de armazenamento do Azure.
6. Verifique problemas de desempenho, conforme descrito abaixo.
    
### <a name="performance-issues"></a>Problemas de desempenho

Limitações de largura de banda de rede podem afetar a replicação. Solucione problemas da seguinte maneira:

1. [Verifique](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) se não há largura de banda ou restrições no seu ambiente de limitação.
2. Execute o [criador de perfil Planejador de Implantações](hyper-v-deployment-planner-run.md).
3. Depois de executar o criador de perfil, execute as recomendações de [largura de banda](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e [armazenamento](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation).
4. Verifique [limitações de rotatividade de dados](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Se você consultar dados de alta rotatividade em uma VM, faça o seguinte:
   - Verifique se sua VM está marcada para ser sincronizada novamente.
   - Execute [estas etapas](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) para investigar a fonte de rotatividade.
   - A rotatividade pode ocorrer quando os arquivos de log do HRL excedem 50% de espaço em disco disponível. Se esse for o problema, provisione mais espaço de armazenamento para todas as VMs em que o problema ocorre.
   - Verifique se a replicação não está em pausa. Se estiver, ele continua a gravar as alterações no arquivo HRL, o que pode contribuir para o aumento de tamanho.
 

## <a name="critical-replication-state-issues"></a>Problemas de estado de replicação crítico

1. Para verificar a integridade da replicação, conecte-se ao console do Gerenciador do Hyper-V, selecione a VM e verifique a integridade.

    ![Integridade da replicação](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Clique em **Exibir integridade da replicação** para ver os detalhes:

    - Se a replicação estiver em pausa, clique com botão direito na VM > **Replicação** > **Retomar replicação**.
    - Se uma VM em um host Hyper-V configurado no Site Recovery migra para um host Hyper-V diferente no mesmo cluster, ou para um computador independente, a replicação para a VM não é impactada. Verifique se o novo host Hyper-V atende a todos os pré-requisitos e está configurado no Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemas de instantâneo consistente com o aplicativo

Um instantâneo consistente com o aplicativo é um instantâneo em um ponto no tempo dos dados do aplicativo dentro da VM. O VSS (Serviço de Cópias de Sombra de Volume) garante que os aplicativos na VM estejam em um estado consistente quando o instantâneo for criado.  Esta seção fornece detalhes sobre alguns problemas comuns que podem ser encontrados.

### <a name="vss-failing-inside-the-vm"></a>VSS com falha dentro da VM

1. Verifique se a versão mais recente do Integration Services está instalada e em execução.  Verifique se há uma atualização executando o seguinte comando em um prompt elevado do PowerShell no host Hyper-V: **get-vm  | select Name, State, IntegrationServicesState**.
2. Verifique se os serviços VSS estão em execução e íntegros:
   - Para verificar se os serviços, entrar VM convidada. Em seguida, abra um prompt de comando do administrador e execute os seguintes comandos para verificar se todos os gravadores VSS estão íntegros.
       - **Vssadmin list writers**
       - **Vssadmin list shadows**
       - **Vssadmin list providers**
   - Verifique a saída. Se os gravadores estiverem em um estado de falha, faça o seguinte:
       - Verifique o log de eventos do aplicativo na VM para erros de operação do VSS.
   - Tente reiniciar esses serviços associados com o gravador com falha:
     - Cópia de Sombra de Volume
       - Provedor de VSS do Azure Site Recovery
   - Depois de fazer isso, aguarde algumas horas para verificar se os instantâneos consistentes com o aplicativo são gerados com êxito.
   - Como último recurso, tente reinicializar a VM. Isso pode resolver os serviços que estão em estado não responsivo.
3. Verifique se que você não tem discos dinâmicos na VM. Isso não tem suporte para instantâneos consistentes com o aplicativo. Você pode abrir o Disk Management (diskmgmt.msc).

    ![Dados dinâmicos](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Verifique se você não tem um disco iSCSI anexado à VM. Não há suporte para isso.
5. Verifique se o serviço de Backup está habilitado. Verifique se está habilitado em **configurações do Hyper-V** > **Integration Services**.
6. Verifique se não há conflitos com aplicativos de que estão criando instantâneos do VSS. Se vários aplicativos estiverem tentando criando instantâneos do VSS ao mesmo tempo, podem ocorrer conflitos. Por exemplo, se um aplicativo de Backup está criando instantâneos do VSS quando o Site Recovery está agendado pela sua política de replicação para criar um instantâneo.   
7. Verifique se a VM está passando por uma taxa de rotatividade alta:
    - Você pode medir a taxa diária de alteração de dados para VMs convidadas usando os contadores de desempenho no host Hyper-V. Para medir a taxa de alteração de dados, habilite o seguinte contador. Agregue um exemplo desse valor entre os discos de VM por 5 a 15 minutos para obter a rotatividade da VM.
        - Categoria: “Dispositivo de armazenamento virtual do Hyper-V”
        - Contador: “Bytes de Gravação / Seg”</br>
        - A taxa de rotatividade de dados aumentará ou permanecerá em um nível alto, dependendo de quão ocupada a VM ou seus aplicativos estiverem.
        - A rotatividade de dados do disco de origem médio é de 2 MB/seg. para armazenamento padrão para Site Recovery. [Saiba mais](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Além disso, você pode [verificar destinos de escalabilidade de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).
8. Execute o [Planejador de Implantações](hyper-v-deployment-planner-run.md).
9. Revise as recomendações para [rede](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e [armazenamento](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS com falha dentro do Host Hyper-V

1. Verifique os logs de eventos para erros do VSS e recomendações:
    - No servidor host Hyper-V, abra o log de eventos do administrador do Hyper-V em **Visualizador de Eventos** > **Logs de Aplicativos e Serviços** > **Microsoft**  >  **Windows** > **Hyper-V** > **Administrador**.
    - Verifique se há algum evento que indica falhas de instantâneo consistente com o aplicativo.
    - Um erro típico é: "Falha do Hyper-V em gerar o conjunto de instantâneo do VSS para a máquina virtual 'XYZ': O gravador apresentou um erro não transitório. Reinicie o serviço VSS pode resolver problemas se ele não estiver respondendo."

2. Para gerar os instantâneos do VSS para a VM, verifique se os serviços de integração do Hyper-V estão instalados na VM e se o serviço de integração de Backup (VSS) está habilitado.
    - Certifique-se de que o serviço VSS/daemons do Integration Services estão em execução no convidado e se estão em um estado **OK**.
    - Você pode verificar isso em uma sessão do PowerShell elevada no host Hyper-V com o comando **et-VMIntegrationService -VMName<VMName>-Name VSS**. Também é possível obter essas informações fazendo logon na VM convidada. [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Verifique se os serviços de integração de Backup/VSS na VM estão em execução e em estado íntegro. Se não, reinicie esses serviços e o serviço do solicitante de Cópia de Sombra de Volume do Hyper-V no servidor host Hyper-V.

### <a name="common-errors"></a>Erros comuns

**Código de erro** | **Mensagem** | **Detalhes**
--- | --- | ---
**0x800700EA** | "Falha do Hyper-V em gerar o conjunto de instantâneo do VSS para a máquina virtual: Mais dados disponíveis. (0x800700EA). A geração do conjunto de instantâneos de VSS pode falhar se a operação de backup estiver em andamento.<br/><br/> A operação de replicação para a máquina virtual falhou: Mais dados disponíveis.” | Verifique se sua VM tem um disco dinâmico habilitado. Não há suporte para isso.
**0x80070032** | "Falha do solicitante de cópia de sombra de volume do Hyper-V em se conectar à máquina virtual <./VMname> porque a versão não corresponde à versão esperada pelo Hyper-V | Verifique se as atualizações mais recentes do Windows estão instaladas.<br/><br/> [Atualize](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) para a versão mais recente do Integration Services.



## <a name="collect-replication-logs"></a>Coletar logs de replicação

Todos os eventos de replicação do Hyper-V são registrados no log do Hyper-V-VMMS\Admin localizado em **Logs de Aplicativos e Serviços** > **Microsoft** > **Windows**. Além disso, você pode habilitar um log analítico para o Serviço de Gerenciamento de Máquinas Virtuais do Hyper-V, como a seguir:

1. Exiba os logs analíticos e de depuração no Visualizador de Eventos. Para disponibilizar os logs, no Visualizador de Eventos, clique em **Visualizar** > **Mostrar Logs Analíticos e de Depuração.**. O log Analítico aparece em **VMMS do Hyper-V**.
2. No painel **Ações**, clique em **Habilitar Log**. 

    ![Habilitar log](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Depois de habilitado, ele é exibido no **Monitor de Desempenho** como uma **Sessão de Rastreamento de Evento** em **Conjuntos de Coletores de Dados**. 
4. Para exibir as informações coletadas, interrompa a sessão de rastreamento desabilitando o log. Em seguida, salve o log e abra-o novamente no Visualizador de Eventos ou use outras ferramentas para convertê-lo conforme solicitado.


### <a name="event-log-locations"></a>Locais de log de eventos

**Log de eventos** | **Detalhes** |
--- | ---
**Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** (Servidor VMM) | Logs para solucionar problemas de VMM.
**Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (host Hyper-V) | Logs para solucionar problemas com o Agente de Serviços de Recuperação do Microsoft Azure. 
**Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** (host Hyper-V)| Logs para solucionar problemas com o Serviço do Microsoft Azure Site Recovery.
**Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (host Hyper-V) | Logs para solucionar problemas de gerenciamento de VM do Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Coleção de log para solução problemas avançada

Estas ferramentas podem ajudar na solução de problemas avançada:

-   Para o VMM, execute a coleção de log do Site Recovery usando a [ferramenta da Plataforma de diagnóstico de suporte (SDP)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Para Hyper-V sem VMM, [faça o download desta ferramenta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) e execute-a no host Hyper-V para coletar os logs.

