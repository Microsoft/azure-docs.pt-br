---
title: Perguntas frequentes sobre SQL Server em Linux máquinas virtuais | Microsoft Docs
description: Este artigo fornece respostas para perguntas frequentes sobre como executar SQL Server em Linux máquinas virtuais.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792541"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Perguntas frequentes para SQL Server em Linux máquinas virtuais
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Este artigo fornece respostas para algumas das perguntas mais comuns sobre a execução de [SQL Server em Linux máquinas virtuais](sql-server-on-linux-vm-what-is-iaas-overview.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Imagens

1. **Quais imagens da galeria de máquinas virtuais do SQL Server estão disponíveis?**

   O Azure mantém imagens de VM (máquina virtual) para todas as versões principais com suporte do SQL Server em todas as edições para Linux e Windows. Para obter mais detalhes, consulte a lista completa de [imagens de VM Linux](sql-server-on-linux-vm-what-is-iaas-overview.md#create) e [imagens de VM Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

1. **As imagens da galeria de máquinas virtuais do SQL Server existentes são atualizadas?**

   A cada dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as atualizações mais recentes do Linux e do Windows. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. Para imagens do Windows, isso inclui todas as atualizações marcadas como importantes no Windows Update, incluindo atualizações de segurança SQL Server importantes e Service Packs. As atualizações cumulativas do SQL Server são tratadas de maneira diferente para o Linux e para o Windows. Para o Linux, as atualizações cumulativas do SQL Server também são incluídas na atualização. Mas, neste momento, as VMs Windows não são atualizadas com as atualizações cumulativas do SQL Server ou do Windows Server.

1. **Que pacotes do SQL Server relacionados também são instalados?**

   Para ver os pacotes de SQL Server que são instalados por padrão em VMs SQL Server em Linux, consulte [pacotes instalados](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **As imagens de máquina virtual do SQL Server podem ser removidas da galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e edição. Por exemplo, quando um novo service pack do SQL Server é lançado, o Azure adiciona uma nova imagem à galeria para esse service pack. A imagem do SQL Server para o service pack anterior é removida imediatamente do Portal do Azure. No entanto, ela ainda estará disponível para provisionamento do PowerShell pelos próximos três meses. Depois de três meses, a imagem do service pack anterior não estará mais disponível. Essa política de remoção também se aplica quando uma versão do SQL Server se torna incompatível quando ela atinge o final de seu ciclo de vida.

## <a name="creation"></a>Criação

1. **Como fazer criar uma máquina virtual Linux com SQL Server?**

   A solução mais fácil é criar uma máquina virtual Linux que inclua SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM SQL Server no portal, confira [provisionar uma máquina virtual Linux em execução SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md). Você também tem a opção de instalação manual do SQL Server em uma VM com um uma edição licenciada gratuitamente (Desenvolvedor ou Express) ou pela reutilização de uma licença local. Se você trouxer sua própria licença, será necessário ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Por que não é possível, provisionar uma VM do SQL Server do SLES ou RHEL com uma assinatura do Azure que tem um limite de gastos?**

   Máquinas virtuais RHEL e SLES exigem uma assinatura sem limite de gastos e um método de pagamento verificado (geralmente um cartão de crédito) associado à assinatura. Se você provisionar uma VM SLES ou RHEL sem remover o limite de gastos, sua assinatura será desabilitada e todas as VMs/serviços serão interrompidos. Se você entrar nesse estado, [remova o limite de gastos](https://account.windowsazure.com/subscriptions) para habilitar novamente a assinatura. Seu crédito restante será restaurado para o ciclo de cobrança atual, mas uma sobretaxa da imagem da VM RHEL ou SLES será aplicada em seu cartão de crédito se você optar por reiniciá-la e continuar a executá-la.

## <a name="licensing"></a>Licenciamento

1. **Como instalar minha cópia licenciada do SQL Server em uma VM do Azure?**

   Primeiro, crie uma máquina virtual exclusivamente do SO Linux. Em seguida, execute as [etapas de instalação do SQL Server](/sql/linux/sql-server-linux-setup#platforms) para sua distribuição do Linux. A menos que você esteja instalando uma das edições licenciadas gratuitamente do SQL Server, você também deve ter uma licença do SQL Server e [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Há imagens da máquina virtual Linux BYOL (Traga sua Própria Licença) para o SQL Server?**

   Neste momento, não há nenhuma imagem de máquina virtual BYOL Linux para o SQL Server. No entanto, você pode instalar manualmente do SQL Server em uma VM somente Linux conforme discutido nas perguntas anteriores.

1. **Posso alterar uma VM para usar minha própria licença do SQL Server se ela foi criada com base em uma das imagens pré-pagas da galeria?**

   Não. Não é possível mudar do licenciamento pago por segundo para o uso de sua própria licença. Você deve criar uma nova VM Linux, instalar o SQL Server e migrar os dados. Consulte a pergunta anterior para obter mais detalhes sobre como trazer sua própria licença.

## <a name="administration"></a>Administração

1. **Posso gerenciar uma máquina virtual Linux que executa o SQL Server com o SSMS (SQL Server Management Studio)?**

   Sim, mas o SSMS atualmente é uma ferramenta somente do Windows. Você deve se conectar remotamente de um computador Windows para usar o SSMS com VMs Linux em execução SQL Server. Localmente no Linux, a nova ferramenta [mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf) pode executar várias tarefas administrativas. Para obter uma ferramenta de gerenciamento de banco de dados multiplataforma, confira [Azure Data Studio](/sql/azure-data-studio/what-is).

1. **Posso remover o SQL Server completamente de uma VM de SQL Server?**

   Sim, mas você continuará a ser cobrado pela VM do SQL Server, conforme descrito em [Diretrizes de preços para VMs do Azure do SQL Server](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Se você não precisar mais do SQL Server, você poderá implantar uma nova máquina virtual e migrar os dados e aplicativos para a nova máquina virtual. Em seguida, você pode remover a máquina virtual do SQL Server.

## <a name="updating-and-patching"></a>Atualização e aplicação de patch

1. **Como atualizar para uma nova versão/edição do SQL Server em uma VM do Azure?**

   Atualmente, não existe uma atualização in-loco para o SQL Server em execução em uma VM do Azure. Crie uma nova máquina virtual do Azure com a versão/edição desejada do SQL Server e migre os bancos de dados para o novo servidor usando [técnicas de migração de dados](/sql/linux/sql-server-linux-migrate-overview) padrão.

## <a name="general"></a>Geral

1. **Há SQL Server soluções de alta disponibilidade com suporte em VMs do Azure?**

   Não no momento. O grupos de disponibilidade Always On e o clustering de failover exigem uma solução de clustering no Linux, como pacemaker. As distribuições Linux com suporte para SQL Server não dão suporte aos complementos de alta disponibilidade na nuvem.

## <a name="resources"></a>Recursos

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Provisionar SQL Server em uma VM do Linux](sql-vm-create-portal-quickstart.md)
* [SQL Server na documentação do Linux](/sql/linux/sql-server-linux-overview)

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Provisionar SQL Server em uma VM do Windows](../windows/sql-vm-create-portal-quickstart.md)
* [Perguntas Frequentes (Windows)](../windows/frequently-asked-questions-faq.md)