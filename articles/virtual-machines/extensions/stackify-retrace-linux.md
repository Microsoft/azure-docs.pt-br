---
title: Extensão do Agente Linux Azure de Stackify Retrace | Microsoft Docs
description: Implantar o agente Linux de Stackify Retrace em uma máquina virtual Linux
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: roiyz
ms.openlocfilehash: ca3232dc3862cffb67d396b3ec2333fdc28dbb65
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706633"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Extensão do Agente Linux de Stackify Retrace

## <a name="overview"></a>Visão geral

O Stackify fornece produtos que acompanham os detalhes sobre o seu aplicativo para ajudar a localizar e corrigir problemas rapidamente. Para as equipes de desenvolvedores, o Retrace é uma potência de super de desempenho do aplicativo totalmente integrada, com vários ambientes. Ele combina várias ferramentas que cada equipe de desenvolvimento precisa.

O Retrace é a ÚNICA ferramenta que fornece todos os recursos a seguir em todos os ambientes em uma única plataforma.

* Gerenciamento de desempenho do aplicativo (APM)
* Aplicativo e registro de log do servidor
* Monitoramento e controle de erro
* Servidor, aplicativo e métricas personalizadas

**Sobre a Extensão do Agente Linux de Stackify**

Esta extensão fornece um caminho de instalação para o agente Linux para Retrace. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional 

O agente do Retrace pode ser executada com essas distribuições Linux

| Distribuição | Versão |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 e 17.04 |
| Debian | 7.9+ e 8.2+, 9 |
| Red Hat | 7.9+ and 8.2+, 9 |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão do Agente do Stackify para Linux requer que a máquina virtual de destino esteja conectada à Internet. 

Você talvez precise ajustar sua configuração de rede para permitir conexões com Stackify, consulte https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Esquema de extensão

---

O JSON a seguir mostra o esquema para a extensão do Agente Stackify Retrace. A extensão requer `environment` e `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Implantação de modelo 

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a extensão do Agente do Linux para o Stackify Retrace durante uma implantação de modelo do Azure Resource Manager.  

O JSON para uma extensão da máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou localizado no nível de raiz ou superior de um modelo JSON do Resource Manager. O posicionamento do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte Definir o nome e o tipo de recursos filho.

O exemplo a seguir pressupõe que a extensão do Linux Stackify Retrace está aninhada dentro do recurso de máquina virtual. Ao aninhar o recurso de extensão, o JSON é colocado no objeto “recursos”: [] objeto da máquina virtual.

A extensão requer `environment` e `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Ao inserir o JSON da extensão na raiz do modelo, o nome do recurso inclui uma referência na máquina virtual pai e o tipo reflete a configuração aninhada.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>Implantação do PowerShell

O comando `Set-AzVMExtension` pode ser usado para implantar a extensão da máquina virtual do agente Linux do Stackify Retrace para uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam ser armazenadas em uma tabela de hash do PowerShell.

A extensão requer `environment` e `activationKey`.

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure 

A ferramenta da CLI do Azure pode ser usada para implantar a extensão da máquina virtual do Agente Linux do Stackify Retrace.  

A extensão requer `environment` e `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="error-codes"></a>Códigos de erro

| Código do erro | Significado | Ação possível |
| :---: | --- | --- |
| 10 | Erro de instalação | wget é obrigatório |
| 20 | Erro de instalação | phython é obrigatório. |
| 30 | Erro de instalação | sudo é obrigatório |
| 40 | Erro de instalação | activationKey é obrigatório |
| 51 | Erro de instalação | Distribuição de sistema operacional não compatível |
| 60 | Erro de instalação | ambiente é obrigatório |
| 70 | Erro de instalação | Unknown |
| 80 | Ero de habilitação | Falha na configuração de serviço |
| 90 | Ero de habilitação | Falha na configuração de serviço |
| 100 | Desabilitar o erro | Falha na interrupção do serviço |
| 110 | Desabilitar o erro | Falha na interrupção do serviço |
| 120 | Erro de desinstalação | Falha na interrupção do serviço |

Se precisar de mais ajuda, entre em contato com a Stackify em https://support.stackify.com.