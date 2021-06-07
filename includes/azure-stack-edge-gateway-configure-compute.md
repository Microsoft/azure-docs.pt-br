---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99524727"
---
Para configurar a computação no Azure Stack Edge Pro, você criará um recurso do Hub IoT do meio do portal do Azure.

1. No portal do Azure do recurso do Azure Stack Edge, acesse **Visão geral** e selecione **IoT Edge**.

   ![Introdução à computação](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. Em **Habilitar o serviço do IoT Edge**, selecione **Adicionar**.

   ![Configurar a computação](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. Na folha **Configurar Computação de borda**, insira as seguintes informações:
   
    |Campo  |Valor  |
    |---------|---------|
    |Subscription     |Selecione uma assinatura para o recurso do Hub IoT. Você pode usar a mesma assinatura usada pelo recurso Azure Stack Edge.         |
    |Grupo de recursos     |Selecione um grupo de recursos para o recurso do Hub IoT. Você pode usar o mesmo grupo de recursos usado pelo recurso do Azure Stack Edge.         |
    |Hub IoT     | Escolha **Novo** ou **Existente**. <br> Por padrão, uma camada Standard (S1) é usada para criar um recurso de IoT. Para usar um recurso de IoT de Camada gratuita, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do Hub IoT usa a mesma assinatura e o mesmo grupo de recursos usados pelo recurso do Azure Stack Edge.     |
    |Nome     |Aceite o nome padrão ou insira um nome para o recurso do Hub IoT.         |

   ![Introdução à computação 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. Ao concluir as configurações, selecione **Examinar + Criar**. Examine as configurações do recurso do Hub IoT e selecione **Criar**.

   A criação de um recurso do Hub IoT leva vários minutos. Depois que o recurso é criado, a **Visão geral** indica que o serviço IoT Edge está sendo executado agora.

   ![Introdução à computação 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Para confirmar se a função de computação de borda foi configurada, vá até **IoT Edge > Propriedades**.

   ![Introdução à computação 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Quando a função de computação de borda está configurada no dispositivo de borda, são criados dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Os dois dispositivos podem ser exibidos no recurso do Hub IoT. Um runtime do IoT Edge também está em execução no dispositivo do IoT Edge. No momento, somente a plataforma Linux está disponível para o dispositivo IoT Edge.

Pode levar de 20 a 30 minutos para configurar a computação já que, nos bastidores, as máquinas virtuais e um cluster do Kubernetes estão sendo criados.

Depois de você configurar com êxito a computação no portal do Azure, um cluster Kubernetes e um usuário padrão associado ao namespace de IoT (um namespace do sistema controlado pelo Azure Stack Edge Pro) passarão a existir.
