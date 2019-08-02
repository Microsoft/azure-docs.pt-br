---
title: Configurar a GPU para visualização de área de trabalho Virtual do Windows - Azure
description: Como habilitar a aceleração de GPU de renderização e codificação na visualização de área de trabalho Virtual do Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: b6a4811f685803ecdc079a690d550618c071c4a6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620190"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Configurar a aceleração de GPU (unidade) para visualização de área de trabalho Virtual do Windows de processamento de gráficos

Visualização de área de trabalho Virtual do Windows oferece suporte a aceleração de GPU de renderização e a codificação para melhor desempenho para aplicativos e a escalabilidade. Aceleração de GPU é particularmente crucial para aplicativos intensivos de elementos gráficos.

Siga as instruções neste artigo para criar uma máquina virtual do Azure GPU otimizada, adicioná-lo ao pool de host e configurá-lo para usar a aceleração de GPU para renderização e codificação. Este artigo pressupõe que você já tiver um locatário de área de trabalho Virtual do Windows configurado.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selecione um tamanho de máquina virtual do Azure GPU otimizada

O Azure oferece inúmeros [tamanhos de máquinas virtuais otimizados para GPU](/azure/virtual-machines/windows/sizes-gpu). A escolha certa para seu pool de host depende de vários fatores, incluindo suas cargas de trabalho de aplicativo específico, desejada qualidade da experiência do usuário e o custo. Em geral, maiores e mais capacitados GPUs oferecem uma melhor experiência de usuário em uma densidade de determinado usuário.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Criar um pool de host, provisionar sua máquina virtual e configurar um grupo de aplicativos

Crie um novo pool de host usando uma VM do tamanho selecionado. Para obter instruções, consulte [Tutorial: Criar um pool de host com o Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Visualização de área de trabalho Virtual do Windows oferece suporte a aceleração de GPU de renderização e codificação nos seguintes sistemas operacionais:

* Windows 10 versão 1511 ou posterior
* Windows Server 2016 ou mais recente

Você também deve configurar um grupo de aplicativos, ou usar o grupo de aplicativos da área de trabalho do padrão (denominado "Grupo de aplicativos de área de trabalho") é criado automaticamente quando você cria um novo pool de host. Para obter instruções, consulte [Tutorial: Gerenciar grupos de aplicativos para visualização de área de trabalho Virtual do Windows](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Visualização de área de trabalho Virtual do Windows suporta apenas o tipo de grupo do aplicativo "Desktop" para pools de host habilitada para GPU. Não há suporte para grupos de aplicativos do tipo "RemoteApp" para pools de host habilitada para GPU.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instalar os drivers gráficos com suporte em sua máquina virtual

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure na visualização de área de trabalho Virtual do Windows, você deve instalar os drivers gráficos NVIDIA. Siga as instruções em [drivers de instalar o NVIDIA GPU em VMs da série N executando Windows](/azure/virtual-machines/windows/n-series-driver-setup) para instalar drivers, manualmente ou usando o [NVIDIA GPU Driver extensão](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Observe que apenas [drivers NVIDIA GRID](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distribuídos pelo Azure têm suporte para visualização de área de trabalho Virtual do Windows.

Após a instalação do driver, é necessária uma reinicialização da VM. Use as etapas de verificação nas instruções acima para confirmar que os drivers gráficos foram instalados com êxito.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurar a renderização acelerada por GPU aplicativo

Por padrão, aplicativos e áreas de trabalho em execução nas configurações de sessão a são processadas com a CPU e não aproveitam as GPUs disponíveis para renderização. Configure a política de grupo do host de sessão habilitar a renderização acelerada por GPU:

1. Conecte-se à área de trabalho da máquina virtual usando uma conta com privilégios de administrador local.
2. Abra o início menu e digite "gpedit. msc" para abrir o Editor de diretiva de grupo.
3. Navegar na árvore para **configuração do computador** > **modelos administrativos** > **componentes do Windows**  >   **Serviços de área de trabalho remota** > **Host da sessão da área de trabalho remota** > **sessão remota ambiente**.
4. Selecione a política **usar o adaptador de gráfico de padrão de hardware para todas as sessões de serviços de área de trabalho remota** e definir essa política como **habilitado** para habilitar a renderização de GPU na sessão remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurar a codificação acelerada por GPU quadro

Área de trabalho remota codifica todos os elementos gráficos renderizados por aplicativos e áreas de trabalho (seja renderizada com GPU ou CPU) para transmissão aos clientes de área de trabalho remota. Por padrão, a área de trabalho remota não aproveita GPUs disponíveis para essa codificação. Configure a política de grupo do host de sessão habilitar a codificação acelerada por GPU quadro. Continue as etapas acima:

1. Selecione a política **modo de priorizar o H.264/AVC 444 gráficos para conexões de área de trabalho remota** e definir essa política como **habilitado** para forçar o codec H.264/AVC 444 na sessão remota.
2. Selecione a política **H.264/AVC configurar hardware codificação para conexões de área de trabalho remota** e definir essa política como **habilitado** para habilitar a codificação de hardware para AVC/H.264 na sessão remota.

    >[!NOTE]
    >No Windows Server 2016, defina a opção **preferir a codificação do Hardware de AVC** à **tentativa sempre**.

3. Agora que as políticas de grupo foram editadas, força uma atualização de diretiva de grupo. Abra o Prompt de comando e digite:

    ```batch
    gpupdate.exe /force
    ```

4. Saia da sessão de área de trabalho remota.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verifique se a renderização acelerada por GPU aplicativo

Para verificar que os aplicativos estão usando a GPU para renderização, tente um destes procedimentos:

* Use o `nvidia-smi` utilitário conforme descrito em [verificar a instalação do driver](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) para verificar a utilização de GPU quando executar seus aplicativos.
* Em versões do sistema operacional com suporte, você pode usar o Gerenciador de tarefas para verificar se há utilização de GPU. Selecione a GPU no guia de "Desempenho" para ver se os aplicativos estão utilizando a GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verifique se a codificação acelerada por GPU quadro

Para verificar se a área de trabalho remota está usando codificação acelerada por GPU:

1. Conecte-se à área de trabalho da VM usando o cliente de área de trabalho Virtual do Windows.
2. Inicie o Visualizador de eventos e navegue até o seguinte nó: **Applications and Services Logs** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS**  >  **Operacionais**
3. Para determinar se a codificação acelerada por GPU é usada, procure o evento ID 170. Se você vir "codificador de hardware AVC habilitado: 1", em seguida, a codificação de GPU é usada.
4. Para determinar se o modo de AVC 444 é usado, procure o evento ID 162. Se você vir "AVC disponível: Perfil inicial de 1: 2048", em seguida, 444 AVC é usado.

## <a name="next-steps"></a>Próximas etapas

Essas instruções devem ter você em funcionamento com aceleração de GPU em um host de sessão de única VM. Algumas considerações adicionais para habilitar a aceleração de GPU em um pool maior de host:

* Considere o uso de [extensão de Driver NVIDIA GPU](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para simplificar a instalação de drivers e atualizações em um número de VMs.
* Considere o uso de política de grupo do Active Directory para simplificar a configuração de diretiva de grupo em um número de VMs. Para obter informações sobre como implantar a política de grupo no domínio do Active Directory, consulte [trabalhando com objetos de diretiva de grupo](https://go.microsoft.com/fwlink/p/?LinkId=620889).
