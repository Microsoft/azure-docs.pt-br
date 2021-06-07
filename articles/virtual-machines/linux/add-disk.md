---
title: Adicionar um disco de dados a uma VM Linux usando a CLI do Azure
description: Saiba como adicionar um disco de dados persistente à VM Linux com a CLI do Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: adf6198cf12011c77fcf3f93d4b595ea433ddefd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580378"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Adicionar um disco a uma VM do Linux

Este artigo mostra a você como anexar um disco persistente à sua VM para que você possa preservar dados, mesmo que sua VM seja provisionada novamente devido à manutenção ou ao redimensionamento.


## <a name="attach-a-new-disk-to-a-vm"></a>Anexar um novo disco a uma VM

Se você quiser adicionar um disco de dados novo vazio em sua VM, use o comando [az vm disk attach](/cli/azure/vm/disk) com o parâmetro `--new`. Se a VM estiver em uma zona de disponibilidade, o disco será criado automaticamente na mesma zona que a VM. Para obter mais informações, consulte [Visão geral de zonas de disponibilidade](../../availability-zones/az-overview.md). O exemplo a seguir cria um disco chamado *myDataDisk* que tem tamanho de 50 Gb:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Anexar um disco existente

Para anexar um disco existente, localize a ID do disco e passe-a para o comando [az vm disk attach](/cli/azure/vm/disk). A exemplo a seguir consulta em busca de um disco chamado *myDataDisk* em *myResourceGroup*, em seguida, anexa-o à VM denominada *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>Formatar e montar o disco

Para participar, formatar e montar o novo disco para que sua VM do Linux possa usá-lo, Secure Shell em sua VM. Para saber mais, confira [Como usar o SSH com o Linux no Azure](mac-create-ssh-keys.md). O seguinte exemplo se conecta a uma VM com o endereço IP público *10.123.123.25* e o nome de usuário *azureuser*:

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>Localize o disco

Após conectado à VM, você precisa encontrar o disco. Neste exemplo, estamos usando `lsblk` para listar os discos. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

A saída deverá ser semelhante ao seguinte exemplo:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

Aqui, `sdc` é o disco que desejamos, pois ele tem 50G. Se você não tem certeza quanto ao disco baseando-se apenas no tamanho, vá para a página da VM no portal, selecione **Discos** e verifique o número LUN do disco em **Discos de dados**. 


### <a name="format-the-disk"></a>Formatar o disco

Formate o disco com `parted`; se o tamanho do disco for de 2 tebibytes (TiB) ou maior, você deverá usar o particionamento GPT e, se for menor que 2 TiB, você poderá usar o particionamento MBR ou GPT. 

> [!NOTE]
> É recomendável usar a última versão de `parted` disponível para sua distribuição.
> Se o tamanho do disco for de 2 tebibytes (TiB) ou mais, use o particionamento GPT. Se o tamanho do disco for menor que 2 TiB, você poderá usar o particionamento MBR ou GPT.  


O exemplo a seguir usa `parted` em `/dev/sdc`, que é onde o primeiro disco de dados normalmente estará na maioria das VMs. Substitua `sdc` pela opção correta para seu disco. Também o estamos formatando usando o sistema de arquivos [XFS](https://xfs.wiki.kernel.org/).

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Use o utilitário [`partprobe`](https://linux.die.net/man/8/partprobe) para garantir que o kernel esteja ciente da nova partição e do novo sistema de arquivos. Deixar de usar `partprobe` pode fazer com que os comandos blkid ou lslbk não retornem o UUID do novo sistema de arquivos imediatamente.


### <a name="mount-the-disk"></a>Monte o disco

Agora, crie um diretório para montar o novo sistema de arquivos usando o `mkdir`. O exemplo a seguir cria um diretório em `/datadrive`:

```bash
sudo mkdir /datadrive
```

Use `mount` para montar então o sistema de arquivos. O exemplo a seguir monta a partição `/dev/sdc1` para o ponto de montagem `/datadrive`:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>Persista a montagem

Para garantir que a unidade seja remontada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo */etc/fstab*. Além disso, é altamente recomendável que o UUID (Identificador Universal Exclusivo) seja usado em */etc/fstab* para fazer referência à unidade, e não apenas ao nome do dispositivo (por exemplo, */dev/sdc1*). Se o sistema operacional detectar um erro de disco durante a inicialização, usar o UUID evita que o disco incorreto seja montado em um determinado local. Os discos de dados restantes seriam então atribuídos a essas mesmas IDs de dispositivo. Para localizar o UUID da nova unidade, use o utilitário `blkid`:

```bash
sudo blkid
```

A saída deve ser semelhante ao seguinte exemplo:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

Em seguida, abra o arquivo */etc/fstab* em um editor de texto, conforme descrito a seguir:

```bash
sudo nano /etc/fstab
```

Neste exemplo, usamos o valor UUID para o novo dispositivo `/dev/sdc1` criado nas etapas anteriores e o ponto de montagem `/datadrive`. Adicione a seguinte linha ao final do arquivo `/etc/fstab`:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

Neste exemplo, estamos usando o editor nano, portanto, quando terminar de editar o arquivo, use `Ctrl+O` para gravá-lo e `Ctrl+X` para sair do editor.

> [!NOTE]
> Remover um disco de dados posteriormente sem editar fstab pode fazer com que a VM falhe ao ser inicializada. A maioria das distribuições fornecem as opções de fstab *nofail* e/ou *nobootwait*. Essas opções permitem que um sistema inicialize mesmo se o disco não for montado no momento da inicialização. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.
>
> A opção *nofail* garante que a VM inicie mesmo que o sistema de arquivos esteja corrompido ou que o disco não exista no momento da inicialização. Sem essa opção, você poderá encontrar um comportamento conforme descrito em [Não é possível conectar-se a uma VM Linux via SSH devido a erros no FSTAB](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)
>
> O Console Serial da VM do Azure pode ser usado para acesso ao console para sua VM se a modificação de fstab resultou em uma falha de inicialização. Mais detalhes estão disponíveis na [Documentação do Console Serial](/troubleshoot/azure/virtual-machines/serial-console-linux).

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte a TRIM/UNMAP para Linux no Azure
Alguns kernels Linux permitem operações TRIM/UNMAP para descartar os blocos não utilizados no disco. Esse recurso é útil principalmente no Armazenamento Standard, para informar o Azure de que as páginas excluídas não são mais válidas e podem ser descartadas, podendo também economizar dinheiro se você criar arquivos grandes e depois excluí-los.

Há duas maneiras de habilitar o suporte a TRIM em sua VM do Linux. Como de costume, consulte sua distribuição para obter a abordagem recomendada:

* Use a opção de montagem `discard` em */etc/fstab*, por exemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```
* Em alguns casos, a opção `discard` pode afetar o desempenho. Como alternativa, você pode executar o comando `fstrim` manualmente na linha de comando ou adicioná-lo a crontab para ser executado normalmente:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Solução de problemas

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Próximas etapas

* Para garantir que a VM Linux seja configurada corretamente, leia as recomendações em [Otimizar sua VM do Linux no Azure](/previous-versions/azure/virtual-machines/linux/optimization) .
* Expanda a capacidade de armazenamento adicionando mais discos e [configure o RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) para obter desempenho adicional.