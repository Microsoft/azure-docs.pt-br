---
title: Perguntas frequentes sobre o SQL Server em Máquinas Virtuais Linux do Azure | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre a execução do SQL Server em VMs Linux do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fb1cafcf9405576749ea91aeea033c6ee783a026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739260"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Perguntas frequentes sobre o SQL Server nas Máquinas Virtuais Linux do Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Este artigo fornece respostas a algumas das perguntas mais comuns sobre a execução do [SQL Server em Máquinas Virtuais Linux do Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Este artigo se concentra em problemas específicos ao SQL Server em VMs Linux. Se você estiver executando o SQL Server em VMs do Windows, consulte as [Perguntas frequentes sobre o Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Imagens

1. **Quais imagens da galeria de máquinas virtuais do SQL Server estão disponíveis?**

   O Azure mantém imagens de máquina virtual para todas as versões principais compatíveis do SQL Server em todas as edições para o Linux e para o Windows. Para obter mais detalhes, consulte a lista completa de [imagens de VM Linux](sql-server-linux-virtual-machines-overview.md#create) e [imagens de VM Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **As imagens da galeria de máquinas virtuais do SQL Server existentes são atualizadas?**

   A cada dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as atualizações mais recentes do Linux e do Windows. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. Para imagens do Windows, isso inclui quaisquer atualizações marcadas como importantes no Windows Update, incluindo service packs e atualizações de segurança do SQL Server. As atualizações cumulativas do SQL Server são tratadas de maneira diferente para o Linux e para o Windows. Para o Linux, as atualizações cumulativas do SQL Server também são incluídas na atualização. Mas, neste momento, as VMs Windows não são atualizadas com as atualizações cumulativas do SQL Server ou do Windows Server.

1. **Que pacotes do SQL Server relacionados também são instalados?**

   Para ver os pacotes do SQL Server que são instalados por padrão em VMs Linux do SQL Server, consulte [Pacotes instalados](sql-server-linux-virtual-machines-overview.md#packages).

1. **As imagens de máquina virtual do SQL Server podem ser removidas da galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e edição. Por exemplo, quando um novo service pack do SQL Server é lançado, o Azure adiciona uma nova imagem à galeria para esse service pack. A imagem do SQL Server para o service pack anterior é removida imediatamente do Portal do Azure. No entanto, ela ainda estará disponível para provisionamento do PowerShell pelos próximos três meses. Depois de três meses, a imagem do service pack anterior não estará mais disponível. Essa política de remoção também se aplica quando uma versão do SQL Server se torna incompatível quando ela atinge o final de seu ciclo de vida.

## <a name="creation"></a>Criação

1. **Como criar uma máquina virtual Linux do Azure com o SQL Server?**

   A solução mais fácil é criar uma máquina virtual Linux que inclua o SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM do SQL por meio do portal, confira [Provisionar uma máquina virtual Linux do SQL Server no Portal do Azure](provision-sql-server-linux-virtual-machine.md). Você também tem a opção de instalação manual do SQL Server em uma VM com um uma edição licenciada gratuitamente (Desenvolvedor ou Express) ou pela reutilização de uma licença local. Se você trouxer sua própria licença, será necessário ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Por que não é possível, provisionar uma VM do SQL Server do SLES ou RHEL com uma assinatura do Azure que tem um limite de gastos?**

   Máquinas virtuais RHEL e SLES exigem uma assinatura sem limite de gastos e um método de pagamento verificado (geralmente um cartão de crédito) associado à assinatura. Se você provisionar uma VM SLES ou RHEL sem remover o limite de gastos, sua assinatura será desabilitada e todas as VMs/serviços serão interrompidos. Se você entrar nesse estado, [remova o limite de gastos](https://account.windowsazure.com/subscriptions) para habilitar novamente a assinatura. Seu crédito restante será restaurado para o ciclo de cobrança atual, mas uma sobretaxa da imagem da VM RHEL ou SLES será aplicada em seu cartão de crédito se você optar por reiniciá-la e continuar a executá-la.

## <a name="licensing"></a>Licenciamento

1. **Como instalar minha cópia licenciada do SQL Server em uma VM do Azure?**

   Primeiro, crie uma máquina virtual exclusivamente do SO Linux. Em seguida, execute as [etapas de instalação do SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) para sua distribuição do Linux. A menos que você esteja instalando uma das edições licenciadas gratuitamente do SQL Server, você também deve ter uma licença do SQL Server e [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Há imagens da máquina virtual Linux BYOL (Traga sua Própria Licença) para o SQL Server?**

   Neste momento, não há nenhuma imagem de máquina virtual BYOL Linux para o SQL Server. No entanto, você pode instalar manualmente do SQL Server em uma VM somente Linux conforme discutido nas perguntas anteriores.

1. **Posso alterar uma VM para usar minha própria licença do SQL Server se ela foi criada com base em uma das imagens pré-pagas da galeria?**

   Não. Não é possível mudar do licenciamento pago por segundo para o uso de sua própria licença. Você deve criar uma nova VM Linux, instalar o SQL Server e migrar os dados. Consulte a pergunta anterior para obter mais detalhes sobre como trazer sua própria licença.

## <a name="administration"></a>Administração

1. **Posso gerenciar uma máquina virtual Linux com SQL Server com o SSMS (SQL Server Management Studio)?**

   Sim, mas o SSMS atualmente é uma ferramenta somente do Windows. Você deve se conectar remotamente de um computador Windows para usar o SSMS com VMs do SQL Server do Linux. Localmente no Linux, a nova ferramenta [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) pode executar várias tarefas administrativas. Para obter uma ferramenta de gerenciamento de banco de dados multiplataforma, confira [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Posso remover o SQL Server completamente de uma VM de SQL?**

   Sim, mas você continuará a ser cobrado pela VM do SQL conforme descrito em [Diretrizes de preços para VMs do Azure do SQL Server](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Se você não precisar mais do SQL Server, você poderá implantar uma nova máquina virtual e migrar os dados e aplicativos para a nova máquina virtual. Em seguida, você pode remover a máquina virtual do SQL Server.

## <a name="updating-and-patching"></a>Atualização e aplicação de patch

1. **Como atualizar para uma nova versão/edição do SQL Server em uma VM do Azure?**

   Atualmente, não existe uma atualização in-loco para o SQL Server em execução em uma VM do Azure. Crie uma nova máquina virtual do Azure com a versão/edição desejada do SQL Server e migre os bancos de dados para o novo servidor usando [técnicas de migração de dados](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview) padrão.

## <a name="general"></a>Geral

1. **Há suporte para soluções de alta disponibilidade do SQL Server em VMs do Azure?**

   Não no momento. Tanto Grupos de disponibilidade AlwaysOn quanto Clustering de Failover exigem uma solução de clustering em Linux, tal como o Pacemaker. As distribuições do Linux com suporte para o SQL Server não dão suporte aos respectivos complementos de alta disponibilidade na nuvem.

## <a name="resources"></a>Recursos

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](sql-server-linux-virtual-machines-overview.md)
* [Provisionar uma VM Linux do SQL Server](provision-sql-server-linux-virtual-machine.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Provisionar uma VM do Windows do SQL Server](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Perguntas Frequentes (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)