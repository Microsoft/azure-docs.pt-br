---
title: "Restaurar um aplicativo no Serviço de Aplicativo do Azure"
description: Saiba como restaurar seu aplicativo de um backup.
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 79d4084deb6d8c028918690c339c21c720e63594
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="restore-an-app-in-azure"></a>Restaurar um aplicativo no Serviço de Aplicativo do Azure
Este artigo mostra como restaurar um aplicativo no [Serviço de Aplicativo do Azure](../app-service/app-service-web-overview.md) do qual você fez backup anteriormente (veja [Fazer backup de seu aplicativo no Azure](web-sites-backup.md)). É possível restaurar seu aplicativo com seus bancos de dados vinculados sob demanda para um estado anterior ou criar um novo aplicativo com base em um backup de seu aplicativo original. O Serviço de Aplicativo do Azure oferece suporte aos seguintes bancos de dados para backup e restauração:
- [Banco de Dados SQL](https://azure.microsoft.com/en-us/services/sql-database/)
- [Banco de Dados do Azure para MySQL (Visualização)](https://azure.microsoft.com/en-us/services/mysql)
- [Banco de Dados do Azure para PostgreSQL (Visualização)](https://azure.microsoft.com/en-us/services/postgres)
- [ClearDB MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SuccessBricksInc.ClearDBMySQLDatabase?tab=Overview)
- [MySQL no aplicativo](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

A restauração usando backups está disponível para aplicativos que são executados nas camadas **Standard** e **Premium**. Para obter informações sobre como escalar verticalmente seu aplicativo, veja [Escalar verticalmente um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-scale.md). A camada **Premium** permite um número maior de backups diários do que a camada **Standard**.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Restaurar um aplicativo por meio de um backup existente
1. Na folha **Configurações** de seu aplicativo no portal do Azure, clique em **Backups** para exibir a folha **Backups**. Depois, clique em **Restaurar**.
   
    ![Escolha restaurar agora][ChooseRestoreNow]
2. Na folha **Restauração** , primeiro selecione a fonte do backup.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    A opção **Backup do aplicativo** mostra todos os backups existentes do aplicativo atual, e você pode selecionar um com facilidade.
    A opção **Armazenamento** permite selecionar qualquer arquivo ZIP de backup em qualquer conta do Armazenamento do Azure e contêiner existentes em sua assinatura.
    Se você está tentando restaurar um backup de outro aplicativo, use a opção **Armazenamento** .
3. Em seguida, especifique o destino para a restauração de aplicativo em **Destino de restauração**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Se você escolher **Substituir**, todos os dados existentes em seu aplicativo atual serão apagados e substituídos. Antes de clicar em **OK**, certifique-se de que isso é exatamente o que você deseja fazer.
   > 
   > 
   
    Você pode selecionar um **Aplicativo Existente** para restaurar o backup do aplicativo para outro aplicativo no mesmo grupo de recursos. Antes de usar essa opção, você já precisa ter criado outro aplicativo em seu grupo de recursos com o espelhamento da configuração de banco de dados para aquele definido no backup do aplicativo. Você também pode criar um **Novo** aplicativo no qual o conteúdo será restaurado.

4. Clique em **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Baixar ou excluir um backup de uma conta de armazenamento
1. Na folha principal **Procurar** do Portal do Azure, selecione **Contas de armazenamento**. Uma lista de suas contas de armazenamento existentes é exibida.
2. Selecione a conta de armazenamento que contém o backup que você deseja baixar ou excluir. A folha da conta de armazenamento é exibida.
3. Na folha da conta de armazenamento, selecione o contêiner desejado
   
    ![Exibir contêineres][ViewContainers]
4. Selecione o arquivo de backup que você deseja baixar ou excluir.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Clique em **Baixar** ou **Excluir**, dependendo da ação desejada.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorar uma operação de restauração
Para ver detalhes sobre o sucesso ou a falha da operação de restauração do aplicativo, navegue até a folha **Log de Atividades** no portal do Azure.  
 

Role para baixo para encontrar a operação de restauração desejada e clique para selecioná-la.

A folha de detalhes exibe as informações disponíveis relacionadas à operação de restauração.

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
