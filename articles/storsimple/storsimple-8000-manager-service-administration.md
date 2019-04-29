---
title: Administração do serviço de Gerenciador de Dispositivos do StorSimple | Microsoft Docs
description: Saiba como gerenciar o dispositivo StorSimple usando o serviço de Gerenciador de Dispositivos do StorSimple no portal do Azure.
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
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: b5490c4e79ee1458b498f539c0db2cc189fce7f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723299"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Usar o serviço de Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple

## <a name="overview"></a>Visão geral

Este artigo descreve a interface do serviço do Gerenciador de Dispositivos do StorSimple, incluindo como se conectar a ele, as várias opções disponíveis e links para os fluxos de trabalho específicos que podem ser executados nessa interface do usuário. Essas diretrizes se aplicam a ambos: tanto ao dispositivo StorSimple físico quanto ao dispositivo de nuvem.

Neste artigo, você aprenderá:

* Conectar ao serviço do Gerenciador de Dispositivos do StorSimple
* Administrar o dispositivo StorSimple por meio do serviço do Gerenciador de Dispositivos do StorSimple

## <a name="connect-to-storsimple-device-manager-service"></a>Conectar ao serviço do Gerenciador de Dispositivos do StorSimple

O serviço Gerenciador de Dispositivo do StorSimple é executado no Microsoft Azure e se conecta a vários dispositivos StorSimple. Você usa um portal central do Microsoft Azure em execução em um navegador para gerenciar esses dispositivos. Para se conectar ao serviço do StorSimple Device Manager, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para conectar-se ao serviço
1. Navegue até [https://portal.azure.com/](https://portal.azure.com/).
2. Usando suas credenciais de conta da Microsoft, faça logon no Portal do Microsoft Azure (localizado na parte superior direita do painel).
3. Role para baixo o painel de navegação esquerdo para acessar o serviço do StorSimple Device Manager.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrar o dispositivo StorSimple usando o serviço do Gerenciador de Dispositivos do StorSimple

A tabela a seguir mostra um resumo de todas as tarefas comuns de gerenciamento e fluxos de trabalho complexos que podem ser executados na interface do usuário do serviço do StorSimple Device Manager. Essas tarefas são organizadas com base nas folhas da interface do usuário nas quais elas foram iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do StorSimple Device Manager

| Se você quiser fazer isso... | Utilize este procedimento. |
| --- | --- |
| Criar um serviço</br>Excluir um serviço</br>Obter a chave de registro do serviço</br>Regenerar chave de registro do serviço |[Implantar um serviço do Gerenciador de Dispositivos do StorSimple](storsimple-8000-manage-service.md) |
| Exibir os logs de atividade |[Usar o resumo do serviço do Gerenciador de Dispositivos do StorSimple](storsimple-8000-service-dashboard.md) |
| Alterar a chave de criptografia de dados do serviço</br>Exibir os logs operacionais |[Usar o painel do serviço do Gerenciador de Dispositivos do StorSimple](storsimple-8000-service-dashboard.md) |
| Desativar um dispositivo</br>Excluir um dispositivo |[Desativar ou excluir um dispositivo](storsimple-8000-deactivate-and-delete-device.md) |
| Saiba mais sobre o failover de dispositivo e a recuperação de desastres</br>Failover para um dispositivo físico</br>Failover para um dispositivo virtual</br>BCDR (recuperação de desastre de continuidade de negócios) |[Failover e recuperação de desastres para o seu dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista de backups para um volume</br>Selecione um conjunto de backup</br>Excluir um conjunto de backups |[Gerenciar backups](storsimple-8000-manage-backup-catalog.md) |
| Clonar um volume |[Clonar um volume](storsimple-8000-clone-volume-u2.md) |
| Restaurar um conjunto de backup |[Restaurar um conjunto de backup](storsimple-8000-restore-from-backup-set-u2.md) |
| Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Excluir uma conta de armazenamento</br>Rotação de chave de contas de armazenamento |[Gerenciar contas de armazenamento](storsimple-8000-manage-storage-accounts.md) |
| Sobre modelos de largura de banda</br>Adicionar um modelo de largura de banda</br>Editar um modelo de largura de banda</br>Excluir um modelo de largura de banda</br>Usar um modelo de largura de banda padrão</br>Criar um modelo de largura de banda de dia inteiro que comece em uma hora especificada |[Gerenciar modelos de largura de banda](storsimple-8000-manage-bandwidth-templates.md) |
| Sobre os registros de controle de acesso</br>Criar um registro de controle de acesso</br>Editar um registro de controle de acesso</br>Excluir um registro de controle de acesso |[Gerenciar registros de controle de acesso](storsimple-8000-manage-acrs.md) |
| Exibir detalhes do trabalho</br>Cancelar um trabalho |[Gerenciar trabalhos](storsimple-8000-manage-jobs-u2.md) |
| Receber notificações de alerta</br>Gerenciar alertas</br>Revisar alertas |[Exibir e gerenciar alertas do StorSimple](storsimple-8000-manage-alerts.md) |
| Criar gráficos de monitoramento |[Monitorar o dispositivo StorSimple](storsimple-monitor-device.md) |
| Adicionar um contêiner de volume</br>Modificar um contêiner de volume</br>Excluir um contêiner de volume |[Gerenciar contêineres de volume](storsimple-8000-manage-volume-containers.md) |
| Adicionar um volume</br>Modificar um volume</br>Colocar um volume offline</br>Excluir um volume</br>Monitorar um volume |[Gerenciar volumes](storsimple-8000-manage-volumes-u2.md) |
| Modificar as configurações de dispositivo</br>Modificar as configurações de tempo</br>Modificar as configurações de DNS.md</br>Configurar interfaces de rede |[Modificar a configuração do dispositivo para seu dispositivo StorSimple](storsimple-8000-modify-device-config.md) |
| Exibir configurações do proxy Web |[Configurar o proxy Web para o seu dispositivo](storsimple-8000-configure-web-proxy.md) |
| Modificar senha de administrador do dispositivo</br>Modificar senha do StorSimple Snapshot Manager |[Alterar senhas de StorSimple](storsimple-8000-change-passwords.md) |
| Configurar o gerenciamento remoto |[Conectar remotamente ao seu dispositivo StorSimple](storsimple-8000-remote-connect.md) |
| Definição de configurações de alerta |[Exibir e gerenciar alertas do StorSimple](storsimple-8000-manage-alerts.md) |
| Configure o CHAP para seu dispositivo StorSimple |[Configure o CHAP para o seu dispositivo StorSimple](storsimple-configure-chap.md) |
| Adicionar uma política de backup</br>Adicionar ou modificar um agendamento</br>Excluir uma política de backup</br>Fazer um backup manual</br>Criar uma política de backup personalizada com vários volumes e agendamentos |[Gerenciar políticas de backup](storsimple-8000-manage-backup-policies-u2.md) |
| Parar controladores do dispositivo</br>Reiniciar os controladores do dispositivo</br>Desligar os controladores do dispositivo</br>Redefinir o dispositivo para os padrões de fábrica</br>(As opções acima são somente para o dispositivo local) |[Gerenciar controlador de dispositivo StorSimple](storsimple-8000-manage-device-controller.md) |
| Saiba mais sobre os componentes de hardware StorSimple</br>Monitorar o status de hardware</br>(As opções acima são somente para o dispositivo local) |[Componentes de hardware do monitor](storsimple-8000-monitor-hardware-status.md) |
| Criar um pacote de suporte |[Criar e gerenciar pacotes de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalar as atualizações do software |[Atualizar seu dispositivo](storsimple-update-device.md) |

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas com a operação diária do seu dispositivo StorSimple ou com qualquer um de seus componentes de hardware, consulte:

* [Solução de problemas usando a ferramenta de Diagnóstico](storsimple-8000-diagnostics.md)
* [Usar LEDs indicadores de monitoramento do StorSimple](storsimple-monitoring-indicators.md)

Se você não conseguir resolver os problemas e precisar criar uma solicitação de serviço, consulte [Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

