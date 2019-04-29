---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: 94f662cea5f20485659a7b93549b758fdd7770f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476263"
---
Quando não precisar mais de um disco de dados conectado a uma VM (máquina virtual), você poderá desanexá-lo facilmente. Ao desanexar um disco da VM, o disco não é removido do armazenamento. Se desejar usar os dados existentes no disco novamente, você poderá reanexá-lo à mesma VM ou anexá-lo a outra.  

> [!NOTE]
> Uma VM no Azure usa diferentes tipos de discos, um disco de sistema operacional, um disco temporário local e discos de dados opcionais. Para obter detalhes, confira [Sobre discos e VHDs para máquinas virtuais](../articles/virtual-machines/linux/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Não é possível desanexar um disco do sistema operacional, a menos que você também exclua a VM.

## <a name="find-the-disk"></a>Localize o disco
Antes de poder desanexar um disco de uma VM, você precisa descobrir o número LUN, que é um identificador para o disco a ser desanexado. Para fazer isso, siga estas etapas:

1. Abra a CLI do Azure e [conecte-se à sua assinatura do Azure](/cli/azure/authenticate-azure-cli). Verifique se você está no modo de Gerenciamento de Serviços do Azure (`azure config mode asm`).
2. Descubra quais discos estão anexados à sua VM. O exemplo a seguir lista os discos para a VM chamada `myVM`:

    ```azurecli
    azure vm disk list myVM
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. Observe o LUN ou o **número de unidade lógica** para o disco que você deseja desanexar.

## <a name="remove-operating-system-references-to-the-disk"></a>Remover as referências do sistema operacional no disco
Antes de desanexar o disco do convidado do Linux, você deve se certificar de que nenhuma das partições no disco está em uso. Certifique-se de que o sistema operacional não tente remontá-los após uma reinicialização. Essas etapas desfazem a configuração que você provavelmente criou ao [anexar](../articles/virtual-machines/linux/classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) o disco.

1. Use o comando `lsscsi` para descobrir o identificador do disco. O `lsscsi` pode ser instalado pelo `yum install lsscsi` (distribuições baseadas no Red Hat) ou pelo `apt-get install lsscsi` (distribuições baseadas no Debian). Você pode encontrar o identificador de disco que está procurando usando o número de LUN. O último número na tupla em cada linha é o LUN. No exemplo a seguir do `lsscsi`, LUN 0 mapeia para */dev/sdc*

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. Use `fdisk -l <disk>` para descobrir as partições associadas ao disco a ser desanexado. O exemplo a seguir mostra a saída de `/dev/sdc`:

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. Desmonte cada partição listada para o disco. O exemplo a seguir desmonta `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

4. Use o comando `blkid` para descobrir os UUIDs de todas as partições. A saída deverá ser semelhante ao seguinte exemplo:

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. Remova entradas no arquivo **/etc/fstab** associado aos caminhos de dispositivo ou UUIDs para todas as partições do disco a ser desanexado.  As entradas para este exemplo poderiam ser:

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    ou o
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>Desanexar o disco
Depois de localizar o número de LUN do disco e remover as referências do sistema operacional, você está pronto para desanexá-lo:

1. Desanexe o disco selecionado da máquina virtual executando o comando `azure vm disk detach
   <virtual-machine-name> <LUN>`. O exemplo a seguir desanexa o LUN `0` da VM chamada `myVM`:
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. É possível verificar se o disco foi desanexado executando `azure vm disk list` novamente. O exemplo a seguir verifica a VM chamada `myVM`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    A saída é semelhante ao exemplo a seguir, que mostra que o disco de dados não está mais anexado:

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

O disco permanece desanexado no armazenamento mas já não está conectado a uma máquina virtual.

