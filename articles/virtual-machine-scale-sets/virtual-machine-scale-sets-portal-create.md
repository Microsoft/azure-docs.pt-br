---
title: "Criar um conjunto de dimensionamento de máquinas virtuais usando o Portal do Azure | Microsoft Docs"
description: Implantar conjuntos de escala usando o portal do Azure.
keywords: "conjuntos de escala de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc52368a1a14ad094e7ab9180b90a9aa4ccdb6d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>Como criar um Conjunto de Dimensionamento de Máquinas Virtuais usando o portal do Azure
Este tutorial mostra como é fácil criar um conjunto de escala de máquina virtual em poucos minutos usando o Portal do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Escolher a imagem de VM no Marketplace
No portal, você pode implantar facilmente um conjunto de dimensionamento com CentOS, CoreOS, Debian, Ubuntu Server, outras imagens do Linux e imagens do Windows Server.

Primeiro, navegue até o [Portal do Azure](https://portal.azure.com) no navegador da Web. Clique em **Novo**, procure por **conjunto de dimensionamento**e, em seguida, selecione a entrada de **Conjunto de dimensionamento da máquina Virtual**:

![pesquisa do portal do conjunto de dimensionamento de máquinas virtuais do azure](./media/virtual-machine-scale-sets-portal-create/portal-search.png)

## <a name="create-the-scale-set"></a>Criar o conjunto de dimensionamento
Agora, você pode usar as configurações padrão e criar rapidamente o conjunto de dimensionamento.

* Insira um nome para o conjunto de dimensionamento.  
Esse nome se torna a base do FQDN do balanceador de carga na frente do conjunto de dimensionamento. Portanto, certifique-se de que o nome seja exclusivo em todo o Azure.

* Selecione o tipo de SO desejado.

* Insira o nome de usuário desejado e selecione o tipo de autenticação que prefere.  
Se você escolher uma senha, ela deverá ter pelo menos 12 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. Veja mais sobre os [requisitos de nome de usuário e senha](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). Se você escolher **Chave pública SSH**, cole somente sua chave pública, *NÃO* sua chave privada:

* Selecione **Sim** ou **não** para **Habilitar dimensionamento acima de 100 instâncias**.  
Se Sim, o conjunto de dimensionamento pode se estender por vários grupos de posicionamento. Para obter mais informações, veja [esta documentação](./virtual-machine-scale-sets-placement-groups.md).

* Certifique-se de selecionar um **tamanho de instância** apropriado.  
Para saber mais sobre os tamanhos de máquina virtual, consulte [Tamanhos de VM do Windows](..\virtual-machines\windows\sizes.md) ou [Tamanhos de VM do Linux](..\virtual-machines\linux\sizes.md).

* Digite o nome do grupo de recursos desejado e a localização.  
Se sua região e **tamanho da instância** oferece suporte à disponibilidade de zonas, o campo **Zonas de disponibilidade** está habilitado. Para obter mais informações sobre as zonas de disponibilidade, consulte este artigo de [visão geral](../availability-zones/az-overview.md).

* Digite o rótulo de nome de domínio desejado (a base do FQDN para o balanceador de carga na frente do conjunto de dimensionamento).  
Este rótulo deve ser exclusivo em todo o Azure.

* Escolha a imagem de disco do sistema operacional desejada, a contagem de instâncias e o tamanho da máquina.

* Escolha o tipo de disco desejado: gerenciado ou não.  
Para obter mais informações, veja [esta documentação](./virtual-machine-scale-sets-managed-disks.md). Essa opção não estará disponível se você optar por fazer com que o conjunto de dimensionamento abranja vários grupos de posicionamento, porque o disco gerenciado é um pré-requisito para isso.

* Habilitar ou desabilitar o dimensionamento automático e configurar, se habilitado.

![prompt para criar o portal do conjunto de dimensionamento de máquinas virtuais do azure](./media/virtual-machine-scale-sets-portal-create/portal-create.png)

## <a name="connect-to-a-vm-in-the-scale-set"></a>Conectar-se a uma VM no conjunto de dimensionamento
Se você optar por limitar seu conjunto de dimensionamento a um único grupo de posicionamento, o conjunto de dimensionamento será implantado com regras NAT configuradas para permitir que você se conecte ao conjunto de dimensionamento facilmente (caso contrário, para se conectar às máquinas virtuais no conjunto de dimensionamento, você provavelmente precisará criar uma jumpbox na mesma rede virtual do conjunto de dimensionamento). Para vê-las, navegue até a guia `Inbound NAT Rules` do balanceador de carga para o conjunto de dimensionamento:

![regras nat do portal do conjunto de dimensionamento de máquinas virtuais do azure](./media/virtual-machine-scale-sets-portal-create/portal-nat-rules.png)

Você pode se conectar a cada VM no conjunto de escala usando estas regras NAT. Por exemplo, para um conjunto de escala do Windows, se houver uma regra NAT na porta de entrada 50000, você poderá se conectar a essa máquina via RDP em `<load-balancer-ip-address>:50000`. Para um conjunto de escala do Linux, você se conectaria usando o comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Próximas etapas
Para ver a documentação sobre como implantar conjuntos de escala da CLI, consulte [esta documentação](virtual-machine-scale-sets-cli-quick-create.md).

Para ver a documentação sobre como implantar conjuntos de dimensionamento do PowerShell, veja [esta documentação](virtual-machine-scale-sets-windows-create.md).

Para ver a documentação sobre como implantar conjuntos de escala do Visual Studio, consulte [esta documentação](virtual-machine-scale-sets-vs-create.md).

Para ver a documentação geral, confira a [página de visão geral da documentação para conjuntos de escala](virtual-machine-scale-sets-overview.md).

Para obter informações gerais, confira a [página de aterrissagem principal para conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

