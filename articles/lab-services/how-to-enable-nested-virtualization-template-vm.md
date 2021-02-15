---
title: Habilitar a virtualização aninhada em uma VM de modelo no Azure Lab Services | Microsoft Docs
description: Neste artigo, saiba como configurar a virtualização aninhada em um computador de modelo no Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 319695088e967dc3156ecab5c1b9458e77fcc186
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251467"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services

No momento, o Azure Lab Services permite que você configure uma máquina virtual de modelo em um laboratório e disponibilize uma única cópia para cada um dos seus usuários. Se você for um educador ensinando sobre rede ou segurança ou dando aulas de TI, talvez seja necessário fornecer a cada um de seus alunos um ambiente no qual várias máquinas virtuais possam se comunicar entre si em uma rede.

A virtualização aninhada permite que você crie um ambiente de várias VMs dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá cada usuário no laboratório com uma máquina virtual configurada com várias VMs dentro dela.  Este artigo aborda como configurar a visualização aninhada em um computador de modelo no Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>O que é virtualização aninhada?

A virtualização aninhada permite que você crie máquinas virtuais dentro de uma máquina virtual. A virtualização aninhada é feita por meio do Hyper-V e só está disponível em VMs do Windows.

Para obter mais informações sobre a virtualização aninhada, confira os seguintes artigos:

- [Virtualização aninhada no Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Como habilitar a virtualização aninhada em uma VM do Azure](../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considerações

Antes de configurar um laboratório com virtualização aninhada, veja algumas coisas a se considerar.

- Ao criar um laboratório, selecione os tamanhos **Médio (virtualização aninhada)** ou **Grande (virtualização aninhada)** para o tamanho da máquina virtual. Esses tamanhos de máquina virtual dão suporte à virtualização aninhada.
- Escolha um tamanho que fornecerá um bom desempenho para as máquinas virtuais do host e cliente.  Lembre-se de que, ao usar a virtualização, o tamanho escolhido deve ser adequado para não apenas um computador, mas para o host, bem como computadores Hyper-V em execução simultânea.
- As máquinas virtuais do cliente não terão acesso aos recursos do Azure, como servidores DNS, na rede virtual do Azure.
- A máquina virtual do host requer a instalação para permitir que o computador cliente tenha conectividade com a Internet.
- As máquinas virtuais cliente são licenciadas como máquinas independentes. Confira [Licenciamento da Microsoft](https://www.microsoft.com/licensing/default) para obter informações sobre licenciamento para sistemas operacionais e produtos Microsoft. Verifique os contratos de licenciamento para qualquer outro software que está sendo usado antes de configurar o computador de modelo.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Habilitar a virtualização aninhada em uma VM modelo

Este artigo pressupõe que você tenha criado um laboratório e uma conta de laboratório.  Para obter mais informações sobre como criar uma conta de laboratório, confira o [tutorial para configurar uma Conta de Laboratório](tutorial-setup-lab-account.md). Para obter mais informações sobre como criar um laboratório, confira o [tutorial para configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Selecione **Grande (virtualização aninhada)** ou **Médio (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  Caso contrário, a virtualização aninhada não funcionará.  

Para se conectar ao computador de modelo, confira [criar e gerenciar um modelo de sala de aula](how-to-create-manage-template.md).

Para habilitar a virtualização aninhada, há algumas tarefas que devem ser realizadas.  

- **Habilitar a função do Hyper-V**. A função do Hyper-V deve ser habilitada para a criação e execução de máquinas virtuais Hyper-V na máquina virtual do Lab Services.
- **Habilitar DHCP**.  Quando a máquina virtual do Lab Services tiver a função DHCP habilitada, as máquinas virtuais do Hyper-V poderão receber um endereço IP automaticamente.
- **Crie uma rede NAT para VMs Hyper-V**.  A rede NAT é configurada para permitir que as máquinas virtuais Hyper-V tenham acesso à Internet.  As máquinas virtuais Hyper-V podem se comunicar entre si.

>[!NOTE]
>A rede NAT criada na VM do Lab Services permitirá que uma VM Hyper-V acesse a Internet e outras VMs Hyper-V na mesma VM do Lab Services.  A VM Hyper-V não poderá acessar os recursos do Azure, como servidores DNS, na rede virtual do Azure.

A realização das tarefas listadas acima pode ser feita usando um script ou as ferramentas do Windows.  Leia as seções abaixo para obter mais detalhes.

### <a name="using-script-to-enable-nested-virtualization"></a>Usar o script para habilitar a virtualização aninhada

Para usar a configuração automatizada para a virtualização aninhada com o Windows Server 2016 ou o Windows Server 2019, confira [Habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services usando um script](how-to-enable-nested-virtualization-template-vm-using-script.md). Você usará scripts dos [scripts do Hyper-V do Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) para instalar a função do Hyper-V.  Os scripts também vão configurar a rede para que as máquinas virtuais Hyper-V possam ter acesso à Internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Usar as ferramentas do Windows para habilitar a virtualização aninhada

Para configurar a virtualização aninhada para Windows Server 2016 ou Windows Server 2019 usando funções e ferramentas administrativas do Windows, confira [Habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services manualmente](how-to-enable-nested-virtualization-template-vm-ui.md).  As instruções também vão abordar como configurar a rede para que as máquinas virtuais Hyper-V possam ter acesso à Internet.
