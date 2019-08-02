---
title: Criar e carregar um VHD do SUSE Linux no Azure
description: Saiba como criar e carregar um disco rígido virtual (VHD) do Azure que contém o sistema operacional SUSE Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 05731acd5e808075145c50281063e8990129d882
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708682"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparar uma máquina virtual do SLES ou openSUSE para o Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já instalou um sistema operacional SUSE ou openSUSE Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>Notas de instalação do SLES / openSUSE
* Veja também [Notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O formato VHDX não tem suporte no Azure, somente o **VHD fixo**.  Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) em discos de dados.
* Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários.  Verifique as etapas a seguir para obter mais informações a esse respeito.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco não processado para VHD, certifique-se de que o tamanho do disco não processado seja um múltiplo de 1 MB antes da conversão. Consulte [Notas de Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="use-suse-studio"></a>Use o SUSE Studio
[SUSE Studio](http://www.susestudio.com) pode criar e gerenciar facilmente suas imagens SLES e openSUSE no Azure e no Hyper-V. Essa é a abordagem recomendada para personalizar suas próprias imagens SLES e openSUSE.

Como alternativa à criação de seu próprio VHD, o SUSE também publica imagens BYOS (Traga sua Própria Assinatura) para SLES no [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Preparar o SUSE Linux Enterprise Server 11 SP4
1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela da máquina virtual.
3. Registre seu sistema SUSE Linux Enterprise para permitir baixar atualizações e instalar pacotes.
4. Atualize o sistema com os patches mais recentes:
   
        # sudo zypper update
5. Instale o agente Linux do Azure do repositório SLES:
   
        # sudo zypper install python-azure-agent
6. Verifique se waagent é definido como "on" em chkconfig e, se não for, habilite-o para iniciar automaticamente:
   
        # sudo chkconfig waagent on
7. Verifique se o serviço de waagent está sendo executado e se não, inicie-o: 
   
        # sudo service waagent start
8. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.
9. Confirme que /boot/grub/menu.lst e /etc/fstab fazem referência ao disco usando o UUID (by-uuid) em vez da ID do disco (by-id). 
   
    Obter o UUID do disco
   
        # ls /dev/disk/by-uuid/
   
    Se /dev/disk/by-id for usado, atualize /boot/grub/menu.lst e /etc/fstab com o valor adequado by-uuid
   
    Antes da alteração
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Depois da alteração
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Modifique as regras de udev para evitar a geração de regras estáticas das interfaces Ethernet. Essas regras podem provocar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. É recomendável editar o arquivo "/etc/sysconfig/network/dhcp" e alterar o parâmetro `DHCLIENT_SET_HOSTNAME` para o seguinte:
    
     DHCLIENT_SET_HOSTNAME="no"
12. Em "/etc/sudoers", exclua o comentário ou remova as seguintes linhas, se estiverem presentes:
    
     Defaults targetpw   # solicitar a senha do usuário de destino, ou seja, raiz ALL    ALL=(ALL) ALL   # WARNING! Deve ser usado somente em conjunto com 'Defaults targetpw'!
13. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.
14. Não crie espaço de permuta no disco do SO.
    
    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
15. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

---
## <a name="prepare-opensuse-131"></a>Preparar o openSUSE 13.1+
1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela da máquina virtual.
3. No shell, execute o comando '`zypper lr`'. Se este comando retornar uma saída semelhante à seguinte, os repositórios estarão configurados conforme o esperado – nenhum ajuste é necessário (observe que os números de versão podem variar):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Se o comando retornar "Nenhum repositório definido...", use os seguintes comandos para adicionar esses repositórios:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Em seguida, você pode verificar se os repositórios foram adicionados executando novamente o comando '`zypper lr`'. Caso um dos repositórios de atualização relevantes não esteja habilitado, habilite-o com o comando a seguir:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Atualize o kernel para a versão mais recente disponível:
   
        # sudo zypper up kernel-default
   
    Ou para atualizar o sistema com todos os patches mais recentes:
   
        # sudo zypper update
5. Instale o Agente Linux do Azure.
   
        # sudo zypper install WALinuxAgent
6. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Além disso, remova os seguintes parâmetros da linha de inicialização do kernel, se existirem:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. É recomendável editar o arquivo "/etc/sysconfig/network/dhcp" e alterar o parâmetro `DHCLIENT_SET_HOSTNAME` para o seguinte:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Importante:** Em "/etc/sudoers", exclua o comentário ou remova as seguintes linhas, se estiverem presentes:
   
     Defaults targetpw   # solicitar a senha do usuário de destino, ou seja, raiz ALL    ALL=(ALL) ALL   # WARNING! Deve ser usado somente em conjunto com 'Defaults targetpw'!
9. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.
10. Não crie espaço de permuta no disco do SO.
    
    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
11. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Verifique se o Agente Linux do Azure é executado durante a inicialização:
    
        # sudo systemctl enable waagent.service
13. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para usar o disco rígido virtual SUSE Linux para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
