---
title: Restaurar aplicativo – Serviço de Aplicativo do Azure
description: Saiba como restaurar seu aplicativo de um backup.
services: app-service
documentationcenter: ''
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
ms.custom: seodec18
ms.openlocfilehash: 1e8bebdb3f54ac59ec19ef798cc3e794473bbec0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832419"
---
# <a name="restore-an-app-in-azure"></a>Restaurar um aplicativo no Serviço de Aplicativo do Azure
Este artigo mostra como restaurar um aplicativo no [Serviço de Aplicativo do Azure](../app-service/overview.md) do qual você fez backup anteriormente (veja [Fazer backup de seu aplicativo no Azure](manage-backup.md)). É possível restaurar seu aplicativo com seus bancos de dados vinculados sob demanda para um estado anterior ou criar um novo aplicativo com base em um dos backups do seu aplicativo original. O Serviço de Aplicativo do Azure oferece suporte aos seguintes bancos de dados para backup e restauração:
- [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)
- [Banco de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql)
- [Banco de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL no aplicativo](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

A restauração usando backups está disponível para aplicativos que são executados nas camadas **Standard** e **Premium**. Para obter informações sobre como escalar verticalmente seu aplicativo, veja [Escalar verticalmente um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-scale.md). A camada **Premium** permite um número maior de backups diários do que a camada **Standard**.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Restaurar um aplicativo por meio de um backup existente
1. Na página **Configurações** de seu aplicativo no Portal do Azure, clique em **Backups** ara exibir a página **Backups**. Depois, clique em **Restaurar**.
   
    ![Escolha restaurar agora][ChooseRestoreNow]
2. Na página **Restaurar**, selecione primeiro a fonte do backup.
   
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
   
   > [!WARNING]
   > Se o Serviço de Aplicativo estiver gravando dados no banco de dados durante a restauração, isso poderá resultar em sintomas como violação de CHAVE PRIMÁRIA e perda de dados. É recomendável parar o Serviço de Aplicativo antes de iniciar a restauração do banco de dados.
   > 
   > 
   
    Você pode selecionar um **Aplicativo Existente** para restaurar o backup do aplicativo para outro aplicativo no mesmo grupo de recursos. Antes de usar essa opção, você já precisa ter criado outro aplicativo em seu grupo de recursos com o espelhamento da configuração de banco de dados para aquele definido no backup do aplicativo. Você também pode criar um **Novo** aplicativo no qual o conteúdo será restaurado.

4. Clique em **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Baixar ou excluir um backup de uma conta de armazenamento
1. Na página principal **Procurar** do Portal do Azure, selecione **Contas de armazenamento**. Uma lista de suas contas de armazenamento existentes é exibida.
2. Selecione a conta de armazenamento que contém o backup que você deseja baixar ou excluir. A página da conta de armazenamento é exibida.
3. Na página da conta de armazenamento, selecione o contêiner desejado
   
    ![Exibir contêineres][ViewContainers]
4. Selecione o arquivo de backup que você deseja baixar ou excluir.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Clique em **Baixar** ou **Excluir**, dependendo da ação desejada.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorar uma operação de restauração
Para ver detalhes sobre o sucesso ou a falha da operação de restauração do aplicativo, navegue até a página **Log de Atividades** no Portal do Azure.  
 

Role para baixo para encontrar a operação de restauração desejada e clique para selecioná-la.

A página de detalhes exibe as informações disponíveis relacionadas à operação de restauração.

## <a name="automate-with-scripts"></a>Automatizar com scripts

É possível automatizar o gerenciamento de backup com scripts, usando a [CLI do Azure](/cli/azure/install-azure-cli) ou o [Azure PowerShell](/powershell/azure/overview).

Para obter exemplos, consulte:

- [Exemplos da CLI do Azure](samples-cli.md)
- [Exemplos do Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
