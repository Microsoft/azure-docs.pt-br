---
title: "Implantar o serviço do StorSimple Device Manager | Microsoft Docs"
description: "Explica como criar e excluir o serviço Gerenciador de Dispositivo do StorSimple no Portal do Azure, além de descrever como gerenciar a chave de registro do serviço."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Implantar o serviço Gerenciador de Dispositivo do StorSimple para a Matriz Virtual do StorSimple
## <a name="overview"></a>Visão geral

O serviço Gerenciador de Dispositivo do StorSimple é executado no Microsoft Azure e se conecta a vários dispositivos StorSimple. Depois de criar o serviço, você pode usá-lo para gerenciar os dispositivos no portal do Microsoft Azure em execução em um navegador. Isso permite monitorar todos os dispositivos que estão conectados ao serviço Gerenciador de Dispositivo do StorSimple de um local único e central, minimizando a sobrecarga administrativa.

As tarefas comuns relacionadas a um serviço Gerenciador de Dispositivo do StorSimple são:

* Criar um serviço
* Excluir um serviço
* Obtenha a chave de registro do serviço
* Regenerar a chave de registro do serviço

Este tutorial descreve como executar cada uma dessas tarefas. As informações contidas neste artigo aplicam-se apenas a Matrizes Virtuais do StorSimple. Para obter mais informações sobre a série 8000 do StorSimple, acesse [implantar um serviço StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Criar um serviço

Para criar um serviço, você precisa ter:

* Uma assinatura com um Enterprise Agreement
* Uma conta de armazenamento ativa do Microsoft Azure
* As informações de cobrança que são usadas para gerenciamento de acesso

Também é possível optar por gerar uma conta de armazenamento ao criar o serviço.

Um único serviço pode gerenciar vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias do serviço para trabalhar com diferentes assinaturas, organizações ou até mesmo locais de implantação.

> [!NOTE]
> Você precisa de instâncias separadas do serviço Gerenciador de Dispositivo do StorSimple para gerenciar as Matrizes Virtuais e os dispositivos da série 8000 do StorSimple.


Execute as etapas a seguir para criar um serviço.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Excluir um serviço

Antes de excluir um serviço, verifique se nenhum dispositivo conectado está usando ele. Se o serviço estiver em uso, desative os dispositivos conectados. A operação de desativação desfaz a conexão entre o dispositivo e o serviço, mas preserva os dados do dispositivo na nuvem.

> [!IMPORTANT]
> Depois que um serviço é excluído, a operação não pode ser revertida. Qualquer dispositivo que estava usando o serviço precisará ser redefinida para as configurações de fábrica para que possa ser usado com outro serviço. Nesse cenário, os dados locais no dispositivo, bem como a configuração, serão perdidos.
 

Execute as etapas a seguir para excluir um serviço.

#### <a name="to-delete-a-service"></a>Para excluir um serviço

1. Acesse **Todos os recursos**. Pesquise por seu serviço Gerenciador de Dispositivo do StorSimple. Selecione o serviço que você deseja excluir.
   
    ![Selecione o serviço para exclusão](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Acesse o painel do serviço para garantir que nenhum dispositivo esteja conectado a ele. Se não houver algum dispositivo registrado nesse serviço, você também verá uma mensagem de cabeçalho sobre isso. Clique em **Excluir**.
   
    ![Excluir serviço](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Quando receber a solicitação de confirmação, clique em **Sim** na notificação de confirmação. 
   
    ![Confirmar exclusão do serviço](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Pode levar alguns minutos para que o serviço seja excluído. Após a exclusão bem-sucedida do serviço, você receberá uma notificação.
   
    ![Exclusão bem-sucedida do serviço](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

A lista de serviços será atualizada.

 ![Lista de serviços atualizada](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Obtenha a chave de registro do serviço
Depois de ter criado um serviço com êxito, você precisará registrar o dispositivo StorSimple no serviço. Para registrar seu primeiro dispositivo StorSimple, será necessária a chave de registro do serviço. Para registrar dispositivos adicionais em um serviço StorSimple existente, serão necessárias a chave de registro e a chave de criptografia dos dados de serviço (que é gerada durante o registro do primeiro dispositivo). Para obter mais informações sobre a chave de criptografia dos dados de serviço, consulte [Segurança do StorSimple](storsimple-security.md). Você pode obter a chave de registro acessando a folha **Chaves** de seu serviço.

Execute as etapas a seguir para obter a chave de registro do serviço.

#### <a name="to-get-the-service-registration-key"></a>Para obter a chave de registro do serviço
1. No **Gerenciador de Dispositivo do StorSimple**, acesse **Gerenciamento&gt;** **Chaves**.
   
   ![Folha Chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Na folha **Chaves**, uma chave de registro de serviço será exibida. Copie a chave de registro usando o ícone de cópia. 

Mantenha a chave de registro do serviço em local seguro. Você precisará dessa chave, bem como da chave de criptografia dos dados de serviço, para registrar dispositivos adicionais nesse serviço. Depois de obter a chave de registro do serviço, você precisará configurar o dispositivo usando o Windows PowerShell para interface do StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registro do serviço
Você precisará regenerar uma chave de registro de serviço se tiver que executar a rotação de chave ou se a lista de administradores de serviço tiver mudado. Quando você regenera a chave, a nova chave é usada somente para registrar dispositivos subsequentes. Os dispositivos que já foram registrados não serão afetados por esse processo.

Execute as etapas a seguir para regenerar uma chave de registro de serviço.

#### <a name="to-regenerate-the-service-registration-key"></a>Para regenerar a chave de registro de serviço
1. No **Gerenciador de Dispositivo do StorSimple**, acesse **Gerenciamento&gt;** **Chaves**.
   
   ![Folha Chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Na folha **Chaves**, clique em **Regenerar**.
   
   ![Clique em regenerar](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Na folha **Regenerar chave de registro do serviço**, revise a ação necessária quando as chaves forem geradas novamente. Todos os dispositivos subsequentes registrados com esse serviço usarão a nova chave de registro. Clique em **Regenerar** para confirmar. Você receberá uma notificação após a conclusão do registro.
   
   ![Confirmar regeneração da chave](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Uma nova chave de registro de serviço será exibida.
   
    ![Confirmar regeneração da chave](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Copie essa chave e salve-a para registrar todos os novos dispositivos nesse serviço.

## <a name="next-steps"></a>Próximas etapas
* Saiba como obter uma [Introdução](storsimple-virtual-array-deploy1-portal-prep.md) com uma Matriz Virtual do StorSimple.
* Saiba como [administrar o seu dispositivo StorSimple](storsimple-ova-web-ui-admin.md).

