---
title: Perguntas comuns sobre a VM no Azure Marketplace
description: Perguntas comuns encontradas ao criar uma máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 2975d1f1558bc7f9e4a12c18882e43a163b97982
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593417"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Perguntas comuns sobre a VM no Azure Marketplace

Essas perguntas frequentes abordam os problemas comuns que você pode encontrar ao criar uma oferta de VM (máquina virtual) no Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como configurar uma VPN (rede virtual privada) para trabalhar com minhas VMs?

Se você está usando o Modelo de implantação do Azure Resource Manager, há três opções:

- [Criar um gateway de VPN baseado em rotas usando o portal do Azure](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Criar um gateway de VPN baseado em rotas usando o Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Criar um Gateway de VPN baseado em rota usando CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para a execução de software de servidor Microsoft em VMs do Azure?

Encontre detalhes em [Suporte de software para servidores da Microsoft para Máquinas Virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Em uma VM, como gerencio a extensão de script personalizado na tarefa de inicialização?

Para detalhes sobre como usar a extensão de script personalizado usando o módulo do Azure PowerShell, os modelos do Azure Resource Manager e as etapas em sistemas Windows para solucionar problemas, confira [Extensão de script personalizado para Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>São aplicativos de 32 bits ou serviços com suporte no Azure Marketplace?

Não. Os serviços padrão para VMs do Azure e sistemas operacionais com suporte são 64-bit. Embora a maioria dos sistemas operacionais de 64 bits dê suporte a versões de 32 bits de aplicativos para compatibilidade com versões anteriores, não há suporte para o uso de aplicativos de 32 bits como parte de sua solução de VM e isso não é recomendável. Crie novamente seu aplicativo como um projeto de 64 bits.

Para obter mais informações, consulte estes artigos:

- [Aplicativos de 32 bits em execução](/windows/desktop/WinProg64/running-32-bit-applications)
- [Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erro: o VHD já está registrado com o repositório de imagens como o recurso

Sempre que estou tento criar uma imagem de minhas VHDs, recebo o erro "O VHD já está registrado no repositório de imagens como o recurso" no Azure PowerShell. Eu não criei uma imagem antes, nem encontrei uma imagem com esse nome no Azure. Como resolver isso?

Esse problema geralmente aparece quando você cria uma VM de um VHD que tem um bloqueio. Confirme se não há VM alocada desse VHD e, em seguida, repita a operação. Se o problema persistir, abra um tíquete de suporte. Confira [Suporte do Partner Center](support.md).

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Como fazer criar uma VM com base em um VHD generalizado?

### <a name="prepare-an-azure-resource-manager-template"></a>Preparar um modelo do Azure Resource Manager

Esta seção descreve como criar e implantar uma imagem de VM (máquina virtual) fornecida pelo usuário. Você pode fazer isso fornecendo imagens VHD do sistema operacional e do disco de dados de um disco rígido virtual implantado pelo Azure. Essas etapas implantam a VM usando o VHD generalizado.

1. Entre no portal do Azure.
2. Carregue o VHD do sistema operacional generalizado e os VHDs do disco de dados em sua conta de armazenamento do Azure.
3. Na home page, selecione criar um recurso, pesquise "implantação de modelo" e selecione criar.
4. Escolha Criar seu modelo no editor.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="Mostra a seleção de um modelo":::

5. Cole o modelo JSON a seguir no editor e selecione salvar.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. Forneça os valores de parâmetro para as páginas de propriedades Implantação Personalizada.

 **TABELA 1**

| **ResourceGroupName** | **Nome do grupo de recursos do Azure existente. Normalmente, use o mesmo RG que o cofre de chaves.** |
| --- | --- |
| TemplateFile | Nome do caminho completo para o arquivo VHDtoImage.json. |
| userStorageAccountName | Nome da conta de armazenamento. |
| dnsNameForPublicIP | Nome DNS para o IP público; deve estar em minúsculas. |
| subscriptionId | O identificador da assinatura do Azure. |
| Location | Local geográfico padrão do Azure para o grupo de recursos. |
| vmName | Nome da máquina virtual. |
| vhdUrl | Endereço Web do disco rígido virtual. |
| vmSize | Tamanho da instância de máquina virtual. |
| publicIPAddressName | Nome do endereço IP público. |
| virtualNetworkName | Nome da rede virtual. |
| nicName | Nome da placa de interface de rede para a rede virtual. |
| adminUserName | Nome de usuário da conta de administrador. |
| adminPassword | Senha do administrador. |
|

7. Depois de fornecer esses valores, selecione Comprar.

O Azure iniciará a implantação. Ele cria uma VM com o VHD não gerenciado especificado, no caminho de conta de armazenamento especificada. Você pode acompanhar o progresso no portal do Azure selecionando Máquinas Virtuais no lado esquerdo do portal. Quando a VM tiver sido criada, o status será alterado de Iniciando para Em execução.

Para a implantação de VM de geração 2, use este modelo:
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>Implantar uma VM do Azure usando o PowerShell

Copie e edite o script a seguir para fornecer valores para as variáveis `$storageaccount` e `$vhdUrl`. Executá-lo para criar um recurso de VM do Azure a partir de seu VHD generalizado existente.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```

## <a name="how-do-i-test-a-hidden-preview-image"></a>Como fazer testar uma imagem de visualização oculta?

Você pode implantar imagens de visualização ocultas usando modelos de início rápido.
Para implantar uma imagem de visualização, 
1. Ir para o respectivo modelo de início rápido para [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux) ou [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows), selecione "implantar no Azure". Isso deve levá-lo a portal do Azure.
2. Em portal do Azure, selecione "Editar modelo".
3. No modelo JSON, pesquise imageReference e atualize o PublisherID, o OfferId, o SkuID e a versão da imagem. Para testar a imagem de visualização, acrescente "-PREVIEW" à OfferId.
 ![imagem](https://user-images.githubusercontent.com/79274470/110191995-71c7d500-7de0-11eb-9f3c-6a42f55d8f03.png)
4. Clique em Salvar
5. Preencha o restante dos detalhes. Examinar e criar


## <a name="next-steps"></a>Próximas etapas

- [Solução de problemas de certificação de VM](azure-vm-create-certification-faq.md)
