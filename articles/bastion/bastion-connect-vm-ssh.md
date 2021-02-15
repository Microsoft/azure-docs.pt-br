---
title: Conectar-se a uma VM do Linux usando a bastiões do Azure
description: Neste artigo, saiba como se conectar à máquina virtual do Linux usando a bastiões do Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 5d61c2a1a0f5d7b26809621af6dfa88cf5080320
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518174"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Conectar-se usando SSH em uma máquina virtual Linux usando a bastiões do Azure

Este artigo mostra como fazer SSH de forma segura e direta para suas VMs do Linux em uma rede virtual do Azure. Você pode se conectar a uma VM diretamente do portal do Azure. Ao usar a bastiões do Azure, as VMs não exigem um cliente, agente ou software adicional. Para obter mais informações sobre a bastiões do Azure, consulte a [visão geral](bastion-overview.md).

Você pode usar a bastiões do Azure para se conectar a uma máquina virtual Linux usando SSH. Você pode usar o nome de usuário/senha e as chaves SSH para autenticação. Você pode se conectar à sua VM com chaves SSH usando:

* Uma chave privada que você insere manualmente
* Um arquivo que contém as informações de chave privada

A chave privada SSH deve estar em um formato que comece com  `"-----BEGIN RSA PRIVATE KEY-----"` e termine com `"-----END RSA PRIVATE KEY-----"` .

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você configurou um host de bastiões do Azure para a rede virtual na qual a VM reside. Para obter mais informações, consulte [criar um host de bastiões do Azure](./tutorial-create-host-portal.md). Depois que o serviço de bastiões for provisionado e implantado em sua rede virtual, você poderá usá-lo para se conectar a qualquer VM nessa rede virtual. 

Quando você usa a bastiões para se conectar, ele pressupõe que você está usando o RDP para se conectar a uma VM do Windows e o SSH para se conectar às suas VMs do Linux. Para obter informações sobre como se conectar a uma VM do Windows, consulte [conectar-se a uma VM-Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Funções necessárias

Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

### <a name="ports"></a>Portas

Para se conectar à VM do Linux via SSH, você deve ter as seguintes portas abertas em sua VM:

* Porta de entrada: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Conectar: usando nome de usuário e senha

1. Abra o [portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e clique em **conectar** e selecione **bastiões** na lista suspensa.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Captura de tela mostra a visão geral de uma máquina virtual no portal do Azure com o Connect selecionado":::
1. Depois de selecionar a bastiões, uma barra lateral aparece com três guias – RDP, SSH e bastiões. Se a bastiões tiver sido provisionada para a rede virtual, a guia de bastiões estará ativa por padrão. Se você não provisionar a bastiões para a rede virtual, consulte [Configurar a bastiões](./tutorial-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="Captura de tela mostra a caixa de diálogo conectar à máquina virtual com bastiões selecionada":::
1. Insira o nome de usuário e a senha para SSH em sua máquina virtual.
1. Selecione o botão **conectar** depois de inserir a chave.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Conectar: Insira manualmente uma chave privada

1. Abra o [portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e clique em **conectar** e selecione **bastiões** na lista suspensa.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Captura de tela mostra a visão geral de uma máquina virtual no portal do Azure com o Connect selecionado":::
1. Depois de selecionar a bastiões, uma barra lateral aparece com três guias – RDP, SSH e bastiões. Se a bastiões tiver sido provisionada para a rede virtual, a guia de bastiões estará ativa por padrão. Se você não provisionar a bastiões para a rede virtual, consulte [Configurar a bastiões](./tutorial-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="Caixa de diálogo conectar à máquina virtual com bastiões selecionada.":::
1. Insira o nome de usuário e selecione **chave privada SSH**.
1. Insira sua chave privada na **chave privada SSH** da área de texto (ou cole-a diretamente).
1. Selecione o botão **conectar** depois de inserir a chave.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Conectar: usando um arquivo de chave privada

1. Abra o [portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e clique em **conectar** e selecione **bastiões** na lista suspensa.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Conectar selecionado":::
1. Depois de selecionar a bastiões, uma barra lateral aparece com três guias – RDP, SSH e bastiões. Se a bastiões tiver sido provisionada para a rede virtual, a guia de bastiões estará ativa por padrão. Se você não provisionar a bastiões para a rede virtual, consulte [Configurar a bastiões](./tutorial-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="BASTIÕES selecionada.":::
1. Insira o nome de usuário e selecione **chave privada SSH do arquivo local**.
1. Selecione o botão **procurar** (o ícone de pasta no arquivo local).
1. Procure o arquivo e selecione **abrir**.
1. Selecione **conectar** para conectar-se à VM. Depois de clicar em conectar, o SSH para essa máquina virtual será aberto diretamente no portal do Azure. Essa conexão é sobre o HTML5 usando a porta 443 no serviço de bastiões por meio do IP privado de sua máquina virtual.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Conectar: usando uma chave privada armazenada em Azure Key Vault

>[!NOTE]
>A atualização do portal para este recurso está sendo distribuída no momento para regiões.
>

1. Abra o [portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e clique em **conectar** e selecione **bastiões** na lista suspensa.
1. Depois de selecionar a bastiões, uma barra lateral aparece com três guias – RDP, SSH e bastiões. Se a bastiões tiver sido provisionada para a rede virtual, a guia de bastiões estará ativa por padrão. Se você não provisionar a bastiões para a rede virtual, consulte [Configurar a bastiões](bastion-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="Guia de bastiões":::
1. Insira o nome de usuário e selecione **chave privada SSH em Azure Key Vault**.
1. Selecione a lista suspensa **Azure Key Vault** e selecione o recurso no qual você armazenou a chave privada SSH. Se você não configurou um recurso de Azure Key Vault, consulte [criar um cofre de chaves](../key-vault/general/quick-create-portal.md) e armazenar sua chave privada SSH como o valor de um novo segredo de Key Vault.

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

Verifique se você tem a **lista** e **obtenha** acesso aos segredos armazenados no recurso de Key Vault. Para atribuir e modificar políticas de acesso para o recurso Key Vault, consulte [atribuir uma política de acesso Key Vault](../key-vault/general/assign-access-policy-portal.md).

1. Selecione o **Azure Key Vault** lista suspensa segredo e selecione o segredo de Key Vault que contém o valor da chave privada SSH.
1. Selecione **conectar** para conectar-se à VM. Depois de clicar em conectar, o SSH para essa máquina virtual será aberto diretamente no portal do Azure. Essa conexão é sobre o HTML5 usando a porta 443 no serviço de bastiões por meio do IP privado de sua máquina virtual.

## <a name="next-steps"></a>Próximas etapas

Leia as [perguntas frequentes de bastiões](bastion-faq.md)
