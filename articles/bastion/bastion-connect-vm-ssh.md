---
title: Conectar-se a uma VM Linux usando o Azure bastiões | Microsoft Docs
description: Neste artigo, saiba como se conectar a máquina Virtual Linux usando o Azure bastião.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 69548541d16db95f633400808f72aebaf59cff08
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477774"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>Conectar-se de usar o SSH para uma máquina virtual do Linux usando o Azure bastião (visualização)

Este artigo mostra como fazer isso para com simplicidade e SSH para VMs Linux em uma rede virtual do Azure. É possível se conectar a uma VM diretamente do portal do Azure. Usando o Azure Bastion, as VMs não precisam de um cliente, de um agente nem de software adicional. Para obter mais informações sobre bastiões do Azure, consulte a [visão geral](bastion-overview.md).

Você pode usar o Azure bastiões para se conectar a uma máquina virtual do Linux usando SSH. Você pode usar o nome de usuário/senha e chaves SSH para autenticação. Você pode se conectar à sua VM com chaves SSH, usando:

* Uma chave privada que você inserir manualmente
* Um arquivo que contém as informações de chave privadas

A chave privada SSH deve estar em um formato que começa com `"-----BEGIN RSA PRIVATE KEY-----"` e termina com `"-----END RSA PRIVATE KEY-----"`.

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que você tiver configurado um host bastião do Azure para a rede virtual no qual reside a VM. Para obter mais informações, consulte [criar um host bastião Azure](bastion-create-host-portal.md). Depois que o serviço de bastião é provisionado e implantado em sua rede virtual, você pode usá-lo para se conectar a qualquer VM nessa rede virtual. Nesta visualização, quando você usa bastiões para se conectar, ele pressupõe que você está usando o RDP para se conectar a uma VM do Windows e SSH para se conectar às suas VMs do Linux.

Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

## <a name="username"></a>Conecte-se: Usando o nome de usuário e senha


1.  Use [esse link](https://aka.ms/BastionHost) para abrir a página de portal de visualização de bastiões do Azure. Navegue até a máquina virtual que você deseja se conectar e clique em **Connect**. A VM deve ser uma máquina virtual Linux ao usar uma conexão SSH.
1. Depois de clicar em conectar, será exibida uma barra lateral tem três guias – bastiões, SSH e RDP. Se bastiões foi provisionado para a rede virtual, a guia de bastiões estiver ativa por padrão. Se você não o tiver provisionado bastiões para a rede virtual, consulte [bastiões configurar](bastion-create-host-portal.md). Se você não vir **bastiões** listado, você não abriu o portal de visualização. Abra o portal usando [esse link](https://aka.ms/BastionHost).

      ![Conectar-se de VM](./media/bastion-connect-vm-ssh/bastion.png)

1. Insira o nome de usuário e a senha para SSH para sua máquina virtual.
1. Clique em **Connect** botão depois de inserir a chave.

## <a name="privatekey"></a>Conecte-se: Inserir manualmente uma chave privada

1.  Use [esse link](https://aka.ms/BastionHost) para abrir a página de portal de visualização de bastiões do Azure. Navegue até a máquina virtual que você deseja se conectar e clique em **Connect**. A VM deve ser uma máquina virtual Linux ao usar uma conexão SSH.
1. Depois de clicar em conectar, será exibida uma barra lateral tem três guias – bastiões, SSH e RDP. Se bastiões foi provisionado para a rede virtual, a guia de bastiões estiver ativa por padrão. Se você não o tiver provisionado bastiões para a rede virtual, consulte [bastiões configurar](bastion-create-host-portal.md). Se você não vir **bastiões** listado, você não abriu o portal de visualização. Abra o portal usando [esse link](https://aka.ms/BastionHost).

      ![Conectar-se de VM](./media/bastion-connect-vm-ssh/bastion.png)

1. Insira o nome de usuário e selecione **chave privada SSH**.
1. Insira sua chave privada para a área de texto **chave privada SSH** (ou colá-lo diretamente).
1. Clique em **Connect** botão depois de inserir a chave.

## <a name="ssh"></a>Conecte-se: Usando um arquivo de chave privada

1.  Use [esse link](https://aka.ms/BastionHost) para abrir a página de portal de visualização de bastiões do Azure. Navegue até a máquina virtual que você deseja se conectar e clique em **Connect**. A VM deve ser uma máquina virtual Linux ao usar uma conexão SSH.

    ![Conectar-se de VM](./media/bastion-connect-vm-ssh/connect.png)

1. Depois de clicar em conectar, será exibida uma barra lateral tem três guias – bastiões, SSH e RDP. Se bastiões foi provisionado para a rede virtual, a guia de bastiões estiver ativa por padrão. Se você não o tiver provisionado bastiões para a rede virtual, consulte [bastiões configurar](bastion-create-host-portal.md). Se você não vir **bastiões** listado, você não abriu o portal de visualização. Abra o portal usando [esse link](https://aka.ms/BastionHost).

    ![Conectar-se de VM](./media/bastion-connect-vm-ssh/bastion.png)

1. Insira o nome de usuário e selecione **chave de privada SSH do arquivo Local**.
1. Clique o **procurar** botão (o ícone de pasta no arquivo local).
1. Procure o arquivo e, em seguida, clique em **aberto**.
1. Clique em **Connect** para se conectar à VM. Depois de clicar em conectar, SSH para esta máquina virtual será aberto diretamente no portal do Azure. Essa conexão é em HTML5 usando a porta 443 no serviço de bastiões sobre IP privado da sua máquina virtual.

## <a name="next-steps"></a>Próximas etapas

Leia o [bastiões perguntas Frequentes](bastion-faq.md)
