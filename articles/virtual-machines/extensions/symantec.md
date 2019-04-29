---
title: Instalar o Symantec Endpoint Protection em uma VM Windows no Azure | Microsoft Docs
description: Saiba como instalar e configurar a extensão de segurança do Symantec Endpoint Protection em uma VM do Azure nova ou existente criada com o modelo Clássico de implantação.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: roiyz
ms.openlocfilehash: 65b52c88741e618e8048451370918b06db73a651
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617870"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Como instalar e configurar o Symantec Endpoint Protection em uma VM do Windows
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

Este artigo mostra como instalar e configurar o cliente do Symantec Endpoint Protection em uma VM (máquina virtual) existente com o Windows Server. Este cliente completo inclui serviços como proteção contra vírus e spyware, firewall e prevenção contra intrusão. O cliente é instalado como uma extensão de segurança usando-se o Agente de VM.

Se tiver uma assinatura da Symantec para uma solução local, você poderá usá-la para proteger as máquinas virtuais do Azure. Se ainda não for cliente, você poderá se inscrever em uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte [Symantec Endpoint Protection na plataforma Azure da Microsoft][Symantec]. Essa página também fornece links para informações de licenciamento e instruções para instalar o cliente se você já for um cliente da Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalar o Symantec Endpoint Protection em uma VM existente
Antes de começar, você precisa do seguinte:

* O módulo Azure PowerShell, versão 0.8.2 ou mais recente, em seu computador de trabalho. Você pode verificar a versão do PowerShell do Azure instalado com o comando **Get-Module azure | format-table version** . Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][PS]. Faça logon em sua assinatura do Azure usando `Add-AzureAccount`.
* O Agente de VM em execução na Máquina Virtual do Azure.

Primeiro, verifique se o Agente de VM já está instalado na máquina virtual. Preencha o nome do serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos em um prompt de comando do Azure PowerShell com nível de administrador. Substitua tudo entre aspas, incluindo os caracteres < e >.

> [!TIP]
> Se você não souber o nome da máquina virtual e do serviço de nuvem, execute **Get-AzureVM** para listar os nomes de todas as máquinas virtuais em sua assinatura atual.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Se o comando **write-host** exibir **True**, o agente de VM está instalado. Se ele exibir **False**, veja as instruções e um link para download na postagem do blog do Azure [Agente de VM e Extensões ‑ Parte 2][Agent].

Se o Agente de VM Agent estiver instalado, execute estes comandos para instalar o agente Symantec Endpoint Protection.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Para verificar se a extensão de segurança Symantec foi instalada e está atualizada:

1. Faça logon na máquina virtual. Para obter instruções, veja [Como fazer logon em uma máquina virtual que executa o Windows Server][Logon].
2. Para Windows Server 2008 R2, clique em **Iniciar > Symantec Endpoint Protection**. Para o Windows Server 2012 ou Windows Server 2012 R2, na tela inicial, digite **Symantec** e, em seguida, clique em **Symantec Endpoint Protection**.
3. Na guia **Status** da janela de **Status-Symantec Endpoint Protection**, aplique atualizações ou reinicie se necessário.

## <a name="additional-resources"></a>Recursos adicionais
[Como fazer logon em uma máquina virtual que executa o Windows Server][Logon]

[Recursos e Extensões de VM do Azure][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
