
Para saber mais sobre discos, consulte [Sobre discos e VHDs para máquinas virtuais](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Anexar um disco vazio
1. Abra a CLI do Azure 1.0 e [conecte-se à sua assinatura do Azure](../articles/xplat-cli-connect.md). Verifique se você está no modo de Gerenciamento de Serviços do Azure (`azure config mode asm`).
2. Insira `azure vm disk attach-new` para criar e anexar um novo disco, conforme o exemplo abaixo. Substitua *myVM* pelo nome da máquina virtual Linux e especifique o tamanho do disco em GB, que é de *100 GB* neste exemplo:

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. Após o disco de dados ser criado e anexado, ele é listado no resultado de `azure vm disk list <virtual-machine-name>` conforme o exemplo abaixo:
   
    ```azurecli
    azure vm disk list TestVM
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>Anexar um disco existente
Anexar um disco existente exige que você tenha um .vhd disponível em uma conta de armazenamento.

1. Abra a CLI do Azure 1.0 e [conecte-se à sua assinatura do Azure](../articles/xplat-cli-connect.md). Verifique se você está no modo de Gerenciamento de Serviços do Azure (`azure config mode asm`).
2. Verifique se o VHD que você deseja anexar já está carregado à sua assinatura do Azure:
   
    ```azurecli
    azure vm disk list
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Se você não encontrar o disco que quer usar, você pode carregar um VHD local à sua assinatura usando `azure vm disk create` ou `azure vm disk upload`. Um exemplo de `disk create` seria como no exemplo a seguir:
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   Você também pode usar `azure vm disk upload` para carregar um VHD em uma conta de armazenamento específico. Leia mais sobre os comandos para gerenciar seus discos de dados de máquinas virtuais do Azure [aqui](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

4. Agora você anexa o VHD desejado à sua máquina virtual:
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   Lembre-se de substituir *myVM* com o nome da sua máquina virtual, e *myVHD* com seu VHD desejado.

5. Você pode verificar se o disco está anexado à máquina virtual com `azure vm disk list <virtual-machine-name>`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> Depois de adicionar um disco de dados, será necessário fazer logon na máquina virtual e inicializar o disco para que a máquina virtual possa usar o disco para armazenamento (consulte as etapas a seguir para saber mais informações sobre como inicializar o disco).
> 
> 

