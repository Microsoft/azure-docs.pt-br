---
title: Provisionar novos locatários em um aplicativo multilocatário que usa o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como provisionar e catalogar novos locatários em um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 803d05e1aaf4d9c26a6132bde30f101ce3905924
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388286"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Saiba como provisionar novos locatários e registrá-los no catálogo

Neste tutorial, você aprenderá a provisionar e catalogar padrões de SaaS. Você também saberá como eles são implementados no aplicativo de banco de dados por locatário Wingtip de Tíquetes SaaS. Você vai criar e inicializar novos bancos de dados de locatário e os registrará no catálogo de locatários do aplicativo. O catálogo é um banco de dados que mantém o mapeamento entre os vários locatários do aplicativo SaaS e seus dados. O catálogo desempenha uma função importante no direcionamento das solicitações de aplicativo e gerenciamento para o banco de dados correto.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> 
> * Provisionar um único locatário novo.
> * Provisione um lote de locatários adicionais.


Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo de banco de dados por locatário SaaS Wingtip Tickets é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo de banco de dados por locatário do Wingtip Tickets SaaS](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter mais informações, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução ao padrão de catálogo de SaaS

Em um aplicativo de SaaS multilocatário com suporte de banco de dados, é importante saber o local em que estão armazenadas as informações de cada locatário. No padrão de catálogo de SaaS, um banco de dados de catálogo é usado para manter o mapeamento entre cada locatário e o banco de dados onde seus dados estão armazenados. Esse padrão se aplica sempre que dados de locatário são distribuídos entre vários bancos de dados.

Cada locatário é identificado por uma chave no catálogo, que é mapeada para o local do seu banco de dados. No aplicativo Wingtip Tickets, a chave é formada por um hash do nome do locatário. Esse esquema permite que o aplicativo crie a chave do nome de locatário incluído na URL do aplicativo. Outros esquemas de chave de locatário podem ser usados.  

O catálogo permite que o nome ou local do banco de dados seja alterado com impacto mínimo sobre o aplicativo. Em um modelo de banco de dados multilocatário, esse recurso também acomoda a 'movimentação' de um locatário entre bancos de dados. O catálogo também pode ser usado para indicar se um locatário ou banco de dados está offline para manutenção ou outras ações. Esse recurso é explorado no [Tutorial de restauração de locatário único](saas-dbpertenant-restore-single-tenant.md).

O catálogo também pode armazenar um locatário adicional ou metadados de banco de dados, como a versão do esquema, o plano de serviço ou os SLAs oferecidos aos locatários. O catálogo pode armazenar outras informações que permitem gerenciamento de aplicativos, suporte ao cliente ou DevOps. 

Além do aplicativo SaaS, o catálogo pode habilitar as ferramentas de banco de dados. No exemplo de banco de dados por locatário SaaS Wingtip Tickets, o catálogo é usado para permitir consultas entre locatários, que é explorada na [tutorial de relatórios Ad hoc](saas-tenancy-cross-tenant-reporting.md). O gerenciamento de trabalhos entre bancos de dados é explorado nos tutoriais [Gerenciamento de esquema](saas-tenancy-schema-management.md) e [Análise de locatários](saas-tenancy-tenant-analytics.md). 

Nos exemplos de Wingtip Tickets SaaS, o catálogo é implementado usando os recursos de Gerenciamento de Fragmentos na [EDCL (Biblioteca de cliente do Banco de Dados Elástico)](sql-database-elastic-database-client-library.md). O EDCL está disponível em Java e em .NET Framework. O EDCL permite que um aplicativo crie, gerencie e use um mapa de fragmentos com backup no banco de dados. 

Um mapa de fragmentos contém uma lista de fragmentos (bancos de dados) e o mapeamento entre chaves (locatários) e fragmentos. Funções de EDCL são usadas durante o provisionamento de locatário para criar as entradas no mapa de fragmento. São usados em tempo de execução por aplicativos para se conectar ao banco de dados correto. O EDCL armazena informações de conexão em cache para minimizar o tráfego ao banco de dados de catálogo e acelerar o aplicativo. 

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis no banco de dados do catálogo, mas *não os edite*. Edite os dados de mapeamento somente com o uso de APIs da Biblioteca de Cliente do Banco de Dados Elástico. Manipular diretamente os dados de mapeamento gera o risco de corrupção do catálogo e não há suporte para isso.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introdução ao padrão de provisionamento de SaaS

Ao adicionar um novo locatário em um aplicativo SaaS que usa um modelo de banco de dados único locatário, você deve provisionar um novo banco de dados de locatário. O banco de dados deve ser criado no local e camada de serviço apropriados. Ele também deve ser inicializado com o esquema e os dados de referência apropriados. E ele deve ser registrado no catálogo sob a chave de locatário apropriada. 

Podem ser usadas diferentes abordagens de provisionamento de banco de dados. Você pode executar scripts SQL, implantar um bacpac ou copiar um banco de dados de modelo. 

O provisionamento de banco de dados deve fazer parte de sua estratégia de gerenciamento de esquema. Você deve verificar se os novos bancos de dados são provisionados com o esquema mais recente. Esse requisito é explorado no [Tutorial de gerenciamento de esquema](saas-tenancy-schema-management.md). 

O aplicativo de banco de dados por locatário Wingtip Tickets provisiona novos locatários copiando um banco de dados modelo chamado _basetenantdb_, que é implantado no servidor de catálogo. O provisionamento pode ser integrado ao aplicativo como parte da experiência de entrada. Ele também pode ter suporte offline com o uso de scripts. Este tutorial explora o provisionamento usando o PowerShell. 

Os scripts de provisionamento copiam o banco de dados _basetenantdb_ para criar um novo banco de dados de locatário em um pool elástico. O banco de dados de locatário é criado no servidor de locatários mapeado para o alias DNS _newtenant_. Esse alias mantém uma referência ao servidor usado para provisionar novos locatários e é atualizado para apontar um servidor de locatário de recuperação nos tutoriais de recuperação de desastre ([DR usando restauração geográfica](saas-dbpertenant-dr-geo-restore.md), [DR usando replicação geográfica](saas-dbpertenant-dr-geo-replication.md)). Os scripts inicializam o banco de dados com informações específicas do locatário e o registram no mapa do fragmento de catálogo. Bancos de dados de locatário são nomes com base no nome do locatário. Esse esquema de nomenclatura não é uma parte crítica do padrão. O catálogo mapeia a chave de locatário para o nome do banco de dados, para que qualquer convenção de nomenclatura possa ser usada. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo de banco de dados por locatário SaaS Wingtip Tickets

O código-fonte do aplicativo e os scripts do SaaS Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Passo a passo detalhado para provisionar e catalogar

Para entender como o aplicativo Wingtip Tickets implementa o novo provisionamento de locatário, adicione um ponto de interrupção e siga o fluxo de trabalho ao provisionar um locatário.

1. No ISE do PowerShell, abra \\...Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ e defina estes parâmetros:

   * **$TenantName** = o nome do novo local do evento (por exemplo, *Bushwillow Blues*).
   * **$VenueType** = um dos tipos predefinidos de local: _blues_, classicalmusic, dance, jazz, judo, motor racing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = **1**, *Provisionar um único locatário*.

2. Para adicionar um ponto de interrupção, coloque o cursor em qualquer lugar na linha que indica *Novo Locatário*. Em seguida, pressione F9.

   ![Ponto de interrupção](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Para executar o script, pressione F5.

4. Depois que a execução do script for interrompida no ponto de interrupção, pressione F11 para entrar no código.

   ![Depurando](media/saas-dbpertenant-provision-and-catalog/debug.png)



Rastreie a execução do script usando as opções do menu **Depurar**. Pressione F10 e F11 para ver ou acessar as funções de chamada. Para saber mais sobre como depurar scripts do PowerShell, confira [Dicas sobre como trabalhar e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Você não precisa seguir explicitamente esse fluxo de trabalho. Explica como depurar o script.

* **Importe o módulo CatalogAndDatabaseManagement.psm1.** Ele fornece um catálogo e abstração de nível de locatário sobre as funções de [Gerenciamento de Fragmento](sql-database-elastic-scale-shard-map-management.md). Esse módulo encapsula a maior parte do padrão do catálogo e vale a pena explorar.
* **Importe o módulo SubscriptionManagement.psm1.** Ele contém funções para entrar no Azure e selecionar a assinatura do Azure com a qual você deseja trabalhar.
* **Obtenha detalhes de configuração.** Intervenha em Get-Configuration usando F11 e veja como a configuração do aplicativo é especificada. Nomes de recurso e outros valores específicos de aplicativo são definidos aqui. Não altere esses valores até que você esteja familiarizado com os scripts.
* **Obtenha o objeto de catálogo.** Intervenha em Get-Catalog, que compõe e retorna um objeto de catálogo usado no script de nível superior. Essa função usa funções de Gerenciamento de Fragmentos importadas do **AzureShardManagement.psm1**. O objeto de catálogo é composto pelos seguintes elementos:

   * O $catalogServerFullyQualifiedName é criado usando o tronco padrão além do seu nome de usuário: _catalog-\<user\>.database.windows.net_.
   * O $catalogDatabaseName é recuperado da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado no banco de dados do catálogo.
   * O objeto $shardMap é inicializado por meio do mapa do fragmentos do _tenantcatalog_ no banco de dados de catálogo. Um objeto de catálogo é composto e retornado. Ele é usado no script de nível superior.
* **Calcule a nova chave de locatário.** Uma função de hash é usada para criar a chave de locatário com base no nome do locatário.
* **Verifique se a chave de locatário já existe.** O catálogo é verificado para garantir que a chave está disponível.
* **O banco de dados do locatário é provisionado com New-TenantDatabase.** Use F11 para ver como o banco de dados é provisionado usando um [modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    O nome do banco de dados é construído com base no nome do locatário para deixar claro qual fragmento pertence a qual locatário. Você também pode usar outras convenções de nomenclatura de banco de dados. Um modelo do Resource Manager cria um banco de dados de locatário com a cópia de um banco de dados modelo _baseTenantDB_ no servidor de catálogo. Como alternativa, você pode criar um banco de dados e inicializá-lo com a importação de um bacpac. Ou você pode executar um script de inicialização de um local conhecido.

    O modelo do Resource Manager está na pasta ...\Módulos de Aprendizado\Comum\: *tenantdatabasecopytemplate.json*

* **O banco de dados do locatário adicional é inicializado.** O nome do local (locatário) e o tipo de local são adicionados. Você também pode fazer outra inicialização aqui.

* **O banco de dados de locatário está registrado no catálogo.** Ele é registrado com *Add-TenantDatabaseToCatalog* usando a chave de locatário. Use F11 para ver os detalhes:

    * O banco de dados de catálogo é adicionado ao mapa de fragmentos (a lista de bancos de dados conhecidos).
    * O mapeamento que vincula o valor da chave ao fragmento é criado.
    * Metadados adicionais sobre o locatário (o nome do local) são adicionados à tabela Locatários no catálogo. A tabela Locatários não faz parte do esquema Shard Management e não é instalada pelo EDCL. Esta tabela ilustra como o banco de dados de catálogo pode ser estendido para dar suporte a dados específicos de aplicativos adicionais.


Após a conclusão do provisionamento, a execução retorna ao script original *Demo-ProvisionAndCatalog*. A página **Eventos** é aberta para o novo locatário no navegador.

   ![Página Eventos](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Provisionar um lote de locatários

Este exercício provisiona um lote com 17 locatários. É recomendável que você provisione esse lote de locatários antes de iniciar outros tutoriais de banco de dados por locatário do Wingtip Tickets SaaS. Há mais do que apenas alguns bancos de dados com os quais trabalhar.

1. No ISE do PowerShell, abra ...\\Módulos de Aprendizado\\Provisionamento e Catálogo\\*Demo-ProvisionAndCatalog.ps1*. Altere o parâmetro *$DemoScenario* para 3:

   * **$DemoScenario** = **3** *Provisionar um lote de locatários*.
2. Para executar o script, pressione F5.

O script implanta um lote de locatários adicionais. Ele usa um [modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla o lote e delega o provisionamento de cada banco de dados a um modelo vinculado. O uso de modelos dessa maneira permite que Azure Resource Manager seja o agente do processo de provisionamento do seu script. Os modelos provisionam bancos de dados em paralelo e, se necessário, controlam as repetições. O script é idempotente e, portanto, se ele falhar ou parar por qualquer motivo, execute-o novamente.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Verificar se o lote de locatários foi implantado com êxito

* No [Portal do Azure](https://portal.azure.com), navegue até sua lista de servidores e abra o servidor *tenants1*. Selecione **Bancos de dados SQL** e verifique se o lote de 17 bancos de dados adicionais está na lista.

   ![Lista de banco de dados](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Outros padrões de provisionamento

Outros padrões de provisionamento não mencionados nesse tutorial:

**Pré-provisionamento de bancos de dados**: O padrão de pré-provisionamento explora o fato de que os bancos de dados de um pool elástico não adicionam custo extra. A cobrança é para o pool elástico, não os bancos de dados. Bancos de dados ociosos não consomem recursos. Com o pré-provisionamento de bancos de dados em um pool e a alocação quando necessário, você pode reduzir o tempo para adicionar os locatários. O número de bancos de dados pré-provisionados pode ser ajustado conforme necessário para manter um buffer adequado para a taxa de provisionamento esperada.

**Provisionamento automático**: No padrão de provisionamento automático, um serviço de provisionamento provisiona servidores, pools e bancos de dados automaticamente, conforme o necessário. Se desejar, você poderá incluir o provisionamento prévio de bancos de dados em pools elásticos. Se os bancos de dados forem encerrados e excluídos, as lacunas nos pools elásticos poderão ser preenchidas pelo serviço de provisionamento. Esse serviço pode ser simples ou complexo, como o tratamento de provisionamento em várias regiões geográficas e a configuração de replicação geográfica para recuperação de desastre. 

Com o padrão de provisionamento automático, um script ou aplicativo cliente envia uma solicitação de provisionamento a uma fila para ser processada pelo serviço de provisionamento. Em seguida, ele sonda o serviço para determinar a conclusão. Se o provisionamento prévio for usado, as solicitações serão manipuladas rapidamente. O serviço provisiona um banco de dados de substituição em segundo plano.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> 
> * Provisionar um único locatário novo.
> * Provisione um lote de locatários adicionais.
> * Intervir nos detalhes do provisionamento de locatários e do registro deles no catálogo.

Experimente o [Tutorial de monitoramento de desempenho](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md)
* [Depurar scripts no ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
