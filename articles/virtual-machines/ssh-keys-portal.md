---
title: Criar chaves SSH no portal do Azure
description: Saiba como gerar e armazenar chaves SSH no portal do Azure para conectar as VMs do Linux.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: abc9a2ae130d987c90ce87ffaecbf2bb44b06010
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88929424"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>Gerar e armazenar chaves SSH no portal do Azure

Se usar o portal com frequência para implantar VMs do Linux, você poderá tornar o uso de chaves SSH mais simples, criando-as diretamente no portal ou carregando-as do seu computador.

Você pode criar uma chave SSH ao criar uma VM pela primeira vez e reutilizá-las para outras VMs. Ou, você pode criar chaves SSH separadamente, para que você tenha um conjunto de chaves armazenadas no Azure para atender às necessidades de suas organizações. 

Se você tiver chaves existentes e quiser simplificar o uso deles no portal, poderá carregá-las e armazená-las no Azure para reutilização.

Para obter informações mais detalhadas sobre como criar e usar chaves SSH com VMs do Linux, consulte [usar chaves SSH para se conectar a VMs Linux](./linux/ssh-from-windows.md).

## <a name="generate-new-keys"></a>Gerar novas chaves

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Na parte superior da página, digite *SSH* para pesquisar. Em **Marketplace**, selecione **chaves SSH**.

1. Na página **chave SSH** , selecione **criar**.

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="Criar um novo grupo de recursos e gerar um par de chaves SSH":::

1. Em **grupo de recursos** , selecione **criar novo** para criar um novo grupo de recursos para armazenar suas chaves. Digite um nome para seu grupo de recursos e selecione **OK**.

1. Em **região** , selecione uma região para armazenar suas chaves. Você pode usar as chaves em qualquer região, essa é apenas a região em que elas serão armazenadas.

1. Digite um nome para a chave no **nome do par de chaves**.

1. Em **origem de chave pública SSH**, selecione **gerar fonte de chave pública**. 

1. Quando terminar, selecione **Revisar + criar**.

1. Depois de passar na validação, selecione **Criar**.

1. Em seguida, você receberá uma janela pop-up para, selecione **baixar chave privada e criar recurso**. Isso baixará a chave SSH como um arquivo. PEM.

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="Criar um novo grupo de recursos e gerar um par de chaves SSH":::

1. Depois que o arquivo. pem for baixado, talvez você queira movê-lo em algum lugar no computador em que é fácil apontar para o cliente SSH.


## <a name="connect-to-the-vm"></a>Conectar-se à VM

No computador local, abra um prompt do PowerShell e digite:

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

Por exemplo, digite: `ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>Carregar uma chave SSH

Você também pode carregar uma chave SSH pública para armazenar no Azure. Para obter informações sobre como criar um par de chaves SSH, consulte [usar chaves SSH para se conectar a VMs Linux](./linux/ssh-from-windows.md).

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Na parte superior da página, digite *SSH* para pesquisar. Em **Marketplace*, selecione **chaves SSH**.

1. Na página **chave SSH** , selecione **criar**.

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="Criar um novo grupo de recursos e gerar um par de chaves SSH":::

1. Em **grupo de recursos** , selecione **criar novo** para criar um novo grupo de recursos para armazenar suas chaves. Digite um nome para seu grupo de recursos e selecione **OK**.

1. Em **região** , selecione uma região para armazenar suas chaves. Você pode usar as chaves em qualquer região, essa é apenas a região em que elas serão armazenadas.

1. Digite um nome para a chave no **nome do par de chaves**.

1. Em **origem de chave pública SSH**, selecione **carregar chave pública existente**. 

1. Cole o conteúdo completo da chave pública na **chave de carregamento** e, em seguida, selecione **revisar + criar**.

1. Depois de concluir a validação, selecione **Criar**. 

Depois que a chave for carregada, você poderá optar por usá-la ao criar uma VM.

## <a name="list-keys"></a>Listar chaves

As chaves SSH criadas no portal são armazenadas como recursos, de modo que você pode filtrar sua exibição de recursos para ver todas elas.

1. No portal, selecione **todos os recursos**.
1. Nos filtros, selecione **tipo**, desmarque a opção **selecionar tudo** para limpar a lista.
1. Digite **SSH** no filtro e selecione **chave SSH**.

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="Criar um novo grupo de recursos e gerar um par de chaves SSH":::

## <a name="get-the-public-key"></a>Obter a chave pública

Se você precisar de sua chave pública, poderá copiá-la facilmente da página do portal para a chave. Basta listar suas chaves (usando o processo na última seção) e, em seguida, selecionar uma chave na lista. A página da sua chave será aberta e você poderá clicar no ícone **Copiar na área de transferência** ao lado da chave para copiá-la.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar chaves SSH com VMs do Azure, confira [usar chaves SSH para se conectar a VMs Linux](./linux/ssh-from-windows.md).
