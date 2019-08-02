---
title: Criação e carregamento de um VHD do Red Hat Enterprise Linux para uso no Azure | Microsoft Docs
description: Saiba como criar e carregar um disco rígido virtual (VHD) do Microsoft Azure, que contém um sistema operacional Red Hat Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: szark
ms.openlocfilehash: f7f9baec5421a63e46536480cde8d60d3877d4d7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670968"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada no Red Hat para o Azure
Neste artigo, você aprenderá como preparar uma máquina virtual do Red Hat Enterprise Linux (RHEL) para usar no Azure. Neste artigo, abordamos as versões 6.7+ e 7.1+ do RHEL. Neste artigo, abordamos os seguintes hipervisores de preparação: Hyper-V, máquina virtual baseada em kernel (KVM) e VMware. Para saber mais informações sobre os requisitos de qualificação para participação no programa Red Hat Cloud Access, confira o [site Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e o artigo[Como executar o RHEL no Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Para obter maneiras de automatizar a criação de RHEL imagens consulte os [Azure Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual baseada em Red Hat a partir do Gerenciador do Hyper-V

### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já baixou um arquivo ISO do site do Red Hat e já instalou a imagem do RHEL em um disco rígido virtual (VHD). Para obter mais detalhes sobre como usar o Gerenciador do Hyper-V para instalar uma imagem do sistema operacional, confira [Como instalar a função Hyper-V e configurar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do RHEL**

* O Azure não oferece suporte para o formato VHDX. O Azure suporta apenas VHD fixo. Você pode usar o Gerenciador do Hyper-V para converter o disco em formato VHD, ou pode usar o cmdlet convert-vhd. Quando criar o disco, se você usar o VirtualBox, selecione **Tamanho fixo** em vez da opção padrão alocada dinamicamente.
* O Azure suporta somente as máquinas virtuais de geração 1. Você pode converter uma máquina virtual de geração 1 do formato de arquivo VHDX em VHD e de expansão dinâmica em um disco de tamanho fixo. Mas observe não é possível alterar a geração de uma máquina virtual. Para saber mais informações, confira [Devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* O tamanho máximo permitido para o VHD é 1.023 GB.
* Gerenciador de Volume lógico (LVM) tem suporte e pode ser usado no disco do sistema operacional ou discos de dados em máquinas virtuais do Azure. No entanto, em geral é recomendável usar partições padrão em disco do sistema operacional em vez de LVM. Essa prática evitará conflitos de nome LVM entre máquinas virtuais clonadas, especialmente se você precisar anexar um disco do sistema operacional em outra máquina virtual idêntica para solução de problemas. Consulte também [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) documentação.
* É necessário suporte de kernel para montar sistemas de arquivos de formato de disco universal (UDF). Na primeira inicialização no Azure, a configuração de provisionamento é transmitida à máquina virtual do Linux por meio de mídia formatada para UDF, a qual é anexada ao convidado. O agente de Linux do Azure deve ser capaz de montar o sistema de arquivos UDF para ler sua configuração e provisionar a máquina virtual.
* Não configure uma partição de permuta no disco do sistema operacional. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recurso temporário.  Verifique as etapas a seguir para obter mais informações sobre esse assunto.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, certifique-se de que o tamanho do disco seja um múltiplo de 1MB antes da conversão. Encontre mais detalhes nas etapas abaixo. Consulte também [Notas de Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual RHEL 6 do Gerenciador do Hyper-V

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

1. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

1. No RHEL 6, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote ao executar o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

        # sudo chkconfig network on

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta modificação, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração.
    
    Além disso, recomendamos que você remova os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.  Você pode deixar a opção `crashkernel` configurada se quiser. Observe que esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais. Essa configuração pode ser um problema em máquinas virtuais menores.


1. Confira se o servidor shell seguro (SSH) está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique o /etc/ssh/sshd_config para incluir a seguinte linha:

        ClientAliveInterval 180

1. Instale o Agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    A instalação do pacote WALinuxAgent removerá os pacotes NetworkManager e NetworkManager-gnome se eles já não tiverem sido removidos na etapa 3.

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente de Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local anexado à máquina virtual, depois da mesma ser provisionada no Azure. Observe que o disco de recurso local é um disco temporário e que pode ser esvaziado se a máquina virtual é desprovisionada. Depois de instalar o agente de Linux do Azure na etapa anterior, modifique os seguintes parâmetros em /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Clique em **Ação** > **Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual RHEL 7 do Gerenciador do Hyper-V

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

1. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

        # sudo systemctl enable network

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta modificação, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Essa configuração também desliga as novas convenções de nomenclatura do RHEL 7 para NICs. Além dos itens acima, recomendamos remover os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

1. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Confira se o servidor SSH está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o Agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente de Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local anexado à máquina virtual, depois da mesma ser provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado se a máquina virtual é desprovisionada. Depois de instalar o agente de Linux do Azure na etapa anterior, modifique os seguintes parâmetros em `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se você quiser cancelar o registro da assinatura, execute o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Clique em **Ação** > **Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparar uma máquina virtual baseada no Red Hat para KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Preparar uma máquina virtual RHEL 6 do KVM

1. Baixe a imagem KVM do RHEL 6 no site do Red Hat.

1. Definir uma senha raiz.

    Gere uma senha criptografada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma senha raiz com guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Altere o segundo campo do usuário raiz de "!!" para a senha criptografada.

1. Crie uma máquina virtual no KVM da imagem qcow2. Defina o tipo de disco como **qcow2** e defina o modelo de dispositivo do adaptador de rede virtual para **virtio**. Em seguida, inicie a máquina virtual e faça logon como raiz.

1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Azure ou no Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

        # chkconfig network on

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta configuração, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração.
    
    Além dos itens acima, recomendamos remover os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.


1. Adicione os módulos do Hyper-V em initramfs:  

    Edite `/etc/dracut.conf` e adicione o seguinte conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recrie initramfs:

        # dracut -f -v

1. Desinstale cloud-init:

        # yum remove cloud-init

1. Verifique se o servidor SSH está instalado e configurado para começar na hora da inicialização:

        # chkconfig sshd on

    Modifique o /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Instale o Agente Linux do Azure executando o seguinte comando:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. O agente de Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local anexado à máquina virtual, depois da mesma ser provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado se a máquina virtual é desprovisionada. Depois de instalar o agente de Linux do Azure na etapa anterior, modifique os seguintes parâmetros em **/etc/waagent.conf**:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

        # subscription-manager unregister

1. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Finalize a máquina virtual no KVM.

1. Converta a imagem qcow2 para o formato VHD.

> [!NOTE]
> Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar o qemu-img 2.2.0 ou inferior, ou atualizar para a versão 2.6 ou posterior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Preparar uma máquina virtual RHEL 7 do KVM

1. Baixe a imagem KVM do RHEL 7 no site do Red Hat. Este procedimento usa RHEL 7 como exemplo.

1. Definir uma senha raiz.

    Gere uma senha criptografada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma senha raiz com guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Altere o segundo campo do usuário raiz de "!!" para a senha criptografada.

1. Crie uma máquina virtual no KVM da imagem qcow2. Defina o tipo de disco como **qcow2** e defina o modelo de dispositivo do adaptador de rede virtual para **virtio**. Em seguida, inicie a máquina virtual e faça logon como raiz.

1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

        # sudo systemctl enable network

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer essa configuração, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Esse comando garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. O comando também desliga as novas convenções de nomenclatura do RHEL 7 para NICs. Além dos itens acima, recomendamos remover os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

1. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Adicione os módulos do Hyper-V em initramfs.

    Edite `/etc/dracut.conf` e adicione o conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recrie initramfs:

        # dracut -f -v

1. Desinstale cloud-init:

        # yum remove cloud-init

1. Verifique se o servidor SSH está instalado e configurado para começar na hora da inicialização:

        # systemctl enable sshd

    Modifique o /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o Agente Linux do Azure executando o seguinte comando:

        # yum install WALinuxAgent

    Habilite o serviço de waagent:

        # systemctl enable waagent.service

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente de Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local anexado à máquina virtual, depois da mesma ser provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado se a máquina virtual é desprovisionada. Depois de instalar o agente de Linux do Azure na etapa anterior, modifique os seguintes parâmetros em `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

        # subscription-manager unregister

1. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Finalize a máquina virtual no KVM.

1. Converta a imagem qcow2 para o formato VHD.

> [!NOTE]
> Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar o qemu-img 2.2.0 ou inferior, ou atualizar para a versão 2.6 ou posterior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparar uma máquina virtual baseada no Red Hat do VMware
### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já instalou uma máquina virtual RHEL no VMware. Para saber mais sobre como instalar um sistema operacional no VMware, confira [Guia de instalação do sistema operacional convidado VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Ao instalar o sistema operacional Linux, recomendamos usar partições-padrão em vez de LVM, que geralmente é o padrão para muitas instalações. Isso evitará conflitos de nome LVM entre a máquina virtual clonada, especialmente se um disco do sistema operacional precisar ser anexado a outra máquina virtual para solução de problemas. Se você preferir, é possível usar LVM ou RAID em discos de dados.
* Não configure uma partição de permuta no disco do sistema operacional. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Confira as etapas a seguir para obter mais informações sobre esse assunto.
* Ao criar o disco rígido virtual, escolha **Armazenar disco virtual como um único arquivo**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Preparar uma máquina virtual RHEL 6 do VMware
1. No RHEL 6, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote ao executar o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Crie um arquivo chamado **network** no diretório /etc/sysconfig/ que contém o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Azure ou no Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

        # sudo chkconfig network on

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Além dos itens acima, recomendamos remover os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

1. Adicione os módulos do Hyper-V em initramfs:

    Edite `/etc/dracut.conf` e adicione o seguinte conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recrie initramfs:

        # dracut -f -v

1. Confira se o servidor SSH está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ClientAliveInterval 180

1. Instale o Agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente de Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local anexado à máquina virtual, depois da mesma ser provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado se a máquina virtual é desprovisionada. Depois de instalar o agente de Linux do Azure na etapa anterior, modifique os seguintes parâmetros em `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual e, em seguida, converta o arquivo VMDK em um arquivo .vhd.

> [!NOTE]
> Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar o qemu-img 2.2.0 ou inferior, ou atualizar para a versão 2.6 ou posterior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Preparar uma máquina virtual RHEL 7 do VMware
1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

        # sudo systemctl enable network

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta modificação, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Essa configuração também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Ele também desativa novas convenções de nomenclatura do RHEL 7 para NICs. Além dos itens acima, recomendamos remover os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

1. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Adicione os módulos do Hyper-V em initramfs.

    Edite `/etc/dracut.conf`e adicione o conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recrie initramfs:

        # dracut -f -v

1. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Essa configuração geralmente é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o Agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente de Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local anexado à máquina virtual, depois da mesma ser provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado se a máquina virtual é desprovisionada. Depois de instalar o agente de Linux do Azure na etapa anterior, modifique os seguintes parâmetros em `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se você quiser cancelar o registro da assinatura, execute o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual e converta o arquivo VMDK no formato VHD.

> [!NOTE]
> Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar o qemu-img 2.2.0 ou inferior, ou atualizar para a versão 2.6 ou posterior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparar uma máquina virtual baseada em Red Hat de um arquivo ISO, usando um arquivo de início rápido automaticamente
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Preparar uma máquina virtual RHEL 7 de um arquivo de início rápido

1.  Crie um arquivo de início rápido que inclui o seguinte conteúdo e salve o arquivo. Para saber mais sobre a instalação de início rápido, consulte o [Guia de instalação de início rápido](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Coloque o arquivo de início rápido onde o sistema de instalação pode acessá-lo.

1. Crie uma nova máquina virtual no Gerenciador do Hyper-V. Na página **Conectar Disco Rígido Virtual**, selecione **Anexar um disco rígido virtual posteriormente** e conclua o Assistente de Nova Máquina Virtual.

1. Abra as configurações da máquina virtual:

    a.  Anexe um novo disco rígido virtual à máquina virtual. Selecione **Formato VHD** e **Tamanho Fixo**.

    b.  Anexe o ISO de instalação à unidade de DVD.

    c.  Configure o BIOS para inicializar do CD.

1. Iniciar a máquina virtual. Quando o guia de instalação for exibida, pressione **Tab** para configurar as opções de inicialização.

1. Insira `inst.ks=<the location of the kickstart file>` no final das opções de inicialização e pressione **Enter**.

1. Aguarde a conclusão da instalação. Quando a instalação for concluída, a máquina virtual desligará automaticamente. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>O driver do Hyper-V não foi incluído no disco de RAM inicial ao usar um hipervisor Hyper-V

Em alguns casos, os instaladores do Linux podem não incluir os drivers para Hyper-V no disco RAM inicial (initrd ou initramfs), a menos que ele detecte que está em execução em um ambiente Hyper-V.

Ao usar um sistema de virtualização diferente (como Virtualbox, Xen etc.) para preparar a imagem do Linux, talvez seja necessário recompilar o initrd para garantir que pelo menos os módulos do kernel hv_vmbus e hv_storvsc estejam disponíveis no disco RAM inicial. Esse já é um problema conhecido em sistemas com base em distribuição no Red Hat em upstream.

Para resolver esse problema, adicione os módulos do Hyper-V nos initramfs e os recompile:

Edite `/etc/dracut.conf` e adicione o seguinte conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Recrie initramfs:

        # dracut -f -v

Para obter mais detalhes, consulte as informações sobre [recriação de initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para usar o disco rígido virtual Red Hat Enterprise Linux para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

Para obter mais detalhes sobre os hipervisores certificados para execução do Red Hat Enterprise Linux, visite [o site da Red Hat](https://access.redhat.com/certified-hypervisors).
