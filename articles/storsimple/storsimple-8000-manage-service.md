---
title: Implantar o serviço do Gerenciador de Dispositivos do StorSimple no Azure | Microsoft Docs
description: Saiba mais sobre as etapas necessárias para a criação, exclusão e migração do serviço e o gerenciamento da chave de registro do serviço.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076557"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Implantar o serviço do Gerenciador de Dispositivos do StorSimple para dispositivos StorSimple da série 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Visão geral

O serviço Gerenciador de Dispositivo do StorSimple é executado no Microsoft Azure e se conecta a vários dispositivos StorSimple. Depois de criar o serviço, você pode usá-lo para gerenciar todos os dispositivos que estão conectados ao serviço do Gerenciador de Dispositivos do StorSimple de um local único e central, minimizando assim a carga administrativa.

Este tutorial descreve as etapas necessárias para a criação, exclusão e migração do serviço e o gerenciamento da chave de Registro do serviço. As informações contidas neste artigo aplicam-se apenas a dispositivos StorSimple da série 8000. Para obter mais informações sobre Matrizes Virtual do StorSimple, acesse [Implantar um serviço do Gerenciador de Dispositivos do StorSimple para sua Matriz Virtual do StorSimple](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  O Portal do Azure dá suporte a dispositivos que executam a Atualização 5.0 ou posterior. Se seu dispositivo não for atualizado, instale a Atualização 5 imediatamente. Para saber mais, vá para [Instalar a Atualização 5](storsimple-8000-install-update-5.md). 
> - Se você estiver usando um Dispositivo de Nuvem StorSimple (8010/8020), você não poderá atualizar um dispositivo de nuvem. Use a versão mais recente do software para criar um novo dispositivo de nuvem com Atualização 5.0 e, em seguida, faça failover para o novo dispositivo de nuvem criado. 
> - Todos os dispositivos que executam a versão 4.0 ou anterior terão funcionalidade de gerenciamento reduzida. 

## <a name="create-a-service"></a>Criar um serviço
Para criar um serviço do Gerenciador de Dispositivos do StorSimple, você precisa ter:

* Uma assinatura com um Enterprise Agreement
* Uma conta de armazenamento ativa do Microsoft Azure
* As informações de cobrança que são usadas para gerenciamento de acesso

Somente as assinaturas com um Contrato Enterprise são permitidas. Também é possível optar por gerar uma conta de armazenamento padrão ao criar o serviço.

Um único serviço pode gerenciar vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias do serviço para trabalhar com diferentes assinaturas, organizações ou até mesmo locais de implantação. 

> [!NOTE]
> Você precisa de instâncias separadas do serviço Gerenciador de Dispositivo do StorSimple para gerenciar as Matrizes Virtuais e os dispositivos da série 8000 do StorSimple.

Execute as etapas a seguir para criar um serviço.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Os atributos a seguir existem para cada serviço do Gerenciador de Dispositivos do StorSimple:

* **Nome** – O nome que foi atribuído ao serviço do Gerenciador de Dispositivos do StorSimple quando ele foi criado. **O nome do serviço não pode ser alterado depois que o serviço é criado. Isso também é válido para outras entidades, como dispositivos, volumes, contêineres de volume e políticas de backup, que não podem ser renomeadas no portal do Azure.**
* **Status** - O status de um dispositivo, que pode ser **Ativo**, **Em criação**, ou **Online**.
* **Local** : a localização geográfica em que o dispositivo StorSimple será implantado.
* **Assinatura** - A assinatura de cobrança que está associada com seu serviço.

## <a name="delete-a-service"></a>Excluir um serviço

Antes de excluir um serviço, verifique se nenhum dispositivo conectado está usando ele. Se o serviço estiver em uso, desative os dispositivos conectados. A operação de desativação desfaz a conexão entre o dispositivo e o serviço, mas preserva os dados do dispositivo na nuvem.

> [!IMPORTANT]
> Depois que um serviço é excluído, a operação não pode ser revertida. Qualquer dispositivo que usava o serviço precisará ser redefinido para as configurações de fábrica para poder ser usado com outro serviço. Nesse cenário, os dados locais no dispositivo, bem como a configuração, serão perdidos.

Execute as etapas a seguir para excluir um serviço.

### <a name="to-delete-a-service"></a>Para excluir um serviço

1. Pesquise o serviço que você deseja excluir. Clique no ícone **Recursos** e insira os termos apropriados para pesquisar. Nos resultados da pesquisa, clique no serviço que você deseja excluir.

    ![Pesquise o serviço a ser excluído](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Isso levará você para a folha do serviço do Gerenciador de Dispositivos do StorSimple. Clique em **Excluir**.

    ![Excluir serviço](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Clique **Sim** na notificação de confirmação. Pode levar alguns minutos para que o serviço seja excluído.

    ![Confirmar exclusão](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Obtenha a chave de registro do serviço

Depois de ter criado um serviço com êxito, você precisará registrar o dispositivo StorSimple no serviço. Para registrar seu primeiro dispositivo StorSimple, será necessária a chave de registro do serviço. Para registrar dispositivos adicionais em um serviço StorSimple existente, você precisará da chave de registro e da chave de criptografia de dados de serviço (que é gerada durante o registro do primeiro dispositivo). Para obter mais informações sobre a chave de criptografia dos dados de serviço, consulte [Segurança do StorSimple](storsimple-8000-security.md). Você pode obter a chave de registro acessando **Chaves** na folha do Gerenciador de Dispositivos do StorSimple.

Execute as etapas a seguir para obter a chave de registro do serviço.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Mantenha a chave de registro do serviço em local seguro. Você precisará dessa chave, bem como da chave de criptografia dos dados de serviço, para registrar dispositivos adicionais nesse serviço. Depois de obter a chave de registro do serviço, você deverá configurar o dispositivo usando a Interface do Windows PowerShell para StorSimple.

Para obter detalhes sobre como usar essa chave de registro, consulte [Etapa 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registro do serviço
Você precisará regenerar uma chave de registro do serviço se for necessário realizar a rotação de chave ou se a lista de administradores de serviço tiver mudado. Quando você regenera a chave, a nova chave é usada somente para registrar dispositivos subsequentes. Os dispositivos que já foram registrados não serão afetados por esse processo.

Execute as etapas a seguir para regenerar uma chave de registro de serviço.

### <a name="to-regenerate-the-service-registration-key"></a>Para regenerar a chave de registro de serviço
1. No **Gerenciador de Dispositivo do StorSimple**, acesse **Gerenciamento&gt;** **Chaves**.
    
    ![Navegue até a folha Chaves](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Na folha **Chaves**, clique em **Regenerar**.

    ![Clique em regenerar](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Na folha **Regenerar chave de registro do serviço**, revise a ação necessária quando as chaves forem geradas novamente. Todos os dispositivos subsequentes registrados com esse serviço usarão a nova chave de Registro. Clique em **Regenerar** para confirmar. Você será notificado quando a regeneração for concluída.

    ![Confirmar a regeneração](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Uma nova chave de registro de serviço será exibida.

5. Copie essa chave e salve-a para registrar todos os novos dispositivos nesse serviço.



## <a name="change-the-service-data-encryption-key"></a>Alterar a chave de criptografia de dados do serviço
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Suporte para operações em dispositivos que executam versões anteriores à atualização 5.0
No Portal do Azure, há suporte apenas para dispositivos StorSimple que executam a Atualização 5.0 ou superior. Os dispositivos que executam versões mais antigas têm suporte limitado. Após você ter migrado para o Portal do Azure, cheque a tabela a seguir para conferir quais operações têm suporte para dispositivos que executam versões anteriores à Atualização 5.0.

| Operação                                                                                                                       | Com suporte      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrar um dispositivo                                                                                                               | Sim            |
| Definir as configurações do dispositivo, tais como configurações gerais, de rede e de segurança                                                                | Sim            |
| Examinar, baixar e instalar atualizações                                                                                             | Sim            |
| Desativar um dispositivo                                                                                                               | Sim            |
| Excluir um dispositivo                                                                                                                   | Sim            |
| Criar, modificar e excluir um contêiner de volume                                                                                   | Não             |
| Criar, modificar e excluir um volume                                                                                             | Não             |
| Criar, modificar e excluir uma política de backup                                                                                      | Não             |
| Fazer um backup manual                                                                                                            | Não             |
| Realizar um backup agendado                                                                                                         | Não aplicável |
| Restaurar de um conjunto de backup                                                                                                        | Não             |
| Clonar para um dispositivo que executa a Atualização 3.0 e posterior <br> O dispositivo de origem executa uma versão anterior à Atualização 3.0.                                | Sim            |
| Clonar para um dispositivo que executa uma versão anterior à Atualização 3.0                                                                          | Não             |
| Failover como dispositivo de origem <br> (de um dispositivo que executa uma versão anterior à Atualização 3.0 para um dispositivo que executa a Atualização 3.0 e posterior)                                                               | Sim            |
| Failover como dispositivo de destino <br> (para um dispositivo que executa uma versão de software anterior à Atualização 3.0)                                                                                   | Não             |
| Limpar um alerta                                                                                                                  | Sim            |
| Exibir políticas de backup, catálogo de backup, volumes, contêineres de volume, gráficos de monitoramento, trabalhos e alertas criados no portal clássico | Sim            |
| Ativar e desativar controladores de dispositivo                                                                                              | Sim            |


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [processo de implantação do StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* [Saiba mais sobre como gerenciar sua conta de armazenamento do StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [usar o serviço StorSimple Device Manager para administrar dispositivos StorSimple](storsimple-8000-manager-service-administration.md).
