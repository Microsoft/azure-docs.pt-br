---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ae29451e3f7ec263f296e69656a5c66045334687
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126714"
---
1. Entre em sua assinatura do Azure usando as etapas listadas em [Conectar-se ao Azure pela CLI clássica do Azure](/cli/azure/authenticate-azure-cli).

2. Verifique se que você está no modo de implantação Clássica da seguinte maneira:

    ```azurecli
    azure config mode asm
    ```

3. Localize a imagem do Linux que você quer carregar nas imagens disponíveis, da seguinte maneira:

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    Em uma janela de prompt de comando do Windows, use **find** em vez de grep.
   
4. Use `azure vm create` para criar VM com a imagem do Linux da lista anterior. Esta etapa cria uma conta de armazenamento e um serviço de nuvem. Você também pode conectar essa VM a um serviço de nuvem existente com uma opção `-c` . Crie um ponto de extremidade SSH para fazer logon na máquina virtual Linux com a opção `-e` . O exemplo a seguir cria uma VM denominada `myVM` usando a imagem `Ubuntu-14_04_4-LTS` no local `West US`, e adiciona um nome de usuário `ops`:
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Para uma máquina virtual Linux, você deve fornecer a opção `-e` no `vm create`. Não é possível ativar SSH após a máquina virtual ter sido criada. Para obter mais detalhes sobre SSH, leia [Como usar SSH com Linux no Azure](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

5. Você pode verificar os atributos da VM usando o comando `azure vm show`. O exemplo a seguir lista as informações para a VM denominada `myVM`:

    ```azurecli   
    azure vm show myVM
    ```

6. Inicie sua VM com o comando `azure vm start` da seguinte maneira:

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>Próximas etapas
Para obter detalhes sobre todos esses comandos de máquina virtual da CLI clássica do Azure, leia [Usando a CLI clássica do Azure com a API da implantação clássica](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

