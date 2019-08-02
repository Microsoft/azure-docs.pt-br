---
title: Criar imagens compartilhadas de máquina Virtual do Azure para Linux usando o portal | Microsoft Docs
description: Saiba como usar o portal do Azure para criar e compartilhar imagens de máquina virtual.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: ea0a5961aff5935efea3eb98fd53904ce864ec2c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708910"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Criar uma galeria de imagem compartilhada usando o portal do Azure

Uma [Galeria de Imagens Compartilhadas](shared-image-galleries.md) simplifica o compartilhamento da imagem personalizada em sua organização. Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para inicializar tarefas de implantação, como pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. 

A galeria de imagens compartilhadas permite compartilhar suas imagens da VM personalizadas com outras pessoas em sua organização, dentro ou entre regiões, em um locatário do AAD. Escolha quais imagens você deseja compartilhar, em quais regiões deseja torná-las disponíveis e com quem deseja compartilhá-las. Você pode criar várias galerias, de modo que pode agrupar logicamente as imagens compartilhadas. 

A galeria é um recurso de nível superior que fornece RBAC (controle de acesso completo baseado em função). As imagens podem ser atualizadas, e você pode optar por replicar cada versão da imagem para um conjunto diferente de regiões do Azure. A galeria funciona apenas com imagens gerenciadas.

O recurso Galeria de Imagens Compartilhadas tem vários tipos de recursos. Usaremos ou criaremos estes itens neste artigo:

| Recurso | DESCRIÇÃO|
|----------|------------|
| **Imagem gerenciada** | Esta é uma imagem básica que pode ser usada sozinha ou para criar uma **versão da imagem** em uma galeria de imagens. Imagens gerenciadas são criadas de VMs generalizadas. Uma imagem gerenciada é um tipo especial de VHD que pode ser usado para criar várias VMs e agora pode ser usado para criar versões de imagem compartilhada. |
| **Galeria de imagens** | Como o Azure Marketplace, uma **galeria de imagens** é um repositório para gerenciar e compartilhar imagens, mas você controla quem tem acesso. |
| **Definição da imagem** | As imagens são definidas dentro de uma galeria e transportam informações sobre a imagem e os requisitos para usá-la internamente. Isso inclui se a imagem é Windows ou Linux, notas sobre a versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é usada para criar uma VM ao usar uma galeria. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Como uma imagem gerenciada, quando você usa uma **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes. |


## <a name="before-you-begin"></a>Antes de começar

Para concluir o exemplo neste artigo, você precisa ter uma imagem gerenciada existente. Você pode seguir o [Tutorial: Crie uma imagem personalizada de uma VM do Azure com o Azure PowerShell](tutorial-custom-images.md) para criar uma se necessário. Se a imagem gerenciada contém um disco de dados, o tamanho do disco de dados não pode ser mais de 1 TB.

Ao trabalhar com este artigo, substitua o grupo de recursos e os nomes de VM quando for necessário.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>Criar VMs de uma imagem

Depois que a versão da imagem estiver completa, você poderá criar uma ou mais novas VMs. 

> [!IMPORTANT]
> Você não pode usar o portal para implantar uma VM de uma imagem em outro locatário do azure. Para criar uma VM de uma imagem compartilhada entre locatários, você deve usar o [CLI do Azure](shared-images.md#create-a-vm) ou [Powershell](../windows/shared-images.md#create-vms-from-an-image).


Este exemplo cria uma VM chamada *myVMfromImage* no *myResourceGroup* no datacenter do *Leste dos EUA*.

1. Na página para a sua versão de imagem, selecione **criar VM** no menu na parte superior da página.
1. Para **grupo de recursos**, selecione **criar novo** e digite *myResourceGroup* para o nome.
1. Na **nome da máquina Virtual**, digite *myVM*.
1. Para **região**, selecione *Leste dos EUA*.
1. Para **opções de disponibilidade**, deixe o padrão de *sem redundância de infraestrutura necessários*.
1. O valor para **imagem** deve ser preenchida automaticamente se tiver iniciado a partir da página para a versão da imagem.
1. Para **tamanho**, escolha um tamanho de VM na lista de tamanhos disponíveis e, em seguida, clique em "Selecionar".
1. Sob **conta de administrador**, selecione **senha** ou **chave pública SSH**, em seguida, insira suas informações.
1. Se você quiser permitir o acesso remoto à VM, sob **portas de entrada públicas**, escolha **permitir portas selecionadas** e, em seguida, selecione **SSH (22)** na lista suspensa. Se você não quiser permitir o acesso remoto à VM, deixe **None** selecionado para **portas de entrada públicas**.
1. Quando tiver terminado, selecione o **revisar + criar** botão na parte inferior da página.
1. Depois que a VM passa a validação, selecione **criar** na parte inferior da página para iniciar a implantação.


## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da máquina virtual, selecione **Excluir**, em seguida, confirme o nome do grupo de recursos para excluir.

Se você quiser excluir recursos individuais, você precisa excluí-los na ordem inversa. Por exemplo, para excluir uma definição de imagem, você precisará excluir todas as versões de imagem criadas a partir dessa imagem.

## <a name="next-steps"></a>Próximas etapas

Você também pode criar um recurso de Galeria de imagens compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão da Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM por meio de uma Versão da Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para obter mais informações sobre galerias de imagens compartilhadas, confira a [Visão geral](shared-image-galleries.md). Se você enfrentar problemas, confira [Solução de problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).

