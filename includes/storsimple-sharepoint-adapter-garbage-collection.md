---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0b5d9deacdd4266da30f17c95b6e575a652d2f76
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171964"
---
Neste procedimento, você vai:

1. [Preparar-se para executar o arquivo executável do Maintainer](#to-prepare-to-run-the-maintainer) .
2. [Preparar o banco de dados de conteúdo e a Lixeira para exclusão imediata de BLOBs órfãos](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Executar o Maintainer.exe](#to-run-the-maintainer).
4. [Reverter as configurações da Lixeira e do banco de dados de conteúdo](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Preparar-se para executar o arquivo executável do Maintainer.
1. No servidor Web front-end, abra o Shell de Gerenciamento do SharePoint 2013 como administrador.
2. Navegue até a pasta *unidade de inicialização*:\Arquivos de Programas\Microsoft SQL Remote Blob Storage 10.50\Maintainer\.
3. Renomeie **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** para **web.config**.
4. Use `aspnet_regiis -pdf connectionStrings` para descriptografar o arquivo web.config.
5. No arquivo web.config descriptografado, sob o nó `connectionStrings` , adicione a cadeia de conexão para sua instância de SQL Server e o nome do banco de dados de conteúdo. Veja os exemplos a seguir.
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Use `aspnet_regiis –pef connectionStrings` para criptografar o arquivo web.config novamente. 
7. Renomeie o web.config para Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Para preparar o banco de dados de conteúdo e a Lixeira para excluir imediatamente BLOBs órfãos
1. No SQL Server, no SQL Management Studio, execute as seguintes consultas de atualização para o banco de dados de conteúdo de destino: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. No servidor Web front-end, em **Administração Central**, edite as **Configurações Gerais do Aplicativo Web** para o banco de dados de conteúdo desejado a fim de desabilitar temporariamente a Lixeira. Essa ação também esvaziará a Lixeira para qualquer coleção de site relacionada. Para fazer isso, clique em **Administração Central** -> **Gerenciamento de Aplicativos** -> **Aplicativos Web (Gerenciar aplicativos Web)**  -> **SharePoint - 80** -> **Configurações Gerais de Aplicativos**. Defina o **Status da Lixeira** como **DESATIVADO**.
   
    ![Configurações Gerais do Aplicativo Web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Para executar o Maintainer
* No servidor Web front-end, no Shell de Gerenciamento do SharePoint 2013, execute o Maintainer como se segue:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Somente a operação `GarbageCollection` tem suporte para o StorSimple neste momento. Observe também que os parâmetros emitidos para Microsoft.Data.SqlRemoteBlobs.Maintainer.exe diferenciam maiúsculas de minúsculas. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Para reverter as configurações da Lixeira e do banco de dados de conteúdo
1. No SQL Server, no SQL Management Studio, execute as seguintes consultas de atualização para o banco de dados de conteúdo de destino:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. No servidor Web front-end, na **Administração Central**, edite as **Configurações Gerais do Aplicativo Web** para o banco de dados de conteúdo desejado a fim de reabilitar a Lixeira. Para fazer isso, clique em **Administração Central** -> **Gerenciamento de Aplicativos** -> **Aplicativos Web (Gerenciar aplicativos Web)**  -> **SharePoint - 80** -> **Configurações Gerais de Aplicativos**. Defina o Status da Lixeira para **ATIVADO**.

