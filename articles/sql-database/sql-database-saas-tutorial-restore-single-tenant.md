---
title: "Restaurar um banco de dados SQL do Azure em um aplicativo SaaS multilocatário | Microsoft Docs"
description: "Saiba como restaurar um banco de dados do SQL de locatário único após a exclusão acidental de dados"
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
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: 792476849e796695dde3f2ec80b56431a17e8fc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>Restaurar um banco de dados SQL do Azure de locatário único em um aplicativo SaaS multilocatário

O aplicativo Wingtip Tickets SaaS é criado usando um modelo de banco de dados por locatário, onde cada locatário possui o seu próprio banco de dados. Uma das vantagens deste modelo é que com ele é fácil restaurar dados de um único locatário isoladamente sem afetar outros locatários.

Neste tutorial, você aprenderá dois padrões de recuperação de dados:

> [!div class="checklist"]

> * Restaurar um banco de dados em um banco de dados paralelo (lado a lado)
> * Restaurar um banco de dados no local


|||
|:--|:--|
| **Restaurar o locatário para um ponto anterior no tempo, em um banco de dados paralelo** | Esse padrão pode ser usado pelo locatário para análise, auditoria, conformidade, etc. O banco de dados original permanece online e inalterado. |
| **Restaurar locatário no local** | Esse padrão geralmente é usado para recuperar um locatário para um ponto anterior no tempo, depois que um locatário exclui dados acidentalmente. O banco de dados original é colocado off-line e substituído pelo banco de dados restaurado. |
|||

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo SaaS Wingtip é implantado. Para implantar em menos de cinco minutos, confira [Implantar e explorar o aplicativo de SaaS do Wingtip](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>Introdução ao padrão de restauração de locatário de SaaS

Para o padrão de restauração de locatário, há dois padrões simples para restaurar dados de um locatário individual. Como os bancos de dados de locatário são isolados uns dos outros, restaurar um locatário não afeta os dados de outro locatário.

No primeiro padrão, os dados são restaurados para um novo banco de dados. O locatário, em seguida, recebe acesso ao banco de dados juntamente com os dados de produção. Esse padrão permite que um administrador de locatário analise os dados restaurados e use-os para substituir seletivamente os valores dos dados atuais. Cabe ao designer do aplicativo SaaS determinar o nível de sofisticação das opções de recuperação de dados. Simplesmente ser capaz de analisar os dados no estado em que estes estavam em um determinado ponto no tempo pode ser o necessário em alguns cenários. Se o banco de dados usa a [Replicação geográfica](sql-database-geo-replication-overview.md), é recomendável copiar os dados necessários da cópia restaurada para o banco de dados original. Se substituir o banco de dados original pelo banco de dados restaurado, reconfigure e ressincronize a replicação geográfica.

No segundo padrão, que assume que o locatário sofreu uma perda ou corrupção de dados, o banco de dados de produção do locatário é restaurado para um ponto anterior no tempo. No padrão de restauração no local, o locatário é colocado offline por um curto período de tempo enquanto o banco de dados é restaurado e colocado online novamente. O banco de dados original é excluído, mas ainda pode ser restaurado se você precisar voltar para um ponto mais anterior ainda no tempo. Uma variação desse padrão pode renomear o banco de dados em vez de excluí-lo, embora renomear o banco de dados não ofereça nenhuma vantagem adicional em termos de segurança de dados.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts de SaaS do Wingtip e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Etapas para baixar os scripts do SaaS Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simular um locatário excluindo acidentalmente os dados

Para demonstrar esses cenários de recuperação, precisamos *acidentalmente* excluir alguns dados em um dos bancos de dados de locatário. Apesar de você pode excluir qualquer registro, a próxima etapa configura a demonstração para não ser bloqueada por violações de integridade referencial! Ela também adiciona alguns dados de compra de ingresso que você pode usar posteriormente nos *tutoriais de análise de Wingtip SaaS*.

Execute o script gerador de ingresso e crie dados adicionais. O gerador de ingresso intencionalmente não compra ingressos para o último evento de cada locatário.

1. Abra ...\\Módulos de aprendizado\\Utilitários\\*Demo-TicketGenerator.ps1* no *ISE do PowerShell*
1. Pressione **F5** para executar o script e gerar clientes e dados de vendas de ingresso.


### <a name="open-the-events-app-to-review-the-current-events"></a>Abra o aplicativo de eventos para analisar os eventos atuais

1. Abra o *Hub de eventos* (http://events.wtp.&lt;user&gt;.trafficmanager.net) e clique em **Sala de concerto Contoso**:

   ![hub de eventos](media/sql-database-saas-tutorial-restore-single-tenant/events-hub.png)

1. Role a lista de eventos e anote o último evento na lista:

   ![último evento](media/sql-database-saas-tutorial-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>Execute o cenário de demonstração para excluir acidentalmente o último evento

1. Abra ... \\Módulos de aprendizado\\Continuidade de negócios e recuperação de desastres\\RestoreTenant\\*Demo-RestoreTenant.ps1* no *PowerShell ISE*e defina o seguinte valor:
   * **$DemoScenario** = **1**, definida como **1** - Excluir eventos sem vendas de ingressos.
1. Pressione **F5** para executar o script e excluir o último evento. Você deve ver uma mensagem de confirmação semelhante a esta:

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. A página de eventos da Contoso abre. Role para baixo e verifique se o evento não existe mais. Se o evento ainda na lista, clique em atualizar e verifique se ele não existe mais.

   ![último evento](media/sql-database-saas-tutorial-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restaurar um banco de dados do locatário em paralelo com o banco de dados de produção

Este exercício restaura o banco de dados de sala de concertos Contoso para um ponto antes da exclusão do evento. Depois que o evento é excluído nas etapas anteriores, você deseja recuperar e ver os dados excluídos. Não é necessário restaurar o banco de dados de produção com o registro excluído, mas você precisa recuperar o banco de dados antigo para acessar os dados antigos por outros motivos comerciais.

 O script *Restore-TenantInParallel.ps1* cria um banco de dados de locatário paralelo e uma entrada no catálogo paralelo ambos chamados *ContosoConcertHall\_old*. Esse padrão de restauração é mais adequado para a recuperação de uma perda de dados pequena ou para cenários de recuperação de conformidade e auditoria. Também é a abordagem recomendada quando você estiver usando [replicação geográfica](sql-database-geo-replication-overview.md).

1. Conclua a seção [simular um usuário excluindo dados acidentalmente](#simulate-a-tenant-accidentally-deleting-data).
1. Abra ... \\Módulos de aprendizado\\Continuidade de negócios e recuperação de desastres\\RestoreTenant\\_Demo-RestoreTenant.ps1_ no *PowerShell ISE*.
1. Configure **$DemoScenario** = **2**, defina-o como **2** para *Restauração de locatário em paralelo*.
1. Pressione **F5** para executar o script.

O script restaura o banco de dados do locatário (para um banco de dados paralelo) para um ponto no tempo antes de excluir o evento na seção anterior. Ele cria um segundo banco de dados, remove os metadados de catálogo existentes que existe neste banco de dados e adiciona o banco de dados ao catálogo na entrada *ContosoConcertHall\_old*.

O script de demonstração abre a página de eventos no seu navegador. Nota da URL: ```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` que isso está mostrando os dados do banco de dados restaurado onde *_old* é adicionado ao nome.

Role os eventos listados no navegador para confirmar se o evento excluído na seção anterior foi restaurado.

Observe que expor o locatário restaurado como um locatário adicional, com o seu próprio aplicativo de eventos para procurar ingressos, é improvável ser como você forneceria um acesso de locatário a dados restaurarados, mas serve para ilustrar facilmente o padrão de restauração.

Na realidade, você provavelmente manteria esse banco de dados restaurado somente por um período definido. Você pode excluir a entrada de locatário restaurado depois que terminar chamando o script *Remove-RestoredTenant.ps1*.

1. Definir **$DemoScenario** para **4** para selecionar o cenário de *remover restaurado locatário*.
1. **Execute** **usando** **F5**
1. A entrada *ContosoConcertHall\_old* agora foi excluída do catálogo. Continue e feche a página de eventos para este locatário no seu navegador.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restaurar um locatário no local, substituindo o banco de dados existente do locatário

Este exercício restaura o locatário de sala de concertos Contoso para um ponto antes da exclusão do evento. O script *Restore-TenantInPlace* restaura o banco de dados do locatário atual para um novo banco de dados apontando para um ponto anterior no tempo e exclui o banco de dados original. Esse padrão de restauração é mais adequado para a recuperação de uma corrupção de dados grave, pois pode haver perda significativa de dados que o locatário precisa acomodar.

1. Abra o arquivo **Demo-RestoreTenant.ps1** no ISE do PowerShell
1. Definir **$DemoScenario** para **5** para selecionar o *cenário de restaurar o locatário no local*.
1. Execute usando **F5**.

O script restaura o banco de dados do locatário para um ponto no tempo cinco minutos antes da exclusão do evento. Ele faz isso colocando primeiro o locatário de sala de concertos Contoso off-line para que não haja atualizações dos dados. Em seguida, um banco de dados paralelo é criado pela restauração do ponto de restauração e nomeado com um carimbo de data/hora para garantir que o nome do banco de dados não está em conflito com o nome de banco de dados do locatário existente. Em seguida, o banco de dados do locatário antigo é excluído, e o banco de dados restaurado é renomeado para o nome do banco de dados original. Por fim, a sala de concertos Contoso é colocada online para permitir o acesso do aplicativo ao banco de dados restaurado.

Você restaurou com êxito o banco de dados para um ponto no tempo antes da exclusão do evento. A página de eventos abre, portanto confirme se o último evento foi restaurado.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]

> * Restaurar um banco de dados em um banco de dados paralelo (lado a lado)
> * Restaurar um banco de dados no local

[Gerenciar o esquema de banco de dados do locatário](sql-database-saas-tutorial-schema-management.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam o aplicativo de SaaS do Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Visão geral da continuidade dos negócios com o Banco de Dados SQL do Azure](sql-database-business-continuity.md)
* [Saiba mais sobre o Banco de Dados SQL](sql-database-automated-backups.md)
