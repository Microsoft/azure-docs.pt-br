---
title: Usar um módulo do IoT Edge para implantar uma carga de trabalho de computação no Azure Stack Edge Pro com GPU | Microsoft Docs
description: Saiba como executar uma carga de trabalho de computação usando um módulo existente do IoT Edge em seu dispositivo de GPU do Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d10e27c80a9253de7482644debd19debce8f4e50
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055290"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Tutorial: Executar uma carga de trabalho de computação com o módulo do IoT Edge na GPU do Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Este tutorial descreve como executar uma carga de trabalho de computação usando um módulo do IoT Edge em seu dispositivo de GPU do Azure Stack Edge Pro. Após você configurar a computação, o dispositivo transformará os dados antes de enviá-los para o Azure.

Esse procedimento pode levar cerca de 10 a 15 minutos para ser concluído.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a computação
> * Adicionar compartilhamentos
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação em seu dispositivo de GPU do Azure Stack Edge Pro, verifique o seguinte:

- Você ativou o dispositivo Azure Stack Edge Pro conforme descrito em [Ativar seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Você tem um módulo do IoT Edge que pode ser executado em seus dados. Neste tutorial, usamos um módulo `filemove2` que move dados do compartilhamento local do Edge em seu dispositivo para o compartilhamento do Edge do qual os dados vão para a conta de Armazenamento do Azure.


## <a name="configure-compute"></a>Configurar a computação

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Adicionar compartilhamentos

Para a implantação simples neste tutorial, você precisará de dois compartilhamentos: um compartilhamento do Edge e outro compartilhamento local do Edge.

1. Para adicionar um compartilhamento do Edge no dispositivo, execute as seguintes etapas:

    1. Em seu recurso do Azure Stack Edge, vá para **Gateway de armazenamento em nuvem > Compartilhamentos**.
    2. Na barra de comandos, selecione **+ Adicionar compartilhamento**.
    3. Na folha **Adicionar compartilhamento**, forneça o nome do compartilhamento e selecione o tipo de compartilhamento.
    4. Para montar o compartilhamento do Microsoft Edge, marque a caixa de seleção **Usar o compartilhamento com a computação de borda**.
    5. Selecione a **Conta de armazenamento**, o **Serviço de armazenamento**, um usuário existente e, em seguida, selecione **Criar**.

        ![Adicionar um compartilhamento do Microsoft Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > Para montar o compartilhamento NFS para computação, a rede de computação deve ser configurada na mesma sub-rede que o endereço IP virtual do NFS. Para obter detalhes de como configurar a rede de computação, acesse [Habilitar a rede de computação no Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    O compartilhamento do Microsoft Edge será criado, e você receberá uma notificação de êxito na criação. A lista de compartilhamento pode ser atualizada, mas você deve aguardar a criação do compartilhamento ser concluída.

2. Para adicionar um compartilhamento local do Edge no dispositivo, repita todas as etapas da etapa anterior e marque a caixa de seleção **Configurar como o compartilhamento local do Microsoft Edge**. Os dados no compartilhamento local permanecem no dispositivo.

    ![Adicionar um compartilhamento local do Microsoft Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Se você criou um compartilhamento NFS local, use a seguinte opção de comando rsync (compartilhamento remoto) para copiar os arquivos no compartilhamento:

    `rsync <source file path> < destination file path>`

    Para obter mais informações sobre o comando `rsync`, acesse a [documentação do `Rsync`](https://www.computerhope.com/unix/rsync.htm).
 
3. Vá para **Gateway de armazenamento em nuvem > Compartilhamentos** para ver a lista atualizada de compartilhamentos.

    ![Lista atualizada de compartilhamentos](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Adicionar um módulo

Você pode adicionar um módulo personalizado ou pré-criado. O dispositivo não vem com módulos predefinidos nem personalizados. Para saber como criar um módulo personalizado, acesse [Desenvolver um módulo em C# para o dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md).

Nesta seção, você adiciona um módulo personalizado ao dispositivo do IoT Edge que foi criado em [Desenvolver um módulo em C# para o Azure Stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md). Esse módulo personalizado usa arquivos de um compartilhamento local do Microsoft Edge no dispositivo do Microsoft Edge e move-os para um compartilhamento do Microsoft Edge (nuvem) no dispositivo. O compartilhamento em nuvem então efetua o push dos arquivos para a conta de Armazenamento do Azure associada com o compartilhamento em nuvem.

Para adicionar um módulo, siga estas etapas:

1. Vá para **IoT Edge > Módulos**. Na barra de comandos, selecione **+ Adicionar módulo**. 

2. Na folha **Adicionar módulo**, adicione os seguintes valores de entrada:

    
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

    ![Adicionar e configurar o módulo](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Selecione **Adicionar**. O módulo é adicionado. A página **IoT Edge > Módulos** é atualizada para indicar que o módulo foi implantado. O status do runtime do módulo adicionado deve ser *em execução*.

    ![Módulo implantado](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificar a transformação e a transferência de dados

A etapa final é garantir que o módulo esteja em execução e processando dados como esperado. O status de tempo de execução do módulo deve estar em execução para seu dispositivo IoT Edge no recurso do Hub IoT.

Para verificar se o módulo está em execução e processando dados conforme o esperado, faça o seguinte:


1. No Explorador de Arquivos, conecte-se aos compartilhamentos do Microsoft Edge e local do Microsoft Edge criados anteriormente. Confira as etapas 

    ![Conectar-se aos compartilhamentos em nuvem e local do Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Adicione dados ao compartilhamento de local.

    ![Arquivo copiado para o compartilhamento local do Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   Os dados são movidos para o compartilhamento em nuvem.

    ![Arquivo movido para o compartilhamento em nuvem do Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Os dados são então enviados por push do compartilhamento em nuvem para a conta de armazenamento. Para ver os dados, você pode usar o Gerenciador de Armazenamento ou o Armazenamento do Azure no portal.

    ![Verificar dados na conta de armazenamento](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
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
> [Usar IU da Web local para administrar um Azure Stack Edge Pro](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)