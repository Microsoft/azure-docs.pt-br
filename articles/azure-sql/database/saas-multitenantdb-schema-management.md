---
title: Gerenciar o esquema em um aplicativo multilocatário
description: Gerenciar o esquema para vários locatários em um aplicativo multilocatário que usa o Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: d222234cd6ff3d910e6dbc51a394695ce467edce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011840"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-databases"></a>Gerenciar o esquema em um aplicativo SaaS que usa bancos de dados multilocatários fragmentados
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este tutorial examina os desafios de manter um grupo de bancos de dados em um aplicativo SaaS (Software como Serviço). As soluções são demonstradas para expandir as alterações de esquema em grupos de bancos de dados.

Como qualquer aplicativo, o aplicativo SaaS Wingtip Tickets evoluirá ao longo do tempo e exigirá alterações no banco de dados. As alterações podem afetar os dados de referência ou o esquema, ou aplicar tarefas de manutenção de banco de dados. Com um aplicativo SaaS usando um padrão de banco de dados por locatário, as alterações devem ser coordenadas entre um grupo potencialmente grande de bancos de dados de locatário. Além disso, você deve incorporar essas alterações no processo de provisionamento de banco de dados para fazer com que elas sejam incluídas nos novos bancos de dados conforme eles são criados.

#### <a name="two-scenarios"></a>Dois cenários

Este tutorial aborda os dois cenários a seguir:
- Implantar as atualizações de dados de referência para todos os locatários.
- A recriação de um índice em uma tabela que contém os dados de referência.

O recurso de [Trabalhos Elástico](./elastic-jobs-overview.md) do Banco de Dados SQL do Azure é usado para executar essas operações em bancos de dados de locatário. Os trabalhos também operam no banco de dados de locatário do “modelo”. No aplicativo de exemplo Wingtip Tickets, esse banco de dados de modelo é copiado para provisionar um novo banco de dados de locatário.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um trabalho de agente.
> * Executar uma consulta de T-SQL em vários bancos de dados de locatário.
> * Atualizar dados de referência em todos os bancos de dados de locatário.
> * Criar um índice em uma tabela em todos os bancos de dados de locatário.

## <a name="prerequisites"></a>Pré-requisitos

- O aplicativo de banco de dados multilocatário Wingtip Tickets já deve estar implantado:
    - Para obter instruções, consulte o primeiro tutorial, que apresenta o aplicativo de banco de dados multilocatário SaaS Wingtip Tickets:<br />[Implantar e explorar um aplicativo multilocatário fragmentado que usa o Banco de dados SQL do Azure](./saas-multitenantdb-get-started-deploy.md).
        - O processo de implantação é executado em menos de cinco minutos.
    - Você deve ter a versão *multilocatário fragmentada* do Wingtip instalada. As versões para *Autônomo* e *Banco de dados por locatário* não dão suporte a este tutorial.

- A última versão do SQL Server Management Studio (SSMS) deve estar instalada. [Baixar e Instalar o SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

- O Azure PowerShell deve estar instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

> [!NOTE]
> Este tutorial usa recursos do serviço do Banco de Dados SQL do Azure que estão em uma versão prévia limitada ([trabalhos de Banco de Dados Elástico](elastic-database-client-library.md)). Se você deseja fazer este tutorial, forneça sua ID de assinatura para *SaaSFeedback\@microsoft.com* com o assunto=Elastic Jobs Preview. Após receber a confirmação de que sua assinatura foi habilitada, [baixe e instale as versões de pré-lançamento mais recentes dos cmdlets de trabalhos](https://github.com/jaredmoo/azure-powershell/releases). Esta é uma versão prévia limitada, então entre em contato com *SaaSFeedback\@microsoft.com* para ver perguntas relacionadas ou obter suporte.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gerenciamento de esquema de SaaS

O modelo de banco de dados multilocatário fragmentado usado nesta amostra permite que um banco de dados de locatários contenha um ou mais locatários. Esta amostra explora o potencial do uso de uma combinação de bancos de dados multilocatário e de locatário único, possibilitando um modelo *híbrido* de gerenciamento de locatários. O gerenciamento de alterações para esses bancos de dados pode ser complicado. Os [Trabalhos Elásticos](./elastic-jobs-overview.md) facilitam a administração e o gerenciamento de grandes quantidades de banco de dados. Os trabalhos permitem uma execução segura e confiável dos scripts de Transact-SQL como tarefas em um grupo de bancos de dados de locatário. As tarefas são independentes da interação ou entrada do usuário. Esse método pode ser usado para implantar alterações no esquema ou nos dados de referência comuns, em todos os locatários em um aplicativo. Trabalhos Elásticos também podem ser usados para manter uma cópia do modelo final do banco de dados. O modelo é usado para criar novos locatários, garantindo sempre que esquema e os dados de referência mais recentes estão sendo usados.

![tela](./media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Versão prévia limitada dos Trabalhos Elásticos

Há uma nova versão dos Trabalhos Elásticos, que agora é um recurso integrado do Banco de Dados SQL do Azure. Essa nova versão dos Trabalhos Elásticos está em versão prévia limitada atualmente. A versão prévia limitada atualmente dá suporte ao uso do PowerShell para criar um agente de trabalho e ao T-SQL para criar e gerenciar trabalhos.
> [!NOTE]
> Este tutorial usa funcionalidades do serviço do Banco de Dados SQL que estão em uma versão prévia limitada (trabalhos de Banco de Dados Elástico). Se você deseja fazer este tutorial, forneça sua ID de assinatura para SaaSFeedback@microsoft.com com o assunto = Elastic Jobs Preview. Após receber a confirmação de que sua assinatura foi habilitada, baixe e instale as versões de pré-lançamento mais recentes dos cmdlets de trabalhos. Esta é uma versão prévia limitada, então contate SaaSFeedback@microsoft.com para conferir perguntas relacionadas ou para obter suporte.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obter o código-fonte e os scripts do aplicativo de banco de dados multilocatário SaaS Wingtip Tickets

Os scripts e o código-fonte do aplicativo de Banco de Dados Multilocatário SaaS Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) no GitHub. Veja as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Criar um banco de dados de agente de trabalho e um novo agente de trabalho

Este tutorial exige que você use o PowerShell para criar o banco de dados de agente de trabalho e o agente de trabalho. Assim como o banco de dados MSDB usado pelo SQL Agent, um agente de trabalho usa um Banco de Dados SQL do Azure para armazenar definições de trabalho, status de trabalho e histórico. Depois que o agente de trabalho é criado, você pode criar e monitorar trabalhos imediatamente.

1. **No ISE do PowerShell**, abra …*Learning Modules\\Schema Management\\\\Demo-SchemaManagement.ps1*.
2. Pressione **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar um banco de dados denominado _jobagent_ no servidor de catálogo. O script cria o agente de trabalho, passando o banco de dados _jobagent_ como um parâmetro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar um trabalho para implantar novos dados de referência para todos os locatários

#### <a name="prepare"></a>Preparar

Cada banco de dados de locatários inclui um conjunto de tipos de local na tabela **VenueTypes**. Cada tipo de local define os tipos de eventos que podem ser hospedados em um local. Esses tipos de local correspondem às imagens de tela de fundo que você vê no aplicativo de eventos de locatário.  Neste exercício, você implanta uma atualização em todos os bancos de dados para adicionar dois tipos de local: *Motorcycle Racing* e *Swimming Club*.

Primeiro, revise os tipos de local incluídos em cada banco de dados de locatário. Conecte-se a um banco de dados de locatário no SSMS (SQL Server Management Studio) e verifique a tabela VenueTypes.  Você também pode consultar essa tabela no Editor de consultas no portal do Azure, acessado pela página do banco de dados.

1. Abra o SSMS e conecte-se ao servidor *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Para confirmar que *Motorcycle Racing* e *Swimming Club* **não estão** na lista de resultados, navegue até o banco de dados *contosoconcerthall* do servidor *tenants1-dpt-&lt;usuário&gt;* e consulte a tabela *VenueTypes*.



#### <a name="steps"></a>Etapas

Agora você cria um trabalho para atualizar a tabela **VenueTypes** em cada banco de dados de locatários adicionando os novos tipos de local.

Para criar um novo trabalho, você usa um conjunto de trabalhos que os procedimentos armazenados do sistema criou no banco de dados _jobagent_. Os procedimentos armazenados foram criados quando a conta de trabalho foi criada.

1. No SSMS, conecte-se ao servidor de locatário: tenants1-mt-&lt;user&gt;.database.windows.net

2. Navegue até o banco de dados *tenants1*.

3. Consulta a tabela *VenueTypes* para confirmar que *Motorcycle Racing* e *Swimming Club* ainda não estão na lista de resultados.

4. Conecte-se ao servidor de catálogo, que é *catalog-mt-&lt;user&gt;.database.windows.net*.

5. Conecte-se ao banco de dados _jobagent_ no servidor de catálogo.

6. No SSMS, abra o arquivo *…\\Learning Modules\\Schema Management\\DeployReferenceData.sql*.

7. Modifique a instrução: defina @User = &lt;user&gt; e substitua o valor User usado quando você implantou o aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets.

8. Pressione **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no script *DeployReferenceData.sql*:

- **sp\_add\_target\_group** cria o nome do grupo de destino *DemoServerGroup* e adiciona os membros de destino ao grupo.

- **sp\_add\_target\_group\_member** adicione os seguintes itens:
    - Um tipo de membro de destino *server*.
        - Este é o servidor *tenants1-mt-&lt;user&gt;* que contém os bancos de dados de locatários.
        - A inclusão do servidor inclui os bancos de dados de locatário que existem no momento em que o trabalho é executado.
    - Um tipo de membro de destino *database* para o banco de dados final (*basetenantdb*) que reside no servidor *catalog-mt-&lt;user&gt;*,
    - Um tipo de membro de destino *database* para incluir o banco de dados *adhocreporting* que é usado em um tutorial posterior.

- **sp\_add\_job** cria um trabalho chamado *Reference Data Deployment*.

- **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar a tabela de referência, VenueTypes.

- As exibições restantes no script exibem a existência dos objetos e monitoram a execução do trabalho. Use essas consultas para examinar o valor do status na coluna **lifecycle** para determinar quando o trabalho foi concluído. O trabalho atualiza o banco de dados de locatários e atualiza os dois outros bancos de dados que contêm a tabela de referência.

No SSMS, navegue até o banco de dados de locatário no servidor *tenants1-mt-&lt;user&gt;*. Consulta a tabela *VenueTypes* para confirmar que *Motorcycle Racing* e *Swimming Club* ainda não foram adicionados à tabela. A contagem total de tipos de local deve ter aumentado em duas unidades.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar um trabalho para gerenciar o índice da tabela de referência

Este exercício cria um trabalho para recriar o índice de chave primária da tabela de referência em todos os bancos de dados de locatário. Uma recompilação de índice é uma operação de gerenciamento de banco de dados normal que um administrador pode executar após carregar uma grande quantidade de dados para melhorar o desempenho.

1. No SSMS, conecte-se ao banco de dados _jobagent_ no servidor *catalog-mt-&lt;user&gt;.database.windows.net*.

2. No SSMS, abra *...\\Learning Modules\\Schema Management\\OnlineReindex.sql*.

3. Pressione **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no script *OnlineReindex.sql*:

* **sp\_add\_job** cria um novo trabalho chamado *Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar o índice.

* As exibições restantes na execução do trabalho no monitor de script. Use essas consultas para examinar o valor do status na coluna **lifecycle** para determinar quando o trabalho foi concluído com êxito em todos os membros do grupo de destino.

## <a name="additional-resources"></a>Recursos adicionais

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Gerenciando bancos de dados de nuvem com escalonamento horizontal](./elastic-jobs-overview.md)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um trabalho de agente para ser executado em trabalhos de T-SQL entre vários bancos de dados
> * Atualizar dados de referência em todos os bancos de dados de locatário
> * Criar um índice em uma tabela em todos os bancos de dados de locatário

Em seguida, confira o [tutorial de relatórios ad hoc](./saas-multitenantdb-adhoc-reporting.md) para explorar a execução de consultas distribuídas entre bancos de dados de locatário.