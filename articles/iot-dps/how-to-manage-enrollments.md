---
title: Como gerenciar registros de dispositivo para o Hub IoT do Azure | Microsoft Docs
description: "Como gerenciar registros de dispositivo para seu serviço DPS no Portal do Azure"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c230e73f83d8acd0f142e037f70a80c9e0e4107e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-device-enrollments-in-the-iot-hub-device-provisioning-service"></a>Como gerenciar registros de dispositivo no Serviço de Provisionamento de Dispositivos no Hub IoT

Um *registro de dispositivos* cria um registro de um único dispositivo ou um grupo de dispositivos que pode em algum momento registrar com o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure. O registro contém a configuração inicial desejada para os dispositivos como parte desse registro, incluindo o Hub IoT desejado. Este artigo mostra como gerenciar registros de dispositivo para o serviço de provisionamento.


## <a name="create-a-device-enrollment"></a>Criar um registro do dispositivo

Há duas maneiras em que você pode registrar seus dispositivos com o serviço de provisionamento:

1. Um **grupo de registro** é uma entrada para um grupo de dispositivos que compartilham um mecanismo de atestado comum de certificados X.509, assinados pela mesma AC raiz. É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que vão todos para o mesmo locatário. Observe que você só pode registrar dispositivos que usam o mecanismo de atestado X.509 como *grupos de registro*. 

    Você pode criar um grupo de registro no portal para um grupo de dispositivos usando as etapas a seguir.

    1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
    2. Clique no serviço de provisionamento do dispositivo no qual você deseja registrar seu dispositivo na lista de recursos.
    3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registro**.
    4. Clique no botão **Adicionar** na parte superior e, em seguida, insira as informações necessárias para a entrada da lista de registro. Carregue o certificado raiz para o grupo de dispositivos. 
    5. Clique em **Salvar**. Após a criação bem-sucedida de seu grupo de registro, você deverá ver o nome do grupo aparecer sob a guia **Grupos de Registro**. 

        ![Grupo de registro no portal](./media/how-to-manage-enrollments/group-enrollment.png)

    
2. Um **registro Individual** é uma entrada para um único dispositivo que pode registrar. Registros individuais podem usar tokens de certificados x509 ou tokens SAS (em um TPM real ou virtual) como mecanismos de atestado. É recomendável usar registros individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que só podem usar tokens SAS por meio do TPM ou TPM virtual como o mecanismo de Atestado. Registros individuais podem ter a ID de dispositivo de Hub IoT desejada especificada.

    Você pode criar um registro individual no portal usando as etapas a seguir. 

    1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
    2. Clique no serviço de provisionamento do dispositivo no qual você deseja registrar seu dispositivo na lista de recursos.
    3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
    4. Clique no botão **Adicionar** na parte superior. 
    5. Selecione o mecanismo de segurança para o dispositivo e insira as informações necessárias para a entrada da lista de registro. Carregue um certificado assinado se seu dispositivo implementa X.509. 
    6. Clique em **Salvar**. Após a criação bem-sucedida de seu grupo de registro, você deverá ver o dispositivo aparecer sob a guia **Registros Individuais**. 

        ![Registro individual no portal](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de registro
Você pode atualizar uma entrada de registro existente no portal usando as etapas a seguir.

1. Abra o serviço de provisionamento do dispositivo no Portal do Azure e clique em **Gerenciar Registros**. 
2. Navegue até a entrada de registro que você deseja modificar. Clique na entrada, que abre informações de resumo sobre o registro do dispositivo. 
3. Nessa página, você pode modificar os itens que não sejam do tipo de segurança e credenciais, tais como o Hub IoT ao qual dispositivo deve ser vinculado, bem como a ID do dispositivo. Você também pode modificar o estado inicial do dispositivo gêmeo. 
4. Depois de concluído, clique em **Salvar** para atualizar o registro do dispositivo. 

    ![Atualizar registro no portal](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>Remover um registro do dispositivo
Em casos em que os dispositivos não precisam ser provisionados para nenhum Hub IoT, você pode remover a entrada de registro relacionado no portal usando as etapas a seguir.

1. Abra o serviço de provisionamento do dispositivo no Portal do Azure e clique em **Gerenciar Registros**. 
2. Navegue até a entrada de registro que você deseja remover e selecione-a. 
3. Clique no botão **Excluir** na parte superior e, em seguida, selecione **Sim** quando a confirmação for solicitada. 
5. Depois que a ação for concluída, você verá sua entrada removida da lista de registros do dispositivo. 
 
    ![Remover registro no portal](./media/how-to-manage-enrollments/remove-enrollment.png)



