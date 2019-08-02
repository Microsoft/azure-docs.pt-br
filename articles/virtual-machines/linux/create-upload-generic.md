---
title: Criar e carregar um VHD Linux no Azure
description: Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: szark
ms.openlocfilehash: 1f9512e4eabf76edecef594b6b6498782725c019
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671596"
---
# <a name="information-for-non-endorsed-distributions"></a>Informações para distribuições não endossadas
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

O SLA (contrato de nível de serviço) da plataforma do Azure aplica-se a máquinas virtuais com o sistema operacional Linux somente quando uma das [distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) é usada com os detalhes da configuração especificados neste artigo. Para essas distribuições endossadas, as imagens do Linux pré-configuradas são fornecidas no Azure Marketplace.

* [Linux no Azure - Distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Suporte para imagens Linux no Microsoft Azure](https://support.microsoft.com/kb/2941892)

Todas as distribuições em execução no Azure têm vários pré-requisitos. Este artigo pode não ser abrangente, pois cada distribuição é diferente. Mesmo que você atenda a todos os critérios abaixo, talvez seja necessário ajustar significativamente o sistema Linux para que funcione corretamente.

É recomendável iniciar com uma das [distribuições endossadas do Linux no Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Os artigos a seguir mostram como preparar as várias distribuições endossadas do Linux com suporte no Azure:

* **[Distribuições com base em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Este artigo concentra-se na orientação geral para executar a distribuição do Linux no Azure.

## <a name="general-linux-installation-notes"></a>Notas de instalação gerais do Linux
* O formato VHDX (disco rígido virtual para Hyper-V) não tem suporte no Azure, apenas *VHD fixo*.  Você pode converter o disco para o formato VHD usando o Hyper-V Manager ou o cmdlet [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd). Se você estiver usando o VirtualBox, selecione **Tamanho fixo** em vez do padrão (alocado dinamicamente) ao criar o disco.
* O Azure oferece suporte somente para máquinas virtuais de geração 1. Você pode converter uma máquina virtual de geração 1 do formato de arquivo VHDX em VHD e de expansão dinâmica em um disco de tamanho fixo. Mas observe não é possível alterar a geração de uma máquina virtual. Para saber mais, confira [Devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* O tamanho máximo permitido para o VHD é 1.023 GB.
* Ao instalar o sistema Linux, é recomendável usar partições padrão em vez do LVM (Gerenciador de Volume Lógico), que é o padrão para muitas instalações. Usar partições padrão evitará conflitos de nome do LVM com VMs clonadas, especialmente se um disco de SO já estiver conectado a outra VM idêntica para solução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser usados nos discos de dados.
* O suporte do kernel para a montagem de sistemas de arquivos UDF é necessário. Na primeira inicialização no Azure, a configuração de provisionamento é passada para a VM do Linux usando a mídia em formato UDF anexada ao convidado. O agente Linux do Azure deve montar o sistema de arquivos UDF para ler a configuração e provisionar a VM.
* As versões do kernel do Linux anteriores à 2.6.37 não são suporte para NUMA no Hyper-V com tamanhos de VM maiores. Esse problema afeta principalmente distribuições mais antigas usando o kernel do Red Hat 2.6.32, e foi corrigido no RHEL (Red Hat Enterprise Linux) 6.6 (kernel-2.6.32-504). Sistemas que executam kernels personalizados anteriores a 2.6.37 ou com base em RHEL anteriores a 2.6.32-504 devem definir o parâmetro de inicialização `numa=off` na linha de comando do kernel em grub.conf. Para obter mais informações, consulte o [KB 436883](https://access.redhat.com/solutions/436883) do Red Hat.
* Não configure uma partição de permuta no disco do sistema operacional. O agente do Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporário, conforme descrito nas etapas a seguir.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto em VHD, será necessário garantir que o tamanho do disco bruto seja um múltiplo de 1 MB antes da conversão, conforme descrito nas etapas a seguir.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalação dos módulos de kernel sem Hyper-V
O Azure é executado no Hipervisor Hyper-V, portanto, o Linux exige que determinados módulos do kernel sejam executados no Azure. Se você tiver uma VM que foi criada fora do Hyper-V, os instaladores do Linux talvez não incluam os drivers do Hyper-V no ramdisk inicial (initrd ou initramfs), exceto se a VM detectar que está em execução em um ambiente Hyper-V. Ao usar um sistema de virtualização diferente (como Virtualbox, KVM, e assim por diante) para preparar a imagem do Linux, talvez seja necessário recompilar o initrd para que pelo menos os módulos do kernel hv_vmbus e hv_storvsc estejam disponíveis no ramdisk inicial.  Esse problema conhecido é para sistemas com base na distribuição anterior do Red Hat e, possivelmente, em outros.

O mecanismo para recriar a imagem initrd ou initramfs pode variar dependendo da distribuição. Consulte a documentação da distribuição ou suporte para o procedimento adequado.  Aqui está um exemplo para recompilar o initrd usando o utilitário `mkinitrd`:

1. Faça o backup da imagem initrd existente:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Recompile o initrd com os módulos do kernel hv_vmbus e hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Redimensionando VHDs
As imagens de VHD no Azure devem ter um tamanho virtual alinhado para 1MB.  Normalmente, os VHDs criados usando o Hyper-V estão alinhados corretamente.  Se o VHD não estiver alinhado corretamente, você poderá receber uma mensagem de erro semelhante à seguinte ao tentar criar uma imagem do VHD.

* O VHD http:\//\<mystorageaccount >.blob.core.windows.net/vhds/MyLinuxVM.vhd tem um tamanho virtual sem suporte de 21475270656 bytes. O tamanho deve ser um número inteiro (em MBs).

Nesse caso, redimensione a VM usando o console do Gerenciador Hyper-V ou o cmdlet do PowerShell [Resize-VHD](https://technet.microsoft.com/library/hh848535.aspx).  Se você não estiver executando em um ambiente Windows, é recomendável usar `qemu-img` para converter (se necessário) e redimensionar o VHD.

> [!NOTE]
> Há um [bug conhecido em qemu-img](https://bugs.launchpad.net/qemu/+bug/1490611) versões >=2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido na versão QEMU 2.6. É recomendável usar `qemu-img` 2.2.0 ou inferior, ou 2.6 ou superior.
> 

1. Redimensionar o VHD diretamente, usando ferramentas como `qemu-img` ou `vbox-manage` pode resultar em um VHD incapaz de ser inicializado.  É recomendável primeiro converter o VHD em uma imagem de disco RAW.  Se a imagem da VM foi criada como uma imagem de disco RAW (o padrão para alguns hipervisores como KVM), você poderá pular esta etapa.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Calcule o tamanho necessário da imagem de disco para que o tamanho virtual seja alinhado a 1 MB.  O script de shell bash a seguir usa `qemu-img info` para determinar o tamanho virtual da imagem do disco e, em seguida, calcula o tamanho para o próximo 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$((($size/$MB + 1)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Redimensione o disco bruto usando `$rounded_size` como definido acima.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Agora, converta o disco RAW novamente em um VHD de tamanho fixo.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Ou, com a versão 2.6+ do qemu, inclua a opção `force_size`.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Requisitos do kernel do Linux

Os drivers LIS (Serviços de Integração do Linux) para Hyper-V e Azure são obtidos diretamente no kernel upstream do Linux. Muitas distribuições que incluem uma versão recente do kernel do Linux (como 3.x) já possuem esses drivers disponíveis ou, caso contrário, fornecem versões portadas desses drivers com seus kernels.  Esses drivers são constantemente atualizados no kernel upstream com novas correções e recursos, portanto, quando possível, é recomendável a execução de uma [distribuição endossada](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que inclui essas correções e atualizações.

Se você estiver executando uma variante do Red Hat Enterprise Linux versões 6.0 a 6.3, será necessário instalar os [últimos drivers LIS para Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Começando com o RHEL 6.4+ (e derivados), os drivers LIS já estão incluídos no kernel e, portanto, nenhum pacote de instalação adicional é necessário.

Se um kernel personalizado for necessário, é recomendável uma versão recente do kernel (como 3.8+). No caso das distribuições ou dos fornecedores que mantêm seus próprios kernels, será necessário reverter os drivers LIS do kernel upstream para seu kernel personalizado.  Mesmo se você já estiver executando uma versão do kernel relativamente recente, é recomendável controlar todas as correções upstream nos drivers LIS e fazer a reversão conforme necessário. Os locais dos arquivos de origem do driver LIS são especificados no arquivo [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) na árvore de origem do kernel do Linux:
```
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
```
Os seguintes patches devem ser incluídos no kernel. Esta lista não pode ser completa para todas as distribuições.

* [ata_piix: adie os discos para os drivers Hyper-V por padrão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Conta para os pacotes em trânsito no caminho RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: evite o uso de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Desabilite WRITE SAME para RAID e drivers de adaptador de host virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: Correção de desreferência de ponteiro nulo](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: as falhas do buffer de anéis podem resultar em congelamento de E/S](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: proteger contra a execução dupla de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>O agente Linux do Azure
O [agente Linux do Azure](../extensions/agent-linux.md) `waagent` provisiona uma máquina virtual do Linux no Azure. Você pode obter a última versão, verificar os problemas com arquivos ou enviar solicitações de pull no [repositório GitHub do agente Linux](https://github.com/Azure/WALinuxAgent).

* O agente Linux consta na licença do Apache 2.0. Muitas distribuições já fornecem pacotes RPM ou deb para o agente, e esses pacotes podem ser facilmente instalados e atualizados.
* O agente Linux do Azure requer Python v2.6+.
* Além disso, o agente requer o módulo python-pyasn1. A maioria das distribuições fornece esse módulo como um pacote separado a ser instalado.
* Em alguns casos, é possível que o agente Linux do Azure não seja compatível com o NetworkManager. Muitos dos pacotes RPM/Deb fornecidos pelas distribuições configuram o NetworkManager como um conflito para o pacote waagent. Nesses casos, ele irá desinstalar o NetworkManager quando você instalar o pacote do agente Linux.
* O agente Linux do Azure deve estar na ou acima da [versão mínima com suporte](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Requisitos gerais do sistema Linux

1. Modifique a linha de inicialização do kernel no GRUB ou no GRUB2 para incluir os seguintes parâmetros, de modo que todas as mensagens do console sejam enviadas para a primeira porta serial. Essas mensagens podem ajudar o suporte do Azure a depurar quaisquer problemas.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Além disso, é recomendável *remover* os seguintes parâmetros, se existirem.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Inicialização gráfica e silenciosa não é útil em um ambiente de nuvem, onde queremos que todos os logs sejam enviados para a porta serial. A opção `crashkernel` pode ser deixada configurada, se necessário, mas observe que esse parâmetro reduz a quantidade de memória disponível na VM em pelo menos 128 MB, o que pode ser problemático para tamanhos de VM menores.

1. Instale o Agente Linux do Azure.
  
    O agente Linux do Azure é necessário para garantir o provisionamento de uma imagem Linux no Azure.  Muitas distribuições fornecem o agente como um pacote RPM ou DEB (o pacote geralmente recebe o nome de WALinuxAgent ou walinuxagent).  Você também pode seguir as etapas descritas no [Guia do agente Linux](../extensions/agent-linux.md)para instalar o agente manualmente.

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Essa configuração geralmente é a padrão.

1. Não crie espaço de troca no disco do sistema operacional.
  
    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. O disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (etapa 2 acima), modifique os seguintes parâmetros em /etc/waagent.conf conforme necessário.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Execute os seguintes comandos para desprovisionar a máquina virtual.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > No Virtualbox você pode ver o seguinte erro após executar `waagent -force -deprovision` que informa `[Errno 5] Input/output error`. Essa mensagem de erro não é crítica e pode ser ignorada.

* Desligar a máquina virtual e carregar o VHD no Azure.

