---
title: Configurar o armazenamento para VMs SQL Server | Microsoft Docs
description: Este tópico descreve como o Azure configura o armazenamento para SQL Server VMs durante o provisionamento (modelo de implantação de Azure Resource Manager). Também explica como você pode configurar o armazenamento para suas VMs existentes do SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565394"
---
# <a name="configure-storage-for-sql-server-vms"></a>Configurar o armazenamento para VMs SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo ensina como configurar seu armazenamento para seu SQL Server em VMs (máquinas virtuais) do Azure.

SQL Server VMs implantadas por meio de imagens do Marketplace seguem automaticamente [as práticas recomendadas de armazenamento](performance-guidelines-best-practices-storage.md) padrão que podem ser modificadas durante a implantação. Algumas dessas definições de configuração podem ser alteradas após a implantação. 


## <a name="prerequisites"></a>Pré-requisitos

Para usar as definições automatizadas de configuração de armazenamento, sua máquina virtual exige as seguintes características:

* Provisionado com uma imagem da [Galeria de SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) ou registrado com a [extensão IaaS do SQL]().
* Deve usar o [Modelo de implantação do Resource Manager](../../../azure-resource-manager/management/deployment-models.md).
* Deve usar os [SSDs premium](../../../virtual-machines/disks-types.md).

## <a name="new-vms"></a>Novas VMs

As seções a seguir descrevem como configurar o armazenamento para novas máquinas virtuais do SQL Server.

### <a name="azure-portal"></a>Portal do Azure

Ao provisionar uma VM do Azure usando uma imagem de SQL Server da galeria, selecione **Alterar configuração** na guia **Configurações de SQL Server** para abrir a página Configuração de Armazenamento Otimizado para Desempenho. Você pode deixar os valores no padrão ou modificar o tipo de configuração de disco que melhor atenda às suas necessidades com base na carga de trabalho. 

![Captura de tela que realça a guia Configurações de SQL Server e a opção de configuração de alteração.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecione o tipo de carga de trabalho para o qual você está implantando o SQL Server em **Otimização de armazenamento**. Com a opção de otimização **Geral**, por padrão você terá um disco de dados com IOPS máximo de 5000 e usará essa mesma unidade para os dados, o log de transações e o armazenamento TempDB. 

Ao selecionar **Processamento transacional** (OLTP) ou **Data warehousing**, será criado um disco separado para os dados, um disco separado para o log de transações e usará o SSD local para o TempDB. Não há diferença de armazenamento entre **Processamento transacional** e **Data warehousing**, mas isso altera a [configuração da distribuição e os sinalizadores de rastreamento](#workload-optimization-settings). Escolher armazenamento Premium define o cache como *ReadOnly* para a unidade de dados e *Nenhum* para a unidade de log de acordo com as [melhores práticas de desempenho de VM do SQL Server](performance-guidelines-best-practices.md). 

![Configuração de armazenamento da VM do SQL Server durante o provisionamento](./media/storage-configuration/sql-vm-storage-configuration.png)

A configuração de disco é totalmente personalizável para que você possa configurar a topologia do armazenamento, o tipo de disco e o IOPS necessário para sua carga de trabalho da VM do SQL Server. Você também tem a possibilidade de usar o UltraSSD (versão prévia) como uma opção para o **Tipo de disco** se a VM do SQL Server estiver em uma das regiões com suporte (Leste dos EUA 2, Sudeste Asiático e Norte da Europa) e se você tiver habilitado [discos ultra para sua assinatura](../../../virtual-machines/disks-enable-ultra-ssd.md).  

Além disso, você tem a capacidade de definir o cache para os discos. As VMs do Azure têm uma tecnologia de cache de várias camadas chamada [Cache BLOB](../../../virtual-machines/premium-storage-performance.md#disk-caching) quando usada com [Discos Premium](../../../virtual-machines/disks-types.md#premium-ssd). O Cache Blob usa uma combinação de RAM da Máquina Virtual e SSD local para cache. 

O cache de disco para SSD Premium pode ser *ReadOnly*, *ReadWrite* ou *nenhum*. 

- O cache *ReadOnly* é altamente benéfico para arquivos de dados do SQL Server armazenados no Armazenamento Premium. O cache *ReadOnly* traz baixa latência de leitura, IOPS de leitura e taxa de transferência altas, pois as leituras são executadas no cache, que está dentro da memória da VM e do SSD local. Essas leituras são muito mais rápidas do que as leituras do disco de dados, que é do armazenamento de BLOBs do Azure. O armazenamento Premium não leva as leituras atendidas no cache em conta para definir o IOPS e a taxa de transferência do disco. Portanto, o aplicativo é capaz de atingir uma Taxa de Transferência e IOPS total mais altos. 
- *Nenhuma* configuração de cache deve ser usada para os discos que hospedam o arquivo de log do SQL Server, pois o arquivo de log é escrito em sequência e não se beneficia com o cache *ReadOnly*. 
- O cache *ReadWrite* não deve ser usado para hospedar arquivos do SQL Server pois o SQL Server não é compatível com a consistência de dados com o cache *ReadWrite*. A capacidade de desperdício de gravação dos cache de blobs *ReadOnly* e as latências serão ligeiramente aumentadas se as gravações passarem por camadas de cache de blobs *ReadOnly*. 


   > [!TIP]
   > A sua configuração de armazenamento deve corresponder às limitações impostas pelo tamanho da VM selecionada. Escolher os parâmetros de armazenamento que excedem o limite de desempenho do tamanho da VM resultará em aviso: `The desired performance might not be reached due to the maximum virtual machine disk performance cap` . Diminua o IOPS alterando o tipo de disco ou aumente o limite de desempenho, aumentando o tamanho da VM. Isso não irá parar o provisionamento. 


Com base em suas opções, o Azure realiza as seguintes tarefas de configuração de armazenamento depois de criar a VM:

* Cria e anexa o SSDs Premium à máquina virtual.
* Configura os discos de dados para serem acessíveis ao SQL Server.
* Configura os discos de dados em um pool de armazenamento com base nos requisitos de tamanho e desempenho (IOPS e taxa de transferência) especificados.
* Associa o pool de armazenamento a uma nova unidade na máquina virtual.
* Otimiza essa nova unidade com base em seu tipo de carga de trabalho especificado (Data warehouse, Processamento transacional ou Geral).

Para obter uma explicação completa de como criar uma VM do SQL Server no portal do Azure, veja [o tutorial de provisionamento](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

Se você usar os modelos do Resource Manager a seguir, dois discos de dados premium são conectados por padrão, sem nenhuma configuração de pool de armazenamento. No entanto, você pode personalizar esses modelos para alterar o número de discos de dados premium anexados à máquina virtual.

* [Criar VM com backup automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Criar VM com aplicação de patch automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Criar VM com integração de AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Modelo de Início Rápido

Você pode usar o modelo de início rápido a seguir para implantar uma VM do SQL Server usando a otimização de armazenamento. 

* [Criar VM com otimização de armazenamento](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Criar VM usando UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para VMs do SQL Server existente, você pode modificar algumas configurações de armazenamento no Portal do Azure. Abra o [recurso de máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e selecione **Visão geral**. A página Visão geral do SQL Server mostra o uso atual de armazenamento de sua VM. Todas as unidades que existem na sua VM são exibidas neste gráfico. Para cada unidade, o espaço de armazenamento é exibido em quatro seções:

* Dados SQL
* Log do SQL
* Outros (armazenamento não SQL)
* Disponível

Para modificar as configurações de armazenamento, selecione **Configurar** em **Configurações**. 

![Captura de tela que realça a opção configurar e a seção uso de armazenamento.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Você pode modificar as configurações de disco das unidades que foram configuradas durante o processo de criação de VM do SQL Server. A seleção de **Estender unidade** abre a página de modificação da unidade, permitindo que você altere o tipo de disco e adicione discos. 

![Configurar o armazenamento para a VM do SQL Server existente](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Alterações automatizadas

Esta seção fornece uma referência para as alterações de configuração de armazenamento que o Azure executa automaticamente durante SQL Server provisionamento ou configuração de VM no portal do Azure.

* O Azure configura um pool de armazenamento com o armazenamento selecionado em sua VM. A próxima seção deste tópico fornece detalhes sobre a configuração do pool de armazenamento.
* A configuração de armazenamento automática sempre usa discos de dados P30 dos [SSDs premium](../../../virtual-machines/disks-types.md). Consequentemente, há um mapeamento 1:1 entre o número selecionado de Terabytes e o número de discos de dados anexados à sua VM.

Para saber mais sobre preços, consulte a página [Preços de armazenamento](https://azure.microsoft.com/pricing/details/storage) na guia **Armazenamento em disco** .

### <a name="creation-of-the-storage-pool"></a>Criação do pool de armazenamento

O Azure usa as configurações a seguir para criar o pool de armazenamento em VMs do SQL Server.

| Configuração | Valor |
| --- | --- |
| Tamanho da distribuição |256 KB (Data warehouse); 64 KB (Transacional) |
| Tamanhos do disco |1 TB cada |
| Cache |Ler |
| Tamanho da alocação |Tamanho da unidade de alocação de NTFS de 64 KB |
| Recuperação | Recuperação simples (sem resiliência) |
| Número de Colunas |Número de discos de dados até 8<sup>1</sup> |


<sup>1</sup>Após a criação do pool de armazenamento, você não pode alterar o número de colunas no pool de armazenamento.


### <a name="workload-optimization-settings"></a>Configurações de otimização da carga de trabalho

A tabela a seguir descreve as três opções de tipo de carga de trabalho disponíveis e suas otimizações correspondentes:

| Tipo de carga de trabalho | Descrição | Otimizações |
| --- | --- | --- |
| **Geral** |Configuração padrão que oferece suporte à maioria das cargas de trabalho |Nenhum |
| **Processamento transacional** |Otimiza o armazenamento para cargas de trabalho OLTP tradicionais do banco de dados |Sinalizador de Rastreamento 1117<br/>Sinalizador de Rastreamento 1118 |
| **Data warehouse** |Otimiza o armazenamento para as cargas de trabalho de análise e emissão de relatórios |Sinalizador de Rastreamento 610<br/>Sinalizador de Rastreamento 1117 |

> [!NOTE]
> Você só pode especificar o tipo de carga de trabalho ao provisionar um SQL Server máquina virtual selecionando-o na etapa de configuração de armazenamento.

## <a name="enable-caching"></a>Habilitar o cache 

Altere a política de cache no nível do disco. Você pode fazer isso usando o portal do Azure, o [PowerShell](/powershell/module/az.compute/set-azvmdatadisk)ou o [CLI do Azure](/cli/azure/vm/disk). 

Para alterar sua política de cache no portal do Azure, siga estas etapas:

1. Pare seu serviço de SQL Server. 
1. Faça logon no [Portal do Azure](https://portal.azure.com). 
1. Navegue até sua máquina virtual, selecione **discos** em **configurações**. 
   
   ![Captura de tela mostrando a folha configuração de disco da VM no portal do Azure.](./media/storage-configuration/disk-in-portal.png)

1. Escolha a política de cache apropriada para seu disco na lista suspensa. 

   ![Captura de tela mostrando a configuração de política de cache de disco no portal do Azure.](./media/storage-configuration/azure-disk-config.png)

1. Depois que a alteração entrar em vigor, reinicialize a VM SQL Server e inicie o serviço SQL Server. 


## <a name="enable-write-accelerator"></a>Habilitar acelerador de gravação

A aceleração de gravação é um recurso de disco disponível somente para VMs (máquinas virtuais) da série M. A finalidade da aceleração de gravação é melhorar a latência de e/s de gravações no armazenamento Premium do Azure quando você precisa de latência de e/s de dígito único devido a cargas de trabalho de alto custo crítico de missão crítica de volume ou ambientes de data warehouse. 

Interrompa todas as SQL Server atividade e desligue o serviço SQL Server antes de fazer alterações na política de aceleração de gravação. 

Se os discos forem distribuídos, habilite a aceleração de gravação para cada disco individualmente e sua VM do Azure deverá ser desligada antes de fazer qualquer alteração. 

Para habilitar a aceleração de gravação usando o portal do Azure, siga estas etapas:

1. Pare seu serviço de SQL Server. Se os discos forem distribuídos, desligue a máquina virtual. 
1. Faça logon no [Portal do Azure](https://portal.azure.com). 
1. Navegue até sua máquina virtual, selecione **discos** em **configurações**. 
   
   ![Captura de tela mostrando a folha configuração de disco da VM no portal do Azure.](./media/storage-configuration/disk-in-portal.png)

1. Escolha a opção de cache com **acelerador de gravação** para o disco na lista suspensa. 

   ![Captura de tela mostrando a política de cache do acelerador de gravação.](./media/storage-configuration/write-accelerator.png)

1. Depois que a alteração entrar em vigor, inicie a máquina virtual e o serviço de SQL Server. 

## <a name="disk-striping"></a>Distribuição de discos

Para obter mais taxa de transferência, você pode adicionar mais discos de dados e usar a distribuição de disco. Para determinar o número de discos de dados, analise a taxa de transferência e a largura de banda necessários para seus SQL Server arquivos de dados, incluindo o log e o tempdb. Taxa de transferência e limites de largura de banda variam por tamanho de VM. Para saber mais, consulte [tamanho da VM](../../../virtual-machines/sizes.md)


* Para Windows 8/Windows Server 2012 ou posterior, use os [Espaços de Armazenamento](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) com as seguintes diretrizes:

  1. Defina a intercalação (tamanho da faixa) como 64 KB (65.536 bytes) para evitar o impacto no desempenho devido ao desalinhamento da partição. Isso deve ser definido com o PowerShell.

  2. Defina a contagem de colunas = número de discos físicos. Use o PowerShell ao configurar mais de 8 discos (não interface do usuário do Gerenciador do Servidor).

Por exemplo, o PowerShell a seguir cria um novo pool de armazenamento com o tamanho de intercalação para 64 KB e o número de colunas igual à quantidade de disco físico no pool de armazenamento:

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * Para o Windows 2008 R2 ou anterior, é possível usar discos dinâmicos (volumes de SO distribuídos) e o tamanho da faixa será sempre 64 KB. Essa opção tornou-se preterida no Windows 8/Windows Server 2012. Para obter informações, veja a declaração de suporte em [O Serviço de Disco Virtual está em transição para a API de Gerenciamento de Armazenamento do Windows](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).
 
  * Se você estiver usando [Espaços de Armazenamento Diretos (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com [Instâncias de Cluster de Failover do SQL Server](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure), será necessário configurar um único pool. Embora diferentes volumes possam ser criados nesse único pool, todos eles compartilharão as mesmas características, como a mesma política de cache.
 
  * Determine o número de discos associados ao seu pool de armazenamento com base nas suas expectativas de carga. Tenha em mente que tamanhos de VM diferentes permitem quantidades diferentes de discos de dados anexados. Para obter mais informações, consulte [tamanhos das máquinas virtuais](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json).


## <a name="next-steps"></a>Próximas etapas

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
