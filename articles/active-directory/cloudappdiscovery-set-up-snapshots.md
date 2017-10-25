---
title: "Criar relatórios de instantâneo do Cloud App Discovery no Azure Active Directory | Microsoft Docs"
description: "Fornece informações sobre a localização e o gerenciamento de aplicativos com o Cloud App Discovery, quais são seus benefícios e como ele funciona."
services: active-directory
keywords: cloud app discovery, gerenciamento de aplicativos
documentationcenter: 
author: curtand
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: curtand
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: dd4cf725689739ce4c8b86a4d8203ffc5aff016b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Criar relatórios de instantâneo do Cloud App Discovery

Antes de configurar o coletor de logs automático, carregue um log manualmente e permita que o Cloud App Security o analise. Se você ainda não tem um log e deseja ver uma amostra de como deve ser a aparência dele, use o procedimento a seguir para baixar uma amostra de arquivo de log.

## <a name="to-create-a-snapshot-report"></a>Para criar um relatório de instantâneo

1. Colete arquivos de log do servidor proxy e do firewall por meio dos quais os usuários em sua organização acessam a Internet. Colete logs durante os períodos de tráfego de pico que representam a atividade do usuário em sua organização.
2. Na [barra de menus do Cloud App Security](https://portal.cloudappsecurity.com), selecione **Descobrir** e, em seguida, **Criar relatório de instantâneo**.
  
  ![Criar novo relatório de instantâneo](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. Insira um **Nome de relatório** e uma **Descrição**.
    
  ![Novo relatório de instantâneo](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. Selecione a **Fonte de dados** da qual você deseja carregar os arquivos de log.
5. Verifique o formato de log para verificar se ele está formatado corretamente de acordo com a amostra que você pode baixar. Clique em **Exibir e verificar** e, em seguida, clique em **Baixar log de exemplo**. Em seguida, compare seu log com a amostra fornecida para verificar se ele é compatível.
  
  ![Verifique seu formato de log](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > O formato de amostra de FTP tem suporte em instantâneos e upload automatizado, enquanto o syslog tem suporte apenas no upload automatizado. Baixar uma amostra de log resulta no download de uma amostra de log de FTP.
6. **Escolha os logs de tráfego** que você deseja carregar. Você pode carregar até 20 arquivos ao mesmo tempo. Também há suporte para arquivos compactados e zipados.
  
7. Clique em **Criar**. Após a conclusão do upload, pode levar algum tempo para que eles possam ser analisados. Se isso acontecer, o Cloud App Discovery enviará uma notificação por email quando eles estiverem prontos.

8. Selecione **Gerenciar relatórios de instantâneo** e selecione o relatório de instantâneo.
  
  ![Gerenciamento de relatórios de instantâneo](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao uso do Cloud App Discovery no Azure AD](cloudappdiscovery-get-started.md)
* [Configurar o upload automático de logs para relatório contínuo](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Usar um analisador de log personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
