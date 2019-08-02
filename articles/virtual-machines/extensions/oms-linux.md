---
title: Extensão da máquina virtual do Azure Monitor para Linux | Microsoft Docs
description: Implante o agente do Log Analytics na máquina virtual do Linux usando uma extensão da máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: roiyz
ms.openlocfilehash: 7d8192a3b6ff732481a4d48f6e188b4bb3989cda
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705944"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual do Azure Monitor para Linux

## <a name="overview"></a>Visão geral

Os logs do Azure Monitor fornece recursos de correção de alerta, monitoramento e alertas nos ativos de nuvem e locais. A extensão da máquina virtual do agente do Log Analytics para Linux é publicada e recebe suporte da Microsoft. A extensão instala o agente do Log Analytics em máquinas virtuais do Azure e registra máquinas virtuais em um espaço de trabalho do Log Analytics existente. Este documento detalha as plataformas com suporte, configurações e opções de implantação para a extensão de máquina virtual do Azure Monitor para Linux.

>[!NOTE]
>Como parte da transição contínua do Microsoft OMS (Operations Management Suite) para o Azure Monitor, o Agente do OMS para Windows ou Linux será chamado de agente do Log Analytics para Windows e agente do Log Analytics para Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão do agente do Log Analytics pode ser executada nessas distribuições do Linux.

| Distribuição | Versão |
|---|---|
| CentOS Linux | 6 (x86/x64) e 7 (x64) |
| Amazon Linux | 2017.09 (x64) | 
| Oracle Linux | 6 e 7 (x86/x64) |
| Red Hat Enterprise Linux Server | 6 (x86/x64) e 7 (x64) |
| Debian GNU/Linux | 8 e 9 (x86/x64) |
| Ubuntu | 14.04 18.04 LTS (x64), 16.04 LTS (x86 x64) e LTS (x86 x64) |
| SUSE Linux Enterprise Server | 12 (x64) e 15 (x64) |

>[!NOTE]
>O OpenSSL inferior à versão 1.x não tem suporte em nenhuma plataforma e a versão 1.10 tem suporte apenas em plataformas x86_64 (64 bits).  
>

### <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela a seguir destaca os pacotes necessários para distribuições Linux com suporte que o agente será instalado no.

|Pacote necessário |DESCRIÇÃO |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|Openssl    | Bibliotecas OpenSSL | 1.0 ou 1.1 |
|Curl | cliente Web cURL | 7.15.5 |
|Python-ctypes | | 
|PAM | Módulos de autenticação conectáveis | | 

>[!NOTE]
>Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog.

### <a name="agent-and-vm-extension-version"></a>Versão do Agente e da Extensão de VM
A tabela a seguir fornece um mapeamento da versão da extensão de VM do Azure Monitor e o pacote do agente do Log Analytics para cada versão. Há um link para as notas de versão da versão do pacote do agente do Log Analytics. As notas de versão incluem detalhes sobre correções de bug e novos recursos disponíveis para uma determinada liberação de agente.  

| Versão da extensão de VM do Linux de Monitor do Azure | Versão do pacote do agente do Log Analytics | 
|--------------------------------|--------------------------|
| 1.11.9 | [1.11.0-7](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-7) |
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
    "typeHandlerVersion": "1.7",
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
>

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (por exemplo) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Modelos são ideais ao implantar um ou mais máquinas virtuais que exigem configuração pós-implantação, tal como integração aos logs do Azure Monitor. Um modelo do Resource Manager de exemplo que inclui a extensão de VM do agente do Log Analytics pode ser encontrado na [Galeria de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A configuração do JSON para uma extensão da máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou localizado no nível de raiz ou superior de um modelo JSON do Resource Manager. O posicionamento da configuração do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

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
    "typeHandlerVersion": "1.7",
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
    "typeHandlerVersion": "1.7",
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

A CLI do Azure pode ser usado para implantar a extensão da VM do Agente do Log Analytics para uma máquina virtual existente. Substitua *espaço de trabalhoId* e *espaço de trabalhoKey* pelos de seu espaço de trabalho do Log Analytics. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.7 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

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
| 9 | Habilitar chamado prematuramente | [Atualize o Agente Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) para a versão mais recente disponível. |
| 10 | A VM já está conectada a um espaço de trabalho do Log Analytics | Para conectar a VM ao workspace especificado no esquema de extensão, defina stopOnMultipleConnections como falso nas configurações públicas ou remova esta propriedade. Essa VM é cobrada uma vez para cada workspace ao qual está conectada. |
| 11 | Configuração inválida fornecida para a extensão | Siga os exemplos anteriores para definir todos os valores de propriedade necessários para a implantação. |
| 17 | Falha na instalação do pacote do Log Analytics | 
| 19 | Falha da instalação do pacote OMI | 
| 20 | Falha da instalação do pacote SCX |
| 51 | Não há suporte para essa extensão no sistema operacional da VM | |
| 55 | Não é possível conectar ao serviço do Azure Monitor ou os pacotes necessários Gerenciador de pacotes ausentes ou dpkg está bloqueado| Verifique se o sistema tem acesso à Internet ou que um proxy HTTP válido foi fornecido. Além disso, verifique a exatidão da ID do workspace e verifique se os utilitários curl e tar estão instalados. |

Informações adicionais podem ser encontradas no [Guia de solução de problemas do Log Analytics-Agent-for-Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
