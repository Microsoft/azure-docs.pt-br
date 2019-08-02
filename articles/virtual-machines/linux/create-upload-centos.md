---
title: Criar e carregar um VHD Linux baseado em CentOS no Azure
description: Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Linux baseado em CentOS.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: szark
ms.openlocfilehash: 72ed518af579bb6b95d3b13400f2fbf6679cd036
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248184"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em CentOS para o Azure

Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Linux baseado em CentOS.

* [Preparar uma máquina virtual CentOS 6.x para o Azure](#centos-6x)
* [Preparar uma máquina virtual CentOS 7.0 ou posterior para o Azure](#centos-70)


## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já instalou um sistema operacional Linux CentOS (ou derivado similar) em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do CentOS**

* Veja também [Notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O formato VHDX não tem suporte no Azure, somente o **VHD fixo**.  Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd. Se você estiver usando o VirtualBox, isso significará selecionar **Tamanho fixo** em vez do padrão alocado dinamicamente durante a criação do disco.
* Ao instalar o sistema Linux, é *recomendável* que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM idêntica para solução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser usados nos discos de dados.
* É necessário suporte a kernel para montar sistemas de arquivos UDF. Na primeira inicialização no Azure, a configuração de provisionamento é transmitida à VM do Linux por meio de mídia formatada para UDF, a qual é anexada ao convidado. O agente de Linux do Azure deve ser capaz de montar o sistema de arquivos UDF para ler sua configuração e provisionar a VM.
* Versões de kernel do Linux abaixo de 2.6.37 não dão suporte ao NUMA no Hyper-V com tamanhos maiores de VM. Esse problema afeta principalmente distribuições mais antigas usando kernel Red Hat 2.6.32 upstream e foi corrigido no RHEL 6.6 (kernel-2.6.32-504). Sistemas que executam kernels personalizados anteriores a 2.6.37 ou com base em RHEL anteriores a 2.6.32-504 devem definir o parâmetro de inicialização `numa=off` na linha de comando do kernel em grub.conf. Para obter mais informações, confira o [KB 436883](https://access.redhat.com/solutions/436883) do Red Hat.
* Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários.  Verifique as etapas a seguir para obter mais informações a esse respeito.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco não processado para VHD, certifique-se de que o tamanho do disco não processado seja um múltiplo de 1 MB antes da conversão. Consulte [Notas de Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="centos-6x"></a>CentOS 6.x

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

3. No CentOS 6, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote ao executar o seguinte comando:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Modifique as regras de udev para evitar a geração de regras estáticas das interfaces Ethernet. Essas regras podem provocar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:

    ```bash
    sudo chkconfig network on
    ```

8. Se quiser usar os espelhos OpenLogic hospedados em datacenters do Azure, substitua o arquivo `/etc/yum.repos.d/CentOS-Base.repo` pelos repositórios a seguir.  Isso também adicionará o repositório **[openlogic]** , que inclui pacotes adicionais como o agente Linux do Azure:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > O restante deste guia parte do pressuposto de que você esteja usando, no mínimo, o repositório `[openlogic]`, que será usado para instalar o agente Linux do Azure abaixo.

9. Adicione a seguinte linha a /etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Execute o seguinte comando para limpar os metadados atuais do yum e atualizar o sistema com os pacotes mais recentes:

    ```bash
    yum clean all
    ```

    A menos que você esteja criando uma imagem para uma versão anterior do CentOS, é recomendável atualizar todos os pacotes para a versão mais recente:

    ```bash
    sudo yum -y update
    ```

    Pode ser necessária uma reinicialização depois de executar esse comando.

11. (Opcional) Instale os drivers dos LIS (Serviços de Integração do Linux).

    > [!IMPORTANT]
    > A etapa é **necessária** para CentOS 6.3 e anteriores e opcionais para versões posteriores.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Como alternativa, você pode seguir as instruções de instalação manual [página de download do LIS](https://go.microsoft.com/fwlink/?linkid=403033) para instalar o RPM para sua VM.

12. Instale o agente Linux do Azure e as dependências:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    ```

    A instalação do pacote WALinuxAgent removerá o NetworkManager e os pacotes NetworkManager-gnome se eles já não tiverem sido removidos conforme descrito na etapa 3.

13. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.

    Além disso, recomendamos que você *remova* os seguintes parâmetros:

    ```console
    rhgb quiet crashkernel=auto
    ```

    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.  Você pode deixar configurada a opção `crashkernel` , mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

    > [!Important]
    > CentOS 6.5 e anteriores também devem definir o parâmetro de kernel `numa=off`. Consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.

15. Não crie espaço de permuta no disco do SO.

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (confira a etapa anterior), modifique adequadamente os seguintes parâmetros em `/etc/waagent.conf` :

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.



## <a name="centos-70"></a>CentOS 7.0+

**Alterações no CentOS 7 (e em derivativos similares)**

A preparação de uma máquina virtual CentOS 7 para o Azure é muito parecida com a preparação das máquinas virtuais CentOS 6, mas há diversas diferenças que merecem atenção:

* O pacote do NetworkManager não entra mais em conflito com o agente Linux do Azure. Esse pacote é instalado por padrão e recomendamos que você não o remova.
* O GRUB2 agora é usado como carregador de inicialização padrão. Com isso, o procedimento de edição de parâmetros do kernel mudou (confira abaixo).
* O XFS agora é o sistema de arquivos padrão. Ainda é possível usar o sistema de arquivos ext4 se você preferir.

**Etapas da configuração**

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

3. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Modifique as regras de udev para evitar a geração de regras estáticas das interfaces Ethernet. Essas regras podem provocar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Se quiser usar os espelhos OpenLogic hospedados em datacenters do Azure, substitua o arquivo `/etc/yum.repos.d/CentOS-Base.repo` pelos repositórios a seguir.  Essa ação adiciona o repositório **[openlogic]** que inclui pacotes para o agente Linux do Azure:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > O restante deste guia parte do pressuposto de que você esteja usando, no mínimo, o repositório `[openlogic]`, que será usado para instalar o agente Linux do Azure abaixo.

7. Execute o comando a seguir para limpar os metadados atuais do yum e instalar atualizações:

    ```bash
    sudo yum clean all
    ```

    A menos que você esteja criando uma imagem para uma versão anterior do CentOS, é recomendável atualizar todos os pacotes para a versão mais recente:

    ```bash
    sudo yum -y update
    ```

    Uma reinicialização necessária talvez depois de executar esse comando.

8. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`, por exemplo:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Ele também desativa novas convenções de nomenclatura do CentOS 7 para NICs. Além disso, recomendamos que você *remova* os seguintes parâmetros:

    ```console
    rhgb quiet crashkernel=auto
    ```

    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar configurada a opção `crashkernel` , mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

9. Depois de editar `/etc/default/grub` como mostrado acima, execute o comando a seguir para recompilar a configuração do grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Se a criação da imagem de **VMware, VirtualBox ou KVM:** Certifique-se de que os drivers Hyper-V estão incluídos no initramfs:

    Edite `/etc/dracut.conf`e adicione o conteúdo:

    ```console
    add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
    ```

    Recompile o initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Instale o agente Linux do Azure e as dependências:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Não crie espaço de permuta no disco do SO.

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (confira a etapa anterior), modifique adequadamente os seguintes parâmetros em `/etc/waagent.conf` :

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Próximas etapas

Agora, você está pronto para usar o disco rígido virtual CentOS Linux para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
