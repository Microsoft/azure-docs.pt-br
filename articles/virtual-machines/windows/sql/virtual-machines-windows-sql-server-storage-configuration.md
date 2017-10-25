---
title: "Configuração de armazenamento para VMs do SQL Server | Microsoft Docs"
description: "Este tópico descreve como o Azure configura o armazenamento para VMs do SQL Server durante o provisionamento (modelo de implantação do Resource Manager). Também explica como você pode configurar o armazenamento para suas VMs existentes do SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: ninarn
ms.openlocfilehash: f10bac1189c94a581487d19fc0cc129acec6a636
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configuração de armazenamento para VMs do SQL Server
Quando você configurar uma imagem de máquina virtual do SQL Server no Azure, o Portal ajuda você a automatizar a configuração de armazenamento. Isso inclui a anexação de armazenamento para a VM, tornando o armazenamento acessível para o SQL Server e configurando-o para otimizar seus requisitos específicos de desempenho.

Este tópico explica como o Azure configura o armazenamento para suas VMs do SQL Server durante o provisionamento e para VMs existentes. Essa configuração tem base nas [práticas recomendadas de desempenho](virtual-machines-windows-sql-performance.md) para VMs do Azure executando o SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para usar as definições automatizadas de configuração de armazenamento, sua máquina virtual exige as seguintes características:

* Deve ser provisionada com uma [imagem da galeria do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#option-1-create-a-sql-vm-with-per-minute-licensing).
* Deve usar o [Modelo de implantação do Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Deve usar o [Armazenamento Premium](../../../storage/common/storage-premium-storage.md).

## <a name="new-vms"></a>Novas VMs
As seções a seguir descrevem como configurar o armazenamento para novas máquinas virtuais do SQL Server.

### <a name="azure-portal"></a>Portal do Azure
Ao provisionar uma VM do Azure usando uma imagem da galeria do SQL Server, você pode optar por configurar automaticamente o armazenamento para sua nova VM. Você especificar o tamanho do armazenamento, limites de desempenho e o tipo de carga de trabalho. A captura de tela a seguir mostra a folha de Configuração de armazenamento usada durante o provisionamento da VM do SQL.

![Configuração de armazenamento da VM do SQL Server durante o provisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Com base em suas opções, o Azure realiza as seguintes tarefas de configuração de armazenamento depois de criar a VM:

* Cria e anexa os discos de dados de armazenamento premium à máquina virtual.
* Configura os discos de dados para serem acessíveis ao SQL Server.
* Configura os discos de dados em um pool de armazenamento com base nos requisitos de tamanho e desempenho (IOPS e taxa de transferência) especificados.
* Associa o pool de armazenamento a uma nova unidade na máquina virtual.
* Otimiza essa nova unidade com base em seu tipo de carga de trabalho especificado (Data warehouse, Processamento transacional ou Geral).

Para obter mais detalhes sobre como o Azure define as configurações de armazenamento, confira a [Seção de configuração de armazenamento](#storage-configuration). Para obter uma explicação completa de como criar uma VM do SQL Server no Portal do Azure, veja [o tutorial provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modelos do Resource Manager
Se você usar os modelos do Resource Manager a seguir, dois discos de dados premium são conectados por padrão, sem nenhuma configuração de pool de armazenamento. No entanto, você pode personalizar esses modelos para alterar o número de discos de dados premium anexados à máquina virtual.

* [Criar VM com backup automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Criar VM com aplicação de patch automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Criar VM com integração de AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>VMs existentes
Para VMs do SQL Server existente, você pode modificar algumas configurações de armazenamento no Portal do Azure. Selecione sua VM, vá até a área de Configurações e, em seguida, selecione Configuração do SQL Server. A folha Configuração do SQL Server mostra o uso do armazenamento atual da VM. Todas as unidades que existem na sua VM são exibidas neste gráfico. Para cada unidade, o espaço de armazenamento é exibido em quatro seções:

* Dados SQL
* Log do SQL
* Outros (armazenamento não SQL)
* Disponível

![Configurar o armazenamento para a VM do SQL Server existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Para configurar o armazenamento a fim de adicionar uma nova unidade ou estender uma unidade existente, clique no link Editar acima do gráfico.

As opções de configuração que você vê variam de acordo com o uso se desse recurso anteriormente. Ao usar pela primeira vez, você pode especificar os requisitos de armazenamento para uma nova unidade. Se você tiver usado esse recurso para criar uma unidade, opte por estender o armazenamento da unidade.

### <a name="use-for-the-first-time"></a>Uso pela primeira vez
Se esta for a primeira vez que você usa esse recurso, especifique os limites de tamanho e desempenho de armazenamento para uma nova unidade. Essa experiência é semelhante ao que você veria no tempo de provisionamento. A principal diferença é que você não tem permissão para especificar o tipo de carga de trabalho. Essa restrição impede a interrupção de quaisquer configurações existentes do SQL Server na máquina virtual.

![Configurar controles deslizantes de armazenamento do SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

O Azure cria uma nova unidade com base em suas especificações. Nesse cenário, o Azure realiza as seguintes tarefas de configuração de armazenamento:

* Cria e anexa os discos de dados de armazenamento premium à máquina virtual.
* Configura os discos de dados para serem acessíveis ao SQL Server.
* Configura os discos de dados em um pool de armazenamento com base nos requisitos de tamanho e desempenho (IOPS e taxa de transferência) especificados.
* Associa o pool de armazenamento a uma nova unidade na máquina virtual.

Para obter mais detalhes sobre como o Azure define as configurações de armazenamento, confira a [Seção de configuração de armazenamento](#storage-configuration).

### <a name="add-a-new-drive"></a>Adicionar uma nova unidade
Se você já tiver configurado o armazenamento na sua VM do SQL Server, a expansão do armazenamento apresenta duas opções novas. A primeira opção é adicionar uma nova unidade, que pode aumentar o nível de desempenho da VM.

![Adicionar uma nova unidade a uma VM do SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

No entanto, depois de adicionar a unidade, você deve executar alguma configuração manual extra para alcançar o aumento de desempenho.

### <a name="extend-the-drive"></a>Estender a unidade
A outra opção para expandir o armazenamento é estender a unidade existente. Essa opção aumenta o armazenamento disponível para sua unidade, mas ele não aumenta o desempenho. Com os pools de armazenamento, você não pode alterar o número de colunas após a criação do pool de armazenamento. O número de colunas determina o número de gravações paralelas, que podem ser distribuídas entre os discos de dados. Portanto, os discos de dados não podem aumentar o desempenho. Eles só podem fornecer mais armazenamento para os dados que estão sendo gravados. Essa limitação também significa que, ao estender a unidade, o número de colunas determina o número mínimo de discos de dados que você pode adicionar. Então se você criar um pool de armazenamento com quatro discos de dados, o número de colunas também será quatro. Sempre que você estender o armazenamento, deverá adicionar pelo menos quatro discos de dados.

![Estender uma unidade para uma VM do SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuração de armazenamento
Esta seção fornece uma referência para as alterações de configuração de armazenamento realizadas automaticamente pelo Azure durante a configuração ou provisionamento de VM do SQL no Portal do Azure.

* Se você tiver selecionado menos de dois TBs de armazenamento para sua VM, o Azure não criará um pool de armazenamento.
* Se você tiver selecionado pelo menos de dois TBs de armazenamento para sua VM, o Azure configurará um pool de armazenamento. A próxima seção deste tópico fornece os detalhes sobre a configuração do pool de armazenamento.
* A configuração de armazenamento automática sempre usa discos de dados P30 do [armazenamento premium](../../../storage/common/storage-premium-storage.md) . Consequentemente, há um mapeamento 1:1 entre o número selecionado de Terabytes e o número de discos de dados anexados à sua VM.

Para saber mais sobre preços, consulte a página [Preços de armazenamento](https://azure.microsoft.com/pricing/details/storage) na guia **Armazenamento em disco** .

### <a name="creation-of-the-storage-pool"></a>Criação do pool de armazenamento
O Azure usa as configurações a seguir para criar o pool de armazenamento em VMs do SQL Server.

| Configuração | Valor |
| --- | --- |
| Tamanho da distribuição |256 KB (Data warehouse); 64 KB (Transacional) |
| Tamanhos do disco |1 TB cada |
| Cache |Ler |
| Tamanho da alocação |Tamanho da unidade de alocação de NTFS de 64 KB |
| Inicialização de arquivo instantânea |Habilitado |
| Bloquear páginas na memória |Habilitado |
| Recuperação |Recuperação simples (sem resiliência) |
| Número de colunas |Número de discos de dados<sup>1</sup> |
| Local do TempDB |Armazenados em discos de dados<sup>2</sup> |

<sup>1</sup>Após a criação do pool de armazenamento, você não pode alterar o número de colunas no pool de armazenamento.

<sup>2</sup> Essa configuração se aplica somente a primeira unidade que você cria usando o recurso de configuração de armazenamento.

## <a name="workload-optimization-settings"></a>Configurações de otimização da carga de trabalho
A tabela a seguir descreve as três opções de tipo de carga de trabalho disponíveis e suas otimizações correspondentes:

| Tipo de carga de trabalho | Descrição | Otimizações |
| --- | --- | --- |
| **Geral** |Configuração padrão que oferece suporte à maioria das cargas de trabalho |Nenhum |
| **Processamento transacional** |Otimiza o armazenamento para cargas de trabalho OLTP tradicionais do banco de dados |Sinalizador de Rastreamento 1117<br/>Sinalizador de Rastreamento 1118 |
| **Data warehouse** |Otimiza o armazenamento para as cargas de trabalho de análise e emissão de relatórios |Sinalizador de Rastreamento 610<br/>Sinalizador de Rastreamento 1117 |

> [!NOTE]
> Você só pode especificar o tipo de carga de trabalho quando provisiona uma máquina virtual do SQL, selecionando-o na etapa de configuração de armazenamento.
>
>

## <a name="next-steps"></a>Próximas etapas
Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
