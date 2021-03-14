---
title: Criar e carregar um VHD Linux do Red Hat Enterprise para uso no Azure
description: Saiba como criar e carregar um disco rígido virtual (VHD) do Microsoft Azure, que contém um sistema operacional Red Hat Linux.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: ff22010f12a7d76e60b629c4c1c4e24946d4af5a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552959"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada no Red Hat para o Azure

Neste artigo, você aprenderá como preparar uma máquina virtual do Red Hat Enterprise Linux (RHEL) para usar no Azure. Neste artigo, abordamos as versões 6.7+ e 7.1+ do RHEL. Neste artigo, abordamos os seguintes hipervisores de preparação: Hyper-V, máquina virtual baseada em kernel (KVM) e VMware. Para saber mais informações sobre os requisitos de qualificação para participação no programa Red Hat Cloud Access, confira o [site Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e o artigo[Como executar o RHEL no Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Para obter maneiras de automatizar a criação de imagens RHEL, consulte [Construtor de imagens do Azure](../image-builder-overview.md).

## <a name="hyper-v-manager"></a>Gerenciador do Hyper-V

Esta seção mostra como preparar uma máquina virtual [RHEL 6](#rhel-6-using-hyper-v-manager), [RHEL 7](#rhel-7-using-hyper-v-manager)ou [RHEL 8](#rhel-8-using-hyper-v-manager) usando o Gerenciador do Hyper-V.

### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já baixou um arquivo ISO do site do Red Hat e já instalou a imagem do RHEL em um disco rígido virtual (VHD). Para obter mais detalhes sobre como usar o Gerenciador do Hyper-V para instalar uma imagem do sistema operacional, confira [Como instalar a função Hyper-V e configurar uma máquina virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Notas de instalação do RHEL**

* O Azure não oferece suporte para o formato VHDX. O Azure suporta apenas VHD fixo. Você pode usar o Gerenciador do Hyper-V para converter o disco em formato VHD, ou pode usar o cmdlet convert-vhd. Quando criar o disco, se você usar o VirtualBox, selecione **Tamanho fixo** em vez da opção padrão alocada dinamicamente.
* O Azure dá suporte a máquinas virtuais Gen1 (inicialização do BIOS) & Gen2 (inicialização UEFI).
* O tamanho máximo permitido para o VHD é 1.023 GB.
* Gerenciador de Volume lógico (LVM) tem suporte e pode ser usado no disco do sistema operacional ou discos de dados em máquinas virtuais do Azure. No entanto, em geral é recomendável usar partições padrão em disco do sistema operacional em vez de LVM. Essa prática evitará conflitos de nome LVM entre máquinas virtuais clonadas, especialmente se você precisar anexar um disco do sistema operacional em outra máquina virtual idêntica para solução de problemas. Consulte também [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) e [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) documentação.
* **É necessário suporte de kernel para montar sistemas de arquivos de formato de disco universal (UDF)**. Na primeira inicialização no Azure, a configuração de provisionamento é transmitida à máquina virtual do Linux por meio de mídia formatada para UDF, a qual é anexada ao convidado. O agente Linux do Azure deve ser capaz de montar o sistema de arquivos UDF para ler sua configuração e provisionar a máquina virtual, sem isso, o provisionamento falhará!
* Não configure uma partição de permuta no disco do sistema operacional. Verifique as etapas a seguir para obter mais informações sobre esse assunto.

* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco não processado para VHD, certifique-se de que o tamanho do disco não processado seja um múltiplo de 1 MB antes da conversão. Encontre mais detalhes nas etapas abaixo. Consulte também [Notas de Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

### <a name="rhel-6-using-hyper-v-manager"></a>RHEL 6 usando o Gerenciador do Hyper-V

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

1. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

1. No RHEL 6, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote ao executar o seguinte comando:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

    ```console
    # sudo chkconfig network on
    ```

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta modificação, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração.
    
    Além disso, recomendamos que você remova os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
    
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.  Você pode deixar a opção `crashkernel` configurada se quiser. Observe que esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais. Essa configuração pode ser um problema em máquinas virtuais menores.


1. Confira se o servidor shell seguro (SSH) está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique o /etc/ssh/sshd_config para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. Instale o Agente Linux do Azure executando o seguinte comando:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

    A instalação do pacote WALinuxAgent removerá os pacotes NetworkManager e NetworkManager-gnome se eles já não tiverem sido removidos na etapa 3.

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente de Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local anexado à máquina virtual, depois da mesma ser provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o agente de Linux do Azure na etapa anterior, modifique os seguintes parâmetros em /etc/waagent.conf:

    ```config-cong
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```

1. Clique em **ação**  >  **desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.


### <a name="rhel-7-using-hyper-v-manager"></a>RHEL 7 usando o Gerenciador do Hyper-V

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

1. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

    ```console
    # sudo systemctl enable network
    ```

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta modificação, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:

    
    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1
    ```
   
    Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial e habilitam a interação com o console serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Essa configuração também desliga as novas convenções de nomenclatura do RHEL 7 para NICs.

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

1. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    > [!NOTE]
    > Se estiver carregando uma VM habilitada para UEFI, o comando para atualizar o grub será `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg` .

1. Confira se o servidor SSH está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Instale o agente Linux do Azure, o Cloud-init e outros utilitários necessários executando o seguinte comando:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Configure Cloud-init para lidar com o provisionamento:

    1. Configurar waagent para Cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Se você estiver migrando uma máquina virtual específica e não quiser criar uma imagem generalizada, defina `Provisioning.Agent=disabled` na `/etc/waagent.conf` configuração.
    
    1. Configurar montagens:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Configurar fonte de origem do Azure:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF
    ```

    1. Se configurado, remova o Swapfile existente:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Configurar o log de inicialização de nuvem:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. A configuração de permuta não cria o espaço de permuta no disco do sistema operacional.

    Anteriormente, o agente Linux do Azure foi usado para configurar automaticamente o espaço de permuta usando o disco de recurso local que está anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. No entanto, isso agora é manipulado por Cloud-init, você **não deve** usar o agente do Linux para formatar o disco de recursos criar o arquivo de permuta, modificar os seguintes parâmetros de forma `/etc/waagent.conf` apropriada:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
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
1. Se você quiser cancelar o registro da assinatura, execute o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Desprovisionar

    Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    > [!CAUTION]
    > Se você estiver migrando uma máquina virtual específica e não quiser criar uma imagem generalizada, ignore a etapa de desprovisionamento. A execução do comando `waagent -force -deprovision` tornará o computador de origem inutilizável; esta etapa destina-se apenas a criar uma imagem generalizada.
    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    

1. Clique em **ação**  >  **desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

### <a name="rhel-8-using-hyper-v-manager"></a>RHEL 8 usando o Gerenciador do Hyper-V

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

1. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

1. Verifique se o serviço Gerenciador de rede será iniciado no momento da inicialização executando o seguinte comando:

    ```console
    # sudo systemctl enable NetworkManager.service
    ```

1. Configure a interface de rede para iniciar automaticamente na inicialização e use DHCP:

    ```console
    # nmcli con mod eth0 connection.autoconnect yes ipv4.method auto
    ```


1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de inicialização do kernel na configuração do grub para incluir parâmetros de kernel adicionais para o Azure e habilite o console serial. 

    1. Remova os parâmetros GRUB atuais:
    ```console
    # grub2-editenv - unset kernelopts
    ```

    1. Edite `/etc/default/grub` em um editor de texto e adicione o seguinte parâmetros:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
    ```
   
   Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial e habilitam a interação com o console serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Essa configuração também desativa as novas convenções de nomenclatura para NICs.
   
   1. Além disso, recomendamos que você remova os seguintes parâmetros:

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

1. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    E para uma VM habilitada para UEFI, execute o seguinte comando:

    ```console
    # sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
    ```

1. Confira se o servidor SSH está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. Instale o agente Linux do Azure, o Cloud-init e outros utilitários necessários executando o seguinte comando:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Configure Cloud-init para lidar com o provisionamento:

    1. Configurar waagent para Cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Se você estiver migrando uma máquina virtual específica e não quiser criar uma imagem generalizada, defina `Provisioning.Agent=disabled` na `/etc/waagent.conf` configuração.
    
    1. Configurar montagens:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Configurar fonte de origem do Azure:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF
    ```

    1. Se configurado, remova o Swapfile existente:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Configurar o log de inicialização de nuvem:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. A configuração de permuta não cria o espaço de permuta no disco do sistema operacional.

    Anteriormente, o agente Linux do Azure foi usado para configurar automaticamente o espaço de permuta usando o disco de recurso local que está anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. No entanto, isso agora é manipulado por Cloud-init, você **não deve** usar o agente do Linux para formatar o disco de recursos criar o arquivo de permuta, modificar os seguintes parâmetros de forma `/etc/waagent.conf` apropriada:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
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
1. Se você quiser cancelar o registro da assinatura, execute o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Desprovisionar

    Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    > [!CAUTION]
    > Se você estiver migrando uma máquina virtual específica e não quiser criar uma imagem generalizada, ignore a etapa de desprovisionamento. A execução do comando `waagent -force -deprovision` tornará o computador de origem inutilizável; esta etapa destina-se apenas a criar uma imagem generalizada.


1. Clique em **ação**  >  **desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.


## <a name="kvm"></a>KVM

Esta seção mostra como usar o KVM para preparar um distribuição [RHEL 6](#rhel-6-using-kvm) ou [RHEL 7](#rhel-7-using-kvm) para carregar no Azure. 

### <a name="rhel-6-using-kvm"></a>RHEL 6 usando o KVM

1. Baixe a imagem KVM do RHEL 6 no site do Red Hat.

1. Definir uma senha raiz.

    Gere uma senha criptografada e copie a saída do comando:

    ```console
    # openssl passwd -1 changeme
    ```

    Defina uma senha raiz com guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Altere o segundo campo do usuário raiz de "!!" para a senha criptografada.

1. Crie uma máquina virtual no KVM da imagem qcow2. Defina o tipo de disco como **qcow2** e defina o modelo de dispositivo do adaptador de rede virtual para **virtio**. Em seguida, inicie a máquina virtual e faça logon como raiz.

1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Azure ou no Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

    ```console
    # chkconfig network on
    ```

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta configuração, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração.
    
    Além dos itens acima, recomendamos remover os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.


1. Adicione os módulos do Hyper-V em initramfs:  

    Edite `/etc/dracut.conf` e adicione o seguinte conteúdo:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Recrie initramfs:

    ```config-conf
    # dracut -f -v
    ```

1. Desinstale cloud-init:

    ```console
    # yum remove cloud-init
    ```

1. Verifique se o servidor SSH está instalado e configurado para começar na hora da inicialização:

    ```console
    # chkconfig sshd on
    ```

    Modifique o /etc/ssh/sshd_config para incluir as seguintes linhas:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Instale o Agente Linux do Azure executando o seguinte comando:

    ```console
    # yum install WALinuxAgent

    # chkconfig waagent on
    ```

1. O agente de Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local anexado à máquina virtual, depois da mesma ser provisionada no Azure. Observe que o disco de recurso local é um disco temporário e poderá ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o agente Linux do Azure na etapa anterior, modifique os seguintes parâmetros no **/etc/waagent.conf** adequadamente:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

    ```console-conf
    # subscription-manager unregister
    ```

1. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

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

1. Finalize a máquina virtual no KVM.

1. Converta a imagem qcow2 para o formato VHD.

    > [!NOTE]
    > Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar o qemu-img 2.2.0 ou inferior, ou atualizar para a versão 2.6 ou posterior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Primeiro, converta a imagem no formato bruto:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw
    ```

    Certifique-se de que o tamanho da imagem bruta é alinhado com 1 MB. Caso contrário, arredonde o tamanho para alinhar com 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Converta o disco bruto em um VHD de tamanho fixo:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Ou, com QEMU versão **2.6 +** inclua a `force_size` opção:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

        
### <a name="rhel-7-using-kvm"></a>RHEL 7 usando KVM

1. Baixe a imagem KVM do RHEL 7 no site do Red Hat. Este procedimento usa RHEL 7 como exemplo.

1. Definir uma senha raiz.

    Gere uma senha criptografada e copie a saída do comando:

    ```console
    # openssl passwd -1 changeme
    ```

    Defina uma senha raiz com guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Altere o segundo campo do usuário raiz de "!!" para a senha criptografada.

1. Crie uma máquina virtual no KVM da imagem qcow2. Defina o tipo de disco como **qcow2** e defina o modelo de dispositivo do adaptador de rede virtual para **virtio**. Em seguida, inicie a máquina virtual e faça logon como raiz.

1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

    ```console
    # sudo systemctl enable network
    ```

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer essa configuração, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Esse comando garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. O comando também desliga as novas convenções de nomenclatura do RHEL 7 para NICs. Além dos itens acima, recomendamos remover os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

1. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

    ```console
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Adicione os módulos do Hyper-V em initramfs.

    Edite `/etc/dracut.conf` e adicione o conteúdo:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Recrie initramfs:

    ```config-conf
    # dracut -f -v
    ```

1. Desinstale cloud-init:

    ```console
    # yum remove cloud-init
    ```

1. Verifique se o servidor SSH está instalado e configurado para começar na hora da inicialização:

    ```console
    # systemctl enable sshd
    ```

    Modifique o /etc/ssh/sshd_config para incluir as seguintes linhas:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

    ```config
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Instale o Agente Linux do Azure executando o seguinte comando:

    ```console
    # yum install WALinuxAgent
    ```

    Habilite o serviço de waagent:

    ```console
    # systemctl enable waagent.service
    ```

1. Instalar Cloud-init siga as etapas em "preparar uma máquina virtual RHEL 7 do Gerenciador do Hyper-V", etapa 12, "instalar Cloud-init para lidar com o provisionamento".

1. Alternar configuração 

    Não crie espaço de permuta no disco do sistema operacional.
    Siga as etapas em ' preparar uma máquina virtual RHEL 7 do Gerenciador do Hyper-V ', etapa 13, ' configuração de permuta '


1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

    ```console
    # subscription-manager unregister
    ```


1. Desprovisionar

    Siga as etapas em "preparar uma máquina virtual RHEL 7 do Gerenciador do Hyper-V", etapa 15, "desprovisionar"

1. Finalize a máquina virtual no KVM.

1. Converta a imagem qcow2 para o formato VHD.

    > [!NOTE]
    > Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar o qemu-img 2.2.0 ou inferior, ou atualizar para a versão 2.6 ou posterior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Primeiro, converta a imagem no formato bruto:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Certifique-se de que o tamanho da imagem bruta é alinhado com 1 MB. Caso contrário, arredonde o tamanho para alinhar com 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Converta o disco bruto em um VHD de tamanho fixo:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Ou, com QEMU versão **2.6 +** inclua a `force_size` opção:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="vmware"></a>VMware

Esta seção mostra como preparar um distribuição [RHEL 6](#rhel-6-using-vmware) ou [RHEL 7](#rhel-6-using-vmware)  do VMware.

### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já instalou uma máquina virtual RHEL no VMware. Para saber mais sobre como instalar um sistema operacional no VMware, confira [Guia de instalação do sistema operacional convidado VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Ao instalar o sistema operacional Linux, recomendamos usar partições-padrão em vez de LVM, que geralmente é o padrão para muitas instalações. Isso evitará conflitos de nome LVM entre a máquina virtual clonada, especialmente se um disco do sistema operacional precisar ser anexado a outra máquina virtual para solução de problemas. Se você preferir, é possível usar LVM ou RAID em discos de dados.
* Não configure uma partição de permuta no disco do sistema operacional. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Confira as etapas a seguir para obter mais informações sobre esse assunto.
* Ao criar o disco rígido virtual, escolha **Armazenar disco virtual como um único arquivo**.

### <a name="rhel-6-using-vmware"></a>RHEL 6 usando VMware
1. No RHEL 6, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote ao executar o seguinte comando:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Crie um arquivo chamado **network** no diretório /etc/sysconfig/ que contém o seguinte texto:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Azure ou no Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

    ```console
    # sudo chkconfig network on
    ```

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
    ```

   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Além dos itens acima, recomendamos remover os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
   
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

1. Adicione os módulos do Hyper-V em initramfs:

    Edite `/etc/dracut.conf` e adicione o seguinte conteúdo:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Recrie initramfs:

    ```config-cong
    # dracut -f -v
    ```

1. Confira se o servidor SSH está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. Instale o Agente Linux do Azure executando o seguinte comando:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente de Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local anexado à máquina virtual, depois da mesma ser provisionada no Azure. Observe que o disco de recurso local é um disco temporário e poderá ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o agente de Linux do Azure na etapa anterior, modifique os seguintes parâmetros em `/etc/waagent.conf`:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

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

1. Desligue a máquina virtual e, em seguida, converta o arquivo VMDK em um arquivo .vhd.

    > [!NOTE]
    > Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar o qemu-img 2.2.0 ou inferior, ou atualizar para a versão 2.6 ou posterior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Primeiro, converta a imagem no formato bruto:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw
    ```

    Certifique-se de que o tamanho da imagem bruta é alinhado com 1 MB. Caso contrário, arredonde o tamanho para alinhar com 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Converta o disco bruto em um VHD de tamanho fixo:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Ou, com QEMU versão **2.6 +** inclua a `force_size` opção:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

### <a name="rhel-7-using-vmware"></a>RHEL 7 usando VMware
1. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o texto a seguir:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o texto a seguir:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

    ```console
    # sudo systemctl enable network
    ```

1. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta modificação, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Essa configuração também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Ele também desativa novas convenções de nomenclatura do RHEL 7 para NICs. Além dos itens acima, recomendamos remover os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se quiser. Observe que este parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

1. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Adicione os módulos do Hyper-V em initramfs.

    Edite `/etc/dracut.conf`e adicione o conteúdo:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Recrie initramfs:

    ```console
    # dracut -f -v
    ```

1. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Essa configuração geralmente é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Instale o Agente Linux do Azure executando o seguinte comando:

    ```console
    # sudo yum install WALinuxAgent

    # sudo systemctl enable waagent.service
    ```

1. Instalar Cloud-init

    Siga as etapas em "preparar uma máquina virtual RHEL 7 do Gerenciador do Hyper-V", etapa 12, "instalar Cloud-init para lidar com o provisionamento".

1. Alternar configuração

    Não crie espaço de permuta no disco do sistema operacional.
    Siga as etapas em ' preparar uma máquina virtual RHEL 7 do Gerenciador do Hyper-V ', etapa 13, ' configuração de permuta '

1. Se você quiser cancelar o registro da assinatura, execute o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Desprovisionar

    Siga as etapas em "preparar uma máquina virtual RHEL 7 do Gerenciador do Hyper-V", etapa 15, "desprovisionar"


1. Desligue a máquina virtual e converta o arquivo VMDK no formato VHD.

    > [!NOTE]
    > Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar o qemu-img 2.2.0 ou inferior, ou atualizar para a versão 2.6 ou posterior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Primeiro, converta a imagem no formato bruto:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw
    ```

    Certifique-se de que o tamanho da imagem bruta é alinhado com 1 MB. Caso contrário, arredonde o tamanho para alinhar com 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Converta o disco bruto em um VHD de tamanho fixo:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Ou, com QEMU versão **2.6 +** inclua a `force_size` opção:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="kickstart-file"></a>Arquivo início rápido

Esta seção mostra como preparar um distribuição RHEL 7 de um ISO usando um arquivo início rápido.

### <a name="rhel-7-from-a-kickstart-file"></a>RHEL 7 de um arquivo início rápido

1.  Crie um arquivo de início rápido que inclui o seguinte conteúdo e salve o arquivo. Para saber mais sobre a instalação de início rápido, consulte o [Guia de instalação de início rápido](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```text
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

    # Install cloud-init
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    # Disable the root account
    usermod root -p '!!'

    # Disabke swap in WALinuxAgent
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n

    # Configure swap using cloud-init
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
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    EOF

    # Deprovision and prepare for Azure if you are creating a generalized image
    sudo cloud-init clean --logs --seed
    sudo rm -rf /var/lib/cloud/
    sudo rm -rf /var/lib/waagent/
    sudo rm -f /var/log/waagent.log

    sudo waagent -force -deprovision+user
    rm -f ~/.bash_history
    export HISTSIZE=0

    %end
    ```

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

Quando você estiver usando um sistema de virtualização diferente (ou seja, VirtualBox, Xen, etc.) para preparar sua imagem do Linux, talvez seja necessário recompilar a initrd para garantir que pelo menos os módulos de kernel hv_vmbus e hv_storvsc estejam disponíveis no disco RAM inicial. Esse já é um problema conhecido em sistemas com base em distribuição no Red Hat em upstream.

Para resolver esse problema, adicione os módulos do Hyper-V nos initramfs e os recompile:

Edite `/etc/dracut.conf` e adicione o seguinte conteúdo:

```config-conf
add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
```

Recrie initramfs:

```console
# dracut -f -v
```

Para obter mais detalhes, consulte as informações sobre [recriação de initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Próximas etapas
* Agora, você está pronto para usar o disco rígido virtual Red Hat Enterprise Linux para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
* Para obter mais detalhes sobre os hipervisores certificados para execução do Red Hat Enterprise Linux, visite [o site da Red Hat](https://access.redhat.com/certified-hypervisors).
* Para saber mais sobre como usar imagens BYOS do RHEL prontas para produção, vá para a página de documentação do [BYOS](../workloads/redhat/byos.md).
