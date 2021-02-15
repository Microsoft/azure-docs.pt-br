---
title: Diagnóstico de inicialização do Azure
description: Visão geral do diagnóstico de inicialização do Azure e diagnóstico de inicialização gerenciada
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: dc9f7dfdf33411f11d51734b48105be200874d02
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738111"
---
# <a name="azure-boot-diagnostics"></a>Diagnóstico de inicialização do Azure

O diagnóstico de inicialização é um recurso de depuração para VMs (máquinas virtuais) do Azure que permite o diagnóstico de falhas de inicialização da VM. O diagnóstico de inicialização permite que um usuário observe o estado de sua VM durante a inicialização coletando informações de log serial e capturas de tela.

## <a name="boot-diagnostics-storage-account"></a>Conta de armazenamento de diagnóstico de inicialização
Ao criar uma VM no portal do Azure, o diagnóstico de inicialização é habilitado por padrão. A experiência de diagnóstico de inicialização recomendada é usar uma conta de armazenamento gerenciada, pois ela gera melhorias significativas no desempenho no momento da criação de uma VM do Azure. Isso ocorre porque uma conta de armazenamento gerenciado do Azure será usada, removendo o tempo necessário para criar uma nova conta de armazenamento de usuário para armazenar os dados de diagnóstico de inicialização.

> [!IMPORTANT]
> Os blobs de dados de diagnóstico de inicialização (que compõem os logs e imagens de instantâneo) são armazenados em uma conta de armazenamento gerenciada. Os clientes serão cobrados somente em GiBs usados pelos BLOBs, não no tamanho provisionado do disco. Os medidores de instantâneo serão usados para cobrança da conta de armazenamento gerenciada. Como as contas gerenciadas são criadas no padrão LRS ou Standard ZRS, os clientes serão cobrados a US $0,05/GB por mês apenas para o tamanho dos BLOBs de dados de diagnóstico. Para saber mais sobre esse preço, confira [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). Os clientes verão esse encargo associado ao URI do recurso da VM. 

Uma experiência de diagnóstico de inicialização alternativa é usar uma conta de armazenamento gerenciada pelo usuário. Um usuário pode criar uma nova conta de armazenamento ou usar uma existente.
> [!NOTE]
> As contas de armazenamento gerenciadas pelo usuário associadas ao diagnóstico de inicialização exigem que a conta de armazenamento e as máquinas virtuais associadas residam na mesma assinatura. 



## <a name="boot-diagnostics-view"></a>Exibição de diagnóstico de inicialização
Localizado na folha máquina virtual, a opção diagnóstico de inicialização está na seção *suporte e solução de problemas* no portal do Azure. A seleção de diagnósticos de inicialização exibirá uma captura de tela e informações de log serial. O log serial contém mensagens de kernel e a captura de tela é um instantâneo do estado atual de suas VMs. Com base em se a VM que está executando o Windows ou Linux determinar a aparência esperada da captura de tela. Para o Windows, os usuários verão um plano de fundo de área de trabalho e para Linux, os usuários verão um prompt de logon.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Captura de tela do diagnóstico de inicialização do Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Captura de tela do diagnóstico de inicialização do Windows":::

## <a name="enable-managed-boot-diagnostics"></a>Habilitar o diagnóstico de inicialização gerenciado 
O diagnóstico de inicialização gerenciado pode ser habilitado por meio dos modelos portal do Azure, CLI e ARM. A habilitação por meio do PowerShell ainda não tem suporte. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Habilitar o diagnóstico de inicialização gerenciado usando o portal do Azure
Ao criar uma VM no portal do Azure, a configuração padrão é ter o diagnóstico de inicialização habilitado usando uma conta de armazenamento gerenciado. Para exibir isso, navegue até a guia *Gerenciamento* durante a criação da VM. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="Captura de tela habilitando o diagnóstico de inicialização gerenciado durante a criação da VM.":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>Habilitar o diagnóstico de inicialização gerenciado usando a CLI
O diagnóstico de inicialização com uma conta de armazenamento gerenciada tem suporte no CLI do Azure 2.12.0 e posterior. Se você não inserir um nome ou URI para uma conta de armazenamento, uma conta gerenciada será usada. Para obter mais informações e exemplos de código, consulte a [documentação da CLI para o diagnóstico de inicialização](/cli/azure/vm/boot-diagnostics).

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Habilitar o diagnóstico de inicialização gerenciado usando modelos de Azure Resource Manager (ARM)
Tudo depois da versão da API 2020-06-01 dá suporte ao diagnóstico de inicialização gerenciado. Para obter mais informações, consulte [exibição da instância de diagnóstico de inicialização](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics).

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>Limitações
- O diagnóstico de inicialização só está disponível para VMs Azure Resource Manager.
- O diagnóstico de inicialização gerenciado não oferece suporte a VMs que usam discos do sistema operacional não gerenciados.
- O diagnóstico de inicialização não oferece suporte a contas de armazenamento Premium, se uma conta de armazenamento Premium for usada para os usuários de diagnóstico de inicialização receberem um `StorageAccountTypeNotSupported` erro ao iniciar a VM. 
- As contas de armazenamento gerenciadas têm suporte na versão da API do Gerenciador de recursos "2020-06-01" e posterior.
- Atualmente, o console serial do Azure é incompatível com uma conta de armazenamento gerenciada para o diagnóstico de inicialização. Saiba mais sobre o [console serial do Azure](./troubleshooting/serial-console-overview.md).
- O portal só dá suporte ao uso de diagnóstico de inicialização com uma conta de armazenamento gerenciada para VMs de instância única.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [console serial do Azure](./troubleshooting/serial-console-overview.md) e como usar o diagnóstico de inicialização para [solucionar problemas de máquinas virtuais no Azure](./troubleshooting/boot-diagnostics.md).
