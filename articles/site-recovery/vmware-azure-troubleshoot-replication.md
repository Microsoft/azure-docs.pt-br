---
title: Solucionar problemas de replicação para recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações sobre a solução de problemas comuns de replicação durante a recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 8b44a1d6119cc658b9460e0a52fa0629f759964a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336198"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Solução de problemas de replicação para VMs VMware e servidores físicos

Este artigo descreve alguns problemas comuns e erros específicos que você pode encontrar ao replicar VMs locais da VMware e servidores físicos no Azure usando o [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Etapa 1: Monitorar a integridade do servidor de processo

O Site Recovery usa o [servidor de processo](vmware-physical-azure-config-process-server-overview.md#process-server) para receber e otimizar os dados replicados e enviá-los para o Azure.

Recomendamos que você monitore a integridade dos servidores de processo no portal, para garantir que eles estejam conectados e funcionando corretamente, e que a replicação esteja progredindo para os computadores de origem associados ao servidor de processo.

- [Saiba mais sobre](vmware-physical-azure-monitor-process-server.md) como monitorar servidores de processo.
- [Examinar práticas recomendadas](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Solucionar problemas](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) de integridade do servidor de processo.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Etapa 2: Solucionar problemas de conectividade e replicação

As falhas de replicação inicial e contínua geralmente são causadas por problemas de conectividade entre o servidor de origem e o servidor de processo ou entre o servidor de processo e o Azure.

Para resolver esses problemas, consulte [Solucionar problemas de conectividade e de replicação](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Etapa 3: Solucionar problemas de computadores de origem que não estão disponíveis para replicação

Ao tentar selecionar o computador de origem para habilitar a replicação por meio do Azure Site Recovery, o computador pode não estar disponível para você por um dos seguintes motivos:

* **Duas máquinas virtuais com o mesmo UUID de instância**: Se houver duas máquinas virtuais no vCenter com o mesmo UUID de instância, a primeira máquina virtual descoberta pelo servidor de configuração será mostrada no portal do Azure. Para resolver esse problema, assegure que não haja duas máquinas virtuais com o mesmo UUID de instância. Esse cenário é geralmente visto em instâncias em que uma VM de backup se torna ativa e é conectada aos nossos registros de descoberta. Consulte [Azure Site Recovery – VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) para resolver esse problema.
* **Credenciais incorretas de usuário do vCenter**: Verifique se você adicionou as credenciais corretas do vCenter durante a configuração do servidor de configuração, usando o modelo OVF ou a configuração unificada. Para verificar as credenciais que você adicionou durante a instalação, consulte [Modificar credenciais para a descoberta automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Privilégios insuficientes do vCenter**: Se as permissões fornecidas para acessar o vCenter não têm as permissões necessárias, pode ocorrer falha ao descobrir máquinas virtuais. Verifique se as permissões descritas em [Preparar uma conta para a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) foram adicionadas à conta de usuário do vCenter.
* **Servidores de gerenciamento do Azure Site Recovery**: Se a máquina virtual for usada como o servidor de gerenciamento em uma ou mais das seguintes funções – servidor de Configuração/servidor de processo de expansão/servidor de destino Mestre, você não poderá escolher a máquina virtual no portal. Os servidores de gerenciamento não podem ser replicados.
* **Já protegida/com failover por meio dos serviços do Azure Site Recovery**: Se a máquina virtual já estiver protegida ou com failover por meio do Site Recovery, essa máquina virtual não estará disponível para seleção de proteção no portal. Verifique se a máquina virtual que você está procurando no portal já não está protegida por algum outro usuário ou sob uma assinatura diferente.
* **vCenter não conectado**: Verifique se o vCenter está no estado conectado. Para verificar, acesse o cofre dos Serviços de Recuperação > Infraestrutura do Site Recovery > Servidores de Configuração > clique no respectivo servidor de configuração > uma folha será aberta à direita com os detalhes dos servidores associados. Verifique se o vCenter está conectado. Se ele estiver em um estado “Não Conectado”, resolva o problema e, em seguida [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **ESXi desligado**: Se o host ESXi no qual a máquina virtual reside estiver no estado desligado, a máquina virtual não será listada ou não será selecionável no portal do Azure. Ligue o host ESXi e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **Reinicialização pendente**: Se houver uma reinicialização pendente na máquina virtual, você não poderá selecioná-la no portal do Azure. Conclua as atividades de reinicialização pendente e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.
* **IP não encontrado**: Se a máquina virtual não tiver um endereço IP válido associado a ela, você não poderá selecioná-la no portal do Azure. Atribua um endereço IP válido à máquina virtual e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Solucionar problemas de máquinas virtuais protegidas que estão esmaecidas no portal

As máquinas virtuais que são replicadas no Site Recovery não estão disponíveis no portal do Azure se há entradas duplicadas no sistema. Para saber como excluir entradas obsoletas e resolver o problema, consulte [Azure Site Recovery – VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Nenhum ponto de recuperação consistente de falha disponível para a VM nos últimos “XXX” minutos

Alguns dos problemas mais comuns estão listados abaixo

### <a name="initial-replication-issues-error-78169"></a>Problemas de replicação inicial [erro 78169]

Na parte superior acima, para verificar se não há problemas relacionados à conectividade, largura de banda ou sincronização de tempo, verifique se:

- Nenhum software antivírus está bloqueando o Azure Site Recovery. Saiba [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sobre exclusões de pasta necessárias para o Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Computadores de origem com variação alta [erro 78188]

Possíveis causas:
- A taxa de alteração de dados (bytes de gravação/s) nos discos listados da máquina virtual é mais do que os [limites com suporte do Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) para o tipo de conta de armazenamento de destino de replicação.
- Há um pico repentino na taxa de rotatividade em função do qual uma grande quantidade de dados está pendente para upload.

Como resolver o problema:
- Verifique se o tipo de conta de armazenamento de destino (Standard ou Premium) é provisionado de acordo com o requisito de taxa de rotatividade na origem.
- Se você já estiver replicando para um disco gerenciado Premium (tipo asrseeddisk), verifique se o tamanho do disco dá suporte à taxa de rotatividade observada de acordo com os limites do Site Recovery. Você pode aumentar o tamanho do asrseeddisk, se necessário. Siga as etapas abaixo:
    - Navegue até a folha Discos da máquina replicada afetada e copie o nome do disco de réplica
    - Navegar até este disco gerenciado de réplica
    - Você poderá ver uma faixa na folha Visão Geral dizendo que uma URL SAS foi gerada. Clique nessa faixa e cancele a exportação. Ignore esta etapa se você não vir a faixa.
    - Assim que a URL SAS for revogada, vá para a folha de configuração do disco gerenciado e aumente o tamanho para que Azure Site Recovery suporte a taxa de rotatividade observada no disco de origem
- Se a variação observada for temporária, aguarde algumas horas para que o carregamento de dados pendente seja atualizado e crie pontos de recuperação.
- Se o disco contiver dados não críticos, como logs temporários, dados de teste, etc., considere mover esses dados para outro lugar ou exclua completamente este disco da replicação.
- Se o problema persistir, use o [planejador de implantação](site-recovery-deployment-planner.md#overview) do Site Recovery para ajudar a planejar a replicação.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Computadores de origem sem pulsação [erro 78174]

Isso acontece quando o agente de Mobilidade do Azure Site Recovery no computador de origem não está se comunicando com o Servidor de Configuração (CS).

Para resolver o problema, use as seguintes etapas para verificar a conectividade de rede da VM de origem no Servidor de Configuração:

1. Verifique se a Máquina de Origem está funcionando.
2. Entre no Computador de Origem usando uma conta que tenha privilégios de administrador.
3. Verifique se os seguintes serviços estão em execução e, se não estiverem, reinicie os serviços:
   - Svagents (agente do InMage Scout VX)
   - Serviço de Aplicativo InMage Scout
4. No Computador de Origem, examine os logs no local para obter os detalhes do erro:

    *C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\svagents \* . log*

### <a name="process-server-with-no-heartbeat-error-806"></a>Servidor de processo sem pulsação [erro 806]
Caso não haja nenhuma pulsação no Servidor de Processo (PS), verifique se:
1. A VM PS está em execução
2. Verifique os seguintes logs no Servidor de Processo para obter detalhes do erro:

    *C:\ProgramData\ASR\home\svsystems\eventmanager \* . log*\
    e
    *C:\ProgramData\ASR\home\svsystems\ monitor_protection \* . log*

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Servidor do destino mestre sem pulsação [erro 78022]

Isso acontece quando o agente de Mobilidade do Azure Site Recovery no Destino Mestre não está se comunicando com o Servidor de Configuração.

Para resolver o problema, use as seguintes etapas para verificar o status do serviço:

1. Verifique se o Destino Mestre está em execução.
2. Entre na VM do Destino Mestre usando uma conta que tenha privilégios de administrador.
    - Verifique se o serviço svagents está em execução. Se estiver em execução, reinicie o serviço.
    - Verifique os logs no local para obter detalhes do erro:

        *C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\svagents \* . log*
3. Para registrar o destino mestre no servidor de configuração, navegue até a pasta **%PROGRAMDATA%\ASR\Agent** e execute o seguinte no prompt de comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID do erro 78144 — nenhum ponto de recuperação consistente com o aplicativo disponível para a VM nos últimos “XXX” minutos

Foram feitas melhorias nas versões do agente de mobilidade [9.23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher) & [9.27](site-recovery-whats-new.md#update-rollup-39) para lidar com os comportamentos de falha de instalação do VSS. Verifique se você está nas versões mais recentes para obter a melhor orientação sobre a solução de falhas do VSS.

Alguns dos problemas mais comuns estão listados abaixo

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: Problema conhecido no SQL Server 2008/2008 R2
**Como corrigir**: Há um problema conhecido com o SQL Server 2008/2008 R2. Consulte este artigo da base de dados [Agente do Azure Site Recovery ou outro backup do VSS de não componentes falha para um servidor que hospeda o SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: Os trabalhos do Azure Site Recovery trabalhos falham nos servidores que hospedam qualquer versão das instâncias do SQL Server com bancos AUTO_CLOSE
**Como corrigir**: Consulte o [artigo](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) da base de dados


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: Problema conhecido no SQL Server 2016 e 2017
**Como corrigir**: Consulte o [artigo](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) da base de dados

#### <a name="cause-4-app-consistency-not-enabled-on-linux-servers"></a>Causa 4: App-Consistency não habilitado em servidores Linux
**Como corrigir: a** Azure site Recovery para o sistema operacional Linux dá suporte a scripts personalizados de aplicativo para consistência de aplicativo. O script personalizado com as opções pre e post será usado pelo agente de mobilidade Azure Site Recovery para consistência de aplicativo. [Aqui](./site-recovery-faq.md#replication) estão as etapas para habilitá-lo.

### <a name="more-causes-due-to-vss-related-issues"></a>Mais causas devido a problemas relacionados ao VSS:

Para solucionar mais problemas, verifique os arquivos no computador de origem para obter o código de erro exato para a falha:

*C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log*

Como localizar os erros no arquivo?
Procure a cadeia de caracteres “vacpError” abrindo o arquivo de log vacp. em um editor

`Ex: `**`vacpError`**`:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|`

No exemplo acima **2147754994** é o código de erro que informa sobre a falha, conforme mostrado abaixo

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O gravador VSS não está instalado — erro 2147221164

*Como corrigir*: Para gerar a marca de consistência do aplicativo, o Azure Site Recovery usa o VSS (Serviço de Cópias de Sombra de Volume) da Microsoft. Ele instala um provedor do VSS para sua operação para obter instantâneos da consistência do aplicativo. Este provedor do VSS é instalado como um serviço. Caso o serviço do provedor do VSS não esteja instalado, a criação do instantâneo de consistência do aplicativo falha com a ID do erro 0x80040154 "classe não registrada". </br>
Consulte o [artigo para solução de problemas de instalação do gravador VSS](./vmware-azure-troubleshoot-push-install.md#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>O gravador VSS está desabilitado — Erro 2147943458

**Como corrigir**: Para gerar a marca de consistência do aplicativo, o Azure Site Recovery usa o VSS (Serviço de Cópias de Sombra de Volume) da Microsoft. Ele instala um provedor do VSS para sua operação para obter instantâneos da consistência do aplicativo. Este provedor do VSS é instalado como um serviço. Caso o serviço do provedor do VSS esteja desabilitado, a criação do instantâneo de consistência do aplicativo falha com a ID do erro "o serviço especificado está desabilitado e não pode ser iniciado (0x80070422)". </br>

- Se o VSS estiver desabilitado,
    - Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **Automático**.
    - Reinicie os seguintes serviços:
        - Serviço de Cópias de Sombra de Volume
        - Provedor de VSS do Azure Site Recovery
        - Serviço VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Error 2147754756

**Como corrigir**: Para gerar a marca de consistência do aplicativo, o Azure Site Recovery usa o VSS (Serviço de Cópias de Sombra de Volume) da Microsoft.
Verifique se o serviço de provedor do VSS do Azure Site Recovery está instalado ou não. </br>

- Repita a instalação do provedor usando os seguintes comandos:
- Desinstalar o provedor existente: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Reinstalar: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **Automático**.
    - Reinicie os seguintes serviços:
        - Serviço de Cópias de Sombra de Volume
        - Provedor de VSS do Azure Site Recovery
        - Serviço VDS

## <a name="error-id-95001---insufficient-permissions-found"></a>ID do erro 95001-permissões insuficientes encontradas

Esse erro ocorre ao tentar habilitar a replicação e as pastas do aplicativo não têm permissões suficientes.

**Como corrigir**: para resolver esse problema, verifique se o usuário IUSR tem função de proprietário para todas as pastas mencionadas abaixo-

- *C\ProgramData\Microsoft Recovery\private Azure site*
- O diretório de instalação. Por exemplo, se o diretório de instalação for uma unidade F, forneça as permissões corretas para-
    - *Arquivos F:\Program (x86) \Microsoft Azure site Recovery\home\svsystems*
- A pasta *\pushinstallsvc* no diretório de instalação. Por exemplo, se o diretório de instalação for uma unidade F, forneça as permissões corretas para-
    - *Arquivos F:\Program (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc*
- A pasta *\etc* no diretório de instalação. Por exemplo, se o diretório de instalação for uma unidade F, forneça as permissões corretas para-
    - *Arquivos F:\Program (x86) \Microsoft Azure site Recovery\home\svsystems\etc*
- *C:\temp.*
- *C:\thirdparty\php5nts*
- Todos os itens sob o caminho abaixo-
    - *C:\thirdparty\rrdtool-1.2.15-win32-perl58\rrdtool\Release\**

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste sua pergunta na [página de Perguntas e Respostas da Microsoft para o Azure Site Recovery](/answers/topics/azure-site-recovery.html). Temos uma comunidade ativa e um dos nossos engenheiros poderá ajudá-lo.
