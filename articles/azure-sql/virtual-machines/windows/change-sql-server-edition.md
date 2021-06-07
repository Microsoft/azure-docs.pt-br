---
title: Alteração no local da edição do SQL Server
description: Saiba como alterar a edição do seu SQL Server máquina virtual no Azure para fazer o downgrade para reduzir o custo ou atualizar para habilitar mais recursos.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b6ea6f947b2cd8d7d9c38a0e5e440ce5fd273bb7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358819"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Alteração no local da edição do SQL Server na VM do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como alterar a edição do SQL Server em uma máquina virtual do Windows no Azure. 

A edição do SQL Server é determinada pela chave do produto (Product Key) e é especificada durante o processo de instalação usando a mídia de instalação. A edição determina quais [recursos](/sql/sql-server/editions-and-components-of-sql-server-2017) estão disponíveis no produto do SQL Server. Você pode alterar a edição do SQL Server com a mídia de instalação e fazer downgrade para reduzir o custo ou atualizar para habilitar mais recursos.

Depois que a edição do SQL Server tiver sido alterada internamente para a VM do SQL Server, você deverá atualizar a propriedade de edição do SQL Server no portal do Azure para fins de cobrança. 

## <a name="prerequisites"></a>Pré-requisitos

Para fazer uma alteração no local da edição do SQL Server, você precisará do seguinte: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Uma [VM SQL Server no Windows](./create-sql-vm-portal.md) registrada com a [extensão do SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md).
- A mídia de instalação com a **edição desejada** do SQL Server. Os clientes que têm [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter a mídia de instalação deles do [Centro de Licenciamento por Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não têm o Software Assurance podem usar a mídia de instalação de uma imagem da VM do SQL Server do Azure Marketplace que tenha a edição desejada por eles (normalmente localizada em `C:\SQLServerFull`). 


## <a name="upgrade-an-edition"></a>Atualizar uma edição

> [!WARNING]
> Atualizar a edição do SQL Server reiniciará o serviço para o SQL Server, juntamente com os serviços associados, como o Analysis Services e o R Services. 

Para atualizar a edição do SQL Server, obtenha a mídia de instalação do SQL Server para a edição desejada do SQL Server e, depois, faça o seguinte:

1. Abra o Setup.exe da mídia de instalação do SQL Server. 
1. Vá para **Manutenção** e escolha a opção **Atualização de Edição**. 

   ![Seleção para atualizar a edição do SQL Server](./media/change-sql-server-edition/edition-upgrade.png)

1. Selecione **Avançar** até acessar a página **Pronto para atualizar a edição** e, em seguida, selecione **Atualizar**. A janela de instalação poderá parar de responder por alguns minutos enquanto a alteração estiver sendo efetivada. Uma página com a mensagem **Concluído** confirmará que a atualização de edição foi concluída. 

Depois que a edição do SQL Server for atualizada, modifique a propriedade de edição da máquina virtual SQL Server no portal do Azure. Isso atualizará os metadados e a cobrança associados a essa VM.

## <a name="downgrade-an-edition"></a>Fazer downgrade de uma edição

Para fazer downgrade da edição do SQL Server, você precisa desinstalar completamente o SQL Server e reinstalá-lo novamente com a mídia de instalação da edição desejada. 

> [!WARNING]
> A desinstalação de SQL Server pode incorrer em tempo de inatividade adicional. 

Você pode fazer downgrade da edição do SQL Server seguindo estas etapas:

1. Faça backup de todos os bancos de dados, incluindo os bancos de dados do sistema. 
1. Mover os bancos de dados do sistema (mestre, modelo e msdb) para uma nova localização. 
1. Desinstale completamente o SQL Server e todos os serviços associados. 
1. Reinicie a máquina virtual. 
1. Instale o SQL Server usando a mídia com a edição desejada do SQL Server.
1. Instale os service packs e as atualizações cumulativas mais recentes.  
1. Substitua os novos bancos de dados do sistema que foram criados durante a instalação pelos bancos de dados do sistema que você moveu anteriormente para uma localização diferente. 

Depois que o downgrade da edição do SQL Server for feito, modifique a propriedade de edição da máquina virtual SQL Server no portal do Azure. Isso atualizará os metadados e a cobrança associados a essa VM.

## <a name="change-edition-in-portal"></a>Alterar edição no portal 

Depois de alterar a edição do SQL Server usando a mídia de instalação e tiver registrado sua VM do SQL Server com a [extensão do SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md), você poderá usar o portal do Azure para modificar a propriedade de edição da VM SQL Server para fins de cobrança. Para fazer isso, siga estas etapas: 

1. Entre no [portal do Azure](https://portal.azure.com). 
1. Vá para seu recurso de máquina virtual do SQL Server. 
1. Em **Configurações**, escolha **Configurar**. Em seguida, selecione a edição desejada do SQL Server na lista suspensa em **Edição**. 

   ![Alterar metadados de edição](./media/change-sql-server-edition/edition-change-in-portal.png)

1. Examine o aviso que diz que você precisa alterar a edição do SQL Server primeiro e que a propriedade de edição precisa corresponder à edição do SQL Server. 
1. Selecione **Aplicar** para aplicar as alterações de metadados da edição. 


## <a name="remarks"></a>Comentários

- A propriedade de edição para a VM do SQL Server precisa corresponder à edição da instância do SQL Server instalada para todas as máquinas virtuais do SQL Server, incluindo os tipos de licença pago conforme o uso e traga sua própria licença.
- Se você remover o recurso de VM do SQL Server, voltará para a configuração de edição embutida em código da imagem.
- A capacidade de alterar a edição é um recurso da extensão do agente IaaS do SQL. A implantação de uma imagem do Azure Marketplace por meio do portal do Azure registra automaticamente uma VM SQL Server com a extensão do agente IaaS do SQL. Mas os clientes que estão instalando automaticamente o SQL Server precisarão [registrar manualmente a respectiva VM do SQL Server](sql-agent-extension-manually-register-single-vm.md).
- Para adicionar uma VM do SQL Server a um conjunto de disponibilidade, é preciso criar a VM novamente. Todas as VMs adicionadas a um conjunto de disponibilidade voltarão para a edição padrão e a edição precisará ser modificada novamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](doc-changes-updates-release-notes.md)