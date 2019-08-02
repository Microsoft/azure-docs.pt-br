---
title: Criar uma VM do Windows de um VHD especializado no portal do Azure | Microsoft Docs
description: Crie uma nova VM do Windows em um VHD no portal do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: cadd4b16ab111f46e49429c6d99e0e692325b3b1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718944"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Crie uma VM a partir de um VHD usando o portal do Azure

Existem várias maneiras de criar uma máquina virtual (VM) no Azure: 

- Se você já tiver um disco rígido virtual (VHD) para usar ou quiser copiar o VHD de uma VM existente para usar, crie uma nova VM, *anexando* o VHD à nova VM como um sistema operacional disco. 

- Você pode criar uma nova VM do VHD de uma VM que foi excluído. Por exemplo, se você tiver uma VM do Azure que não está funcionando corretamente, poderá excluir a VM e usar seu VHD para criar uma nova VM. Você pode reutilizar o mesmo VHD ou criar uma cópia do VHD criando um instantâneo e, em seguida, criando um novo disco gerenciado a partir do instantâneo. Embora a criação de um instantâneo precise de mais algumas etapas, ele preserva o VHD original e fornece um retorno.

- Use uma VM clássica e use o VHD para criar uma VM que usa o modelo de implantação do Resource Manager e discos gerenciados. Para obter melhores resultados, **pare** a VM clássica no portal do Azure antes de criar o instantâneo.
 
- Você pode criar uma VM do Azure a partir de um VHD local fazendo o upload do VHD local e anexando-o a uma nova VM. Você usa o PowerShell ou outra ferramenta para carregar o VHD em uma conta de armazenamento e, em seguida, cria um disco gerenciado a partir do VHD. Para obter mais informações, consulte [carregar um VHD especializado](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Não use um disco especializado, se você quiser criar várias VMs. Em vez disso, para implantações maiores, [criar uma imagem](capture-image-resource.md) e, em seguida [usar essa imagem para criar várias VMs](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Copiar um disco

Crie um instantâneo e crie um disco a partir do instantâneo. Essa estratégia permite que você mantenha o VHD original como um substituto:

1. No [Portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **Todos os serviços**.
2. No **todos os serviços** caixa de pesquisa, digite **discos** e, em seguida, selecione **discos** para exibir a lista de discos disponíveis.
3. Selecione o disco que você deseja usar. O **disco** página para que o disco é exibido.
4. No menu na parte superior, selecione **criar snapshot**. 
5. Insira um **Nome** para o instantâneo.
6. Escolha um **Grupo de Recursos** para o instantâneo. Você pode usar um grupo de recursos existente ou criar um novo.
7. Para **Tipo de conta**, escolha entre **Armazenamento padrão (HDD)** ou **Premium (SSD)** .
8. Quando terminar, selecione **criar** para criar o instantâneo.
9. Depois que o instantâneo foi criado, selecione **criar um recurso** no menu à esquerda.
10. Na caixa de pesquisa, digite **disco gerenciado** e, em seguida, selecione **Managed Disks** na lista.
11. Sobre a página **Managed Disks**, selecione **criar**.
12. Insira um **nome** para o disco.
13. Escolha um **Grupo de Recursos** para o disco. Você pode usar um grupo de recursos existente ou criar um novo. Essa seleção também será usada como o grupo de recursos no qual você cria a VM a partir do disco.
14. Para **Tipo de conta**, escolha entre **Armazenamento padrão (HDD)** ou **Premium (SSD)** .
15. Em **tipo de fonte**, verifique se **snapshot** está selecionado.
16. Na lista suspensa **Fonte instantâneo**, selecione o instantâneo que você deseja usar.
17. Faça quaisquer outros ajustes conforme necessário e, em seguida, selecione **criar** para criar o disco.

## <a name="create-a-vm-from-a-disk"></a>Criar uma VM de um disco

Depois de ter o VHD de disco gerenciado que você deseja usar, você pode criar a VM no portal:

1. No [Portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **Todos os serviços**.
2. No **todos os serviços** caixa de pesquisa, digite **discos** e, em seguida, selecione **discos** para exibir a lista de discos disponíveis.
3. Selecione o disco que você deseja usar. O **disco** página esse disco é aberto.
4. Na página **Visão geral**, verifique se **DISK STATE** está listado como **Desmarcado**. Se não estiver, talvez seja necessário desanexar o disco da VM ou excluir a VM para liberar o disco.
4. No menu na parte superior da página, selecione **Criar VM**.
5. Na página **Básico** da nova VM, insira um **Nome da máquina virtual** e selecione um grupo de recursos **existente** ou crie um novo.
6. Para **Tamanho**, selecione **Alterar tamanho** para acessar a página **Tamanho**.
7. Selecione uma linha do tamanho VM e, em seguida, escolha **selecionar**.
8. Na página **Rede**, você pode deixar o portal criar todos os novos recursos ou selecionar uma **rede virtual** e **grupo de segurança de rede** existente. O portal sempre cria uma nova interface de rede e o endereço IP público para a nova VM. 
9. Na página **Gerenciamento**, faça as alterações nas opções de monitoramento.
10. Na página **Guest config**, adicione as extensões conforme necessário.
11. Quando terminar, selecione **Review + create**. 
12. Se a configuração da VM passar na validação, selecione **criar** para iniciar a implantação.

## <a name="next-steps"></a>Próximas etapas

Você também pode usar o PowerShell para [carregar um VHD no Azure e criar uma VM especializada](create-vm-specialized.md).


