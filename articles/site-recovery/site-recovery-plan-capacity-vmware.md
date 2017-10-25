---
title: "Planejar a capacidade e o dimensionamento para a replicação do VMware para o Azure com o Azure Site Recovery | Microsoft Docs"
description: Use este artigo para planejar a capacidade e a escala ao replicar VMs do VMware para o Azure com o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/24/2017
ms.author: rayne
ms.openlocfilehash: 8b580ac239bfb6d7b633fb03d4cfb91b168b0610
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Planejar capacidade e dimensionamento para replicação VMware com o Azure Site Recovery

Use este artigo para descobrir como planejar a capacidade e o dimensionamento ao replicar VMs VMware locais e servidores físicos no Azure com o [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Como iniciar o planejamento de capacidade?

Colete informações sobre seu ambiente de replicação executando o [Planejador de Implantação do Azure Site Recovery](https://aka.ms/asr-deployment-planner-doc) para replicação do VMware. [Saiba mais](site-recovery-deployment-planner.md) sobre essa ferramenta. Você coletará informações sobre VMs compatíveis e incompatíveis, discos por VM e variação de dados por disco. A ferramenta também inclui requisitos de largura de banda da rede, bem como a infraestrutura do Azure necessária para replicação e failover de teste bem-sucedidos.

## <a name="capacity-considerations"></a>Considerações sobre a capacidade

**Componente** | **Detalhes** |
--- | --- | ---
**Replicação** | **Taxa máxima de alteração diária**: um computador protegido só pode usar um servidor de processo, e um único servidor de processo pode lidar com uma taxa de alteração diária de até 2 TB. Portanto, 2 TB é a taxa de alteração diária máxima com suporte para um computador protegido.<br/><br/> **Taxa de transferência máxima**: um computador replicado pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão pode lidar com um máximo de 20 mil solicitações por segundo e o recomendável é manter o número de operações IOPS (entrada/saída por segundo) em um computador de origem como 20 mil. Por exemplo, se você tiver um computador de origem com 5 discos e cada disco gerar 120 IOPS (8 K de tamanho) no computador de origem, ele estará dentro do limite IOPS de 500 por disco do Azure. (O número de contas de armazenamento necessário é igual ao total de IOPS do computador de origem, dividido por 20 mil).
**Servidor de configuração** | O servidor de configuração deve ser capaz de lidar com a capacidade da taxa de alteração diária em todas as cargas de trabalho em execução em computadores protegidos e precisa ter largura de banda suficiente para replicar continuamente os dados no Armazenamento do Azure.<br/><br/> Como prática recomendada, aloque o servidor de configuração na mesma rede e no mesmo segmento de LAN que os computadores que deseja proteger. Ele pode ser colocado em uma rede diferente, mas os computadores que você quer proteger devem ter visibilidade de rede de camada 3 para ele.<br/><br/> As recomendações de tamanho para o servidor de configuração são resumidas na tabela da seção a seguir.
**Servidor de processo** | O primeiro servidor de processo é instalado por padrão no servidor de configuração. Você pode implantar servidores de processo adicionais par dimensionar seu ambiente. <br/><br/> O servidor de processo recebe os dados de replicação de computadores protegidos e os otimiza com caching, compactação e criptografia. Em seguida, ele envia os dados ao Azure. O computador do servidor de processo deve ter recursos suficientes para executar essas tarefas.<br/><br/> O servidor de processo usa o cache baseado em disco. Use um disco de cache separado de 600 GB ou mais para lidar com alterações de dados armazenados em caso de afunilamento ou interrupção da rede.

## <a name="size-recommendations-for-the-configuration-server"></a>Recomendações de tamanho para o servidor de configuração

**CPU** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes * 4 núcleos a 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Replique menos de 100 computadores.
12 vCPUs (2 soquetes * 6 núcleos @ 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Replique entre 100 e 150 computadores.
16 vCPUs (2 soquetes * 8 núcleos @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Replique entre 150 e 200 computadores.
Implantar outro servidor de processo | | | > 2 TB | Implante servidores de processo adicionais se estiver replicando mais de 200 computadores ou se a taxa de alteração diária de dados ultrapassar 2 TB.

Em que:

* Cada computador de origem é configurado com 3 discos de 100 GB.
* Usamos armazenamento de benchmark de 8 unidades SAS de 10 K de RPM, com RAID 10, para as medidas do disco de cache.

## <a name="size-recommendations-for-the-process-server"></a>Recomendações de tamanho para o servidor de processo

Se precisar proteger mais de 200 computadores, ou se a taxa de alteração diária for maior que 2 TB, você poderá adicionar servidores de processo para manipular a carga de replicação. Para escalar horizontalmente, você pode:

* Aumente o número de servidores de configuração. Por exemplo, você pode proteger até 400 computadores com dois servidores de configuração.
* Adicione mais servidores de processo e use-os para manipular o tráfego em vez do (ou além do) servidor de configuração.

A tabela a seguir descreve um cenário em que:

* Você não planeja usar o servidor de configuração como um servidor de processo.
* Você configurou um servidor de processo adicional.
* Você configurou máquinas virtuais protegidas para usar o servidor em processo adicional.
* Cada computador de origem protegido é configurado com três discos de 100 GB cada.

**Servidor de configuração** | **Servidor de processo adicional** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes * 4 núcleos a 2,5 GHz), 16 GB de memória | 4 vCPUs (2 soquetes * 2 núcleos a 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Replicar 85 computadores ou menos.
8 vCPUs (2 soquetes * 4 núcleos a 2,5 GHz), 16 GB de memória | 8 vCPUs (2 soquetes * 4 núcleos a 2,5 GHz), 12 GB de memória | 600 GB | 250 GB a 1 TB | Replique entre 85 e 150 computadores.
12 vCPUs (2 soquetes * 6 núcleos a 2,5 GHz), 18 GB de memória | 12 vCPUs (2 soquetes * 6 núcleos a 2,5 GHz), 24 GB de memória | 1 TB | 1 TB a 2 TB | Replique entre 150 e 225 computadores.

A maneira como você escala seus servidores depende de sua preferência por um modelo que escale vertical ou horizontalmente.  Você pode escalar verticalmente implantando alguns servidores de processo e de configuração de alto nível ou escalar horizontalmente implantando mais servidores com poucos recursos. Por exemplo, se você precisa proteger 220 computadores, faça o seguinte:

* Instale o servidor de configuração com 12 vCPUs, 18 GB de memória e um servidor de processo adicional com 12 vCPUs, 24 GB de memória. Configure computadores protegidos para usar apenas o servidor de processo adicional.
* Instale dois servidores de configuração (2 x 8 vCPUs, 16 GB de RAM) e dois servidores de processo adicionais (1 x 8 vCPUs e 4 vCPUs x 1 para tratar de 135 + 85 [220] computadores). Configure computadores protegidos para usar apenas os servidores de processo adicionais.


## <a name="control-network-bandwidth"></a>Controlar largura de banda da rede

Depois de usar [a ferramenta Planejador de Implantação](site-recovery-deployment-planner.md) para calcular a largura de banda necessária para a replicação (a replicação inicial e, depois, a delta), você pode controlar a quantidade de largura de banda utilizada para a replicação usando algumas opções:

* **Restringir a largura de banda**: o tráfego VMware que replica para o Azure passa por um servidor de processo específico. Você pode limitar a largura de banda nos computadores em execução como servidores de processo.
* **Influenciar a largura de banda**: você pode influenciar a largura de banda usada para replicação usando algumas chaves do Registro:
  * O valor de registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** especifica o número de threads usados para a transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação.
  * O **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** especifica o número de threads usados para transferência de dados durante o failback.

### <a name="throttle-bandwidth"></a>Restringir a largura de banda

1. Abra o snap-in do MMC do Backup do Azure no computador atuando como o servidor de processo. Por padrão, um atalho para o Backup está disponível na área de trabalho ou na seguinte pasta: C:\Arquivos de Programas\Serviços de Recuperação do Microsoft Azure\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.

    ![Captura de tela da opção de snap-in do MMC do Backup do Azure para alterar propriedades](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na guia **Limitação**, selecione **Habilitar limitação de uso da largura de banda de Internet para operações de backup**. Defina os limites para horas de trabalho e folga. Os intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Captura de tela da caixa de diálogo Propriedades de Backup do Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Você também pode usar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para definir a limitação. Veja um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que nenhuma limitação é necessária.

### <a name="influence-network-bandwidth-for-a-vm"></a>Influenciar largura de banda da rede para uma VM

1. No Registro da VM, navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para influenciar o tráfego de largura de banda em um disco de replicação, modifique o valor de **UploadThreadsPerVM** ou crie a chave caso ela não exista.
   * Para influenciar a largura de banda para o tráfego de failback do Azure, modifique o valor de **DownloadThreadsPerVM**.
2. O valor padrão é 4. Em uma rede "sobreprovisionada", os valores padrão dessas chaves do registro precisam ser alterados. O máximo é 32. Monitore o tráfego para otimizar o valor.


## <a name="deploy-additional-process-servers"></a>Implantar servidores de processo adicionais

Se for necessário escalar horizontalmente sua implantação para além de 200 computadores de origem ou se você tiver uma taxa de rotatividade diária total acima de 2 TB, serão necessários servidores de processo adicionais para lidar com o volume do tráfego. Siga estas instruções para configurar o servidor de processo. Depois de configurar o servidor, você migrará os computadores de origem para usá-lo.

1. Nos **servidores do Site Recovery**, clique no servidor de configuração e em **Servidor de Processo**.

    ![Captura de tela da opção de servidores do Site Recovery para adicionar um servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. Em **Tipo de servidor**, clique em **Servidor de processo (local)**.

    ![Captura de tela da caixa de diálogo Servidor de Processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Baixe o arquivo de Instalação Unificada do Site Recovery e execute-o para instalar o servidor de processo. Isso também o registra no cofre.
4. Em **Antes de começar**, selecione **Adicionar servidores em processo adicionais para escalar horizontalmente a implantação**.
5. Conclua o assistente da mesma forma que fez ao [configurar](#step-2-set-up-the-source-environment) o servidor de configuração.

    ![Captura de tela do assistente de Instalação Unificada do Azure Site Recovery](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do servidor de configuração e a frase secreta. Para obter a frase secreta, execute: **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe –n** no servidor de configuração.

    ![Captura de tela da página Detalhes do Servidor de Configuração](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar computadores para usar o novo servidor de processo
1. Em **Configurações** > **Servidores do Site Recovery**, clique no servidor de configuração e expanda **Servidores em processo**.

    ![Captura de tela da caixa de diálogo Servidor de Processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Clique com o botão direito do mouse no servidor em processo atualmente em uso, e clique em **Mudar**.

    ![Captura de tela da caixa de diálogo Servidor de configuração](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Em **Selecionar servidor do processo de destino**, selecione o novo servidor de processo que quer usar e as máquinas virtuais que serão manipuladas pelo servidor. Clique no ícone de informações para obter informações sobre o servidor. Para ajudá-lo a tomar decisões de carregamento, o espaço médio necessário para replicar cada máquina virtual selecionada no novo servidor de processo será exibido. Clique na marca de seleção para começar a replicar no novo servidor de processo.


## <a name="next-steps"></a>Próximas etapas

Baixe e execute o [Planejador de Implantação do Azure Site Recovery](https://aka.ms/asr-deployment-planner)
