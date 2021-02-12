---
title: Configurar um laboratório com GPUs no Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório com máquinas virtuais de GPU (unidade de processamento gráfico).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 8293ed1bfb53895b9631d9730fb75a2364457180
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452371"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Configurar um laboratório com máquinas virtuais de GPU

Este artigo mostra como fazer as seguintes tarefas:

- Escolha entre a *Visualização* e as GPUs (unidades de processamento gráfico de *computação* ).
- Verifique se os drivers de GPU apropriados estão instalados.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Escolha entre visualização e tamanhos de GPU de computação
Na primeira página do assistente de criação de laboratório, na lista suspensa **qual tamanho de máquina virtual você precisa?** , selecione o tamanho das VMs que são necessárias para sua classe.  

![Captura de tela do painel "novo laboratório" para selecionar um tamanho de VM](./media/how-to-setup-gpu/lab-gpu-selection.png)

Nesse processo, você tem a opção de selecionar uma **Visualização** ou GPUs de **computação** .  É importante escolher o tipo de GPU com base no software que seus alunos usarão.  

Conforme descrito na tabela a seguir, o tamanho da GPU de *computação* destina-se a aplicativos de computação intensiva.  Por exemplo, o [aprendizado profundo no tipo de classe de processamento de idioma natural](./class-type-deep-learning-natural-language-processing.md) usa o tamanho **pequeno de GPU (computação)** .  A GPU de computação é adequada para esse tipo de classe, pois os alunos usam estruturas e ferramentas de aprendizado profundo que são fornecidas pela [imagem de máquina virtual de ciência de dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) para treinar modelos de aprendizado profundo com grandes conjuntos de dados.

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| GPU Pequena (Computação) | -&nbsp;6 &nbsp; núcleos<br>-&nbsp;56 &nbsp; GB de &nbsp; RAM  | [Standard_NC6](../virtual-machines/nc-series.md) |Esse tamanho é mais adequado para aplicativos de computação intensiva, como ia (inteligência artificial) e aprendizado profundo. |

Os tamanhos de GPU de *Visualização* destinam-se a aplicativos com uso intensivo de gráficos.  Por exemplo, o [tipo de classe de engenharia SolidWorks](./class-type-solidworks.md) mostra o uso do tamanho **pequeno de GPU (visualização)** .  A GPU de visualização é adequada para esse tipo de classe, pois os alunos interagem com o ambiente CAD (design auxiliado por computador) SOLIDWORKS 3D para modelar e visualizar objetos sólidos.

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| GPU Pequena (Visualização) | -&nbsp;6 &nbsp; núcleos<br>-&nbsp;56 &nbsp; GB de &nbsp; RAM  | [Standard_NV6](../virtual-machines/nv-series.md) | Esse tamanho é mais adequado para visualização remota, streaming, jogos e codificação que usam estruturas como OpenGL e DirectX. |
| GPU Média (Visualização) | -&nbsp;12 &nbsp; núcleos<br>-&nbsp;112 &nbsp; GB de &nbsp; RAM  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Esse tamanho é mais adequado para visualização remota, streaming, jogos e codificação que usam estruturas como OpenGL e DirectX. |

> [!NOTE]
> Talvez você não veja alguns desses tamanhos de VM na lista ao criar um laboratório de sala de aula. A lista é preenchida com base na capacidade atual do local do laboratório. Se o criador da conta de laboratório [permitir que os criadores de laboratório escolham um local para ele ](allow-lab-creator-pick-lab-location.md), você poderá tentar escolher um local diferente para o laboratório e ver se o tamanho da VM está disponível. Para a disponibilidade de VMs, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/?products=virtual-machines).

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Verifique se os drivers de GPU apropriados estão instalados
Para aproveitar os recursos de GPU de suas VMs de laboratório, verifique se os drivers de GPU apropriados estão instalados.  No assistente de criação de laboratório, quando você seleciona um tamanho de VM de GPU, você pode selecionar a opção **instalar drivers de GPU** .  

![Captura de tela do "novo laboratório" mostrando a opção "instalar drivers de GPU"](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Conforme mostrado na imagem anterior, essa opção é habilitada por padrão, o que garante que os drivers *mais recentes* sejam instalados para o tipo de GPU e imagem que você selecionou.
- Quando você seleciona um tamanho de GPU de *computação* , suas VMs de laboratório são alimentadas pela GPU [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) .  Nesse caso, os drivers de [CUDA (arquitetura de dispositivo unificado de computação)](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) mais recentes são instalados, o que permite a computação de alto desempenho.
- Quando você seleciona um tamanho de GPU de *Visualização* , suas VMs de laboratório são alimentadas pela tecnologia [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU e [Grid](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  Nesse caso, os drivers de grade mais recentes são instalados, o que permite o uso de aplicativos com uso intensivo de gráficos.

### <a name="install-the-drivers-manually"></a>Instalar os drivers manualmente
Talvez seja necessário instalar uma versão de driver diferente da versão mais recente.  Esta seção mostra como instalar manualmente os drivers apropriados, dependendo se você estiver usando uma GPU de *computação* ou uma GPU de *Visualização* .

#### <a name="install-the-compute-gpu-drivers"></a>Instalar os drivers de GPU de computação

Para instalar manualmente os drivers para o tamanho de GPU de computação, faça o seguinte:

1. No assistente de criação de laboratório, quando você estiver [criando seu laboratório](./how-to-manage-classroom-labs.md), desabilite a configuração **instalar drivers de GPU** .

1. Depois que o laboratório for criado, conecte-se à VM de modelo para instalar os drivers apropriados.

   ![Captura de tela da página de downloads do driver NVIDIA](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. Em um navegador, vá para a [página de downloads do Driver Nvidia](https://www.nvidia.com/Download/index.aspx).  
   b. Defina o **tipo de produto** como **Tesla**.  
   c. Defina a **série de produtos** para a **série K**.  
   d. Defina o **sistema operacional** de acordo com o tipo de imagem base que você selecionou quando criou seu laboratório.  
   e. Defina o **Kit de ferramentas CUDA** para a versão do driver CUDA que você precisa.  
   f. Selecione **Pesquisar** para procurar seus drivers.  
   g. Selecione **baixar** para baixar o instalador.  
   h. Execute o instalador para que os drivers sejam instalados na VM do modelo.  
1. Valide se os drivers estão instalados corretamente seguindo as instruções na seção [validar os drivers instalados](how-to-setup-lab-gpu.md#validate-the-installed-drivers) . 
1. Depois de instalar os drivers e outros softwares necessários para sua classe, selecione **publicar** para criar as VMs dos seus alunos.

> [!NOTE]
> Se você estiver usando uma imagem do Linux, depois de baixar o instalador, instale os drivers seguindo as instruções em [instalar drivers do CUDA no Linux](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="install-the-visualization-gpu-drivers"></a>Instalar os drivers de GPU de visualização

Para instalar manualmente os drivers para o tamanho de GPU de visualização, faça o seguinte:

1. No assistente de criação de laboratório, quando você estiver [criando seu laboratório](./how-to-manage-classroom-labs.md), desabilite a configuração **instalar drivers de GPU** .
1. Depois que o laboratório for criado, conecte-se à VM de modelo para instalar os drivers apropriados.
1. Instale os drivers de grade fornecidos pela Microsoft na VM de modelo seguindo as instruções para seu sistema operacional:
   -  [Drivers de grade NVIDIA do Windows](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)
   -  [Drivers de grade NVIDIA do Linux](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#nvidia-grid-drivers)
  
1. Reinicie a VM de modelo.
1. Valide se os drivers estão instalados corretamente seguindo as instruções na seção [validar os drivers instalados](how-to-setup-lab-gpu.md#validate-the-installed-drivers) .
1. Depois de instalar os drivers e outros softwares necessários para sua classe, selecione **publicar** para criar as VMs dos seus alunos.

### <a name="validate-the-installed-drivers"></a>Validar os drivers instalados
Esta seção descreve como validar se os drivers de GPU estão instalados corretamente.

#### <a name="windows-images"></a>Imagens do Windows
1.  Siga as instruções na seção "verificar a instalação do driver" de [instalar drivers NVIDIA GPU em VMs da série N que executam o Windows](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation).
1.  Se você estiver usando uma GPU de *Visualização* , também poderá:
    - Exiba e ajuste as configurações de GPU no painel de controle NVIDIA. Para fazer isso, no **painel de controle do Windows**, selecione **hardware** e, em seguida, selecione **painel de controle NVIDIA**.

      ![Captura de tela do painel de controle do Windows mostrando o link do painel de controle NVIDIA](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Exiba o desempenho da GPU usando o **Gerenciador de tarefas**.  Para fazer isso, selecione a guia **desempenho** e, em seguida, selecione a opção **GPU** .

       ![Captura de tela mostrando a guia desempenho da GPU do Gerenciador de tarefas](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > As configurações do painel de controle do NVIDIA podem ser acessadas somente para GPUs de *Visualização* .  Se você tentar abrir o painel de controle NVIDIA para uma GPU de computação, obterá o seguinte erro: "as configurações de vídeo NVIDIA não estão disponíveis.  No momento, você não está usando uma exibição anexada a uma GPU NVIDIA. "  Da mesma forma, as informações de desempenho de GPU no Gerenciador de tarefas são fornecidas somente para GPUs de visualização.

#### <a name="linux-images"></a>Imagens do Linux
Siga as instruções na seção "verificar a instalação do driver" de [instalar drivers NVIDIA GPU em VMs da série N que executam o Linux](../virtual-machines/linux/n-series-driver-setup.md#verify-driver-installation).

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Criar e gerenciar laboratórios](how-to-manage-classroom-labs.md)
- [Tipo de classe CAD (design auxiliado por computador) SOLIDWORKS](class-type-solidworks.md)
- [Tipo de classe MATLAB (laboratório de matriz)](class-type-matlab.md)