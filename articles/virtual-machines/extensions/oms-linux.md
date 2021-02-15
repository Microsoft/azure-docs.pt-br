---
title: Extensão da máquina virtual do Log Analytics para Linux
description: Implante o agente do Log Analytics na máquina virtual do Linux usando uma extensão da máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: f75ad90a562a39f940e1006a2e4d9123eff2b47c
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202174"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual do Log Analytics para Linux

## <a name="overview"></a>Visão geral

Os Logs do Azure Monitor fornecem recursos de monitoramento, alertas e correção de alertas em ativos locais e de nuvem. A Microsoft publica e oferece suporte à extensão da máquina virtual do Log Analytics para Linux. A extensão instala o agente do Log Analytics em máquinas virtuais do Azure e registra máquinas virtuais em um espaço de trabalho do Log Analytics existente. Este documento detalha as plataformas com opções de plataformas, configurações e implantação com suporte para a extensão da máquina virtual do Log Analytics para Linux.

>[!NOTE]
>Como parte da transição contínua do Microsoft OMS (Operations Management Suite) para o Azure Monitor, o Agente do OMS para Windows ou Linux será chamado de agente do Log Analytics para Windows e agente do Log Analytics para Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

Para obter detalhes sobre as distribuições do Linux com suporte, consulte o artigo [visão geral do Azure monitor Agents](../../azure-monitor/platform/agents-overview.md#supported-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Versão do Agente e da Extensão de VM
A tabela a seguir fornece um mapeamento da versão da extensão de VM do Log Analytics e o pacote do agente do Log Analytics para cada versão. Há um link para as notas de versão da versão do pacote do agente do Log Analytics. As notas de versão incluem detalhes sobre correções de bug e novos recursos disponíveis para uma determinada liberação de agente.  

| Versão da extensão de VM do Linux do Log Analytics | Versão do pacote do agente do Log Analytics | 
|--------------------------------|--------------------------|
| 1.13.33 | [1.13.33](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.33-0) |
| 1.13.27 | [1.13.27](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.27-0) |
| 1.13.15 | [1.13.9-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.9-0) |
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Central de Segurança do Azure

A Central de Segurança do Azure provisiona o agente do Log Analytics e o conecta a um espaço de trabalho do Log Analytics criado pela ASC na sua assinatura do Azure automaticamente. Se você estiver usando a Central de Segurança do Azure, não execute as etapas neste documento. Isso substitui o workspace configurado e interrompe a conexão com a Central de Segurança do Azure.

### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão do Agente do Log Analytics para Linux requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do Agente do Log Analytics. A extensão requer a ID do espaço de trabalho e a chave do espaço de trabalho no espaço de trabalho do Log Analytics de destino. Esses valores podem ser [encontrados no seu espaço de trabalho do Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) no portal do Azure. Como a chave do workspace deve ser tratada como um dado confidencial, ela é armazenada em uma configuração protegida. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino. Observe que **workspaceId** e **workspaceKey** diferenciam maiúsculas de minúsculas.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>O esquema acima assume que ele será colocado no nível raiz do modelo. Se você colocá-lo dentro do recurso de máquina virtual no modelo, as propriedades `type` e `name` deverão ser alteradas, conforme descrito [abaixo](#template-deployment).

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.13 |
| workspaceId (por exemplo) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Implantação de modelo

>[!NOTE]
>Determinados componentes da extensão de VM Log Analytics também são fornecidos na [extensão de VM de diagnóstico](./diagnostics-linux.md). Devido a essa arquitetura, os conflitos podem surgir se ambas as extensões forem instanciadas no mesmo modelo do ARM. Para evitar esses conflitos de tempo de instalação, use a [ `dependsOn` diretiva](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) para garantir que as extensões sejam instaladas sequencialmente. As extensões podem ser instaladas em qualquer ordem.

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Esses modelos são ideais para implantação de uma ou mais máquinas virtuais que exigem configuração pós-implantação, tal como integração aos Logs do Azure Monitor. Um exemplo de modelo do Resource Manager que inclui a extensão de VM do Agente do Log Analytics pode ser encontrado na [Galeria de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A configuração do JSON para uma extensão da máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou localizado no nível de raiz ou superior de um modelo JSON do Resource Manager. O posicionamento da configuração do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/templates/child-resource-name-type.md). 

O exemplo a seguir pressupõe que a extensão de VM está aninhada dentro do recurso de máquina virtual. Ao aninhar o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Ao inserir o JSON da extensão na raiz do modelo, o nome do recurso inclui uma referência na máquina virtual pai e o tipo reflete a configuração aninhada.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

A CLI do Azure pode ser usado para implantar a extensão da VM do Agente do Log Analytics para uma máquina virtual existente. Substitua o valor *myWorkspaceKey* abaixo por sua chave do workspace e o valor *myWorkspaceId* pela ID do workspace. Esses valores podem ser encontrados no portal do Azure, no workspace do Log Analytics, em *Configurações avançadas*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução da extensão é registrada no seguinte arquivo:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código do Erro | Significado | Ação possível |
| :---: | --- | --- |
| 9 | Habilitar chamado prematuramente | [Atualize o Agente Linux do Azure](./update-linux-agent.md) para a versão mais recente disponível. |
| 10 | A VM já está conectada a um espaço de trabalho do Log Analytics | Para conectar a VM ao workspace especificado no esquema de extensão, defina stopOnMultipleConnections como falso nas configurações públicas ou remova esta propriedade. Essa VM é cobrada uma vez para cada workspace ao qual está conectada. |
| 11 | Configuração inválida fornecida para a extensão | Siga os exemplos anteriores para definir todos os valores de propriedade necessários para a implantação. |
| 17 | Falha na instalação do pacote do Log Analytics | 
| 19 | Falha da instalação do pacote OMI | 
| 20 | Falha da instalação do pacote SCX |
| 51 | Não há suporte para essa extensão no sistema operacional da VM | |
| 52 | Esta extensão falhou devido a uma dependência ausente | Verifique a saída e os logs para obter mais informações sobre qual dependência está faltando. |
| 53 | Esta extensão falhou devido a parâmetros de configuração ausentes ou errados | Verifique a saída e os logs para obter mais informações sobre o que deu errado. Além disso, verifique a exatidão da ID do espaço de trabalho e verifique se a máquina está conectada à Internet. |
| 55 | Não é possível se conectar ao serviço Azure Monitor. Os pacotes necessários estão ausentes ou o gerenciador de pacotes dpkg está bloqueado| Verifique se o sistema tem acesso à Internet ou se um proxy HTTP válido foi fornecido. Além disso, verifique a exatidão da ID do espaço de trabalho e verifique se os utilitários de rotação e tar estão instalados. |

Informações adicionais podem ser encontradas no [Guia de solução de problemas do Log Analytics-Agent-for-Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
