---
title: Criar um VHD compatível com o Azure para o Azure Marketplace | Microsoft Docs
description: Explica como criar um VHD para uma oferta de máquina virtual no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 04a1741bbe4e60567a22445c5674ec03b232640c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744360"
---
# <a name="create-an-azure-compatible-vhd"></a>Criar um VHD compatível com o Azure

Este artigo detalha as etapas necessárias para criar um disco rígido virtual (VHD) para uma oferta de máquina virtual (VM) no Azure Marketplace.  Ele também inclui práticas recomendadas para vários aspectos, como o uso do RDP (Remote Desktop Protocol), a seleção de um tamanho para a VM, a instalação das atualizações mais recentes do Windows e a generalização da imagem do VHD.  As seções a seguir enfocam principalmente os VHDs baseados em Windows; para obter mais informações sobre a criação de VHDs baseados em Linux, consulte [Linux em distribuições endossadas pelo Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> É altamente recomendável seguir as orientações deste tópico para usar o Azure para criar uma VM que contenha um sistema operacional pré-configurado e endossado.  Se isso não é compatível com sua solução, é possível criar e configurar uma VM local usando um sistema operacional aprovado.  Você pode configurá-lo e prepará-lo para upload, conforme descrito em [ Preparar um VHDX ou VHDX do Windows para fazer o upload para o Azure ](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Selecione uma base aprovada
O VHD do sistema operacional para a imagem VM deve ser baseado em uma imagem de base aprovada do Azure que contém o Windows Server ou o SQL Server.
Para começar, crie uma VM a partir de uma das seguintes imagens, localizadas no portal do Microsoft Azure:

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/), [2012 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/), [2008 R2 SP1](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Se você estiver usando o atual Portal de do Azure ou PowerShell, as imagens do Windows Server publicadas em 8 de setembro de 2014 e posteriormente serão aprovadas.

Como alternativa, o Azure oferece uma variedade de distribuições aprovadas do Linux.  Para obter uma lista atual, consulte [Linux em distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Criar VM no portal do Azure 

No portal do [Microsoft Azure](https://ms.portal.azure.com/), crie a imagem base usando as etapas a seguir.

1. Entre no portal com a conta da Microsoft para a assinatura do Azure na qual você deseja publicar sua oferta de VM.
2. Crie um novo grupo de recursos e forneça seu **Nome do grupo de recursos**, **Assinatura** e **Local do grupo de recursos**.  Para obter instruções, consulte [gerenciar grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Clique em **Máquinas virtuais** na barra de menu à esquerda para exibir a página de detalhes de máquinas virtuais. 
4. Nesta nova página, clique em **+ Adicionar** para exibir o blade **Compute**.  Se você não vir o tipo de VM na primeira tela, poderá procurar o nome da VM base, por exemplo:

    ![Lâmina de computação da nova VM](./media/publishvm_014.png)

5. Depois de selecionar a imagem virtual adequada, forneça os seguintes valores:
   * No blade **Basics**, insira um **Name** para a máquina virtual, entre 1-15 caracteres alfanuméricos. (Este exemplo usa `DemoVm009`.)
   * Digite um **nome de usuário** e uma **senha** forte, que são usados para criar uma conta local na VM.  (Aqui `adminUser` é usado.)  A senha deve ter de 8 a 123 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. Para obter mais informações, consulte [Requisitos de nome de usuário e senha](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Selecione o grupo de recursos que você criou (aqui `DemoResourceGroup`).
   * Selecione um **Local** do Azure Datacenter (aqui `West US`).
   * Clique em **Okey** para salvar esses valores. 

6. Selecione o tamanho da VM para implantar usando as seguintes recomendações:
   * Se você pretende desenvolver o VHD no local, o tamanho não importa. Considere o uso de uma das VMs menores.
   * Se você pretende desenvolver a imagem no Azure, considere o uso de um dos tamanhos de VM recomendados para a imagem selecionada.
   * Para informações de preços, consulte o seletor do **tipo de preço recomendado** exibido no portal. Ele exibirá os três tamanhos recomendados fornecidos pelo editor. (Aqui, o editor é Microsoft.)

   ![Lâmina de tamanho da nova VM](./media/publishvm_015.png)

7. Na folha **Configurações**, defina a opção  **Usar disco gerenciado** como **Não**.  Isso permite gerenciar manualmente o novo VHD. (O blade **Configurações** também permite que você altere outras alterações nas opções de armazenamento e rede, por exemplo, selecionando **Premium (SSD)** em **tipo de disco**.)  Clique em **OK** para continuar.

    ![Folha de configurações da nova VM](./media/publishvm_016.png)

8. Clique em **Resumo** para examinar suas opções. Quando você vir a mensagem **Validação aprovada**, clique em **OK**.

    ![Lâmina de resumo da nova VM](./media/publishvm_017.png)

O Azure inicia o fornecimento da máquina virtual especificada.  Você pode acompanhar seu progresso clicando na guia **Virtual Machines** à esquerda.  Depois de criado, o status mudará para **Running**.  Nesse ponto, você pode [conectar-se à máquina virtual](./cpp-connect-vm.md).


## <a name="next-steps"></a>Próximas etapas

Se você encontrou dificuldades para criar seu novo VHD baseado no Azure, consulte [Problemas comuns durante a criação do VHD](./cpp-common-vhd-creation-issues.md).  Caso contrário, em seguida você deve [conectar-se às VMs](./cpp-connect-vm.md) criado no Azure. 
