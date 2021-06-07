---
title: Criar e carregar um VHD do Linux baseado em CentOS
description: Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Linux baseado em CentOS.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 4745e631bd92675f8dd1ef0d390baa88f7666552
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554659"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em CentOS para o Azure

Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Linux baseado em CentOS.

* [Preparar uma máquina virtual CentOS 6.x para o Azure](#centos-6x)
* [Preparar uma máquina virtual CentOS 7.0 ou posterior para o Azure](#centos-70)


## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já instalou um sistema operacional Linux CentOS (ou derivado similar) em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Notas de instalação do CentOS**

* Veja também as [Notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O formato VHDX não tem suporte no Azure, somente o **VHD fixo**.  Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd. Se você estiver usando o VirtualBox, isso significará selecionar **Tamanho fixo** em vez do padrão alocado dinamicamente durante a criação do disco.
* Ao instalar o sistema Linux, é *recomendável* que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM idêntica para solução de problemas. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) podem ser usados nos discos de dados.
* **É necessário suporte a kernel para montar sistemas de arquivos UDF.** Na primeira inicialização no Azure, a configuração de provisionamento é transmitida à VM do Linux por meio de mídia formatada para UDF, a qual é anexada ao convidado. O agente de Linux do Azure deve ser capaz de montar o sistema de arquivos UDF para ler sua configuração e provisionar a VM.
* Versões de kernel do Linux abaixo de 2.6.37 não dão suporte ao NUMA no Hyper-V com tamanhos maiores de VM. Esse problema afeta principalmente distribuições mais antigas usando kernel Red Hat 2.6.32 upstream e foi corrigido no RHEL 6.6 (kernel-2.6.32-504). Sistemas que executam kernels personalizados anteriores a 2.6.37 ou com base em RHEL anteriores a 2.6.32-504 devem definir o parâmetro de inicialização `numa=off` na linha de comando do kernel em grub.conf. Para obter mais informações, consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Não configure uma partição de permuta no disco do SO. Verifique as etapas a seguir para obter mais informações a esse respeito.
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

8. Se quiser usar os espelhos OpenLogic hospedados em datacenters do Azure, substitua o arquivo `/etc/yum.repos.d/CentOS-Base.repo` pelos repositórios a seguir.  Isso também adicionará o repositório **[openlogic]**, que inclui pacotes adicionais como o agente Linux do Azure:

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

    Como alternativa, você pode seguir as instruções de instalação manual [página de download do LIS](https://www.microsoft.com/download/details.aspx?id=55106) para instalar o RPM para sua VM.

12. Instale o agente Linux do Azure e as dependências. Iniciar e habilitar o serviço waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
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
    sudo waagent -force -deprovision+user
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

**Etapas de configuração**

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

10. Se estiver criando a imagem do **VMware, VirtualBox ou KVM:** Verifique se os drivers do Hyper-V estão incluídos no initramfs:

    Edite `/etc/dracut.conf`e adicione o conteúdo:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Recompile o initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Instale o agente Linux do Azure e as dependências para extensões de VM do Azure:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Instalar Cloud-init para lidar com o provisionamento

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```


13. A configuração de permuta não cria o espaço de permuta no disco do sistema operacional.

    Anteriormente, o agente Linux do Azure foi usado para configurar automaticamente o espaço de permuta usando o disco de recurso local que está anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. No entanto, isso agora é manipulado por Cloud-init, você **não deve** usar o agente do Linux para formatar o disco de recursos criar o arquivo de permuta, modificar os seguintes parâmetros de forma `/etc/waagent.conf` apropriada:

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Se você quiser montar, Formatar e criar a permuta, poderá:
    * Passe isso como uma configuração de Cloud-init toda vez que criar uma VM
    * Use uma diretiva Cloud-init inclusas na imagem que fará isso toda vez que a VM for criada:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

14. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history


    # export HISTSIZE=0

    # logout
    ```

15. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Próximas etapas

Agora, você está pronto para usar o disco rígido virtual CentOS Linux para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).