---
title: Console serial do Azure | Microsoft Docs
description: O console serial do Azure permite que se conectar à sua VM quando SSH ou RDP não estiverem disponíveis.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 28c5a3085d84b25deb7c5ee09a9c9cc4d7a06819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374058"
---
# <a name="azure-serial-console"></a>Console serial do Azure

O console serial no portal do Azure fornece acesso a um console baseado em texto para VMs (máquinas virtuais) e instâncias do conjunto de dimensionamento de máquinas virtuais que executam Linux ou Windows. Essa conexão serial conecta-se à porta serial ttyS0 ou COM1 da VM ou da instância do conjunto de dimensionamento de máquinas virtuais, fornecendo acesso independente à rede ou ao estado do sistema operacional. O console serial só pode ser acessado pelo portal do Azure, e apenas os usuários que têm a função de acesso de Colaborador ou superior à VM ou ao conjunto de dimensionamento de máquinas virtuais podem usá-lo.

O console serial funciona da mesma maneira nas VMs e nas instâncias do conjunto de dimensionamento de máquinas virtuais. Neste documento, todas as menções às VMs incluirão implicitamente as instâncias do conjunto de dimensionamento de máquinas virtuais, salvo indicação em contrário.

O console serial está geralmente disponível em regiões globais do Azure e em visualização pública no Azure governamental. Ele ainda não está disponível na nuvem do Azure China.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Pré-requisitos para acessar o console serial do Azure
Para acessar o console serial na sua VM ou na instância do conjunto de dimensionamento de máquinas virtuais, você precisará do seguinte:

- O diagnóstico de inicialização deve ser habilitado para a VM
- Uma conta de usuário que usa autenticação de senha deve existir na VM. Você pode criar um usuário baseado em senha com a função [Redefinir senha](../extensions/vmaccess.md#reset-password) da extensão de acesso à VM. Selecione **Redefinir senha** na seção **Suporte + solução de problemas**.
- A conta do Azure que acessa o console serial deve ter a [função Colaborador da máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) na conta de armazenamento do [diagnóstico de inicialização](boot-diagnostics.md) e na VM
- Implantações clássicas não são suportadas. A VM ou a instância do conjunto de dimensionamento de máquinas virtuais precisa usar o modelo de implantação do Azure Resource Manager.

> [!NOTE]
> O console serial é incompatível atualmente com uma conta de armazenamento de diagnóstico de inicialização gerenciada. Para usar o console serial, verifique se você está usando uma conta de armazenamento personalizada.

## <a name="get-started-with-the-serial-console"></a>Introdução ao console serial
O console serial para VMs e conjunto de dimensionamento de máquinas virtuais pode ser acessado apenas pelo portal do Azure:

### <a name="serial-console-for-virtual-machines"></a>Console serial para Máquinas Virtuais
É simples usar o console serial para VMs. Basta clicar em **Console serial** na seção **Suporte + Solucionar problemas** no portal do Azure.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue até **Todos os recursos** e selecione uma Máquina Virtual. A página de visão geral da VM é aberta.

  1. Role para baixo até a seção **Support + troubleshooting** e selecione **Console serial**. Um novo painel com o console serial abre e inicia a conexão.

     ![Janela do console serial do Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console serial para Conjuntos de Dimensionamento de Máquinas Virtuais
O console serial está disponível para conjuntos de dimensionamento de máquinas virtuais, acessível em cada instância dentro do conjunto de dimensionamento. Você precisará navegar até a instância individual de um conjunto de dimensionamento de máquinas virtuais para ver o botão **Console serial**. Se o conjunto de dimensionamento de máquinas virtuais não tiver o diagnóstico de inicialização habilitado, atualize o modelo do conjunto de dimensionamento de máquinas virtuais para habilitar o diagnóstico de inicialização e, em seguida, atualize todas as instâncias para o novo modelo para poder acessar o console serial.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue até **Todos os recursos** e selecione um conjunto de dimensionamento de máquinas virtuais. A página Visão geral do conjunto de dimensionamento de máquinas virtuais é aberta.

  1. Navegue até **Instâncias**

  1. Selecione uma instância do conjunto de dimensionamento de máquinas virtuais

  1. Na seção **Suporte + solução de problemas**, selecione **Console serial**. Um novo painel com o console serial abre e inicia a conexão.

     ![Console serial do conjunto de dimensionamento de máquinas virtuais do Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>TLS 1.2 no console serial
O console serial usa TLS 1.2 de ponta a ponta para proteger toda a comunicação dentro do serviço. O console serial tem uma dependência em uma conta de armazenamento de diagnóstico de inicialização gerenciada pelo usuário e o TLS 1.2 deve ser configurado separadamente para a conta de armazenamento. As instruções para isso estão [aqui](../../storage/common/transport-layer-security-configure-minimum-version.md).

## <a name="advanced-uses-for-serial-console"></a>Usos avançados para o console serial
Além do acesso do console à sua VM, o console serial do Azure também pode ser usado para o seguinte:
* Enviar um [comando de solicitação do sistema para sua VM](./serial-console-nmi-sysrq.md)
* Enviar uma [interrupção não mascarável para sua VM](./serial-console-nmi-sysrq.md)
* [Reinicializar ou forçar o ciclo de energia](./serial-console-power-options.md) de maneira elegante


## <a name="next-steps"></a>Próximas etapas
A documentação adicional do console serial está disponível na barra lateral.
- Mais informações estão disponíveis para [console serial para VMs do Linux](./serial-console-linux.md).
- Mais informações estão disponíveis para [console serial para VMs do Windows](./serial-console-windows.md).
