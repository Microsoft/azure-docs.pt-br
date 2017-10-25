---
title: "Perguntas frequentes sobre o SQL Server em Máquinas Virtuais Linux do Azure | Microsoft Docs"
description: "Este artigo fornece respostas a perguntas frequentes sobre a execução do SQL Server em VMs Linux do Azure."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Perguntas frequentes sobre o SQL Server nas Máquinas Virtuais Linux do Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Este tópico fornece respostas a algumas das perguntas mais comuns sobre a execução do [SQL Server em Máquinas Virtuais Linux do Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Este tópico concentra-se em problemas específicos ao SQL Server em VMs Linux. Se você estiver executando o SQL Server em VMs do Windows, consulte as [Perguntas frequentes sobre o Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. **Como criar uma máquina virtual Linux do Azure com o SQL Server?**

   A solução mais fácil é criar uma máquina virtual Linux que inclua o SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM do SQL por meio do portal, confira [Provisionar uma máquina virtual Linux do SQL Server no Portal do Azure](provision-sql-server-linux-virtual-machine.md). Você também tem a opção de instalação manual do SQL Server em uma VM com um uma edição licenciada gratuitamente (Desenvolvedor ou Express) ou pela reutilização de uma licença local. Se você trouxer sua própria licença, será necessário ter o [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Como atualizar para uma nova versão/edição do SQL Server em uma VM do Azure?**

   Atualmente, não existe uma atualização in-loco para o SQL Server em execução em uma VM do Azure. Crie uma nova máquina virtual do Azure com a versão/edição desejada do SQL Server e migre os bancos de dados para o novo servidor usando [técnicas de migração de dados](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview) padrão.

1. **Como instalar minha cópia licenciada do SQL Server em uma VM do Azure?**

   Primeiro, crie uma máquina virtual exclusivamente do SO Linux. Em seguida, execute as [etapas de instalação do SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) para sua distribuição do Linux. A menos que você esteja instalando uma das edições licenciadas gratuitamente do SQL Server, você também deve ter uma licença do SQL Server e [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Há imagens da máquina virtual Linux BYOL (Traga sua Própria Licença) para o SQL Server?**

   Neste momento, não há nenhuma imagem de máquina virtual BYOL Linux para o SQL Server. No entanto, você pode instalar manualmente do SQL Server em uma VM somente Linux conforme discutido nas perguntas anteriores.

1. **Posso alterar uma VM para usar minha própria licença do SQL Server se ela foi criada com base em uma das imagens pré-pagas da galeria?**

   Não. Não é possível mudar do licenciamento pago por minuto para o uso de sua própria licença. Você deve criar uma nova VM Linux, instalar o SQL Server e migrar os dados. Consulte a pergunta anterior para obter mais detalhes sobre como trazer sua própria licença.

1. **Que pacotes do SQL Server relacionados também são instalados?**

   Para ver os pacotes do SQL Server que são instalados por padrão em VMs Linux do SQL Server, consulte [Pacotes instalados](sql-server-linux-virtual-machines-overview.md#packages).

1. **Há suporte para soluções de alta disponibilidade do SQL Server em VMs do Azure?**

   Não no momento. Tanto Grupos de disponibilidade AlwaysOn quanto Clustering de Failover exigem uma solução de clustering em Linux, tal como o Pacemaker. As distribuições do Linux com suporte para o SQL Server não dão suporte aos respectivos complementos de alta disponibilidade na nuvem.

1. **Por que não é possível, provisionar uma VM do SQL Server do SLES ou RHEL com uma assinatura do Azure que tem um limite de gastos?**

   Máquinas virtuais RHEL e SLES exigem uma assinatura sem limite de gastos e um método de pagamento verificado (geralmente um cartão de crédito) associado à assinatura. Se você provisionar uma VM SLES ou RHEL sem remover o limite de gastos, sua assinatura será desabilitada e todas as VMs/serviços serão interrompidos. Se você entrar nesse estado, [remova o limite de gastos](https://account.windowsazure.com/subscriptions) para habilitar novamente a assinatura. Seu crédito restante será restaurado para o ciclo de cobrança atual, mas uma sobretaxa da imagem da VM RHEL ou SLES será aplicada em seu cartão de crédito se você optar por reiniciá-la e continuar a executá-la.

## <a name="resources"></a>Recursos

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](sql-server-linux-virtual-machines-overview.md)
* [Provisionar uma VM Linux do SQL Server](provision-sql-server-linux-virtual-machine.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Provisionar uma VM do Windows do SQL Server](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Perguntas Frequentes (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)