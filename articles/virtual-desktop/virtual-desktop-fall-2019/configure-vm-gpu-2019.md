---
title: Configurar GPU para área de trabalho virtual do Windows (clássico)-Azure
description: Como habilitar o processamento e a codificação com aceleração de GPU na área de trabalho virtual do Windows (clássico).
author: gundarev
ms.topic: how-to
ms.date: 03/30/2020
ms.author: denisgun
ms.openlocfilehash: fa7b80b021e00d25dea4f96432ae922c15474058
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95023047"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-classic"></a>Configurar a aceleração da GPU (unidade de processamento gráfico) para a área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../configure-vm-gpu.md).

A Área de Trabalho Virtual do Windows dá suporte à renderização e à codificação acelerada por GPU para obter melhor desempenho do aplicativo e escalabilidade. A aceleração de GPU é particularmente fundamental para aplicativos com uso intensivo de gráficos.

Siga as instruções neste artigo para criar uma máquina virtual do Azure otimizada para GPU, adicioná-la ao seu pool de host e configurá-la para usar a aceleração de GPU para renderização e codificação. Este artigo pressupõe que você já tenha um locatário da Área de Trabalho Virtual do Windows.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selecione um tamanho de máquina virtual do Azure com otimização de GPU

O Azure oferece uma série de [tamanhos de máquina virtual otimizada para GPU](../../virtual-machines/sizes-gpu.md). A escolha certa para seu pool de host depende de vários fatores, incluindo suas cargas de trabalho de aplicativo específicas, a qualidade desejada da experiência do usuário e o custo. Em geral, GPUs maiores e mais compatíveis oferecem uma experiência de usuário melhor em uma determinada densidade do usuário.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Criar um pool de host, provisionar sua máquina virtual e configurar um grupo de aplicativos

Crie um pool de host usando uma VM do tamanho selecionado. Para obter instruções, consulte [tutorial: criar um pool de hosts com o Azure Marketplace](../create-host-pools-azure-marketplace.md).

A Área de Trabalho Virtual do Windows dá suporte à renderização e à codificação acelerada por GPU nos seguintes sistemas operacionais:

* Windows 10 versão 1511 ou posterior
* Windows Server 2016 ou mais recente

Você também deve configurar um grupo de aplicativos ou usar o grupo de aplicativos da área de trabalho padrão (denominado “Grupo de Aplicativos da Área de Trabalho”) criado automaticamente quando você cria um pool de host. Para obter instruções, confira [Tutorial: Gerenciar grupos de aplicativos para a Área de Trabalho Virtual do Windows](../manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instalar os drivers gráficos com suporte em sua máquina virtual

Para aproveitar as funcionalidades de GPU de VMs da série N do Azure na Área de Trabalho Virtual do Windows, você deve instalar os drivers gráficos apropriados. Siga as instruções em [Sistemas operacionais e drivers com suporte](../../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) para instalar drivers do fornecedor de gráficos apropriado, seja manualmente ou usando uma extensão de VM do Azure.

Há suporte apenas a drivers distribuídos pelo Azure na Área de Trabalho Virtual do Windows. Adicionalmente, para VMs do Azure com GPUs NVIDIA, somente os [Drivers NVIDIA Grid](../../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers) têm suporte na área de trabalho virtual do Windows.

Após a instalação do driver, será necessário reiniciar a VM. Use as etapas de verificação nas instruções acima para confirmar que os drivers gráficos foram instalados com êxito.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurar renderização de aplicativo acelerada por GPU

Por padrão, aplicativos e áreas de trabalho sendo executados em configurações de várias sessões são renderizados com a CPU e não utilizam as GPUs disponíveis para renderização. Configure a Política de Grupo para que o host da sessão habilite a renderização acelerada por GPU:

1. Conecte-se à área de trabalho da VM usando uma conta com privilégios de administrador local.
2. Abra o menu Iniciar e digite “gpedit.msc” para abrir o Editor de Política de Grupo.
3. Navegue até a árvore **Configuração do Computador** > **Modelos Administrativos** > **Componentes do Windows** > **Serviços de Área de Trabalho Remota** > **Host da Sessão da Área de Trabalho Remota** > **Ambiente de Sessão Remota**.
4. Selecione a política **Usar o adaptador gráfico padrão de hardware para todas as sessões de Serviços de Área de Trabalho Remota** e defina essa política como **Habilitada** para habilitar a renderização de GPU na sessão remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurar codificação de quadros acelerada por GPU

Área de Trabalho Remota codifica todos os gráficos renderizados por aplicativos e áreas de trabalho (sejam renderizados com GPU ou com CPU) para transmissão para clientes da Área de Trabalho Remota. Por padrão, a Área de Trabalho Remota não utiliza as GPUs disponíveis para essa codificação. Configure a Política de Grupo para que o host da sessão habilite a codificação de quadros acelerada por GPU. Continuando as etapas acima:

1. Selecione a política **Priorizar o modo gráfico H.264/AVC 444 para conexões da Área de Trabalho Remota** e defina essa política como **Habilitada** para forçar o codec H.264/AVC 444 na sessão remota.
2. Selecione a política **Configurar codificação de hardware H.264/AVC para conexões da Área de Trabalho Remota** e defina essa política como **Habilitada** para habilitar a codificação de hardware para AVC/H.264 na sessão remota.

    >[!NOTE]
    >No Windows Server 2016, defina a opção **Preferir a codificação de hardware AVC** como **Sempre tentar**.

3. Agora que as políticas de grupo foram editadas, force uma atualização da política de grupo. Abra o Prompt de Comando e digite:

    ```batch
    gpupdate.exe /force
    ```

4. Saia da sessão de Área de Trabalho Remota.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verificar a renderização de aplicativo acelerada por GPU

Para verificar se os aplicativos estão usando a GPU para renderização, tente qualquer um dos seguintes:

* Para VMs do Azure com uma GPU NVIDIA, use o utilitário `nvidia-smi`, conforme descrito em [Verificar a instalação do driver](../../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) para verificar a utilização da GPU ao executar seus aplicativos.
* Em versões do sistema operacional com suporte, você pode usar o Gerenciador de Tarefas para verificar a utilização da GPU. Selecione a GPU na guia "Desempenho" para ver se os aplicativos estão utilizando a GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verificar a codificação de quadros acelerada por GPU

Para verificar se Área de Trabalho Remota está usando a codificação acelerada por GPU:

1. Conecte-se à área de trabalho da VM usando o cliente da Área de Trabalho Virtual do Windows.
2. Inicie o Visualizador de Eventos e navegue até o seguinte nó: **Logs de Aplicativos e Serviços** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operacional**
3. Para determinar se a codificação acelerada por GPU é usada, procure a ID de evento 170. Se você está vendo "Codificador de hardware AVC habilitado: 1", então a codificação de GPU é usada.
4. Para determinar se o modo AVC 444 é usado, procure a ID do evento 162. Se você está vendo "AVC Disponível: 1 Perfil Inicial: 2048", então o AVC 444 é usado.

## <a name="next-steps"></a>Próximas etapas

Essas instruções devem colocar você em funcionamento com a aceleração de GPU em um host da sessão (uma VM). Algumas considerações adicionais para habilitar a aceleração de GPU em um pool de host maior:

* Considere usar uma [extensão de VM](../../virtual-machines/extensions/overview.md) para simplificar a instalação e as atualizações de driver em várias VMs. Use a [Extensão de Driver de GPU NVIDIA](../../virtual-machines/extensions/hpccompute-gpu-windows.md) para VMs com GPUs NVIDIA e use a Extensão de Driver de GPU AMD para VMs com GPUs AMD.
* Considere usar a Política de Grupo do Active Directory para simplificar a configuração de política de grupo em várias VMs. Para obter informações sobre como implantar a Política de Grupo no domínio do Active Directory, confira [Trabalhar com Objetos de Política de Grupo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)).