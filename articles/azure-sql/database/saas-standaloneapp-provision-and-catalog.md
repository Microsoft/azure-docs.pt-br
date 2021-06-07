---
title: Tutorial de SaaS multilocatário
description: Provisionar e catalogar novos locatários usando o padrão de aplicativo autônomo
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 2343800f8801105ca75f285972b441ecb027d1a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793238"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Provisionar e catalogar novos locatários usando o padrão de aplicativo autônomo
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo cobre provisionamento e catalogação de novos locatários usando o aplicativo autônomo por padrão SaaS de locatário.
Este artigo tem duas partes principais:
* Discussão conceitual sobre provisionamento e catalogação de novos locatários
* Um tutorial que destaca o código PowerShell de exemplo que realiza provisionamento e catalogação
    * O tutorial usa o aplicativo SaaS de exemplo Wingtip Tickets, adaptado para o aplicativo autônomo por padrão de locatário.

## <a name="standalone-application-per-tenant-pattern"></a>Padrão de aplicativo por locatário autônomo

O aplicativo autônomo por padrão locatário é um dos vários padrões para aplicativos SaaS multilocatários.  Neste padrão, um aplicativo autônomo é provisionado para cada locatário. O aplicativo compreende componentes de nível de aplicativo e um Banco de Dados SQL do Azure.  Cada aplicativo de locatário pode ser implantado na assinatura do fornecedor.  Alternativamente, o Azure oferece um [programa de aplicativos gerenciados](../../azure-resource-manager/managed-applications/overview.md) no qual um aplicativo pode ser implantado na assinatura de um locatário e gerenciado pelo fornecedor em nome do locatário.

   ![padrão de aplicativo por locatário](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Ao implantar um aplicativo para um locatário, o aplicativo e o banco de dados são provisionados em um novo grupo de recursos criado para o locatário.  O uso de grupos de recursos separados isola os recursos de aplicativos de cada locatário e permite que sejam gerenciados de forma independente. Dentro de cada grupo de recursos, cada instância do aplicativo é configurada para acessar diretamente a base de dados correspondente.  Esse modelo de conexão contrasta com outros padrões que utilizam um catálogo para conexões de agente entre o aplicativo e o banco de dados.  E como não há compartilhamento de recurso, cada banco de dados de locatário deve ser provisionado com recursos suficientes para lidar com sua carga de pico. Este padrão tende a ser usado para aplicativos SaaS com menos locatários, onde há uma forte ênfase no isolamento dos locatários e menos ênfase nos custos de recurso.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Usar um catálogo de locatário com o aplicativo por padrão de locatário

Embora cada banco de dados e aplicativo do locatário seja totalmente isolado, vários cenários de análise e gerenciamento podem operar entre os locatários.  Por exemplo, aplicar uma alteração de esquema para uma nova versão do aplicativo requer alterações no esquema de cada banco de dados de locatário. Os cenários de relatórios e análises também podem exigir acesso a todos os bancos de dados de locatário, independentemente de onde estejam implantados.

   ![Diagrama que mostra como usar um catálogo de locatários com o padrão por locatário do aplicativo.](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

O catálogo de locatário contém um mapeamento entre um identificador de locatário e um banco de dados de locatário, permitindo que um identificador seja resolvido para um servidor e nome de banco de dados.  No aplicativo SaaS Wingtip, o identificador de locatário é calculado como um hash do nome do locatário, embora outros esquemas possam ser utilizados.  Embora aplicativos autônomos não precisem do catálogo para gerenciar conexões, o catálogo pode ser utilizado para escopo de outras ações para um conjunto de banco de dados de locatário. Por exemplo, o Elastic Query pode usar o catálogo para determinar o conjunto de bancos de dados em que as consultas são distribuídas para o relatório de locatários cruzados.

## <a name="elastic-database-client-library"></a>Biblioteca de cliente do Banco de Dados Elástico

No aplicativo de exemplo Wingtip, o catálogo é implementado pelos recursos de gerenciamento de fragmentos da EDCL [(Biblioteca de Clientes do Banco de Dados Elástico)](elastic-database-client-library.md).  A biblioteca permite que um aplicativo crie, gerencie e use um mapa de fragmentos que esteja armazenado em um banco de dados. No exemplo de Tickets Wingtip, o catálogo é armazenado no banco de dados do *catálogo de locatário*.  O fragmento mapeia uma chave de locatário para o fragmento (banco de dados) em que os dados desse locatário sejam armazenados.  As funções da EDCL gerenciam um *mapa de fragmentos global* armazenado em tabelas no banco de dados do *catálogo de locatário* e um *mapa de fragmentos local* armazenado em cada fragmento.

As funções da EDCL podem ser chamadas a partir de aplicativos ou scripts do PowerShell para criar e gerenciar as entradas no mapa de fragmentos. Outras funções da EDCL podem ser usadas para recuperar o conjunto de fragmentos ou conectar ao banco de dados correto para uma determinada chave de locatário.

> [!IMPORTANT]
> Não edite os dados no banco de dados do catálogo ou no mapa de fragmentos local nos bancos de dados do locatário diretamente. As atualizações diretas não são compatíveis devido ao alto risco de dados corrompidos. Nesse caso, edite os dados de mapeamento somente por meio de APIs de EDCL.

## <a name="tenant-provisioning"></a>Provisionamento de locatário

Cada locatário requer um novo grupo de recursos do Azure, que deve ser criado antes que os recursos possam ser provisionados dentro dele. Quando o grupo de recursos existir, um modelo de Gerenciamento de Recursos do Azure poderá ser utilizado para implantar os componentes de aplicativos e o banco de dados e depois configurar a conexão do banco de dados. Para inicializar o esquema de banco de dados, o modelo pode importar um arquivo bacpac.  Alternativamente, o banco de dados pode ser criado como uma cópia de um banco de dados "modelo".  O banco de dados é posteriormente atualizado com dados iniciais do local e registrado no catálogo.

## <a name="tutorial"></a>Tutorial

Neste tutorial, você aprenderá a:

* Provisionar um catálogo
* Registrar os bancos de dados de locatário de exemplo que você implantou anteriormente no catálogo
* Provisionar um locatário adicional e registrá-lo no catálogo

Um modelo do Azure Resource Manager é usado para implantar e configurar o aplicativo, criar o banco de dados do locatário e, em seguida, importar um arquivo bacpac para inicializá-lo. A solicitação de importação pode permanecer na fila por vários minutos antes de ser executada.

No final deste tutorial, você terá um conjunto de aplicativos de locatário autônomo, com cada banco de dados registrado no catálogo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps)
* Os três aplicativos de locatário de exemplo são implantados. Para implantar esses aplicativos em menos de cinco minutos, consulte [Implantar e explorar o padrão de aplicativo autônomo SaaS Wingtip Tickets](./saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Provisionar o catálogo

Nesta tarefa, você aprenderá como provisionar o catálogo usado para registrar todos os bancos de dados de locatário. Você vai:

* **Provisionar o banco de dados do catálogo** usando um modelo de gerenciamento de recursos do Azure. O banco de dados é inicializado, importando um arquivo bacpac.
* **Registre os aplicativos de locatário de exemplo** que você implantou anteriormente.  Cada locatário é registrado usando uma chave construída a partir de um hash do nome do locatário.  O nome do locatário também é armazenado em uma tabela de extensão no catálogo.

1. No PowerShell ISE, abra *...\Learning Modules\UserConfig.psm* e atualize o valor **\<user\>** para o valor utilizado ao implementar os três aplicativos de exemplo.  **Salve o arquivo**.
1. No PowerShell ISE, abra *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e defina **$Scenario = 1**. Implante o catálogo de locatário e registre os locatários predefinidos.

1. Adicione um ponto de interrupção, colocando o cursor em qualquer lugar na linha indicando, `& $PSScriptRoot\New-Catalog.ps1`, e, em seguida, pressione **F9**.

    ![definindo um ponto de interrupção para o rastreamento](./media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Execute o script pressionando **F5**.
1.  Após a execução do script parar no ponto de interrupção, pressione **F11** para intervir no script New-Catalog.ps1.
1.  Rastreie a execução do script usando as opções do menu Depurar, F10 e F11, para contornar ou intervir nas funções chamadas.
    *   Para saber mais sobre como depurar scripts do PowerShell, confira [Dicas sobre como trabalhar e depurar scripts do PowerShell](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Quando o script for concluído, o catálogo existirá e todos os locatários de exemplo serão registrados.

Agora, analise os recursos que você criou.

1. Abra o [Portal do Azure](https://portal.azure.com/) e navegue pelos grupos de recursos.  Abra o grupo de recursos **wingtip-sa-catalog-\<user\>** e anote o servidor de catálogo e o banco de dados.
1. Abra o banco de dados no portal e selecione *Data Explorer* no menu à esquerda.  Clique no comando Login e, em seguida, insira a Senha = **P\@ssword1**.


1. Explore o esquema do banco de dados *tenantcatalog*.
   * Os objetos no esquema `__ShardManagement` são todos fornecidos pela Biblioteca de Clientes do Banco de Dados Elástico.
   * A tabela `Tenants` e `TenantsExtended` são extensões adicionadas no exemplo que demonstram como é possível estender o catálogo para fornecer valor adicional.
1. Execute a consulta, `SELECT * FROM dbo.TenantsExtended`.

   ![data explorer](./media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Como alternativa ao uso do Data Explorer, é possível conectar-se ao banco de dados do SQL Server Management Studio. Para fazer isso, conecte-se ao servidor wingtip-


    Observe que você não deve editar dados diretamente no catálogo - sempre use as APIs de gerenciamento de fragmentos.

## <a name="provision-a-new-tenant-application"></a>Provisionar uma nova solicitação de aplicativo de locatário

Nesta tarefa, você aprenderá como provisionar um aplicativo de locatário único. Você vai:

* **Criar um novo grupo de recursos** para o locatário.
* **Provisione o aplicativo e o banco de dados** no novo grupo de recursos, utilizando um modelo de gerenciamento de recursos do Azure.  Esta ação inclui a inicialização do banco de dados com dados de referência e esquema comuns, importando um arquivo bacpac.
* **Inicializar o banco de dados com informações de locatário básicas**. Esta ação inclui especificar o tipo do local, que determina a fotografia usada como tela de fundo em seu site de eventos.
* **Registrar o banco de dados no banco de dados do catálogo**.

1. No PowerShell ISE, abra *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e defina **$Scenario = 2**. Implantar o catálogo de locatário e registre os locatários predefinidos

1. Adicione um ponto de interrupção no script, colocando o cursor em qualquer lugar na linha 49 indicando, `& $PSScriptRoot\New-TenantApp.ps1` e, em seguida, pressione **F9**.
1. Execute o script pressionando **F5**.
1.  Após a execução do script parar no ponto de interrupção, pressione **F11** para intervir no script New-Catalog.ps1.
1.  Rastreie a execução do script usando as opções do menu Depurar, F10 e F11, para contornar ou intervir nas funções chamadas.

Após o provisionamento do locatário, o novo site de eventos do locatário será aberto.

   ![corrida de folha de bordo vermelha](./media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Assim, é possível inspecionar os novos recursos criados no Portal do Azure.

   ![recursos de corrida de folha de bordo vermelha](./media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Para parar a cobrança, exclua os grupos de recursos

Ao terminar de explorar o exemplo, exclua todos os grupos de recursos que você criou para parar a cobrança associada.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre aplicativos de banco de dados SaaS multilocatário, consulte [Padrões de design para aplicativos SaaS multilocatários](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]
> * Como implantar o Aplicativo Autônomo SaaS Wingtip Tickets.
> * Sobre os servidores e bancos de dados que constituem o aplicativo.
> * Como excluir recursos de exemplo para interromper a cobrança relacionada.

Você pode explorar como o catálogo é usado para dar suporte a vários cenários entre locatários usando a versão do banco de dados por locatário do [aplicativo SaaS Wingtip Tickets](./saas-dbpertenant-wingtip-app-overview.md).