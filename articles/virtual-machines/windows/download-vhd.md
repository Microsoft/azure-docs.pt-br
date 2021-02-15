---
title: Baixar um VHD do Windows Azure
description: Baixe um VHD do Windows usando o portal do Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 5e6f7b461d205478d6a3fe3eecbbb933d0533ec9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200508"
---
# <a name="download-a-windows-vhd-from-azure"></a>Baixar um VHD do Windows Azure

Neste artigo, você aprenderá a fazer o download de um arquivo VHD (disco rígido virtual do Windows) do Azure usando o portal do Azure.

## <a name="optional-generalize-the-vm"></a>Opcional: generalizar a VM

Se você quiser usar o VHD como uma [imagem](tutorial-custom-images.md) para criar outras VMS, deverá usar o [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) para generalizar o sistema operacional. 

Para usar o VHD como uma imagem para criar outras VMs, generalizar a VM.

1. Se você ainda não fez isso, entre no [portal do Azure](https://portal.azure.com/).
2. [Conecte-se à VM](connect-logon.md). 
3. Na VM, abra uma janela de prompt de comando como administrador.
4. Altere o diretório para *%windir%\system32\sysprep* e execute sysprep.exe.
5. Na caixa de diálogo Ferramenta de Preparação do Sistema, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a opção **Generalizar** está marcada.
6. Em Opções de Desligamento, selecione **Desligar** e **OK**. 


## <a name="stop-the-vm"></a>Pare a VM.

Não é possível baixar um VHD por meio do Azure se ele estiver anexado a uma VM em execução. Você precisa parar a VM para baixar um VHD. 

1. No menu Hub no portal do Azure, clique em **Máquinas Virtuais**.
1. Selecione a VM na lista.
1. Na folha da VM, clique em **Parar**.


## <a name="generate-download-url"></a>Gerar URL de download

Para baixar o arquivo VHD, você precisa gerar uma URL de [SAS (assinatura de acesso compartilhado)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json). Quando a URL é gerada, uma hora de expiração é atribuída à URL.

1. Na página da VM, clique em **discos** no menu à esquerda.
1. Selecione o disco do sistema operacional para a VM.
1. Na página do disco, selecione exportação de **disco** no menu à esquerda.
1. O tempo de expiração padrão da URL é de *3600* segundos. Aumente isso para **36000** para discos do sistema operacional Windows.
1. Clique em **Gerar URL**.

> [!NOTE]
> O tempo de expiração é aumentado de padrão a fim de fornecer tempo suficiente para baixar o arquivo VHD grande para um sistema operacional Windows Server. Você pode esperar que um arquivo VHD que contém o sistema operacional Windows Server leve várias horas para ser baixado, dependendo da conexão. Se você estiver baixando um VHD para um disco de dados, a hora padrão será suficiente. 
> 
> 

## <a name="download-vhd"></a>Baixar o VHD

1. Na URL que foi gerada, clique em Baixar o arquivo VHD.
1. Talvez seja necessário clicar em **salvar** no navegador para iniciar o download. O nome padrão do arquivo VHD é *abcd*.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [carregar um arquivo VHD no Azure](upload-generalized-managed.md). 
- [Crie discos gerenciados de discos não gerenciados em uma conta de armazenamento](attach-disk-ps.md).
- [Gerenciar discos do Azure com o PowerShell](tutorial-manage-data-disk.md).
