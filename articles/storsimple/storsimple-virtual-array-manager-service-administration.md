---
title: Administração do Microsoft Azure StorSimple Manager Virtual Array | Microsoft Docs
description: Saiba como gerenciar o Virtual Array local do StorSimple usando o serviço do StorSimple Device Manager no portal do Azure.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: bb6bb491ca71e5ced5aecc8137e9e1cbd950e80b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123798"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Usar o serviço do StorSimple Device Manager para administrar o StorSimple Virtual Array
![fluxo do processo de instalação](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Visão geral
Este artigo descreve a interface do serviço do StorSimple Device Manager, incluindo como conectá-la, bem como as várias opções disponíveis, e fornece links para os fluxos de trabalho específicos que podem ser executados nessa interface do usuário.

Após ler este artigo, você saberá como:

* Conectar-se ao serviço do StorSimple Device Manager
* Navegar para a interface do usuário do serviço do StorSimple Device Manager
* Administrar o StorSimple Virtual Array por meio do serviço StorSimple Device Manager

> [!NOTE]
> Para exibir as opções de gerenciamento disponíveis para o dispositivo da série 8000 StorSimple, vá para [Usar o serviço StorSimple Manager para administrar o dispositivo](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Conectar-se ao serviço do StorSimple Device Manager
O serviço do StorSimple Device Manager é executado no Microsoft Azure e conecta-se a vários StorSimple Virtual Arrays. Você usa um portal central do Microsoft Azure em execução em um navegador para gerenciar esses dispositivos. Para se conectar ao serviço do StorSimple Device Manager, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para conectar-se ao serviço
1. Vá para [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Usando suas credenciais de conta da Microsoft, faça logon no Portal do Microsoft Azure (localizado na parte superior direita do painel).
3. Navegue para Procurar --> “Filtro” em StorSimple Device Managers para exibir todos os gerenciadores dos seu dispositivo em uma determinada assinatura.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Usar o serviço do StorSimple Device Manager para realizar tarefas de gerenciamento
A tabela a seguir mostra um resumo de todas as tarefas comuns de gerenciamento e fluxos de trabalho complexos que podem ser executados na folha de resumo do serviço do StorSimple Device Manager. Essas tarefas são organizadas com base nas folhas nas quais elas foram iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do StorSimple Device Manager
| Se você quiser fazer isso... | Utilize este procedimento |
| --- | --- |
| Criar um serviço</br>Excluir um serviço</br>Obtenha a chave de registro do serviço</br>Regenerar a chave de registro do serviço |[Implantar o serviço do StorSimple Device Manager](storsimple-virtual-array-manage-service.md) |
| Exibir os logs de atividade |[Usar o resumo do serviço do StorSimple](storsimple-virtual-array-service-summary.md) |
| Desativar uma Matriz Virtual</br>Excluir uma Matriz Virtual |[Desativar ou excluir uma matriz virtual](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Failover de dispositivo e recuperação de desastre</br>Pré-requisitos de failover</br>BCDR (recuperação de desastre de continuidade de negócios)</br>Erros durante a recuperação de desastre |[Failover de dispositivo e a recuperação de desastre para sua StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| Fazer backup de compartilhamentos e volumes</br>Fazer um backup manual</br>Alterar o agendamento de backup</br>Exibir os backups existentes |[Fazer backup de sua Matriz Virtual StorSimple](storsimple-virtual-array-backup.md) |
| Clonar compartilhamentos de um conjunto de backup</br>Clonar volumes de um conjunto de backup</br>Recuperação em nível de item (somente servidor de arquivos) |[Clonar de um backup do StorSimple Virtual Array](storsimple-virtual-array-clone.md) |
| Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Excluir uma conta de armazenamento |[Gerenciar contas de armazenamento para a Matriz Virtual StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Sobre os registros de controle de acesso</br>Adicionar ou modificar um registro de controle de acesso </br>Excluir um registro de controle de acesso |[Gerenciar registros de controle de acesso para a Matriz Virtual StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Exibir detalhes do trabalho |[Gerenciar trabalhos do StorSimple Virtual Array](storsimple-virtual-array-manage-jobs.md) |
| Definição de configurações de alerta</br>Receber notificações de alerta</br>Gerenciar alertas</br>Revisar alertas |[Exibir e gerenciar alertas para a Matriz Virtual StorSimple](storsimple-virtual-array-manage-alerts.md) |
| Modificar a senha de administrador do dispositivo |[Alterar a senha do administrador do dispositivo da Matriz Virtual StorSimple](storsimple-virtual-array-change-device-admin-password.md) |
| Instalar as atualizações do software |[Atualizar a Matriz Virtual](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Você deve usar a [Interface do usuário da web local](storsimple-ova-web-ui-admin.md) para as seguintes tarefas:
> 
> * [Recuperar a chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Criar um pacote de suporte](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Parar e reiniciar uma Matriz Virtual](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre a interface do usuário da web e como usá-la, acesse [Usar a interface do usuário da web do StorSimple para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

