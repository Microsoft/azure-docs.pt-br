---
title: Criar e carregar um VHD do SUSE Linux no Azure
description: Saiba como criar e carregar um disco rígido virtual (VHD) do Azure que contém o sistema operacional SUSE Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 3d6a981db93cd84f0dbe5ab229ba1e90ee0bd1e7
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200729"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparar uma máquina virtual do SLES ou openSUSE para o Azure


Este artigo pressupõe que você já instalou um sistema operacional SUSE ou openSUSE Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-installation-notes"></a>Notas de instalação do SLES / openSUSE
* Veja também as [Notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O formato VHDX não tem suporte no Azure, somente o **VHD fixo**.  Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) em discos de dados.
* Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários.  Verifique as etapas a seguir para obter mais informações a esse respeito.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco não processado para VHD, certifique-se de que o tamanho do disco não processado seja um múltiplo de 1 MB antes da conversão. Consulte [Notas de Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="use-suse-studio"></a>Use o SUSE Studio
[SUSE Studio](https://studioexpress.opensuse.org/) pode criar e gerenciar facilmente suas imagens SLES e openSUSE no Azure e no Hyper-V. Essa é a abordagem recomendada para personalizar suas próprias imagens SLES e openSUSE.

Como uma alternativa para compilar seu próprio VHD, o SUSE também publica imagens BYOS (traga sua própria assinatura) para SLES no [depósito de VM](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Preparar SUSE Linux Enterprise Server para o Azure
1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela da máquina virtual.
3. Registre seu sistema SUSE Linux Enterprise para permitir baixar atualizações e instalar pacotes.
4. Atualize o sistema com os patches mais recentes:

    ```console
    # sudo zypper update
    ```
    
5. Instalar o agente Linux do Azure e a nuvem-init

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. Habilitar waagent & Cloud-init para iniciar na inicialização

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. Atualizar waagent e configuração de inicialização de nuvem

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Edite o arquivo/etc/default/grub para garantir que os logs do console sejam enviados para a porta serial e, em seguida, atualize o arquivo de configuração principal com Grub2-mkconfig-o/boot/Grub2/grub.cfg

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.
    
9. Verifique se o arquivo/etc/fstab faz referência ao disco usando seu UUID (por UUID)
         
10. Modifique as regras de udev para evitar a geração de regras estáticas das interfaces Ethernet. Essas regras podem provocar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. É recomendável editar o arquivo "/etc/sysconfig/network/dhcp" e alterar o parâmetro `DHCLIENT_SET_HOSTNAME` para o seguinte:

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. Em "/etc/sudoers", exclua o comentário ou remova as seguintes linhas, se estiverem presentes:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão.

14. Alternar configuração
 
    Não crie espaço de permuta no disco do sistema operacional.

    Anteriormente, o agente Linux do Azure foi usado para configurar automaticamente o espaço de permuta usando o disco de recurso local que está anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. No entanto, isso agora é manipulado por Cloud-init, você **não deve** usar o agente do Linux para formatar o disco de recursos criar o arquivo de permuta, modificar os seguintes parâmetros de forma `/etc/waagent.conf` apropriada:

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Se você quiser montar, Formatar e criar a permuta, poderá:
    * Passe isso como uma configuração de Cloud-init sempre que criar uma VM.
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

15. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

---
## <a name="prepare-opensuse-131"></a>Preparar o openSUSE 13.1+
1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela da máquina virtual.
3. No shell, execute o comando '`zypper lr`'. Se este comando retornar uma saída semelhante à seguinte, os repositórios estarão configurados conforme o esperado – nenhum ajuste é necessário (observe que os números de versão podem variar):

   | # | Alias                 | Name                  | Habilitada | Atualizar
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Nuvem: Tools_13.1      | Nuvem: Tools_13.1      | Sim     | Sim
   | 2 | openSUSE_13 openSUSE_13.1_OSS     | openSUSE_13 openSUSE_13.1_OSS     | Sim     | Sim
   | 3 | openSUSE_13 openSUSE_13.1_Updates | openSUSE_13 openSUSE_13.1_Updates | Sim     | Sim

    Se o comando retornar "Nenhum repositório definido...", use os seguintes comandos para adicionar esses repositórios:

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    Em seguida, você pode verificar se os repositórios foram adicionados executando novamente o comando '`zypper lr`'. Caso um dos repositórios de atualização relevantes não esteja habilitado, habilite-o com o comando a seguir:

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. Atualize o kernel para a versão mais recente disponível:

    ```console
    # sudo zypper up kernel-default
    ```

    Ou para atualizar o sistema com todos os patches mais recentes:

    ```console
    # sudo zypper update
    ```

5. Instale o Agente Linux do Azure.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Além disso, remova os seguintes parâmetros da linha de inicialização do kernel, se existirem:

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. É recomendável editar o arquivo "/etc/sysconfig/network/dhcp" e alterar o parâmetro `DHCLIENT_SET_HOSTNAME` para o seguinte:

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Importante:** Em "/etc/sudoers", exclua o comentário ou remova as seguintes linhas, se estiverem presentes:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.
10. Não crie espaço de permuta no disco do SO.

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Verifique se o Agente Linux do Azure é executado durante a inicialização:

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para usar o disco rígido virtual SUSE Linux para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).