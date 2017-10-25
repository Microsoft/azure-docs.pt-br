---
title: "Gerenciar o esquema de Banco de Dados Azure SQL em um aplicativo multilocatário | Microsoft Docs"
description: "Gerenciar o esquema para vários locatários em um aplicativo multilocatário que usa o Banco de Dados SQL do Azure"
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: billgib; sstein
ms.openlocfilehash: 461da2528d515072bf04a82bb8ba64a853443f98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Gerenciar o esquema para vários locatários em um aplicativo multilocatário que usa o Banco de Dados SQL do Azure

O [primeiro tutorial de SaaS do Wingtip](sql-database-saas-tutorial.md) mostra como o aplicativo pode provisionar um banco de dados do locatário e registrá-lo no catálogo. Como qualquer aplicativo, o aplicativo de SaaS do Wingtip evoluirá ao longo do tempo e, às vezes, exigirá alterações no banco de dados. As alterações podem incluir um esquema novo ou alterado, dados de referência novos ou alterados e tarefas de manutenção de rotina do banco de dados para garantir o desempenho ideal do aplicativo. Com um aplicativo SaaS, essas alterações precisam ser implantadas de maneira coordenada em uma frota potencialmente grande de bancos de dados de locatário. Para que essas alterações estejam em bancos de dados de locatário futuros, elas precisam ser incorporadas ao processo de provisionamento.

Este tutorial explora dois cenários: implantação de atualizações de dados de referência para todos os locatários e reajuste de um índice na tabela que contém os dados de referência. Para executar essas operações em todos os locatários é usado o recurso [Trabalhos elásticos](sql-database-elastic-jobs-overview.md) e também um banco de dados de locatário *golden*, que é usado como um modelo para novos bancos de dados.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]

> * Criar uma conta de trabalho
> * Consultar vários locatários
> * Atualizar dados em todos os bancos de dados de locatário
> * Criar um índice em uma tabela em todos os bancos de dados de locatário


Para concluir este tutorial, certifique-se de atender a todos os seguintes pré-requisitos:

* O aplicativo SaaS Wingtip é implantado. Para implantar em menos de cinco minutos, confira [Implantar e explorar o aplicativo de SaaS do Wingtip](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* A última versão do SQL Server Management Studio (SSMS) está instalada. [Baixar e Instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Este tutorial usa funcionalidades do serviço do Banco de Dados SQL que estão em uma versão prévia limitada (trabalhos de Banco de Dados Elástico). Se você quiser fazer este tutorial, forneça sua ID de assinatura para SaaSFeedback@microsoft.com com o assunto = Elastic Jobs Preview. Após receber a confirmação de que sua assinatura foi habilitada, [baixe e instale as versões de pré-lançamento mais recentes dos cmdlets de trabalhos](https://github.com/jaredmoo/azure-powershell/releases). Esta é uma versão prévia limitada, então contate SaaSFeedback@microsoft.com para conferir perguntas relacionadas ou para obter suporte.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gerenciamento de esquema de SaaS

O padrão de SaaS de único locatário por banco de dados se beneficia, de muitas maneiras, do isolamento de dados resultante, mas, ao mesmo tempo, apresenta a complexidade adicional de manter e gerenciar vários bancos de dados. Os [Trabalhos Elásticos](sql-database-elastic-jobs-overview.md) facilitam a administração e o gerenciamento da camada de dados do SQL. Os trabalhos permitem que você execute tarefas (scripts T-SQL) de maneira segura e confiável, independentes da interação ou da entrada do usuário, em relação a um grupo de bancos de dados. Esse método pode ser usado para implantar esquemas e alterações de dados de referência comum em todos os locatários em um aplicativo. Os Trabalhos Elásticos também podem ser usados para manter uma cópia *golden* do banco de dados usado para criar novos locatários, garantindo que ela sempre tenha os dados de esquema e de referência mais recentes.

![tela](media/sql-database-saas-tutorial-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Versão prévia limitada dos Trabalhos Elásticos

Há uma nova versão dos Trabalhos Elásticos, que agora é um recurso integrado do Banco de Dados SQL do Azure (que não requer serviços ou componentes adicionais). Essa nova versão dos Trabalhos Elásticos está em versão prévia limitada atualmente. Essa versão prévia limitada atualmente dá suporte ao PowerShell para criar contas de trabalho e ao T-SQL para criar e gerenciar trabalhos.

> [!NOTE]
> *Este tutorial usa funcionalidades do serviço do Banco de Dados SQL que estão em uma versão prévia limitada (trabalhos de Banco de Dados Elástico). Se você quiser fazer este tutorial, forneça sua ID de assinatura para SaaSFeedback@microsoft.com com o assunto = Elastic Jobs Preview. Após receber a confirmação de que sua assinatura foi habilitada, [baixe e instale as versões de pré-lançamento mais recentes dos cmdlets de trabalhos](https://github.com/jaredmoo/azure-powershell/releases). Esta é uma versão prévia limitada, então contate SaaSFeedback@microsoft.com para conferir perguntas relacionadas ou para obter suporte.*

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts de SaaS do Wingtip e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Etapas para baixar os scripts do SaaS Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="create-a-job-account-database-and-new-job-account"></a>Criar um banco de dados de conta de trabalho e uma nova conta de trabalho

Este tutorial exige que você use o PowerShell para criar o banco de dados de conta de trabalho e a conta de trabalho. Como no MSDB e no SQL Agent, os Trabalhos Elásticos usam um Banco de Dados SQL do Azure para armazenar definições de trabalho, status de trabalho e o histórico. Depois que a conta de trabalho é criada, você pode criar e monitorar trabalhos imediatamente.

1. Abra ...\\Módulos de aprendizado\\Gerenciamento de esquema\\*Demo-SchemaManagement.ps1* no **ISE do PowerShell**.
1. Pressione **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar um banco de dados *S2* denominado **jobaccount** no servidor de catálogo. Em seguida, ele cria a conta de trabalho, passando o banco de dados jobaccount como um parâmetro para a chamada de criação da conta de trabalho.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar um trabalho para implantar novos dados de referência para todos os locatários

Cada banco de dados de locatário inclui um conjunto de tipos de local que define os tipos de eventos que são sediados em um local. Neste exercício, você implanta uma atualização em todos os bancos de dados de locatário para adicionar dois tipos de local: *Motorcycle Racing* e *Swimming Club*. Esses tipos de local correspondem à imagem de tela de fundo que você vê no aplicativo de eventos de locatário.

Clique no menu suspenso Tipo de Local e verifique se apenas 10 opções de tipo de local estão disponíveis e, especificamente, se "Motorcycle Racing" e "Swimming Club" não são incluídos na lista.

Agora vamos criar um trabalho para atualizar a tabela *VenueTypes* em todos os bancos de dados de locatário e adicionar os novos tipos de local.

Para criar um novo trabalho, usamos um conjunto de trabalhos que os procedimentos armazenados do sistema criou no banco de dados jobaccount quando a conta de trabalho foi criada.

1. Abra o SSMS e conecte-se ao servidor de catálogo: catalog-\<usuário\>.database.windows.net
1. Conecte-se também ao servidor de locatário: tenants1-\<usuário\>.database.windows.net
1. Navegue até o banco de dados *contosoconcerthall* do servidor *tenants1* e consulte a tabela *VenueTypes* para confirmar que *Motorcycle Racing* e *Swimming Club* **não estão** na lista de resultados.
1. Abra o arquivo ...\\Módulos de aprendizado\\Gerenciamento de esquema\\DeployReferenceData.sql
1. Modifique a instrução: SET @wtpUser = &lt;user&gt; e substitua o valor User usado na implantação do aplicativo Wingtip
1. Verifique se está conectado ao banco de dados jobaccount e pressione **F5** para executar o script

* **sp\_add\_target\_group** cria o nome do grupo de destino DemoServerGroup, agora é preciso adicionar os membros de destino.
* **sp\_add\_target\_group\_member** adiciona um tipo de membro de destino do *servidor*, o que considera todos os bancos de dados neste servidor (observe que esse é o servidor tenants1-&lt;User&gt; que contém os bancos de dados do locatário) na hora em que a execução de trabalho deve ser incluída no trabalho. O segundo está adicionando um tipo de membro de destino de *banco de dados*, especificamente banco de dados "golden", basetenantdb, que reside no servidor catalog-&lt;User&gt; e, por fim, outro tipo de membro de grupo de destino de *banco de dados* para incluir o banco de dados adhocanalytics que será usado em um tutorial posterior.
* **sp\_add\_job** cria um trabalho chamado “Reference Data Deployment”
* **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar a tabela de referência, VenueTypes
* As exibições restantes no script exibem a existência dos objetos e monitoram a execução do trabalho. Use essas consultas para examinar o valor de status na coluna **lifecycle** para determinar quando o trabalho foi concluído com êxito em todos os bancos de dados de locatário, e os dois bancos de dados adicionais que contêm a tabela de referência.

1. No SSMS, navegue até o banco de dados *contosoconcerthall* do servidor *tenants1* e consulte a tabela *VenueTypes* para confirmar que *Motorcycle Racing* e *Swimming Club* **estão** na lista de resultados.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar um trabalho para gerenciar o índice da tabela de referência

De maneira semelhante ao exercício anterior, este exercício cria um trabalho para recompilar o índice na chave primária da tabela de referência, uma operação típica de gerenciamento de banco de dados que um administrador deve executar após um carregamento de grandes volumes de dados em uma tabela.

Crie um trabalho usando os mesmos trabalhos dos procedimentos armazenados do "sistema".

1. Abra o SSMS e conecte-se ao servidor catalog-&lt;user&gt;.database.windows.net
1. Abra o arquivo ...\\Módulos de aprendizado\\Gerenciamento de esquema\\OnlineReindex.sql
1. Clique o botão direito do mouse, selecione Conexão e conecte-se ao servidor catalog-&lt;User&gt;.database.windows.net, se ainda não estiver conectado
1. Verifique se está conectado ao banco de dados jobaccount e pressione F5 para executar o script

* sp\_add\_job cria um novo trabalho chamado “Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”
* sp\_add\_jobstep cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar o índice




## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]

> * Criar uma conta de trabalho para consultar em vários locatários
> * Atualizar dados em todos os bancos de dados de locatário
> * Criar um índice em uma tabela em todos os bancos de dados de locatário

[Tutorial de análise ad hoc](sql-database-saas-tutorial-adhoc-analytics.md)


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação de aplicativo de SaaS do Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Gerenciando bancos de dados de nuvem com escalonamento horizontal](sql-database-elastic-jobs-overview.md)
* [Criar e gerenciar bancos de dados de nuvem com escalonamento horizontal](sql-database-elastic-jobs-create-and-manage.md)
