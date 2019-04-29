---
title: Preparar um VHD do Debian Linux no Azure | Microsoft Docs
description: Aprenda a criar arquivos imagens VHD Debian para implantação no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: 6ef0a9f6efbf5f8398ba242150b2eb6102875f7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799289"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Preparar um VHD do Debian para o Azure
## <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já instalou um sistema operacional Linux Debian a partir de um arquivo .iso baixado do [site do Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd; Hyper-V é apenas um exemplo. Para obter instruções sobre como usar a Hyper-V, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Notas de instalação
* Veja também as [Notas de Instalação Geral do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet **convert-vhd** .
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) em discos de dados.
* Não configure uma partição de permuta no disco do SO. O agente Linux do Azure pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco não processado para VHD, certifique-se de que o tamanho do disco não processado seja um múltiplo de 1 MB antes da conversão. Para obter mais informações, consulte [Notas de Instalação do Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Usar o gerenciamento do Azure para criar VHDs Debian
Existem ferramentas disponíveis para gerar VHDs Debian para o Azure, como os scripts [azure-manage](https://github.com/credativ/azure-manage) do [Credativ](https://www.credativ.com/). Essa é a abordagem recomendada em vez de criar uma imagem do zero. Por exemplo, para criar um VHD no Debian 8, execute os seguintes comandos para baixar o `azure-manage` utilitário (e dependências) e executar o `azure_build_image` script:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Preparar manualmente um VHD do Debian
1. No Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.
3. Se você instalou o sistema operacional usando um arquivo ISO, em seguida, comente qualquer linha relacionada a "`deb cdrom`" no `/etc/apt/source.list`.

4. Edite o arquivo `/etc/default/grub` e modifique o parâmetro **GRUB_CMDLINE_LINUX** da seguinte maneira para incluir parâmetros adicionais de kernel para o Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Recompile o grub e execute:

        # sudo update-grub

6. Adicione repositórios do Azure do Debian a /etc/apt/sources.list para Debian 8 ou 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9 "Stretch"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Instale o Agente Linux do Azure:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Para Debian 9 +, é recomendável usar o novo kernel do Debian Cloud para uso com as VMs no Azure. Para instalar esse novo kernel, primeiro crie um arquivo chamado /etc/apt/preferences.d/linux.pref com o conteúdo a seguir:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Em seguida, execute "sudo apt-get install linux-image-cloud-amd64" para instalar o novo kernel Debian Cloud.

9. Desprovisione a máquina virtual, prepare-a para provisionamento no Azure e execute:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Clique em **Ação** -> Desligar no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para usar o disco rígido virtual Debian para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

