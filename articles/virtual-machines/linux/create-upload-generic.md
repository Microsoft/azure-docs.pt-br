---
title: Criar e carregar um VHD Linux no Azure
description: "Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: ccadf55c492c097ef96f25e469dbf36fc87b6102
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="information-for-non-endorsed-distributions"></a>Informações para as distribuições não endossadas
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

O SLA (contrato de nível de serviço) da plataforma do Azure aplica-se a máquinas virtuais com o sistema operacional Linux somente quando uma das [distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) é usada com os detalhes da configuração especificados neste artigo. Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.

* [Linux no Azure - Distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Suporte para imagens Linux no Microsoft Azure](https://support.microsoft.com/kb/2941892)

Todas as distribuições em execução no Azure precisam atender a diversos pré-requisitos para ter a chance de serem executadas corretamente na plataforma.  Este artigo não é conclusivo, já que cada distribuição é diferente. Dessa forma, mesmo que você atenda a todos os critérios abaixo, talvez seja necessário ajustar significativamente o seu sistema Linux para garantir que ele seja executado corretamente na plataforma.

Por isso, recomendamos que você inicie com uma das nossas [distribuições endossadas do Linux no Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) sempre que possível. Os artigos a seguir guiam você pela preparação das diversas distribuições endossadas do Linux que têm suporte no Azure:

* **[Distribuições com base em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

O restante deste artigo traz orientações gerais para execução da sua distribuição do Linux no Azure.

## <a name="general-linux-installation-notes"></a>Notas de instalação gerais do Linux
* O formato VHDX não tem suporte no Azure, somente o **VHD fixo**.  Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd. Se você estiver usando o VirtualBox, isso significará selecionar **Tamanho fixo** em vez do padrão alocado dinamicamente durante a criação do disco.
* O Azure oferece suporte somente para máquinas virtuais de geração 1. Você pode converter uma máquina virtual de geração 1 do formato de arquivo VHDX em VHD e de expansão dinâmica em um disco de tamanho fixo. Mas não é possível alterar a geração de uma máquina virtual. Para saber mais, confira [Devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* O tamanho máximo permitido para o VHD é 1.023 GB.
* Ao instalar o sistema Linux, é *recomendável* que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM idêntica para solução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser usados nos discos de dados.
* É necessário suporte a kernel para montar sistemas de arquivos UDF. Na primeira inicialização no Azure, a configuração de provisionamento é transmitida à VM do Linux por meio de mídia formatada para UDF, a qual é anexada ao convidado. O agente de Linux do Azure deve ser capaz de montar o sistema de arquivos UDF para ler sua configuração e provisionar a VM.
* Versões de kernel do Linux abaixo de 2.6.37 não dão suporte ao NUMA no Hyper-V com tamanhos maiores de VM. Esse problema afeta principalmente distribuições mais antigas usando kernel Red Hat 2.6.32 upstream e foi corrigido no RHEL 6.6 (kernel-2.6.32-504). Sistemas que executam kernels personalizados anteriores a 2.6.37 ou com base em RHEL anteriores a 2.6.32-504 devem definir o parâmetro de inicialização `numa=off` na linha de comando do kernel em grub.conf. Para obter mais informações, confira o [KB 436883](https://access.redhat.com/solutions/436883) do Red Hat.
* Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários.  Verifique as etapas a seguir para obter mais informações a esse respeito.
* Todos os VHDs devem ter tamanhos que sejam múltiplos de 1 MB.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalação dos módulos de kernel sem Hyper-V
O Azure é executado no hipervisor Hyper-V, de modo que o Linux requer que determinados módulos de kernel estejam instalados para executar no Azure. Se você tem uma VM que foi criada fora do Hyper-V, os instaladores do Linux não incluem os drivers Hyper-V no ramdisk inicial (initrd ou initramfs), a menos que ele detecte que está executando um ambiente Hyper-V. Ao usar um sistema de virtualização diferente (ou seja, Virtualbox, KVM, etc.) para preparar a imagem do Linux, talvez seja necessário recompilar o initrd para garantir que pelo menos os módulos kernel `hv_vmbus` e `hv_storvsc` estejam disponíveis no ramdisk inicial.  Isso é um problema conhecido pelo menos em sistemas com base na distribuição Red Hat upstream.

O mecanismo para recriar a imagem initrd ou initramfs pode variar dependendo da distribuição. Consulte a documentação da distribuição ou suporte para o procedimento adequado.  Aqui está um exemplo de como recompilar o initrd usando o utilitário `mkinitrd` :

Primeiro, faça backup da imagem initrd existente:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Em seguida, recompile o initrd com os módulos kernel `hv_vmbus` e `hv_storvsc`:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Redimensionando VHDs
As imagens de VHD no Azure devem ter um tamanho virtual alinhado para 1MB.  Normalmente, os VHDs criados usando o Hyper-V já foram alinhados corretamente.  Se o VHD não estiver alinhado corretamente, você receberá uma mensagem de erro semelhante à seguinte quando tentar criar uma *imagem* de seu VHD:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Para corrigir isso, você pode redimensionar a VM usando o console do Gerenciador do Hyper-V ou o cmdlet do Powershell [Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx) .  Se você não estiver executando em um ambiente Windows, então é recomendável usar qemu-img para converter (se necessário) e redimensionar o VHD.

> [!NOTE]
> Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar o qemu-img 2.2.0 ou inferior, ou atualizar para a versão 2.6 ou posterior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Redimensionar o VHD diretamente, usando ferramentas como `qemu-img` ou `vbox-manage` pode resultar em um VHD incapaz de ser inicializado.  Então convém primeiro converter o VHD para uma imagem de disco bruta.  Se a imagem VM já foi criada como imagem de disco bruta (o padrão para alguns Hypervisors como KVM), você pode ignorar esta etapa:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Calcule o tamanho necessário da imagem do disco para garantir que o tamanho virtual esteja alinhado para 1MB.  O seguinte script de bash shell pode ajudar com isso.  O script usa "`qemu-img info`" para determinar o tamanho virtual da imagem do disco e, em seguida, calcula o tamanho para o próximo 1 MB:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Redimensione o disco bruto usando $rounded_size conforme definido no script acima:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Agora, converta o disco bruto em um VHD de tamanho fixo:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Ou então, com a versão de qemu **2.6 ou superior**, inclui a opção `force_size`:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Requisitos do kernel do Linux
Os drivers LIS (Serviços de Integração do Linux) para Hyper-V e Azure são obtidos diretamente no kernel upstream do Linux. Muitas distribuições que contam com uma versão recente do kernel do Linux (por exemplo, versões 3.x) já terão esses drivers ou fornecerão versões revertidas desses drivers com seus kernels.  Os drivers são atualizados constantemente no kernel upstream com reparos e recursos. Por isso, recomendamos que você execute, sempre que possível, uma [distribuição endossada](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que inclua esses reparos e essas atualizações.

Se você estiver executando uma variante das versões **de 6.0 a 6.3** do Red Hat Enterprise Linux, será necessário instalar os drivers LIS mais recentes para o Hyper-V. Os drivers LIS com o kernel já estão incluídos nas versões [6.4 e posteriores](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)do RHEL (e derivados). Os drivers LIS com o kernel já estão incluídos nas versões **6.4 e posteriores** do RHEL (e derivados). Por isso, não é necessário ter outros pacotes de instalação para executar esses sistemas no Azure.

Se for necessário usar um kernel personalizado, recomendamos que você use uma versão mais recente ( **3.8 ou posterior**). No caso das distribuições ou dos fornecedores que mantêm seus próprios kernels, será necessário um pouco de esforço para a reversão dos drivers LIS do kernel upstream para seu kernel personalizado.  Recomendamos que você acompanhe os reparos upstream nos drivers LIS e faça as reversões necessárias, mesmo se estiver executando uma versão relativamente recente do kernel. A localização dos arquivos de origem do driver do LIS consta no arquivo [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) , na árvore do kernel do Linux:

    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/

Sabemos que a ausência dos patches a seguir resulta em problemas no Azure. Por isso, eles devem constar no kernel. De forma alguma essa lista pode ser considerada completa para todas as distribuições:

* [ata_piix: adie os discos para os drivers Hyper-V por padrão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: conta para pacotes em trânsito no caminho RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: evite o uso de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: desabilite WRITE SAME para RAID e drivers do adaptador de host virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: correção de desreferência de ponteiro NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: as falhas do buffer de anéis podem resultar em congelamento de E/S](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: proteger contra a execução dupla de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>O agente Linux do Azure
O [agente Linux do Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (waagent) é necessário para garantir o provisionamento correto da máquina virtual Linux no Azure. Você pode obter a versão mais recente, verificar os problemas com arquivos ou enviar solicitações pull no [repositório GitHub do agente Linux](https://github.com/Azure/WALinuxAgent).

* O agente Linux consta na licença do Apache 2.0. Diversas distribuições já fornecem pacotes RPM ou DEB para o agente. Assim, em alguns casos, é possível instalá-los e atualizá-los sem dificuldades.
* O agente Linux do Azure requer Python v2.6+.
* Além disso, o agente requer o módulo python-pyasn1. A maioria das distribuições fornece esse módulo como uma pacote autônomo instalável.
* Em alguns casos, é possível que o agente Linux do Azure não seja compatível com o NetworkManager. Muitos pacotes RPM/DEB fornecidos pelas distribuições configuram o NetworkManager como um conflito para o pacote do waagent e, portanto, desinstalam o NetworkManager quando você instala o pacote do agente Linux.

## <a name="general-linux-system-requirements"></a>Requisitos gerais do sistema Linux

* Modifique a linha de inicialização do kernel no GRUB ou GRUB2 para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.
  
    Além disso, recomendamos que você *remova* os seguintes parâmetros, se eles existirem:
  
        rhgb quiet crashkernel=auto
  
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial. Você pode deixar configurada a opção `crashkernel` , mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

* Instalando o agente Linux do Azure
  
    O agente Linux do Azure é necessário para garantir o provisionamento de uma imagem Linux no Azure.  Muitas distribuições fornecem o agente como um pacote RPM ou DEB (o pacote geralmente recebe o nome de "WALinuxAgent" ou "walinuxagent").  Você também pode seguir as etapas descritas no [Guia do agente Linux](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)para instalar o agente manualmente.

* Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.

* Não crie espaço swap no disco do sistema operacional
  
    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* Por fim, execute os comandos a seguir para desprovisionar a máquina virtual:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > No Virtualbox, você pode ver o seguinte erro após executar 'waagent -force -deprovision': `[Errno 5] Input/output error`. Essa mensagem de erro não é crítica e pode ser ignorada.
  > 
  > 

* Em seguida, você deverá desligar a máquina virtual e carregar o VHD no Azure.

