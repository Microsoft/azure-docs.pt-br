---
title: "Provisionar novos locatários em um aplicativo multilocatário que usa o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba como provisionar e catalogar novos locatários em um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure"
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
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: febf9d9f8a32a95ae61734cc2d3632bb74f6c8f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="learn-how-to-provision-and-catalog-new-tenants-and-register-them-in-the-catalog"></a>Saiba como provisionar e catalogar novos locatários e registrá-los no catálogo

Neste tutorial, você aprenderá sobre os padrões de provisionamento e catálogo de SaaS, e como eles são implementados no aplicativo SaaS Wingtip. Você vai criar e inicializar novos bancos de dados de locatário e os registrará no catálogo de locatários do aplicativo. O catálogo é um banco de dados que mantém o mapeamento entre os vários locatários do aplicativo SaaS e seus dados. O catálogo desempenha uma função importante no direcionamento das solicitações de aplicativo para o banco de dados correto.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]

> * Provisionar um novo locatário individual, o que inclui como isso é implementado
> * Provisionar um lote de locatários adicionais


Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo SaaS Wingtip é implantado. Para implantar em menos de cinco minutos, confira [Implantar e explorar o aplicativo de SaaS do Wingtip](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução ao padrão de catálogo de SaaS

Em um aplicativo de SaaS multilocatário com suporte de banco de dados, é importante saber o local em que estão armazenadas as informações de cada locatário. No padrão de catálogo de SaaS, um banco de dados de catálogo é usado para manter o mapeamento entre cada locatário e o local onde seus dados estão armazenados. O aplicativo SaaS Wingtip usa uma arquitetura de locatário único por banco de dados, mas o padrão básico de armazenamento do mapeamento de locatário para banco de dados em um catálogo aplica-se tanto ao uso de um banco de dados multilocatário quanto ao de locatário único.

Cada locatário recebe uma chave que o identifica no catálogo, e que é mapeada até o local do banco de dados apropriado. No aplicativo SaaS Wingtip, a chave é formada por um hash do nome do locatário. Isso permite que a parte do nome do locatário da URL do aplicativo seja usada para construir a chave. Outros esquemas de chave de locatário podem ser usados.  

O catálogo permite que o nome ou local do banco de dados seja alterado com impacto mínimo sobre o aplicativo.  Em um modelo de banco de dados multilocatário, isso também acomoda a 'movimentação' de um locatário entre bancos de dados.  O catálogo também pode ser usado para indicar se um locatário ou banco de dados está offline para manutenção ou outras ações. Isso é explorado no [tutorial de restauração de locatário único](sql-database-saas-tutorial-restore-single-tenant.md).

Além disso, o catálogo, que é, na verdade, um banco de dados de gerenciamento de um aplicativo SaaS, pode armazenar metadados adicionais de locatário ou de banco de dados, como a camada ou edição de um banco de dados, versão do esquema, plano de serviço ou SLAs oferecidos aos locatários, e outras informações que permitem o gerenciamento de aplicativos, atendimento ao cliente ou processos de devOps.  

Além do aplicativo SaaS, o catálogo pode habilitar as ferramentas de banco de dados.  No exemplo de SaaS Wingtip, o catálogo é usado para permitir consultas entre locatários, exploradas no [tutorial de análise ad hoc](sql-database-saas-tutorial-adhoc-analytics.md). O gerenciamento de trabalhos entre bancos de dados é explorado nos tutoriais [gerenciamento de esquema](sql-database-saas-tutorial-schema-management.md) e [análise de locatários](sql-database-saas-tutorial-tenant-analytics.md). 

No aplicativo SaaS Wingtip, o catálogo é implementado usando os recursos de Gerenciamento de Fragmentos na [EDCL (Biblioteca de Cliente do Banco de Dados Elástico)](sql-database-elastic-database-client-library.md). O EDCL permite que um aplicativo crie, gerencie e use um mapa de fragmentos com backup no banco de dados. Um mapa de fragmentos contém uma lista de fragmentos (bancos de dados) e o mapeamento entre chaves (locatários) e bancos de dados.  As funções de EDCL podem ser usadas em aplicativos ou scripts do PowerShell durante o provisionamento de locatários, a fim de criar as entradas no mapa de fragmentos, e em aplicativos para conexão eficiente ao banco de dados correto. O EDCL armazena informações de conexão em cache para minimizar o tráfego ao banco de dados de catálogo e acelerar o aplicativo.  

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis no banco de dados de catálogo, mas *não os edite*! Edite os dados de mapeamento somente com o uso de APIs da Biblioteca de Cliente do Banco de Dados Elástico. Manipular diretamente os dados de mapeamento gera o risco de corrupção do catálogo e não há suporte para isso.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introdução ao padrão de Provisionamento de SaaS

Ao integrar um novo locatário em um aplicativo SaaS que usa um modelo de banco de dados de locatário único, é necessário provisionar um novo banco de dados de locatário.  Ele deve ser criado no local e na camada de serviço apropriados, inicializado com o esquema e os dados de referência apropriados e, em seguida, registrado no catálogo sob a chave de locatário apropriada.  

Abordagens diferentes podem ser usadas no provisionamento de banco de dados, o que pode incluir a execução de scripts SQL, a implantação de um bacpac ou a cópia de um banco de dados de modelo 'final'.  

A abordagem de provisionamento usada deve ser compreendida em sua estratégia geral de gerenciamento de esquema, que deve garantir que novos bancos de dados sejam provisionados com o esquema mais recente.  Isso é explorado no [tutorial de gerenciamento de esquema](sql-database-saas-tutorial-schema-management.md).  

Os aplicativo SaaS Wingtip provisiona novos locatários copiando um banco de dados final chamado basetenantdb, implantado no servidor de catálogo.  O provisionamento pode ser integrado ao aplicativo como parte de uma experiência de inscrição, e/ou com suporte offline por meio do uso de scripts. Este tutorial explorará o provisionamento usando o PowerShell. Os scripts de provisionamento copiam basetenantdb para criar um novo banco de dados de locatário em um pool elástico e, depois, o inicializam com informações específicas do locatário e o registram no mapa de fragmentos do catálogo.  No aplicativo de exemplo, os bancos de dados recebem nomes com base no nome do locatário, mas isso não é uma parte crítica do padrão, o uso do catálogo permite que qualquer nome seja atribuído ao banco de dados.+ 


## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts de SaaS do Wingtip e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Etapas para baixar os scripts do SaaS Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).


## <a name="provision-and-catalog-detailed-walkthrough"></a>Passo a passo detalhado para provisionar e catalogar

Para entender como o aplicativo Wingtip implementa o novo provisionamento de locatário, adicione um ponto de interrupção e percorra o fluxo de trabalho durante o provisionamento de um locatário:

1. Abra ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ e defina estes parâmetros:
   * **$TenantName** = o nome do novo local do evento (por exemplo, *Bushwillow Blues*).
   * **$VenueType** = um dos tipos predefinidos de local: *blues*, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = **1**, defina como **1** para *Provisionar um único locatário*.

1. Adicione um ponto de interrupção, colocando o cursor em qualquer local na linha 48, que diz: *New-Tenant `* e pressione **F9**.

   ![ponto de interrupção](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Para executar o script, pressione **F5**.

1. Depois que a execução do script for interrompida no ponto de interrupção, pressione **F11** para entrar no código.

   ![ponto de interrupção](media/sql-database-saas-tutorial-provision-and-catalog/debug.png)



Rastreie a execução do script usando as opções de menu **Depurar** – **F10** e **F11** para passar por cima ou intervir nas funções chamadas. Para saber mais sobre como depurar scripts do PowerShell, confira [Dicas sobre como trabalhar e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


As etapas a seguir não devem ser explicitamente seguidas porque são uma explicação do fluxo de trabalho pelo qual você passará ao depurar o script:

1. **Importar o módulo SubscriptionManagement.psm1** que contém funções para entrar no Azure e selecionar a assinatura do Azure com a qual você está trabalhando.
1. **Importar o módulo CatalogAndDatabaseManagement.psm1** que fornece um catálogo e uma abstração em nível de locatário em relação às funções de [Gerenciamento de Fragmentos](sql-database-elastic-scale-shard-map-management.md). Este é um módulo importante que encapsula a maior parte do padrão do catálogo e vale a pena explorar.
1. **Obter detalhes de configuração**. Intervenha em Get-Configuration (com F11) e veja como a configuração do aplicativo é especificada. Os nomes de recursos e outros valores específicos do aplicativo são definidos aqui, mas não altere nenhum desses valores até que você esteja familiarizado com os scripts.
1. **Obter o objeto de catálogo**. Intervenha em Get-Catalog que compõe e retorna um objeto de catálogo usado no script de nível superior.  Essa função usa funções de Gerenciamento de Fragmentos importadas do **AzureShardManagement.psm1**. O objeto de catálogo é composto pelo seguinte:
   * O $catalogServerFullyQualifiedName é criado usando o tronco padrão além do seu nome de Usuário: _catalog-\<usuário\>.database.windows.net_.
   * O $catalogDatabaseName é recuperado da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado no banco de dados do catálogo.
   * O objeto $shardMap é inicializado por meio do mapa do fragmentos do *tenantcatalog* no banco de dados de catálogo.
   Um objeto de catálogo é composto e retornado e usado no script de nível superior.
1. **Calcular a nova chave de locatário**. Uma função de hash é usada para criar a chave de locatário com base no nome do locatário.
1. **Verificar se a chave de locatário já existe**. O catálogo é verificado para garantir que a chave está disponível.
1. **O banco de dados do locatário é provisionado com New-TenantDatabase.** Use **F11** para intervir e ver como o banco de dados é provisionado usando um [modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

O nome do banco de dados é construído com base no nome do locatário para deixar claro qual fragmento pertence a qual locatário. (Outras estratégias para nomenclatura de banco de dados podem ser usadas com facilidade.)+ Um modelo do Resource Manager é usado para criar um banco de dados de locatário com a cópia de um banco de dados final (baseTenantDB) no servidor de catálogo. Uma abordagem alternativa seria criar um banco de dados vazio e, em seguida, inicializá-lo com a importação de um bacpac, ou executar um script de inicialização de um local bem conhecido.  

O modelo do Resource Manager está na pasta ...\Módulos de Aprendizado\Comum\: *tenantdatabasecopytemplate.json*

Depois que o banco de dados do locatário é criado, ele é, em seguida, **inicializado com o nome do local (locatário) e o tipo de local**. Outra inicialização também poderia ser feita aqui.

O **banco de dados de locatário é registrado no catálogo** com *Add-TenantDatabaseToCatalog* usando a chave do locatário. Use **F11** para ver os detalhes:

* O banco de dados de catálogo é adicionado ao mapa de fragmentos (a lista de bancos de dados conhecidos).
* O mapeamento que vincula o valor da chave ao fragmento é criado.
* Metadados adicionais (o nome do local) sobre o locatário são adicionados à tabela Locatários no catálogo.  A tabela Locatários não faz parte do esquema ShardManagement e não é instalada pelo EDCL.  Esta tabela ilustra como o banco de dados Catálogo pode ser estendido para dar suporte a dados específicos de aplicativos adicionais.   


Depois que o provisionamento for concluído, a execução retornará ao script original *Demo-ProvisionAndCatalog*, que abre a página **Eventos** do novo locatário no navegador:

   ![events](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Provisionar um lote de locatários

Este exercício provisiona um lote com 17 locatários. É recomendável provisionar esse lote de locatários antes de iniciar os outros tutoriais de SaaS do Wingtip, de forma que haja mais do que apenas alguns bancos de dados com os quais trabalhar.

1. Abra ...\\Módulos de Aprendizado\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* no *ISE do PowerShell* e altere o parâmetro *$DemoScenario* para 3:
   * **$DemoScenario** = **3**, altere para **3** para *Provisionar um lote de locatários*.
1. Pressione **F5** e execute o script.

O script implanta um lote de locatários adicionais. Ele usa um [modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla o lote e, em seguida, delega o provisionamento de cada banco de dados a um modelo vinculado. O uso de modelos dessa maneira permite que Azure Resource Manager seja o agente do processo de provisionamento do seu script. Os modelos provisionam bancos de dados em paralelo nos locais em que isso for possível e lidam com repetições, se necessário, otimizando o processo geral. O script é idempotente; portanto, se ele falhar ou parar por qualquer motivo, execute-o novamente.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verificar se o lote de locatários foi implantado com êxito

* Abra o servidor *tenants1* navegando até sua lista de servidores no [Portal do Azure](https://portal.azure.com), clique em **Bancos de Dados SQL** e verifique se o lote de 17 bancos de dados adicionais está na lista:

   ![lista de banco de dados](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Outros padrões de provisionamento

Outros padrões de provisionamento não mencionados nesse tutorial incluem:

**Pré-provisionamento de bancos de dados.** O padrão de pré-provisionamento explora o fato de que os bancos de dados de um pool elástico não adicionam custo extra. A cobrança é pelo pool elástico, não pelos bancos de dados e os bancos de dados ociosos não consomem nenhum recurso. Ao pré-provisionar bancos de dados em um pool e alocá-los quando necessário, o tempo de integração do locatário poderá ser reduzido consideravelmente. O número de bancos de dados pré-provisionados pode ser ajustado conforme necessário para manter um buffer adequado para a taxa de provisionamento esperada.

**Provisionamento automático.** No padrão de provisionamento automático, um serviço de provisionamento dedicado é usado para provisionar servidores, pools e bancos de dados automaticamente, conforme necessário – incluindo o pré-provisionamento de bancos de dados em pools elásticos, se desejado. E se os bancos de dados forem encerrados e excluídos, as lacunas nos pools elásticos poderão ser preenchidas pelo serviço de provisionamento conforme desejado. Esse serviço poderá ser simples ou complexo – por exemplo, manipulação do provisionamento em várias áreas geográficas e poderá configurar a replicação geográfica automaticamente, caso essa estratégia esteja sendo usada para a recuperação de desastre. Com o padrão de provisionamento automático, um script ou aplicativo cliente poderia enviar uma solicitação de provisionamento para uma fila para ser processada pelo serviço de provisionamento e, em seguida, pesquisaria o serviço para determinar a conclusão. Se o pré-provisionamento fosse usado, as solicitações seriam manipuladas rapidamente com o serviço gerenciando o provisionamento de um banco de dados de substituição em execução em segundo plano.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]

> * Provisionar um novo único locatário
> * Provisionar um lote de locatários adicionais
> * Intervir nos detalhes do provisionamento de locatários e do registro deles no catálogo

Experimente o [Tutorial de monitoramento de desempenho](sql-database-saas-tutorial-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam o aplicativo de SaaS do Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de cliente do banco de dados elástico](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Como depurar scripts no ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
