---
title: Planejar a capacidade de recuperação de desastres do VMware com o Azure Site Recovery
description: Este artigo pode ajudar você planejar a capacidade e o dimensionamento ao configurar sua recuperação de desastre de VMs do VMware para o Azure usando o Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 4b86d0c189bcf0687a703f2338188df2090feaf0
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368019"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planejar a capacidade e o dimensionamento da recuperação de desastre do VMware para o Azure

Use este artigo para planejar a capacidade e o dimensionamento ao replicar VMs do VMware locais e servidores físicos para o Azure usando o [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Como iniciar o planejamento de capacidade?

Para aprender sobre os requisitos de infraestrutura do Planejador de Implantações do Azure Site Recovery, colete as informações sobre seu ambiente de replicação executando o [Planejador de implantação do Azure Site Recovery](./site-recovery-deployment-planner.md) para replicação do VMware. Para obter mais informações, veja [Sobre o Planejador de Implantação do Site Recovery para VMware no Azure](site-recovery-deployment-planner.md). 

O Planejador de Implantação do site Recovery fornece um relatório com informações completas sobre VMs compatíveis e incompatíveis, discos por VM e rotatividade de dados por disco. A ferramenta também inclui requisitos de largura de banda da rede para atingir o RPO desejado, bem como a infraestrutura do Azure necessária para replicação e failover de teste bem-sucedidos.

## <a name="capacity-considerations"></a>Considerações sobre a capacidade

Componente | Detalhes
--- | ---
**Replicação** | **Taxa de alteração diária máxima**: um computador protegido pode usar apenas um servidor de processo. Um único servidor de processo pode lidar com uma taxa de alteração diária de até 2 TB. Assim, 2 TB é a taxa de alteração diária máxima com suporte para um computador protegido.<br /><br /> **Taxa de transferência máxima**: Uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de Armazenamento do Azure padrão pode lidar com um máximo de 20 mil solicitações por segundo. É recomendável limitar o número de IOPS (operações de entrada/saída por segundo) em um computador de origem a 20 mil. Por exemplo, se você tiver um computador de origem com cinco discos e cada disco gere 120 IOPS (8 K de tamanho) no computador de origem, o computador de origem estará dentro do limite de 500 IOPS por disco do Azure. (O número de contas de armazenamento necessário é igual ao total de IOPS do computador de origem, dividido por 20 mil.)
**Servidor de configuração** | O servidor de configuração deve ser capaz de lidar com a capacidade de taxa de alteração diária em todas as cargas de trabalho em execução em computadores protegidos. O computador de configuração deve ter largura de banda suficiente para replicar continuamente os dados no Armazenamento do Azure.<br /><br /> Uma melhor prática é alocar o servidor de configuração na mesma rede e no mesmo segmento de LAN que os computadores que deseja proteger. Você pode colocar o servidor de configuração em uma rede diferente, mas os computadores que você deseja proteger devem ter visibilidade de rede de camada 3.<br /><br /> As recomendações de tamanho para o servidor de configuração são resumidas na tabela da seção a seguir.
**Servidor de processo** | O primeiro servidor de processo é instalado por padrão no servidor de configuração. Você pode implantar servidores de processo adicionais par dimensionar seu ambiente. <br /><br /> O servidor de processo recebe dados de replicação de computadores protegidos. O servidor de processo otimiza os dados por meio de caching, compactação e criptografia. Em seguida, o servidor de processo envia os dados para o Azure. O computador do servidor de processo deve ter recursos suficientes para executar essas tarefas.<br /><br /> O servidor de processo usa o cache baseado em disco. Use um disco de cache separado de 600 GB ou mais para lidar com alterações de dados que são armazenadas caso ocorra um gargalo ou interrupção da rede.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Recomendações de tamanho para o servidor de rede de configuração e servidor de processo integrado

Um servidor de configuração que usa um servidor de processo integrado é usado para proteger a carga de trabalho pode lidar com até 200 máquinas virtuais com base nas seguintes configurações:

CPU | Memória | Tamanho do disco de cache | Taxa de alteração de dados | Computadores protegidos
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Use para replicar menos de 100 computadores.
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | Use para replicar de 100 a 150 computadores.
16 vCPUs (2 soquetes * 8 núcleos \@ 2,5 GHz) | 32 GB | 1 TB | >1 TB a 2 TB | Use para replicar de 151 a 200 computadores.
Implante outro servidor de configuração usando um [modelo de OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Implante um novo servidor de configuração se estiver replicando mais de 200 computadores.
Implante outro [servidor de processo](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Implante um novo servidor de processo de expansão se a taxa de alteração diária geral é maior que 2 TB.

Nestas configurações:

* Cada computador de origem tem três discos de 100 GB cada.
* Usamos armazenamento de benchmark de oito unidades de assinatura de acesso compartilhado de 10 mil RPM com RAID 10 para medidas do disco de cache.

## <a name="size-recommendations-for-the-process-server"></a>Recomendações de tamanho para o servidor de processo

O servidor de processo é o componente que trata da replicação de dados no Azure Site Recovery. Se a taxa de alteração diária for maior que 2 TB, você deverá adicionar servidores de processo de expansão para tratar da carga de replicação. Para escalar horizontalmente, você pode:

* Aumente o número de servidores de configuração implantando com um [modelo de OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Por exemplo, você pode proteger até 400 computadores usando dois servidores de configuração.
* Adicione [servidores de processo de expansão](vmware-azure-set-up-process-server-scale.md#download-installation-file). Use os servidores de processo em expansão para tratar do tráfego de replicação, em vez do (ou além do) servidor de configuração.

A tabela a seguir descreve este cenário:

* Você configurou um servidor de processo de expansão.
* Você configurou máquinas virtuais protegidas para usar o servidor em processo de expansão.
* Cada computador de origem protegido tem três discos de 100 GB cada.

Servidor de processo adicional | Tamanho do disco de cache | Taxa de alteração de dados | Computadores protegidos
--- | --- | --- | ---
4 vCPUs (2 soquetes * 2 núcleos \@ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Use para replicar 85 computadores ou menos.
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz), 12 GB de memória | 600 GB | 251 GB a 1 TB | Use para replicar de 86 a 150 computadores.
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz), 24 GB de memória | 1 TB | >1 TB a 2 TB | Use para replicar de 151 a 225 computadores.

A maneira como você escala seus servidores depende de sua preferência por um modelo que escale vertical ou horizontalmente. Para aumentar, implante alguns servidores de processo e servidores de configuração de alto nível. Para aumentar, implante mais servidores que tenham menos recursos. Por exemplo, se você quiser proteger 200 computadores com uma taxa de alteração de dados diária geral de 1,5 TB, poderá executar uma das seguintes ações:

* Configurar um único servidor de processo (16 vCPU, 24 GB de RAM).
* Configurar dois servidores de processo (2 x 8 vCPU, 2 * 12 GB de RAM).

## <a name="control-network-bandwidth"></a>Controlar largura de banda da rede

Depois de usar o [Planejador de Implantação do Site Recovery](site-recovery-deployment-planner.md) para calcular a largura de banda necessária para a replicação (replicação inicial e então delta), há duas opções para controlar a quantidade de largura de banda usada para replicação:

* **Restringir a largura de banda**: o tráfego VMware que replica para o Azure passa por um servidor de processo específico. Você pode limitar a largura de banda nos computadores em execução como servidores de processo.
* **Influenciar a largura de banda**: você pode influenciar a largura de banda usada para replicação usando algumas chaves do registro:
  * O valor de registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** especifica o número de threads usados para a transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação.
  * O valor de Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** especifica o número de threads usados para transferência de dados durante o failback.

### <a name="throttle-bandwidth"></a>Limitação da largura de banda

1. Abra o snap-in do MMC do Backup do Azure no computador que você usa como o servidor de processo. Por padrão, um atalho para o backup está disponível na área de trabalho ou na seguinte pasta: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. No snap-in, selecione **Alterar as Propriedades**.

    ![Captura de tela da opção de snap-in do MMC do Backup do Azure para alterar propriedades](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na guia **limitação** , selecione **habilitar limitação de uso de largura de banda da Internet para operações de backup**. Defina os limites para horas de trabalho e folga. Os intervalos válidos são de 512 Kbps a 1.023 Mbps.

    ![Captura de tela da caixa de diálogo Propriedades de Backup do Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Você também pode usar o cmdlet [Set-OBMachineSetting](/previous-versions/windows/powershell-scripting/hh770409(v=wps.640)) para definir a limitação. Veja um exemplo:

```azurepowershell-interactive
$mon = [System.DayOfWeek]::Monday
$tue = [System.DayOfWeek]::Tuesday
Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)
```

**Set-OBMachineSetting -NoThrottle** indica que nenhuma limitação é necessária.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Alterar largura de banda da rede para uma VM

1. No Registro da VM, vá para **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para alterar o tráfego de largura de banda em um disco de replicação, modifique o valor de **UploadThreadsPerVM**. Crie a chave, caso ela não exista.
   * Para alterar a largura de banda para o tráfego de failback do Azure, modifique o valor de **DownloadThreadsPerVM**.
2. O valor padrão para cada chave é **4**. Em uma rede "sobreprovisionada", os valores padrão dessas chaves do registro precisam ser alterados. É o valor máximo que você pode usar é **32**. Monitore o tráfego para otimizar o valor.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Configurar a infraestrutura do Site Recovery para proteger mais de 500 VMs

Antes de configurar a infraestrutura do Site Recovery, acesse o ambiente para medir os seguintes fatores: máquinas virtuais compatíveis, taxa diária de alteração de dados, largura de banda de rede necessária para o RPO que você quer atingir, número de componentes do Site Recovery necessários e o tempo necessário para concluir a replicação inicial. Conclua as seguintes etapas para coletar as informações necessárias:

1. Para medir esses parâmetros, execute o Planejador de Implantação da do Site Recovery no seu ambiente. Para obter diretrizes úteis, veja [Sobre o Planejador de Implantação do Site Recovery para VMware no Azure](site-recovery-deployment-planner.md).
2. Implante um servidor de configuração que cumpra as [recomendações de tamanho para o servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Se sua carga de trabalho de produção exceder 650 máquinas de virtuais, implante outro servidor de configuração.
3. Com base na taxa de alteração de dados diária medida, implante [servidores de processo de expansão](vmware-azure-set-up-process-server-scale.md#download-installation-file) com a ajuda das [diretrizes de tamanho](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Se você esperar que a taxa de alteração de dados de uma máquina virtual de disco exceda 2 MBps, certifique-se de usar discos gerenciados Premium. O Planejador de Implantação do site Recovery é executado por um período específico. Picos na taxa de alteração de dados em outros momentos podem não ser capturados no relatório.
5. [Defina a largura de banda de rede](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) com base no RPO que você deseja atingir.
6. Quando a infraestrutura estiver configurada, habilite a recuperação de desastre para sua carga de trabalho. Para saber como, veja [Configurar o ambiente de origem para replicação de VMware no Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Implantar servidores de processo adicionais

Se você aumentar sua implantação além de 200 computadores de origem ou tiver uma taxa diária de rotatividade total acima de 2 TB, deverá adicionar servidores de processo para tratar do volume de tráfego. Aprimoramos o produto na versão 9,24 para fornecer [alertas do servidor de processo](vmware-physical-azure-monitor-process-server.md#process-server-alerts) sobre quando configurar um servidor de processo de expansão. [Configure o servidor de processo](vmware-azure-set-up-process-server-scale.md) para proteger novos computadores de origem ou [Equilibre a carga](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar computadores para usar o novo servidor de processo

1. Selecione **configurações**  >  **site Recovery servidores**. Selecione o servidor de configuração e, em seguida, expanda **Servidores de processo**.

    ![Captura de tela da caixa de diálogo Servidor de Processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Clique com o botão direito do mouse no servidor em processo atualmente em uso e selecione **Alternar**.

    ![Captura de tela da caixa de diálogo Servidor de configuração](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Em **Selecionar servidor de processo de destino**, selecione o novo servidor de processo que você deseja usar. Em seguida, selecione as máquinas virtuais de que o servidor tratará. Para obter informações sobre o servidor, selecione o ícone de informações. Para ajudá-lo a tomar decisões de carregamento, o espaço médio necessário para replicar cada máquina virtual selecionada no novo servidor de processo é mostrado. Clique na marca de seleção para começar a replicar no novo servidor de processo.

## <a name="deploy-additional-master-target-servers"></a>Implantar servidores de destino mestre adicionais

Nos cenários a seguir, é necessário mais de um servidor de destino mestre:

*   Você deseja proteger uma máquina virtual baseada em Linux.
*   O servidor de destino mestre disponível no servidor de configuração não tem acesso ao armazenamento de dados da VM.
*   O número total de discos no servidor de destino mestre (o número de discos locais no servidor mais o número de discos a serem protegidos) é maior que 60 discos.

Para saber como adicionar um servidor de destino mestre a uma máquina virtual baseada em Linux, veja [Instalar um servidor de destino mestre do Linux para failback](vmware-azure-install-linux-master-target.md).

Para adicionar um servidor de destino mestre para uma máquina virtual do Windows:

1. Vá para **cofre dos serviços de recuperação**  >  **site Recovery**  >  **servidores de configuração**de infraestrutura.
2. Selecione o servidor de configuração necessário e, em seguida, selecione **Servidor de Destino Mestre**.

    ![Captura de tela que mostra o botão Adicionar Servidor de Destino Mestre](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Baixe o arquivo de configuração unificado e, em seguida, execute o arquivo na VM para configurar o servidor de destino mestre.
4. Selecione **instalar destino mestre**  >  **Avançar**.

    ![Captura de tela que mostra como selecionar a opção Instalar destino mestre](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Selecione o local de instalação padrão e, em seguida, **Instalar**.

     ![Captura de tela que mostra o local de instalação padrão](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Para registrar o destino mestre com o servidor de configuração, selecione **Prosseguir para a configuração**.

    ![Captura de tela que mostra o botão Prosseguir para configuração](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Insira o endereço IP do servidor de configuração e, em seguida, insira a frase secreta. Para saber como gerar uma frase secreta, veja [Gerar uma frase secreta do servidor de configuração](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Captura de tela que mostra em que local inserir o endereço IP e a frase secreta para o servidor de configuração](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Selecione **Registrar**. Ao final do registro, selecione **Concluir**.

Quando o registro for concluído com êxito, o servidor será listado no portal do Azure no **cofre dos serviços de recuperação**  >  **site Recovery**  >  **servidores de configuração**de infraestrutura, nos servidores de destino mestre do servidor de configuração.

 > [!NOTE]
 > Baixe a versão mais recente do [arquivo de configuração unificado do servidor de destino mestre para Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Próximas etapas

Baixe e execute o [Planejador de Implantação do Site Recovery](https://aka.ms/asr-deployment-planner).