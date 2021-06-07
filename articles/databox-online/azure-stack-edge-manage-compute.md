---
title: Gerenciamento de computação do Azure Stack Edge pro | Microsoft Docs
description: Descreve como gerenciar as configurações de computação de borda, como gatilho, módulos, exibir configuração de computação, remover a configuração por meio do portal do Azure no Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: ebf967fe02ab6424b9952bb0315b70fadb547fe3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97967747"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro"></a>Gerenciar a computação em seu Azure Stack Edge pro

Este artigo descreve como gerenciar a computação em seu Azure Stack Edge pro. É possível gerenciar a computação pelo portal do Azure ou pela IU da Web local. Use o portal do Azure para gerenciar módulos, gatilhos e configuração de computação e a IU da Web local para gerenciar configurações de computação.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Gerenciar disparadores
> * Gerenciar configuração da computação


## <a name="manage-triggers"></a>Gerenciar disparadores

Os eventos são coisas que acontecem dentro de seu ambiente de nuvem ou em seu dispositivo no qual você pode executar uma ação. Por exemplo, quando um arquivo é criado em um compartilhamento, ele é um evento. Gatilhos acionam os eventos. Para o Azure Stack Edge pro, os gatilhos podem estar em resposta a eventos de arquivo ou a um agendamento.

- **Arquivo**: esses gatilhos estão em resposta a eventos de arquivo, como a criação de um arquivo, a modificação de um arquivo.
- **Agendado**: esses gatilhos estão em resposta a uma agenda que você pode definir com uma data de início, uma hora de início e o intervalo de repetição.


### <a name="add-a-trigger"></a>Adicionar um gatilho

Siga estas etapas no portal do Azure para criar um gatilho.

1. Na portal do Azure, vá para o recurso de borda do Azure Stack e, em seguida, vá para **IOT Edge gatilhos de >**. Selecione **+ Adicionar gatilho** na barra de comandos.

    ![Selecione adicionar gatilho](media/azure-stack-edge-manage-compute/add-trigger-1.png)

2. Na folha **Adicionar gatilho**, forneça um nome exclusivo para o gatilho.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Selecione um **Tipo** para o gatilho. Escolha **Arquivo** quando o gatilho for em resposta a um evento de arquivo. Selecione **Agendado** quando desejar que o gatilho inicie em um horário definido e seja executado em um intervalo de repetição especificado. Dependendo da sua seleção, um conjunto de opções diferente é apresentado.

    - **Gatilho de arquivo** – escolha na lista suspensa de um compartilhamento montado. Quando um evento de arquivo é acionado nesse compartilhamento, o gatilho invocaria uma função do Azure.

        ![Adicionar compartilhamento SMB](media/azure-stack-edge-manage-compute/add-file-trigger.png)

    - **Gatilho agendado** – especifique a data/hora de início e o intervalo de repetição em horas, minutos ou segundos. Além disso, insira o nome de um tópico. Um tópico oferecerá a você a flexibilidade de rotear o gatilho para um módulo implantado no dispositivo.

        Um exemplo de cadeia de caracteres de rota é: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`.

        ![Adicionar compartilhamento NFS](media/azure-stack-edge-manage-compute/add-scheduled-trigger.png)

4. Selecione **Adicionar** para criar o gatilho. Uma notificação mostra que a criação do gatilho está em andamento. Depois que o gatilho é criado, a folha é atualizada para refletir o novo gatilho.
 
    ![Lista de gatilhos atualizada](media/azure-stack-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Excluir um gatilho

Siga estas etapas no portal do Azure para excluir um gatilho.

1. Na lista de gatilhos, selecione o gatilho que você deseja excluir.

    ![Selecionar gatilho](media/azure-stack-edge-manage-compute/delete-trigger-1.png)

2. Clique com o botão direito do mouse e selecione **Excluir**.

    ![Selecione excluir](media/azure-stack-edge-manage-compute/delete-trigger-2.png)

3. Quando solicitada a confirmação, clique em **Sim**.

    ![Confirmar exclusão](media/azure-stack-edge-manage-compute/delete-trigger-3.png)

A lista de gatilhos também é atualizada para refletir a exclusão.

## <a name="manage-compute-configuration"></a>Gerenciar configuração da computação

Use o portal do Azure para exibir a configuração de computação, remover uma configuração de computação existente ou para atualizar a configuração de computação para sincronizar as chaves de acesso para o dispositivo IoT e o dispositivo de IoT Edge para o Azure Stack Edge pro.

### <a name="view-compute-configuration"></a>Exibir configuração de computação

Siga estas etapas no portal do Azure para exibir a configuração de computação para seu dispositivo.

1. Na portal do Azure, vá para o recurso de borda do Azure Stack e vá para **IoT Edge > visão geral**. 

    ![Selecione Exibir computação](media/azure-stack-edge-manage-compute/view-compute-1.png)

2. Vá para a página de **Propriedades** . Anote a configuração de computação em seu dispositivo. Quando você tiver configurado a computação, você terá criado um recurso de hub IoT. Nesse recurso de hub IoT, um dispositivo IoT e um dispositivo IoT Edge são configurados. Há suporte apenas para a execução dos módulos do Linux no dispositivo IoT Edge.

    ![Exibir configuração](media/azure-stack-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Remover configuração da computação

Siga estas etapas no portal do Azure para remover a configuração de computação de borda existente para seu dispositivo.

1. Na portal do Azure, vá para o recurso de borda do Azure Stack e vá para **IoT Edge > visão geral**. Selecione **remover** na barra de comandos.

    ![Selecione Remover computação](media/azure-stack-edge-manage-compute/remove-compute-1.png)

2. Se você remover a configuração de computação, será necessário reconfigurar o dispositivo caso seja necessário usar a computação novamente. Quando solicitado a confirmar, selecione **Sim**.

    ![Selecione remover computação 2](media/azure-stack-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Sincronizar as chaves de acesso do dispositivo IoT e IoT Edge

Quando você configura a computação no Azure Stack Edge pro, um dispositivo IoT e um dispositivo de IoT Edge são criados. Esses dispositivos recebem chaves de acesso simétricas automaticamente. Como uma melhor prática de segurança, essas chaves são trocadas regularmente por meio do serviço de Hub IoT.

Para trocar essas chaves, é possível acessar o serviço de Hub IoT que você criou e selecione o dispositivo IoT ou IoT Edge. Cada dispositivo tem uma chave de acesso primária e uma secundária. Atribua a chave de acesso primária à chave de acesso secundária e gere novamente a chave de acesso primária.

Se o dispositivo IoT e IoT Edge as chaves do dispositivo tiverem sido girados, você precisará atualizar a configuração em seu Azure Stack Edge pro para obter as últimas chaves de acesso. A sincronização ajuda o dispositivo obter as chaves mais recentes para seu dispositivo IoT e IoT Edge. Azure Stack o Edge pro usa apenas as chaves de acesso primárias.

Siga estas etapas no portal do Azure para sincronizar as chaves de acesso para seu dispositivo.

1. Na portal do Azure, vá para o recurso de borda do Azure Stack e vá para **IoT Edge > visão geral**. Selecione **Atualizar configuração** na barra de comandos.

    ![Selecione Atualizar configuração](media/azure-stack-edge-manage-compute/refresh-configuration-1.png)

2. Selecione **Sim** quando solicitada a confirmação.

     ![Selecione Sim quando solicitado](media/azure-stack-edge-manage-compute/refresh-configuration-2.png)

3. Saia da caixa de diálogo depois que a sincronização tiver sido concluída.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [gerenciar a rede de computação de borda por meio de portal do Azure](azure-stack-edge-extend-compute-access-modules.md).
