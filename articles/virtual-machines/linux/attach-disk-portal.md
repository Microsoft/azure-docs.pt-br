---
title: Anexação de um disco de dados a uma VM do Linux
description: Utilize o portal para anexar disco de dados novo ou existente a uma VM Linux.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.collection: linux
ms.openlocfilehash: 0fe584ea8559c285ee7e25caca958ff56aa9454d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601816"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utilize o portal para anexar um disco de dados a uma VM Linux 
Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Linux por meio do portal do Azure. Você também pode [anexar um disco de dados a uma VM do Windows no Portal do Azure](../windows/attach-managed-disk-portal.md). 

Antes de anexar discos à sua VM, veja estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](../sizes.md).
* Discos anexados às máquinas virtuais são, na verdade, os arquivos. vhd armazenados no Azure. Para obter detalhes, confira a [Introdução aos discos gerenciados](../managed-disks-overview.md).
* Depois de anexar o disco, será necessário [conectar a VM Linux para montar o novo disco](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Acesse o [portal](https://portal.azure.com/) do Azure para encontrar a VM. Pesquise por **Máquinas virtuais** e selecione essa opção.
2. Escolha a VM na lista.
3. Na página **máquinas virtuais** , em **configurações**, escolha **discos**.


## <a name="attach-a-new-disk"></a>Anexar um novo disco

1. No painel **discos** , em **discos de dados**, selecione **criar e anexar um novo disco**.

1. Insira um nome para o disco gerenciado. Examine as configurações padrão e atualize o **tipo de armazenamento**, o **tamanho (GIB)**, a **criptografia** e o **cache de host** conforme necessário.
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Examine as configurações de disco.":::


1. Quando terminar, selecione **salvar** na parte superior da página para criar o disco gerenciado e atualizar a configuração da VM.


## <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. No painel **discos** , em **discos de dados**, selecione  **anexar discos existentes**.
1. Clique no menu suspenso para nome do **disco** e selecione um disco na lista de discos gerenciados disponíveis. 

1. Clique em **Salvar** para anexar o disco gerenciado existente e atualizar a configuração da VM:
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Conectar-se à VM do Linux para montar o novo disco
Para participar, formatar e montar o novo disco para que sua VM do Linux possa usá-lo, Secure Shell em sua VM. Para saber mais, confira [Como usar o SSH com o Linux no Azure](mac-create-ssh-keys.md). O exemplo a seguir se conecta a uma VM com o endereço IP público de *10.123.123.25* com o nome de usuário *azureuser*: 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>Localize o disco

Uma vez conectado à sua VM, você precisa encontrar o disco. Neste exemplo, estamos usando `lsblk` para listar os discos. 

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
sdc     3:0:0:0       4G
```

Neste exemplo, o disco que adicionei é `sdc` . É um LUN 0 e 4 GB.

Para obter um exemplo mais complexo, veja a aparência de vários discos de dados no Portal:

:::image type="content" source="./media/attach-disk-portal/find-disk.png" alt-text="Captura de tela de vários discos mostrados no Portal.":::

Na imagem, você pode ver que há três discos de dados: 4 GB no LUN 0, 16 GB no LUN 1 e 32G no LUN 2.

Esta é a aparência que pode ser semelhante ao uso de `lsblk` :

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

Da saída de `lsblk` você pode ver que o disco de 4 GB no LUN 0 é `sdc` , o disco 16 GB na LUN 1 é `sdd` , e o disco 32G no LUN 2 é `sde` .

### <a name="partition-a-new-disk"></a>Particionar um novo disco

Se você estiver usando um disco existente que contenha dados, pule para montar o disco. Se você estiver anexando um novo disco, precisará particionar o disco.

O `parted` utilitário pode ser usado para particionar e formatar um disco de dados.

> [!NOTE]
> É recomendável que você use a versão mais recente `parted` que está disponível para seu distribuição.
> Se o tamanho do disco for 2 tebibytes (TiB) ou maior, você deverá usar o particionamento GPT. Se o tamanho do disco estiver abaixo de 2 TiB, você poderá usar o particionamento MBR ou GPT.  


O exemplo a seguir usa `parted` on `/dev/sdc` , que é onde o primeiro disco de dados normalmente estará na maioria das VMs. Substitua `sdc` pela opção correta para seu disco. Também estamos Formatando-a usando o sistema de arquivos [xfs](https://xfs.wiki.kernel.org/) .

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Use o [`partprobe`](https://linux.die.net/man/8/partprobe) Utilitário para verificar se o kernel está ciente da nova partição e do sistema de arquivos. A falha ao usar `partprobe` pode fazer com que os comandos blkid ou lslbk não retornem o UUID para o novo FileSystem imediatamente.

### <a name="mount-the-disk"></a>Monte o disco

Crie um diretório para montar o novo sistema de arquivos usando o `mkdir`. O exemplo a seguir cria um diretório em `/datadrive` :

```bash
sudo mkdir /datadrive
```

Use `mount` para montar então o sistema de arquivos. O exemplo a seguir monta a partição */dev/sdc1* para o `/datadrive` ponto de montagem:

```bash
sudo mount /dev/sdc1 /datadrive
```

Para garantir que a unidade seja remontada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo */etc/fstab*. Também é altamente recomendável que o UUID (identificador universal exclusivo) seja usado em */etc/fstab* para se referir à unidade em vez de apenas ao nome do dispositivo (como, */dev/sdc1*). Se o sistema operacional detectar um erro de disco durante a inicialização, usar o UUID evita que o disco incorreto seja montado em um determinado local. Os discos de dados restantes seriam então atribuídos a essas mesmas IDs de dispositivo. Para localizar o UUID da nova unidade, use o utilitário `blkid`:

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

Neste exemplo, use o valor UUID para o `/dev/sdc1` dispositivo que foi criado nas etapas anteriores e o mountpoint de `/datadrive` . Adicione a seguinte linha ao final do `/etc/fstab` arquivo:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

Usamos o editor do nano, portanto, quando você terminar de editar o arquivo, use `Ctrl+O` para gravar o arquivo e `Ctrl+X` sair do editor.

> [!NOTE]
> Remover um disco de dados posteriormente sem editar fstab pode fazer com que a VM falhe ao ser inicializada. A maioria das distribuições fornecem as opções de fstab *nofail* e/ou *nobootwait*. Essas opções permitem que um sistema inicialize mesmo se o disco não for montado no momento da inicialização. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.
> 
> A opção *nofail* garante que a VM inicie mesmo que o sistema de arquivos esteja corrompido ou que o disco não exista no momento da inicialização. Sem essa opção, você poderá encontrar um comportamento conforme descrito em [Não é possível conectar-se a uma VM Linux via SSH devido a erros no FSTAB](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)


## <a name="verify-the-disk"></a>Verificar o disco

Agora você pode usar `lsblk` novamente para ver o disco e o mountpoint.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

A saída será parecida com esta:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

Você pode ver que `sdc` agora está montado em `/datadrive` .

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

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e para ajudar a solucionar problemas de disco, consulte [solucionar problemas de nome do dispositivo VM Linux](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems).

Você também pode [anexar um disco de dados](add-disk.md) utilizando a CLI do Azure.
