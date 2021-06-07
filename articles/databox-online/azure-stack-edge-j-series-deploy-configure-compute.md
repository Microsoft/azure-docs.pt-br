---
title: Tutorial para filtrar e analisar dados com a computação no Azure Stack Edge Pro com GPU | Microsoft Docs
description: Saiba como configurar a função de computação em dispositivo de GPU do Azure Stack Edge Pro e usá-la para transformar dados antes de enviá-los para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 1dcf429ddc697dfbfdac721cb8de137b9382b234
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058775"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro"></a>Tutorial: Transformar dados com o Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este tutorial descreve como configurar uma função de computação no dispositivo Azure Stack Edge Pro. Depois de configurar a função de computação, o Azure Stack Edge Pro pode transformar os dados antes de enviar para o Azure.

Esse procedimento pode levar cerca de 10 a 15 minutos para ser concluído.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a computação
> * Adicionar compartilhamentos
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação em seu dispositivo Azure Stack Edge Pro, verifique o seguinte:
- Você ativou o dispositivo Azure Stack Edge Pro conforme descrito em [Ativar seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).


## <a name="configure-compute"></a>Configurar a computação

Para configurar a computação no Azure Stack Edge Pro, você criará um recurso do Hub IoT.

1. No portal do Azure do recurso do Azure Stack Edge, acesse **Visão geral** e selecione **IoT Edge**.

   ![Introdução à computação](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. Em **Habilitar o serviço do IoT Edge**, selecione **Adicionar**.

   ![Configurar a computação](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. Em **Criar um serviço do IoT Edge**, insira as configurações para o recurso do Hub IoT:

   |Campo   |Valor    |
   |--------|---------|
   |Subscription      | Assinatura usada pelo recurso do Azure Stack Edge. |
   |Grupo de recursos    | Grupo de recursos usado pelo recurso do Azure Stack Edge. |
   |Hub IoT           | Escolha entre **Criar** ou **Usar existente**. <br> Por padrão, uma camada Standard (S1) é usada para criar um recurso de IoT. Para usar um recurso de IoT de Camada gratuita, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do Hub IoT usa a mesma assinatura e o mesmo grupo de recursos usados pelo recurso do Azure Stack Edge.     |
   |Nome              | Se você não quiser usar o nome padrão fornecido para um novo recurso do Hub IoT, insira um nome diferente. |

    ![Introdução à computação 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Ao concluir as configurações, selecione **Examinar + Criar**. Examine as configurações do recurso do Hub IoT e selecione **Criar**.

   A criação de um recurso do Hub IoT leva vários minutos. Depois que o recurso é criado, a **Visão geral** indica que o serviço IoT Edge está sendo executado agora.

    ![Introdução à computação 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Para confirmar se a função de Computação de borda foi configurada, selecione **Propriedades**.

   ![Introdução à computação 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

   Quando a função de computação de borda está configurada no dispositivo de borda, são criados dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Os dois dispositivos podem ser exibidos no recurso do Hub IoT. Um runtime do IoT Edge também está em execução no dispositivo do IoT Edge. No momento, somente a plataforma Linux está disponível para o dispositivo IoT Edge.


## <a name="add-shares"></a>Adicionar compartilhamentos

Para a implantação simples neste tutorial, você precisará de dois compartilhamentos: um compartilhamento do Edge e outro compartilhamento local do Edge.

1. Adicione um compartilhamento do Microsoft Edge no dispositivo seguindo as seguintes etapas:

    1. No recurso Azure Stack Edge, acesse **Computação de Borda > Introdução**.
    2. No bloco **Adicionar compartilhamentos**, selecione **Adicionar**.

        ![Adicionar bloco de compartilhamento](./media/azure-stack-edge-j-series-deploy-configure-compute/add-share-1.png) 

    3. Na folha **Adicionar compartilhamento**, forneça o nome do compartilhamento e selecione o tipo de compartilhamento.
    4. Para montar o compartilhamento do Microsoft Edge, marque a caixa de seleção **Usar o compartilhamento com a computação de borda**.
    5. Selecione a **Conta de armazenamento**, o **Serviço de armazenamento**, um usuário existente e, em seguida, selecione **Criar**.

        ![Adicionar um compartilhamento do Microsoft Edge](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-1.png) 

    Se você criou um compartilhamento NFS local, use a seguinte opção de comando rsync (`rsync`) para copiar os arquivos no compartilhamento:

    `rsync <source file path> < destination file path>`

    Para obter mais informações sobre o comando `rsync`, acesse a [documentação do `Rsync`](https://www.computerhope.com/unix/rsync.htm).

    > [!NOTE]
    > Para montar o compartilhamento NFS para computação, a rede de computação deve ser configurada na mesma sub-rede que o endereço IP virtual do NFS. Para obter detalhes de como configurar a rede de computação, acesse [Habilitar a rede de computação no Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    O compartilhamento do Microsoft Edge será criado, e você receberá uma notificação de êxito na criação. A lista de compartilhamento pode ser atualizada, mas você deve aguardar a criação do compartilhamento ser concluída.

2. Adicione um compartilhamento local do Microsoft Edge no dispositivo do Microsoft Edge repetindo todas as etapas da etapa anterior e marcando a caixa de seleção **Configurar como o compartilhamento local do Microsoft Edge**. Os dados no compartilhamento local permanecem no dispositivo.

    ![Adicionar um compartilhamento local do Microsoft Edge](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-2.png)

  
3. Selecione **Adicionar compartilhamentos** para ver a lista atualizada de compartilhamentos.

    ![Lista atualizada de compartilhamentos](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Adicionar um módulo

Você pode adicionar um módulo personalizado ou pré-criado. Não há módulos personalizados neste dispositivo do Edge. Para saber como criar um módulo personalizado, acesse [Desenvolver um módulo em C# para o dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md).

Nesta seção, você adiciona um módulo personalizado ao dispositivo do IoT Edge que foi criado em [Desenvolver um módulo em C# para o Azure Stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md). Esse módulo personalizado usa arquivos de um compartilhamento local do Microsoft Edge no dispositivo do Microsoft Edge e move-os para um compartilhamento do Microsoft Edge (nuvem) no dispositivo. O compartilhamento em nuvem então efetua o push dos arquivos para a conta de Armazenamento do Azure associada com o compartilhamento em nuvem.

1. Acesse **Computação de borda > Introdução**. No bloco **Adicionar módulos**, selecione o tipo de cenário como **simples**. Selecione **Adicionar**.
2. Na folha **Configurar e adicionar módulo**, insira os seguintes valores:

    
    |Campo  |Valor  |
    |---------|---------|
    |Nome     | Um nome exclusivo para o módulo. Esse módulo é um contêiner do Docker que você pode implantar no dispositivo do IoT Edge associado ao Azure Stack Edge Pro.        |
    |URI da imagem     | O URI da imagem para a imagem de contêiner correspondente ao módulo.        |
    |Credenciais necessárias     | Se essa opção for marcada, o nome de usuário e a senha serão usados para recuperar os módulos com uma URL correspondente.        |
    |Compartilhamento de entrada     | Selecione um compartilhamento de entrada. O compartilhamento local do Microsoft Edge é o compartilhamento de entrada, nesse caso. O módulo usado aqui move os arquivos do compartilhamento local do Microsoft Edge para um compartilhamento do Microsoft Edge, em que são carregados para a nuvem.        |
    |Compartilhamento de saída     | Selecione um compartilhamento de saída. O compartilhamento do Microsoft Edge é o compartilhamento de saída, nesse caso.        |
    |Tipo de gatilho     | Selecione **Arquivo** ou **Agendamento**. Um gatilho de arquivo é acionado sempre que ocorre um evento de arquivo, como uma gravação de arquivo no compartilhamento de entrada. Um gatilho agendado é acionado de acordo com um agendamento definido por você.         |
    |Nome do gatilho     | Um nome exclusivo para o gatilho.         |
    |Variáveis de ambiente| Informações opcionais que ajudarão a definir o ambiente no qual o módulo será executado.   |

    ![Adicionar e configurar o módulo](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-1.png)

3. Selecione **Adicionar**. O módulo é adicionado. Vá para a página **Visão geral**. O bloco **Módulos** é atualizado para indicar que o módulo foi implantado. 

    ![Módulo implantado](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência de dados

A etapa final é garantir que o módulo esteja conectado e funcionando como esperado. O status de tempo de execução do módulo deve estar em execução para seu dispositivo IoT Edge no recurso do Hub IoT.

Para verificar se o módulo está em execução, faça o seguinte:

1. Selecione o bloco **Adicionar módulo**. Isso direcionará você para a folha **Módulos**. Na lista de módulos, identifique o módulo implantado. O status do runtime do módulo adicionado deve ser *em execução*.

    ![Exibir o módulo implantado](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-3.png)
 
1. No Explorador de Arquivos, conecte-se aos compartilhamentos do Microsoft Edge e local do Microsoft Edge criados anteriormente.

    ![Verificar a transformação de dados – 1](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-2.png) 
 
1. Adicione dados ao compartilhamento de local.

    ![Verificar a transformação de dados – 2](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-3.png) 
 
   Os dados são movidos para o compartilhamento em nuvem.

    ![Verificar a transformação de dados – 3](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-4.png)  

   Os dados são então enviados por push do compartilhamento em nuvem para a conta de armazenamento. Para ver os dados, você pode usar o Gerenciador de Armazenamento.

    <!--![Verify data transform](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-5.png)-->
 
Você concluiu o processo de validação.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar a computação
> * Adicionar compartilhamentos
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

Para saber como administrar seu dispositivo Azure Stack Edge Pro, confira:

> [!div class="nextstepaction"]
> [Usar IU da Web local para administrar um Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)