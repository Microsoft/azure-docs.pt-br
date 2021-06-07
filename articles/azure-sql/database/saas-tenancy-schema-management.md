---
title: Gerenciar o esquema em um aplicativo de locatário único
description: Gerenciar o Esquema para vários locatários em um aplicativo de locatário único que usa o Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: e4328be0aade0658dedb034dbbb6980b810f771a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793187"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Gerenciar o esquema em um aplicativo SaaS usando o padrão de banco de dados por locatário com o Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Conforme um aplicativo de banco de dados evolui, alterações inevitavelmente precisam ser feitas no esquema do banco de dados ou nos dados de referência.  As tarefas de manutenção de banco de dados também são exigidas periodicamente. O gerenciamento de um aplicativo que usa o padrão de banco de dados por locatário requer que você aplique essas alterações ou tarefas de manutenção em um grupo de bancos de dados de locatário.

Este tutorial explora dois cenários: implantação de atualizações de dados de referência para todos os locatários e recriação de um índice na tabela que contém os dados de referência. O recurso [Trabalhos elásticos](./elastic-jobs-overview.md) é usado para executar essas ações em todos os bancos de dados de locatário e no banco de dados de modelo usado para criar novos bancos de dados de locatário.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> 
> * Criar um trabalho de agente
> * Fazer com que os trabalhos de T-SQL sejam executados em todos os bancos de dados de locatário
> * Atualizar dados de referência em todos os bancos de dados de locatário
> * Criar um índice em uma tabela em todos os bancos de dados de locatário


Para concluir este tutorial, certifique-se de atender a todos os seguintes pré-requisitos:

* O aplicativo Wingtip Tickets SaaS Database Per Tenant é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip Tickets](./saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps)
* A última versão do SQL Server Management Studio (SSMS) está instalada. [Baixar e Instalar o SSMS](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gerenciamento de esquema de SaaS

O padrão de banco de dados por locatário isola dados do locatário com eficiência, mas aumenta o número de bancos de dados para gerenciar e manter. Os [Trabalhos Elásticos](./elastic-jobs-overview.md) facilitam a administração e o gerenciamento de vários bancos de dados. Os trabalhos permitem uma execução segura e confiável de tarefas (scripts Transact-SQL) em um grupo de bancos de dados. Trabalhos podem implantar esquemas e alterações de dados de referência comum em todos os locatários em um aplicativo. Os Trabalhos Elásticos também podem ser usados para manter um banco de dados de *modelos* usado para criar novos locatários, fazendo com que ele sempre tenha os dados de esquema e de referência mais recentes.

![tela](./media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-public-preview"></a>Versão prévia pública dos Trabalhos Elásticos

Há uma nova versão dos Trabalhos Elásticos, que agora é um recurso integrado do Banco de Dados SQL do Azure. Essa nova versão dos Trabalhos Elásticos está em versão prévia pública atualmente. No momento, a versão prévia pública é compatível com o PowerShell para criar um agente de trabalho e com o T-SQL para criar e gerenciar trabalhos.
Para saber mais, confira o artigo sobre [Trabalhos do Banco de Dados Elástico](./elastic-jobs-overview.md).

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo de banco de dados por locatário SaaS Wingtip Tickets

O código-fonte do aplicativo e os scripts de gerenciamento estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar um banco de dados de agente de trabalho e um novo agente de trabalho

Este tutorial requer que você use o PowerShell para criar um agente de trabalho e seu banco de dados de agente de trabalho de suporte. O banco de dados do agente de trabalho contém definições de trabalho, o status do trabalho e o histórico. Depois que o agente de trabalho e seu banco de dados são criados, você pode criar e monitorar trabalhos imediatamente.

1. **No ISE do PowerShell**, abra …\\Learning Modules\\Schema Management\\*Demo-SchemaManagement.ps1*.
1. Pressione **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar um banco de dados de nome *osagent* no servidor de catálogo. Em seguida, ele cria o agente de trabalho usando o banco de dados como um parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar um trabalho para implantar novos dados de referência para todos os locatários

No aplicativo Wingtip Tickets, cada banco de dados do locatário inclui um conjunto de tipos de local com suporte. Cada local é de um tipo específico, que define os tipos de evento que podem ser hospedados e determina a imagem de tela de fundo usada no aplicativo. Para o aplicativo dar suporte a novos tipos de eventos, esses dados de referência devem ser atualizados e novos tipos de local devem ser adicionados.  Neste exercício, você implanta uma atualização em todos os bancos de dados de locatário para adicionar dois tipos de local: *Motorcycle Racing* e *Swimming Club*.

Primeiro, revise os tipos de local incluídos em cada banco de dados de locatário. Conecte-se a um banco de dados de locatário no SSMS (SQL Server Management Studio) e verifique a tabela VenueTypes.  Você também pode consultar essa tabela no Editor de consultas no portal do Azure, acessado pela página do banco de dados. 

1. Abra o SSMS e conecte-se ao servidor *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Para confirmar que *Motorcycle Racing* e *Swimming Club* **não estão** na lista de resultados, navegue até o banco de dados _contosoconcerthall_ do servidor *tenants1-dpt-&lt;usuário&gt;* e consulte a tabela *VenueTypes*.

Agora vamos criar um trabalho para atualizar a tabela *VenueTypes* em todos os bancos de dados de locatário a fim de adicionar os novos tipos de local.

Para criar um novo trabalho, use um conjunto de trabalhos que os procedimentos armazenados do sistema criou no banco de dados _jobagent_ quando a conta do agente de trabalho foi criada.

1. No SSMS, conecte-se ao servidor de catálogo: *catalog-dpt-&lt;user&gt;.database.windows.net* 
1. No SSMS, abra o arquivo …\\Learning Modules\\Schema Management\\DeployReferenceData.sql
1. Modifique a instrução: DEFINA @wtpUser = &lt;user&gt; e substitua o valor User usado quando você implantou o aplicativo Banco de Dados por Locatário SaaS Wingtip Tickets
1. Verifique se você está conectado ao banco de dados _jobaccount_ e pressione **F5** para executar o script

Observe os seguintes elementos no script *DeployReferenceData.sql*:
* **SP\_add\_target\_group** cria o nome do grupo de destino DemoServerGroup.
* **SP\_add\_target\_group\_member** é usado para definir o conjunto de bancos de dados de destino.  Primeiro, o servidor _tenants1-dpt -&lt;user&gt;_ é adicionado.  Adicionar o servidor como um destino faz com que os bancos de dados no servidor no momento da execução do trabalho sejam incluídos no trabalho. Em seguida, o banco de dados _basetenantdb_ e o banco de dados *adhocreporting* (usado em um tutorial posterior) são adicionadas como destinos.
* **sp\_add\_job** cria um trabalho denominado _Reference Data Deployment_.
* **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar a tabela de referência, VenueTypes.
* As exibições restantes no script exibem a existência dos objetos e monitoram a execução do trabalho. Use essas consultas para examinar o valor do status na coluna **lifecycle** para determinar quando o trabalho foi concluído em todos os bancos de dados de destino.

Quando o script for concluído, você poderá verificar se os dados de referência foram atualizados.  No SSMS, navegue até o servidor *contosoconcerthall* banco de dados de *tenants1-dpt -&lt;user&gt;* e consulta a tabela *VenueTypes*.  Verifique se *Motorcycle Racing* e *Swimming Club* **estão** presentes agora.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar um trabalho para gerenciar o índice da tabela de referência

Este exercício usa um trabalho para recriar o índice de chave primária da tabela de referência.  Essa é uma operação de manutenção de banco de dados típica que pode ser feita após o carregamento de grandes quantidades de dados.

Crie um trabalho usando os mesmos trabalhos dos procedimentos armazenados do "sistema".

1. Abra o SSMS e conecte-se ao servidor _catalog-dpt-&lt;user&gt;.database.windows.net_
1. Abra o arquivo _...\\Módulos de aprendizado\\Gerenciamento de esquema\\OnlineReindex.sql_
1. Clique com o botão direito do mouse, selecione Conexão e conecte-se ao servidor _catalog-dpt-&lt;user&gt;.database.windows.net_, se ainda não estiver conectado
1. Verifique se você está conectado ao banco de dados _jobaccount_ e pressione **F5** para executar o script

Observe os seguintes elementos no script _OnlineReindex.sql_:
* **sp\_add\_job** cria um novo trabalho chamado “Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”
* **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar o índice
* As exibições restantes na execução do trabalho no monitor de script. Use essas consultas para examinar o valor do status na coluna **lifecycle** para determinar quando o trabalho foi concluído com êxito em todos os membros do grupo de destino.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> 
> * Criar um trabalho de agente para ser executado em trabalhos de T-SQL de vários bancos de dados
> * Atualizar dados de referência em todos os bancos de dados de locatário
> * Criar um índice em uma tabela em todos os bancos de dados de locatário

Em seguida, confira o [tutorial de relatórios ad hoc](./saas-tenancy-cross-tenant-reporting.md) para explorar a execução de consultas distribuídas entre bancos de dados de locatário.


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação do aplicativo Banco de Dados por Locatário SaaS Wingtip Tickets](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Gerenciando bancos de dados de nuvem com escalonamento horizontal](./elastic-jobs-overview.md)