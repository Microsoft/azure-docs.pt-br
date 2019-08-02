---
title: Como executar uma atualização in-loco de edição do SQL Server em VM do Azure | Microsoft Docs
description: Saiba como alterar a edição da VM do SQL Server no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607540"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Como executar uma atualização in-loco de edição do SQL Server em VM do Azure

Este artigo descreve como alterar a edição do SQL Server para um SQL Server existente em uma máquina de virtual do Windows no Azure. 

A edição do SQL Server é determinada pela chave do produto e é especificada com o processo de instalação. A edição determina quais [recursos](/sql/sql-server/editions-and-components-of-sql-server-2017) estão disponíveis dentro do produto SQL Server. Você pode alterar a edição do SQL Server com a mídia de instalação e o downgrade para reduzir o custo ou atualizar para ativar mais recursos.

Se você atualizou a edição do SQL Server usando a mídia de instalação depois de registrar com o provedor de recursos de VM do SQL, em seguida, para atualizar o Azure cobrança adequadamente, você deve definir a propriedade de edição do SQL Server do recurso da VM do SQL da seguinte maneira:

1. Faça logon no [Portal do Azure](https://portal.azure.com). 
1. Navegue até seu recurso de máquina virtual do SQL Server. 
1. Sob **as configurações**, selecione **configurar** e selecione sua edição desejada do SQL Server na lista suspensa sob **Edition**. 

   ![Metadados da alteração de edição](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Leia o aviso informando que você deve alterar a edição do SQL Server pela primeira vez, e essa propriedade edition deve corresponder à edição do SQL Server. 
1. Selecione **aplicar** para aplicar as alterações de metadados de edição. 


## <a name="prerequisites"></a>Pré-requisitos

Para fazer uma alteração no local da edição do SQL Server, você precisará do seguinte: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Um Windows [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrado com o [provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- A instalação de mídia com a edição desejada do SQL Server. Os clientes que têm [do software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) pode obter sua mídia de instalação do [Centro de licenciamento por Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes não têm o software assurance pode usar a mídia de instalação de uma imagem de VM do SQL Server do marketplace com sua edição desejada.


## <a name="upgrade-edition"></a>Atualizar edição

  > [!WARNING]
  > - **Atualização de edição do SQL Server reiniciará o serviço do SQL Server, bem como quaisquer serviços associados, como o Analysis Services e R Services.** 

Para atualizar a edição do SQL Server, obtenha a mídia de instalação do SQL Server para a edição desejada do SQL Server e, em seguida, faça o seguinte:

1. Inicie o Setup.exe da mídia de instalação do SQL Server. 
1. Navegue até **manutenção** e escolha o **a atualização de edição** opção. 

   ![Atualizar a edição do SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Selecione **próxima** até chegar a **pronto para atualizar a edição** página e, em seguida, selecione **atualizar**. A janela de instalação poderá parar de responder por alguns minutos enquanto a alteração está em vigor e, em seguida, você verá uma **concluir** página de confirmação de que a atualização de edição foi concluída. 

Após a atualização de edição do SQL Server, você deve modificar a propriedade "Edition" da máquina Virtual Sql no portal do Azure, conforme mostrado acima. Isso atualizará os metadados e a cobrança associada a essa VM.

## <a name="downgrade-edition"></a>Edição de downgrade

  > [!WARNING]
  > - **Fazer downgrade da edição do SQL Server requer a desinstalação completa do SQL Server, que pode incorrer em tempo de inatividade adicional**. 


Para fazer o downgrade da edição do SQL Server, você precisará desinstalar o SQL Server completamente e reinstalá-lo novamente com a mídia de instalação da edição desejada. 

Você pode fazer o downgrade da edição do SQL Server seguindo estas etapas:

1. Fazer backup de todos os bancos de dados, incluindo os bancos de dados do sistema. 
1. Mova bancos de dados do sistema (mestre, modelo e msdb) para um novo local. 
1. Desinstale completamente o SQL Server e todos os serviços associados. 
1. Reinicie a máquina virtual. 
1. Instale o SQL Server usando a mídia com a edição desejada do SQL Server.
1. Instale as atualizações cumulativas e service packs mais recentes.  
1. Substitua o novo sistema bancos de dados que foram criados durante a instalação com os bancos de dados do sistema que foram movidos anteriormente para um local diferente. 

Depois que a edição do SQL Server está desatualizada, você deve modificar a propriedade 'Edição' da máquina virtual SQL no portal do Azure, conforme mostrado acima. Isso atualizará os metadados e a cobrança associada a essa VM.

## <a name="remarks"></a>Comentários

 - A propriedade de edição para a VM do SQL Server deve corresponder a edição do SQL Server instalado para a máquina virtual para todas as máquinas virtuais SQL incluindo PAYG e BYOL tipos de licença.
 - Se você remover o recurso de VM do SQL Server, você voltará para a configuração de edição embutido da imagem.
  - A capacidade de alterar a edição é um recurso do provedor de recursos de VM do SQL. Implantar uma imagem do marketplace no portal do Azure automaticamente registra uma VM do SQL Server com o provedor de recursos. No entanto, os clientes que são de instalação automática do SQL Server precisará manualmente [registrar sua VM do SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md).
- A adição de uma VM do SQL Server para um conjunto de disponibilidade requer recriar a VM. Como tais, quaisquer VMs adicionadas a um disponibilidade conjunto voltará para a edição padrão e a edição precisará ser modificada novamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientações sobre preço do SQL Server em uma VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


