---
title: Instalar o Trend Micro Deep Security em uma VM | Microsoft Docs
description: Este artigo descreve como instalar e configurar o Trend Micro security em uma VM criada com o modelo de implantação Clássico no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: roiyz
ms.openlocfilehash: 0e70bc692357e9db9fa02a2f210320507b1b5824
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705894"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Windows
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Este artigo mostra como instalar e configurar o Trend Micro Deep Security as a Service em uma VM (máquina virtual) nova ou existente com o Windows Server em execução. O Deep Security as a Service inclui proteção anti-malware, firewall, sistema de prevenção de intrusões e monitoramento de integridade.

O cliente é instalado como uma extensão de segurança via Agente de VM. Em uma nova máquina virtual, você instala o Deep Security Agent, pois o Agente de VM é criado automaticamente pelo portal do Azure.

Uma VM existente criada com o portal do Azure, a CLI do Azure ou o PowerShell pode não ter um agente de VM. Em uma máquina virtual existente sem o Agente de VM, você precisa baixar e instalá-lo primeiro. Este artigo aborda ambas as situações.

Se você tiver uma assinatura atual da Trend Micro para uma solução local, poderá usá-la para ajudar a proteger as máquinas virtuais do Azure. Se ainda não for cliente, você poderá se inscrever em uma assinatura de avaliação. Para saber mais sobre essa solução, confira a postagem no blog da Trend Micro [Extensão do agente de VM do Microsoft Azure para segurança profunda](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalar o Agente do Deep Security Agent em uma nova VM

O [portal do Azure](https://portal.azure.com) permite que você instale a extensão de segurança da Trend Micro ao usar uma imagem do **Marketplace** para criar a máquina virtual. Se você estiver criando uma única máquina virtual, usar o portal será uma maneira fácil de adicionar proteção da Trend Micro.

O uso de uma entrada do **Marketplace** abre um assistente que ajuda você a configurar a máquina virtual. Você usa a folha **Configurações**, o terceiro painel do assistente, para instalar a extensão de segurança da Trend Micro.  Para obter instruções gerais, consulte [Criar uma máquina virtual que executa o Windows no portal do Azure](../windows/classic/tutorial.md).

Quando chegar à folha **Configurações** do assistente, realize as seguintes etapas:

1. Clique em **Extensões** e, em seguida, em **Adicionar extensão** no próximo painel.

   ![Começar a adicionar a extensão][1]

2. Selecione **Deep Security Agent** no painel **Novo recurso**. No painel do Deep Security Agent, clique em **Criar**.

   ![Identificar o Deep Security Agent][2]

3. Insira o **Identificador de Locatário** e a **Senha de Ativação do Locatário** da extensão. Opcionalmente, você pode inserir um **Identificador de Política de Segurança**. Em seguida, clique em **OK** para adicionar o cliente.

   ![Fornecer detalhes da extensão][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalar o Agente do Deep Security em uma VM existente
Para instalar o agente em uma VM existente, você precisa dos seguintes itens:

* O módulo do Azure PowerShell versão 0.8.2 ou mais recente instalado no computador local. Você pode verificar a versão do Azure PowerShell que tem instalada usando o comando **Get-Module azure | format-table version** . Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Faça logon em sua assinatura do Azure usando `Add-AzureAccount`.
* O agente de VM instalado na máquina virtual de destino.

Primeiramente, verifique se que o agente de VM já está instalado. Preencha o nome do serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos em um prompt de comando do Azure PowerShell com nível de administrador. Substitua tudo entre aspas, incluindo os caracteres < e >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se você não souber o nome da máquina virtual e serviço de nuvem, execute **Get-AzureVM** para exibir essas informações para todas as máquinas virtuais em sua assinatura atual.

Se o comando **write-host** retornar **True**, o agente da VM estará instalado. Se ele retornar **False**, confira as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o Agente de VM estiver instalado, execute estes comandos.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Próximas etapas
Leva alguns minutos para o agente abrir quando ele está instalado. Depois disso, você precisa ativar o Deep Security na máquina virtual para que ela possa ser gerenciada por um Deep Security Manager. Consulte os artigos a seguir para obter instruções adicionais:

* Artigo de tendência sobre essa solução, [Segurança na nuvem Instant-On do Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* Um [script do Windows PowerShell de exemplo](https://go.microsoft.com/fwlink/?LinkId=404100) para configurar a máquina virtual
* [Instruções](https://go.microsoft.com/fwlink/?LinkId=404099) para o exemplo

## <a name="additional-resources"></a>Recursos adicionais
[Como fazer logon em uma máquina virtual executando o Windows Server]

[Recursos e extensões de VM do Azure]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Como fazer logon em uma máquina virtual executando o Windows Server]:../windows/classic/connect-logon.md
[Recursos e extensões de VM do Azure]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
