---
title: Otimizar sua VM Linux no Azure | Microsoft Docs
description: Saiba algumas dicas de otimização para certificar-se de que você configurou sua VM do Linux para otimizar o desempenho no Azure
keywords: máquina virtual linux, linux máquina virtual, máquina virtual ubuntu
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 30d153863a20dcdddc702ee5a37c34a2938d7446
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473902"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Otimizar sua VM do Linux no Azure
É fácil criar uma máquina virtual (VM) Linux desde a linha de comando ou do portal. Este tutorial mostra como garantir que você configurou uma VM para otimizar o desempenho na plataforma Microsoft Azure. Este tópico usa uma VM do Ubuntu Server, mas é possível também criar uma máquina virtual do Linux usando [suas próprias imagens como modelos](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Pré-requisitos
Este tópico pressupõe que você já tenha uma Assinatura do Azure ativa ([inscrição de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e já tenha provisionado uma VM na sua Assinatura do Azure. Certifique-se de que você tenha a versão mais recente da [CLI do Azure](/cli/azure/install-az-cli2) instalada e conectada à sua assinatura do Azure com [logon az](/cli/azure/reference-index) antes de [criar uma VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Disco do sistema operacional do Azure
Depois de criar uma VM Linux no Azure, ela terá dois discos associados. **/dev/sda** é o disco do sistema operacional, **/dev/sdb** é o disco temporário.  Use o disco do sistema operacional principal (**/dev/sda**) somente para o sistema operacional, pois ele é otimizado para um tempo de inicialização rápido da VM e não fornece bom desempenho para suas cargas de trabalho. Você deseja anexar um ou mais discos à sua VM para obter um armazenamento persistente e otimizado para seus dados. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Adicionando discos para metas de desempenho e tamanho
Com base no tamanho da VM, é possível anexar até 16 discos adicionais em um computador da série A, 32 discos em uma da série D e 64 discos em uma da série G, cada uma com até 1 TB de tamanho. Adicione discos adicionais conforme necessário para seus requisitos de IOps e espaço. Cada disco tem uma meta de desempenho de 500 IOps para o Armazenamento Standard e até 5000 IOps por disco para Armazenamento Premium.

Para atingir os IOps mais altos nos discos de Armazenamento Premium com configuração de cache definidas como **ReadOnly** ou **None**, você deve desabilitar as **barreiras** durante a montagem do sistema de arquivos no Linux. Você não precisa de barreiras, pois as gravações em discos de backup do Armazenamento Premium são duráveis para essas configurações de cache.

* Se você usar **reiserFS**, desabilite as barreiras usando a opção de montagem `barrier=none` (para habilitar as barreiras, use `barrier=flush`)
* Se você usar **ext3/ext4**, desabilite as barreiras usando a opção de montagem `barrier=0` (para habilitar as barreiras, use `barrier=1`)
* Se você usar **XFS**, desabilite as barreiras usando a opção de montagem `nobarrier` (para habilitar as barreiras, use a opção `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Considerações sobre a conta de armazenamento não gerenciado
A ação padrão ao criar uma VM com a CLI do Azure é usar o Azure Managed Disks.  Esses discos são tratados pela plataforma do Azure e não exigem nenhuma preparação ou local para armazenamento.  Os discos não gerenciados exigem uma conta de armazenamento e têm algumas considerações adicionais de desempenho.  Para saber mais sobre discos gerenciados, veja [Visão geral dos Azure Managed Disks](../windows/managed-disks-overview.md).  A seção a seguir descreve as considerações de desempenho somente quando você usa os discos não gerenciados.  Novamente, o padrão e a solução de armazenamento recomendada é usar discos gerenciados.

Se você criar uma VM com discos não gerenciados, certifique-se de anexar discos de contas de armazenamento que residam na mesma região que sua VM para garantir a proximidade e minimizar a latência de rede.  Cada conta de Armazenamento Standard tem um máximo de 20k IOps e uma capacidade de tamanho de 500 TB.  Este limite funciona para aproximadamente 40 discos muito usados, incluindo o disco do sistema operacional e os discos de dados que você criar. Para contas de Armazenamento Premium, não há nenhum limite máximo de IOps, mas há um limite de tamanho de 32 TB. 

Ao lidar com cargas de trabalho de IOps altas e se tiver escolhido o Armazenamento Standard para seus discos, talvez seja necessário dividir os discos em várias contas de armazenamento para certificar-se de que você não tenha atingido o limite de 20.000 IOps para contas de Armazenamento Standard. Sua VM pode conter uma combinação de discos em diferentes contas de armazenamento e tipos de conta de armazenamento para atingir a configuração ideal.
 

## <a name="your-vm-temporary-drive"></a>Unidade temporária da VM
Por padrão, quando você cria uma VM, o Azure fornece um disco de sistema operacional (**/dev/sda**) e um disco temporário (**/dev/sdb**).  Todos os discos extras que você adicionar aparecerão como **/dev/sdc**, **/dev/sdd**, **/dev/sde** e assim por diante. Todos os dados no disco temporário (**/dev/sdb**) não são duráveis e poderão ser perdidos se eventos específicos, como redimensionamento de VM, reimplantação ou manutenção, forçarem uma reinicialização da VM.  O tamanho e tipo de disco temporário está relacionado ao tamanho da VM escolhida no momento da implantação. Todas as VMs de tamanho Premium (séries DS, G e DS_V2) e a unidade temporária contam com um SSD local para desempenho adicional de até 48k IOps. 

## <a name="linux-swap-file"></a>Arquivo de permuta do Linux
Se a sua VM do Azure for de uma imagem do Ubuntu ou CoreOS, você poderá usar o CustomData para enviar um cloud-config para cloud-init. Se você [carregou uma imagem do Linux personalizada](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que usa a inicialização de nuvem, configure também as partições de troca usando a inicialização de nuvem.

Nas Imagens de nuvem do Ubuntu, use a inicialização de nuvem para configurar a partição de troca. Para obter mais informações, consulte [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Para imagens sem suporte à inicialização de nuvem, as imagens de VM implantadas no Azure Marketplace têm um agente Linux de VM integrado com o sistema operacional. Esse agente permite que a VM interaja com vários serviços do Azure. Supondo que você implantou uma imagem padrão do Azure Marketplace, você precisaria fazer o seguinte para configurar corretamente as configurações de arquivo de permuta do Linux:

Localizar e modificar duas entradas no arquivo **/etc/waagent.conf** . Eles controlam a existência de um arquivo de permuta dedicado e o tamanho do arquivo de permuta. Os parâmetros que você quer modificar são `ResourceDisk.EnableSwap=N` e `ResourceDisk.SwapSizeMB=0` 

Altere os parâmetros para as seguintes configurações:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={tamanho em MB para atender às suas necessidades} 

Depois de fazer a alteração, você precisará reiniciar o waagent ou sua VM Linux para refletir essas alterações.  Você sabe que as alterações foram implementadas e que um arquivo de permuta foi criado ao usar o comando `free` para exibir o espaço livre. O exemplo a seguir tem um arquivo de permuta de 512 MB criado como resultado da modificação do arquivo **waagent.conf**:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de agendamento de E/S para o Armazenamento Premium
Com o kernel do Linux 2.6.18, o padrão de algoritmo de agendamento de E/S foi alterado de Deadline para CFQ (algoritmo de fila completamente justa). Para padrões de E/S de acesso aleatório, há uma diferença insignificante nas diferenças de desempenho entre CFQ e Deadline.  Para discos baseados em SSD nos quais o padrão de E/S de disco é predominantemente sequencial, alternar de volta para o algoritmo NOOP ou Deadline pode resultar um melhor desempenho de E/S.

### <a name="view-the-current-io-scheduler"></a>Exibir o agendador de E/S atual
Use o seguinte comando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Você verá o seguinte resultado, que indica o agendador atual.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Alterar o dispositivo atual (/dev/sda) do algoritmo de agendamento de e/s
Use os seguintes comandos:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Aplicar esta configuração somente para **/dev/sda** não é útil. Ela precisa ser definida em todos os discos de dados, nos quais a E/S sequencial domina o padrão de E/S.  

Você verá o seguinte resultado, que indica que **grub.cfg** foi recriado com êxito e que o agendador padrão foi atualizado para o NOOP.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Para a família de distribuição Red Hat, só é necessário o seguinte comando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Usando o RAID de software para alcançar I/Ops mais altos
Se suas cargas de trabalho exigem mais IOps que um único disco pode fornecer, você precisa usar uma configuração RAID de software de vários discos. Como o Azure já executa a resiliência do disco na camada de malha local, você obterá o maior nível de desempenho de uma configuração de distribuição de RAID-0.  Provisione e crie discos no ambiente do Azure e anexe-os à sua VM Linux antes do particionamento, da formatação e da montagem das unidades.  Mais detalhes sobre como definir uma configuração de RAID de software em sua VM Linux no Azure podem ser encontrados no documento **[Configuração do RAID de software no Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**.

## <a name="next-steps"></a>Próximas etapas
Lembre-se de que, como com todas as discussões de otimização, você precisará executar testes antes e após cada alteração para medir o impacto que elas causarão.  A otimização é um processo passo a passo que tem resultados distintos em computadores diferentes no seu ambiente.  O que funciona para uma configuração pode não funcionar para outras.

Alguns links úteis para recursos adicionais:

* [Guia do usuário do agente Linux para o Azure](../extensions/agent-linux.md)
* [Otimizando o desempenho do MySQL nas VMs Linux do Azure](classic/optimize-mysql.md)
* [Configurar RAID de software no Linux](configure-raid.md)
