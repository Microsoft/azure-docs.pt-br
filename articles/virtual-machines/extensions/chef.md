---
title: Extensão do chefe para VMs do Azure
description: Implante o cliente do Chef em uma máquina virtual usando a extensão para VM do Chef.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 09/21/2018
ms.openlocfilehash: e316bf9763dd7c2cbbab21992086eac52d108912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554778"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Extensão para VM do Chef para Linux e Windows

O Chef Software fornece uma plataforma de automação DevOps para Linux e Windows que permite o gerenciamento das configurações físicas e virtuais do servidor. A extensão para VM do Chef é uma extensão que habilita o Chef em máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

Há suporte para a extensão para VM do Chef em todos os [sistemas operacionais com suporte para a extensão](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) no Azure.

### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão para VM do Chef exige que a máquina virtual de destino esteja conectada à Internet a fim de recuperar o payload do cliente do Chef da CDN (rede de distribuição de conteúdo).  

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão para VM do Chef. A extensão exige, no mínimo, a URL do servidor do Chef, o nome do cliente de validação e a chave de validação para o servidor do Chef; esses valores podem ser encontrados no arquivo `knife.rb` no starter-kit.zip cujo download é feito quando você instala o [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) ou um [servidor do Chef](https://downloads.chef.io/chef-server) independente. Como a chave de validação deve ser tratada como dados confidenciais, é necessário configurá-la sob o elemento **protectedSettings**, o que significa que ela só será descriptografada na máquina virtual de destino.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Valores de propriedades principais

| Nome | Valor/Exemplo | Tipo de Dados
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (date) |
| editor | `Chef.Bootstrap.WindowsAzure` | string |
| type | `LinuxChefClient` (Linux), `ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.13` | string (double) |

### <a name="settings"></a>Configurações

| Nome | Valor/Exemplo | Tipo de Dados | Necessário?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | Y |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | string | Y |
| settings/runlist | `recipe[mycookbook::default]` | string | Y |

### <a name="protected-settings"></a>Configurações protegidas

| Nome | Exemplo | Tipo de Dados | Necessário?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Modelos podem ser usados para implantar uma ou mais máquinas virtuais, instalar o cliente do Chef, conectar-se ao servidor do Chef e executar a configuração inicial no servidor, conforme definido pela [lista de execução](https://docs.chef.io/run_lists.html)

Um modelo do Resource Manager de exemplo que inclui a extensão de VM chefe pode ser encontrado na [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

A configuração do JSON para uma extensão da máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou localizado no nível de raiz ou superior de um modelo JSON do Resource Manager. O posicionamento da configuração do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/templates/child-resource-name-type.md).

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

A CLI do Azure pode ser usada para implantar a extensão para VM do Chef em uma VM existente. Substitua a **validation_key** pelo conteúdo da sua chave de validação (esse arquivo como uma extensão `.pem`).  Substitua **validation_client_name**, **chef_server_url** e **run_list** pelos valores do arquivo `knife.rb` no seu kit de início.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Solução de problemas e suporte

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

A saída de execução da extensão é registrada no seguinte arquivo:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código do Erro | Significado | Ação possível |
| :---: | --- | --- |
| 51 | Não há suporte para essa extensão no sistema operacional da VM | |

Informações adicionais sobre solução de problemas podem ser encontradas no [Leiame da extensão para VM do Chef](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> Para qualquer outra coisa diretamente relacionada ao chefe, entre em contato com o [suporte do chefe](https://www.chef.io/support/).

## <a name="next-steps"></a>Próximas etapas

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
