---
title: Preparar um VHD do Debian Linux
description: Saiba como criar imagens de VHD Debian para implantações de VM no Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: d822aa30926c473d08bc5d785211c82d038ef652
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203177"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Preparar um VHD do Debian para o Azure
## <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já instalou um sistema operacional Linux Debian a partir de um arquivo .iso baixado do [site do Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd; Hyper-V é apenas um exemplo. Para obter instruções sobre como usar a Hyper-V, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Notas de instalação
* Veja também as [Notas de Instalação Geral do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet **Convert-VHD** .
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) em discos de dados.
* Não configure uma partição de permuta no disco do SO. O agente Linux do Azure pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco não processado para VHD, certifique-se de que o tamanho do disco não processado seja um múltiplo de 1 MB antes da conversão. Para obter mais informações, consulte [Notas de Instalação do Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Usar o gerenciamento do Azure para criar VHDs Debian
Há ferramentas disponíveis para gerar VHDs de Debian para o Azure, como os scripts do [Azure-Manage](https://github.com/credativ/azure-manage) de [credativ](https://www.credativ.com/). Essa é a abordagem recomendada em vez de criar uma imagem do zero. Por exemplo, para criar um VHD no Debian 8, execute os seguintes comandos para baixar o `azure-manage` utilitário (e dependências) e executar o `azure_build_image` script:

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="manually-prepare-a-debian-vhd"></a>Preparar manualmente um VHD do Debian
1. No Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.
3. Se você instalou o sistema operacional usando um arquivo ISO, em seguida, comente qualquer linha relacionada a "`deb cdrom`" no `/etc/apt/source.list`.

4. Edite o arquivo `/etc/default/grub` e modifique o parâmetro **GRUB_CMDLINE_LINUX** da seguinte maneira para incluir parâmetros adicionais de kernel para o Azure.

    ```config-grub
    GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"
    ```

5. Recompile o grub e execute:

    ```console
    # sudo update-grub
    ```

6. Adicione repositórios do Azure de Debian ao/etc/apt/sources.List para o Debian 8, 9 ou 10:

    **Debian 8.x "Jessie"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian jessie main
    deb-src http://debian-archive.trafficmanager.net/debian jessie main
    deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
    deb http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb http://debian-archive.trafficmanager.net/debian jessie-backports main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
    ```

    **Debian 9 "Stretch"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian stretch main
    deb-src http://debian-archive.trafficmanager.net/debian stretch main
    deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-backports main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main
    ```
    
    **Debian 10. x "Buster"**
    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian buster main
    deb-src http://debian-archive.trafficmanager.net/debian buster main
    deb http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb http://debian-archive.trafficmanager.net/debian buster-updates main
    deb-src http://debian-archive.trafficmanager.net/debian buster-updates main
    deb http://debian-archive.trafficmanager.net/debian buster-backports main
    deb-src http://debian-archive.trafficmanager.net/debian buster-backports main
    ```

7. Instale o Agente Linux do Azure:

    ```console
    # sudo apt-get update
    # sudo apt-get install waagent
    ```

8. Para Debian 9 +, é recomendável usar o novo kernel do Debian Cloud para uso com as VMs no Azure. Para instalar esse novo kernel, primeiro crie um arquivo chamado /etc/apt/preferences.d/linux.pref com o conteúdo a seguir:

    ```config-pref
    Package: linux-* initramfs-tools
    Pin: release n=stretch-backports
    Pin-Priority: 500
    ```

    Em seguida, execute "sudo apt-get install linux-image-cloud-amd64" para instalar o novo kernel Debian Cloud.

9. Desprovisione a máquina virtual, prepare-a para provisionamento no Azure e execute:

    ```console
    # sudo waagent –force -deprovision
    # export HISTSIZE=0
    # logout
    ```

10. Clique em **ação** -> desligar no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para usar o disco rígido virtual Debian para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).