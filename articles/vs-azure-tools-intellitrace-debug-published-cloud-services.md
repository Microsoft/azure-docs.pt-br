---
title: "Depurando um serviço de nuvem do Azure publicado com o Visual Studio e o IntelliTrace | Microsoft Docs"
description: "Saiba como depurar um serviço de nuvem com o Visual Studio e o IntelliTrace"
services: visual-studio-online
documentationcenter: n/a
author: kraigb
manager: ghogen
editor: 
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 7905dfb97cbd7578a8422567fe674839d00c21ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>Depurando um serviço de nuvem do Azure publicado com o Visual Studio e o IntelliTrace
Com o IntelliTrace, você pode registrar em log informações extensas de depuração sobre uma instância de função quando ela é executada no Azure. Se precisa encontrar a causa de um problema, você pode usar os logs do IntelliTrace para examinar seu código no Visual Studio como se ele estivesse em execução no Azure. Na verdade, o IntelliTrace registra dados de ambiente e a execução do código de chave quando seu aplicativo Azure está em execução como um serviço de nuvem no Azure, além de permitir que você reproduza os dados gravados usando o Visual Studio. 

Você poderá usar o IntelliTrace se tiver o Visual Studio Enterprise instalado e seu aplicativo Azure se destinar ao .NET Framework 4 ou uma versão posterior. O IntelliTrace coleta informações sobre suas funções no Azure. As máquinas virtuais para essas funções sempre executam sistemas operacionais de 64 bits.

Como alternativa, é possível usar a [depuração remota](http://go.microsoft.com/fwlink/p/?LinkId=623041) para anexação direta a um serviço de nuvem em execução no Azure.

> [!IMPORTANT]
> O IntelliTrace destina-se apenas aos cenários de depuração e não deve ser usado para uma implantação de produção.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Configurar o IntelliTrace em um aplicativo do Azure
Para habilitar o IntelliTrace para um aplicativo Azure, você deve criar e publicar o aplicativo de um projeto Azure do Visual Studio. É preciso configurar o IntelliTrace para o aplicativo Azure antes de publicá-lo no Azure. Se você publicar o aplicativo sem configurar o IntelliTrace, precisará republicar o projeto. Para obter mais informações, consulte [Publicando projetos de serviços de nuvem do Azure usando o Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Quando estiver pronto para implantar o aplicativo Azure, verifique se os destinos de compilação do projeto estão definidos como **Depurar**.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e, no menu de contexto, selecione **Publicar**.
   
1. Na caixa de diálogo **Publicar Aplicativo do Azure**, selecione a assinatura do Azure e selecione **Avançar**.

1. Na página **Configurações**, selecione a guia **Configurações Avançadas**.

1. Ative a opção **Habilitar IntelliTrace** para coletar logs do IntelliTrace para o aplicativo quando ele for publicado na nuvem.
   
1. Para personalizar a configuração básica do IntelliTrace, selecione **Configurações** ao lado de **Habilitar IntelliTrace**.

    ![Link de configurações do IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. Na caixa de diálogo **Configurações do IntelliTrace**, é possível especificar quais eventos são registrados, se as informações de chamada são coletadas, para quais módulos e processos os logs são coletados e quanto espaço é alocado para o registro. Para saber mais sobre o IntelliTrace, consulte [Depurando com o IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![Configurações do IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

O log do IntelliTrace é um arquivo de log circular do tamanho máximo especificado nas configurações do IntelliTrace (o tamanho padrão é 250 MB). Os logs do IntelliTrace são coletados em um arquivo no sistema de arquivos da máquina virtual. Quando você solicita os logs, um instantâneo é feito nesse momento e baixado no computador local.

Depois que o serviço de nuvem do Azure for publicado no Azure, você poderá determinar se o IntelliTrace foi habilitado por meio do nó do Azure no **Gerenciador de Servidores**, conforme mostrado na seguinte imagem:

![Gerenciador de Servidores – IntelliTrace habilitado](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Baixar logs do IntelliTrace de uma instância de função
Com o Visual Studio, é possível baixar os logs do IntelliTrace de uma instância de função seguindo estas etapas:

1. Em **Gerenciador de Servidores**, expanda o nó **Serviços de Nuvem** e localize a instância de função cujos logs você deseja baixar. 

1. Clique com o botão direito do mouse na instância de função e, no menu de contexto, selecione **Exibir Logs do IntelliTrace**. 

    ![Opção de menu Exibir logs do IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. Os logs do IntelliTrace são baixados em um arquivo em um diretório no computador local. Toda vez que você solicita os logs do IntelliTrace, um novo instantâneo é criado. Durante o download dos logs, o Visual Studio exibe o andamento da operação na janela **Log de Atividades do Azure**. Como mostrado na figura a seguir, você pode expandir o item de linha da operação para ver mais detalhes.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

É possível continuar trabalhando no Visual Studio enquanto os logs do IntelliTrace estão sendo baixados. Quando o download do log for concluído, ele será aberto no Visual Studio.

> [!NOTE]
> Os logs do IntelliTrace podem conter exceções que são geradas e tratadas posteriormente pela estrutura. O código interno da estrutura gera essas exceções como uma parte normal de iniciar uma função, de modo que você pode ignorá-las com segurança.
> 
> 

## <a name="next-steps"></a>Próximas etapas
- [Opções de depuração dos serviços de nuvem do Azure](vs-azure-tools-debugging-cloud-services-overview.md)
- [Publicando um serviço de nuvem do Azure usando o Visual Studio](vs-azure-tools-publishing-a-cloud-service.md)