---
title: Executar um módulo de GPU no dispositivo Microsoft Azure Stack GPU pro Edge | Microsoft Docs
description: Descreve como configurar e executar um módulo na GPU em um dispositivo Azure Stack Edge pro por meio do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 8be0a444dca4cae9b8f7a1e7c04be8a3046e56be
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637743"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Configurar e executar um módulo na GPU no dispositivo Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

O dispositivo pro Edge Azure Stack contém uma ou mais GPU (unidade de processamento gráfico). As GPUs são uma opção popular para cálculos de IA, pois oferecem recursos de processamento paralelo e são mais rápidas na renderização de imagens do que as CPUs (unidades de processamento central). Para obter mais informações sobre a GPU contida em seu dispositivo Azure Stack Edge pro, acesse [Azure Stack especificações técnicas do dispositivo do Edge pro](azure-stack-edge-gpu-technical-specifications-compliance.md).

Este artigo descreve como configurar e executar um módulo na GPU em seu dispositivo Azure Stack Edge pro. Neste artigo, você usará os **dígitos** do módulo de contêiner disponíveis publicamente escritos para GPUs NVIDIA T4. Esse procedimento pode ser usado para configurar outros módulos publicados pela NVIDIA para essas GPUs.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você tem acesso a um dispositivo Azure Stack Edge pro de 1 nó com GPU habilitado. Esse dispositivo foi ativado com um recurso no Azure.  

## <a name="configure-module-to-use-gpu"></a>Configurar o módulo para usar a GPU

Para configurar um módulo para usar a GPU em seu dispositivo Azure Stack Edge pro para executar um módulo,<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> Siga estas etapas.

1. No portal do Azure, vá para o recurso associado ao seu dispositivo.

2. Em **visão geral**, selecione **IOT Edge**.

    ![Configurar o módulo para usar a GPU 1](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-1.png)

3. Em **Habilitar o serviço do IoT Edge**, selecione **Adicionar**.

   ![Configurar o módulo para usar a GPU 2](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-2.png)

4. Em **Criar um serviço do IoT Edge**, insira as configurações para o recurso do Hub IoT:

   |Campo   |Valor    |
   |--------|---------|
   |Subscription      | Assinatura usada pelo recurso do Azure Stack Edge. |
   |Grupo de recursos    | Grupo de recursos usado pelo recurso do Azure Stack Edge. |
   |Hub IoT           | Escolha entre **Criar** ou **Usar existente**. <br> Por padrão, uma camada Standard (S1) é usada para criar um recurso de IoT. Para usar um recurso de IoT de Camada gratuita, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do Hub IoT usa a mesma assinatura e o mesmo grupo de recursos usados pelo recurso do Azure Stack Edge.     |
   |Nome              | Se você não quiser usar o nome padrão fornecido para um novo recurso do Hub IoT, insira um nome diferente. |

   Ao concluir as configurações, selecione **Examinar + Criar**. Examine as configurações do recurso do Hub IoT e selecione **Criar**.

   ![Introdução à computação 2](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-3.png)

   A criação de um recurso do Hub IoT leva vários minutos. Depois que o recurso é criado, a **Visão geral** indica que o serviço IoT Edge está sendo executado agora.

   ![Introdução à computação 3](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-4.png)

5. Para confirmar se a função de Computação de borda foi configurada, selecione **Propriedades**.

   ![Introdução à computação 4](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-5.png)

6. Em **Propriedades**, selecione o link para **IOT Edge dispositivo**.

   ![Configurar o módulo para usar a GPU 6](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-2.png)

   No painel direito, você verá o dispositivo IoT Edge associado ao dispositivo Azure Stack Edge pro. Este dispositivo corresponde ao dispositivo IoT Edge que você criou ao criar o recurso do Hub IoT.
 
7. Selecione este dispositivo IoT Edge.

   ![Configurar o módulo para usar a GPU 7](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-3.png)

8. Selecione **Definir módulos**.

   ![Configurar o módulo para usar a GPU 8](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-4.png)

9. Selecione **+ Adicionar** e, em seguida, selecione **+ IOT Edge módulo**. 

    ![Configurar o módulo para usar a GPU 9](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-5.png)

10. Na guia **Adicionar módulo de IOT Edge** :

    1. Forneça o **URI da imagem**. Você usará os **dígitos** do módulo NVIDIA disponíveis publicamente aqui. 
    
    2. Defina a **política de reinicialização** como **sempre**.
    
    3. Defina o **estado desejado** como **em execução**.
    
    ![Configurar o módulo para usar a GPU 10](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-6.png)

11. Na guia **variáveis de ambiente** , forneça o nome da variável e o valor correspondente. 

    1. Para que o módulo atual use uma GPU nesse dispositivo, use o NVIDIA_VISIBLE_DEVICES. 

    2. Defina o valor como 0 ou 1. Um valor de 0 ou 1 garante que pelo menos uma GPU seja usada pelo dispositivo para este módulo. Quando você define o valor como 0, 1, isso significa que ambas as GPUs em seu dispositivo estão sendo usadas por este módulo.

       ![Configurar o módulo para usar a GPU 11](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-7.png)

       Para obter mais informações sobre variáveis de ambiente que você pode usar com a GPU NVIDIA, vá para [tempo de execução de contêiner NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Uma GPU só pode ser mapeada para um módulo. Um módulo pode, no entanto, usar uma, ambas ou nenhuma GPUs.

12. Digite um nome para o módulo. Neste ponto, você pode optar por fornecer a opção de criação de contêiner e modificar as configurações de módulo de configuração ou, se terminar, selecionar **Adicionar**. 

    ![Configurar o módulo para usar a GPU 12](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-8.png)

13. Verifique se o módulo está em execução e selecione **revisar + criar**.

    ![Configurar o módulo para usar a GPU 13](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-9.png)

14. Na guia **revisar + criar** , as opções de implantação que você selecionou são exibidas. Examine as opções e selecione **criar**.
    
    ![Configurar o módulo para usar a GPU 14](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-10.png)

15. Anote o **status do tempo de execução** do módulo.
    
    ![Configurar o módulo para usar a GPU 15](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-11.png)

    Leva alguns minutos para que o módulo seja implantado. Selecione **Atualizar** e você deverá ver a atualização do **status de tempo de execução** para **em execução**.

    ![Configurar o módulo para usar a GPU 16](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as variáveis de ambiente que você pode usar com a GPU NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
