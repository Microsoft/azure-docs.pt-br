---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: be170144fddeb1a69592f1714ec745d559665832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82982404"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Criar uma máquina virtual habilitada para Docker

Para fins de teste, use uma VM Ubuntu habilitada para Docker para acessar um registro de contêiner do Azure. Para usar Azure Active Directory autenticação para o registro, instale também o [CLI do Azure][azure-cli] na VM. Se você já tiver uma máquina virtual do Azure, ignore esta etapa de criação.

Você pode usar o mesmo grupo de recursos para sua máquina virtual e o registro de contêiner. Essa configuração simplifica a limpeza no final, mas não é necessária. Se você optar por criar um grupo de recursos separado para a máquina virtual e a rede virtual, execute [AZ Group Create][az-group-create]. O exemplo a seguir pressupõe que você definiu variáveis de ambiente para o nome do grupo de recursos e o local do registro:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Agora, implante uma máquina virtual do Ubuntu do Azure padrão com [AZ VM Create][az-vm-create]. O exemplo a seguir cria uma VM chamada *myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Quando o comando for concluído, observe o `publicIpAddress` exibido pela CLI do Azure. Use esse endereço para estabelecer conexões SSH à VM.

### <a name="install-docker-on-the-vm"></a>Instalar o Docker na VM

Depois que a VM estiver em execução, estabeleça uma conexão SSH à VM. Substitua *publicIpAddress* pelo endereço IP público da VM.

```bash
ssh azureuser@publicIpAddress
```

Execute os seguintes comandos para instalar o Docker na VM do Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Após a instalação, execute o seguinte comando para verificar se o Docker está funcionando corretamente na VM:

```bash
sudo docker run -it hello-world
```

Saída:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Siga as etapas em [Instalar a CLI do Azure com apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar a CLI do Azure em sua máquina virtual do Ubuntu. Por exemplo:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Saia da conexão SSH.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group