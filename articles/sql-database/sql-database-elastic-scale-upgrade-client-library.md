---
title: "Atualizar para a biblioteca de cliente de banco de dados elástico mais recente | Microsoft Docs"
description: Atualizar aplicativos e bibliotecas usando o Nuget
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 0a546510-76e7-465e-9271-f15ff0cfa959
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
ms.openlocfilehash: 7e28d128645e856c0efa6e4f78d8f9f36982a76c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Atualizar um aplicativo para usar a biblioteca de cliente de banco de dados elástico mais recente
Novas versões da [biblioteca de cliente de Banco de Dados Elástico](sql-database-elastic-database-client-library.md) estão disponíveis por meio do NuGet e da interface do Gerenciador de Pacotes NuGet no Visual Studio. Atualizações contêm correções de bugs e suporte para novos recursos de biblioteca de cliente.

**Para obter a versão mais recente:** acesse [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Recompile seu aplicativo com a nova biblioteca, além de alterar os metadados do Gerenciador de Mapa de Fragmentos existentes armazenados em seus Bancos de Dados SQL do Azure para dar suporte aos novos recursos.

Executar estas etapas nesta ordem garante que as versões antigas da biblioteca de cliente não estejam mais presentes em seu ambiente quando objetos de metadados são atualizados, o que significa que os objetos de metadados da versão antiga não serão criados após a atualização.   

## <a name="upgrade-steps"></a>Etapas de atualização
**1. Atualize seus aplicativos.** No Visual Studio, baixe e referencie a versão mais recente da biblioteca do cliente em todos os seus projetos de desenvolvimento que usam a biblioteca. Em seguida, recrie e implante. 

* Em sua solução do Visual Studio, selecione **Ferramentas** --> **NuGet Package Manager** -->  **Gerenciar pacotes NuGet para solução**. 
* (Visual Studio 2013) No painel esquerdo, escolha **Atualizações** e selecione o botão **Atualizar** no pacote **Biblioteca de cliente de escala elástica do Banco de dados SQL do Azure** que aparece na janela.
* (Visual Studio 2015) Defina a caixa de Filtro para **Atualização disponível**. Selecione o pacote para atualizar e clique no botão **Atualizar** .
* (2017 do visual Studio) Na parte superior da caixa de diálogo, selecione **Atualizações**. Selecione o pacote para atualizar e clique no botão **Atualizar** .
* Criar e implantar. 

**2. Atualize seus scripts.** Se você estiver usando scripts do **PowerShell** para gerenciar os fragmentos, [baixe a nova versão da biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e copie-a para o diretório de onde você executa scripts. 

**3. Atualize o seu serviço de mesclagem de divisão.** Se você usar o serviço de mesclagem de divisão de banco de dados elástico para reorganizar dados fragmentados, [baixe e implante a versão mais recente do serviço](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Etapas detalhadas de atualização para o serviço podem ser encontradas [aqui](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Atualizar seus bancos de dados do Gerenciador do Mapa de Fragmentos**. Atualizar os metadados que suportam seus mapas de fragmento no banco de dados SQL do Azure.  Há duas maneiras que você pode fazer isso, usando o PowerShell ou C#. Ambas as opções são mostradas abaixo.

***Opção 1: Atualizar os metadados usando o PowerShell***

1. Baixar o utilitário de linha de comando mais recente para NuGet [aqui](http://nuget.org/nuget.exe) e salvar em uma pasta. 
2. Abra um Prompt de Comando, navegue para a mesma pasta e execute o comando: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navegue até a subpasta que contém a nova versão DLL do cliente que acabou de baixar, por exemplo: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Baixe o scriptlet da atualização de clientes de banco de dados elástico do [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)e salve-o na mesma pasta que contém a DLL.
5. Nessa pasta, execute "PowerShell .\upgrade.ps1" no prompt de comando e siga os prompts.

***Opção 2: Atualizar os metadados usando C#***

Como alternativa, crie um aplicativo do Visual Studio que abre sua ShardMapManager, itera em todos os fragmentos e executa a atualização de metadados chamando os métodos [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) e [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) como neste exemplo: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Essas técnicas para atualizações de metadados podem ser aplicadas várias vezes sem danos. Por exemplo, se uma versão mais antiga do cliente cria inadvertidamente um fragmento depois que você atualizou, você pode executar a atualização novamente entre todos os fragmentos para garantir que a versão mais recente de metadados está presente em toda a infraestrutura. 

**Observação:** Novas versões da biblioteca de cliente atualizadas continuam a funcionar com as versões anteriores dos metadados do Gerenciador de Mapa de Fragmentos no Banco de Dados SQL do Azure e vice-versa.   No entanto, para aproveitar alguns dos novos recursos mais recente do cliente, os metadados precisam ser atualizados.   Observe que as atualizações de metadados não afetarão nenhum dados do usuário ou dados específicos do aplicativo, somente os objetos criados e usados pelo Gerenciador de mapa de fragmentos.  E os aplicativos continuam a operar na sequência de atualização descrita acima. 

## <a name="elastic-database-client-version-history"></a>O histórico de versões de cliente do banco de dados elástico
Para ver o histórico de versão, vá para [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

