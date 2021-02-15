---
title: Criar uma VM do Linux com a API REST
description: Saiba como criar uma máquina virtual do Linux no Azure que use Managed Disks e autenticação SSH com a API REST do Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: b3eeaf5f343b6026e02d17e4d3bd90dba4b991c4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737038"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Criar uma máquina virtual do Linux que usa autenticação SSH com a API REST

Uma VM (máquina virtual) do Linux no Azure consiste em vários recursos, como discos e interfaces de rede, e define parâmetros como localização, tamanho e imagem do sistema operacional e configurações de autenticação.

É possível criar uma VM do Linux por meio do portal do Azure, CLI do Azure 2.0, muitos SDKs do Azure, modelos do Azure Resource Manager e muitas ferramentas de terceiros, como Ansible ou Terraform. Em última análise, todas essas ferramentas usam a API REST para criar a VM do Linux.

Este artigo mostra a você como usar a API REST para criar uma VM do Linux que executa o Ubuntu 18.04-LTS com discos gerenciados e autenticação SSH.

## <a name="before-you-start"></a>Antes de começar

Antes de criar e enviar a solicitação, você precisará de:

* O `{subscription-id}` para sua assinatura
  * Se você tiver várias assinaturas, confira [Trabalhando com várias assinaturas](/cli/azure/manage-azure-subscriptions-azure-cli)
* Um `{resourceGroupName}` criado com antecedência
* Um [adaptador de rede virtual](../../virtual-network/virtual-network-network-interface.md) no mesmo grupo de recursos
* Um par de chaves SSH (você pode [gerar um novo](mac-create-ssh-keys.md) caso não tenha)

## <a name="request-basics"></a>Noções básicas de solicitação

Para criar ou atualizar uma máquina virtual, use a seguinte operação *PUT*:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Além dos parâmetros `{subscription-id}` e `{resourceGroupName}`, você precisará especificar o `{vmName}` (`api-version` é opcional, mas este artigo foi testado com `api-version=2017-12-01`)

Os cabeçalhos a seguir são necessários:

| Cabeçalho da solicitação   | Descrição |
|------------------|-----------------|
| *Tipo de Conteúdo:*  | Obrigatórios. Defina como `application/json`. |
| *Autorização:* | Obrigatórios. Defina como um [token de acesso](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` válido. |

Para obter informações gerais sobre como trabalhar com solicitações da API REST, confira [Componentes de uma solicitação/resposta da API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo da solicitação

As definições comuns a seguir são usadas para criar um corpo de solicitação:

| Nome                       | Obrigatório | Type                                                                                | Descrição  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| local                   | True     | string                                                                              | Local do recurso. |
| name                       |          | string                                                                              | Nome da máquina virtual. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Especifica as configurações de hardware da máquina virtual. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Especifica as configurações de armazenamento dos discos da máquina virtual. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Especifica as configurações do sistema operacional da máquina virtual. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Especifica as interfaces de rede da máquina virtual. |

Abaixo, há um exemplo de corpo da solicitação. Lembre-se de especificar o nome da VM nos parâmetros `{computerName}` e `{name}`, o nome do adaptador de rede que criou em `networkInterfaces`, seu nome de usuário em `adminUsername` e `path` e a parte *pública* do seu par de chaves SSH (localizada em, por exemplo, `~/.ssh/id_rsa.pub`) em `keyData`. Outros parâmetros que talvez queira modificar incluem `location` e `vmSize`.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Para obter uma lista completa das definições disponíveis no corpo da solicitação, consulte [máquinas virtuais criar ou atualizar definições do corpo da solicitação](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Enviando a solicitação

É possível usar o cliente de sua preferência para enviar essa solicitação HTTP. Você também pode usar uma [ferramenta no navegador](/rest/api/compute/virtualmachines/createorupdate) clicando no botão **Experimentar**.

### <a name="responses"></a>Respostas

Há duas respostas bem-sucedidas para a operação criar ou atualizar uma máquina virtual:

| Nome        | Tipo                                                                              | Descrição |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Criado | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Criado     |

Uma resposta *201 Criado* condensada do exemplo anterior de corpo da solicitação que cria uma VM mostra que uma *vmId* foi atribuída e o *provisioningState* como *Criando*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Para saber mais sobre as respostas da API REST, veja [Processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as APIs REST do Azure ou outras ferramentas de gerenciamento, como a CLI do Azure ou o Azure PowerShell, veja o seguinte:

- [API REST do provedor de Computação do Azure](/rest/api/compute/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)
- [CLI do Azure](/cli/azure/)
- [Módulo do Azure PowerShell](/powershell/azure/)
