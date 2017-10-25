---
title: Failover regional no Azure Cosmos DB | Microsoft Docs
description: "Saiba mais sobre como o failover manual e automáticos funcionam com o Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d8ba08bc9f99cb77c9f03949fc5db299eb222c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Failover regional automático para a continuidade dos negócios no Azure Cosmos DB
O Azure Cosmos DB simplifica a distribuição global de dados oferecendo [contas de banco de dados em várias regiões](distribute-data-globally.md) totalmente gerenciadas que fornecem claras compensações entre consistência, disponibilidade e desempenho, tudo com garantias correspondentes. As contas do Cosmos DB oferecem alta disponibilidade, latências de milissegundos de digito único, [níveis bem definidos de consistência](consistency-levels.md), failover regional transparente com APIs de hospedagem múltipla e a capacidade de dimensionar de forma elástica a produtividade e o armazenamento no mundo todo. 

O Cosmos DB dá suporte tanto a failovers explícitos quanto àqueles controlados por política, que permitem controlar o comportamento do sistema de ponta a ponta em caso de falhas. Neste artigo, veremos:

* Como funcionam os failovers manuais no Cosmos DB?
* Como funcionam os failovers automáticos no Cosmos DB e o que acontece quando um data center fica indisponível?
* Como você pode usar failovers manuais em arquiteturas de aplicativo?

Saiba mais sobre failovers regionais nesse vídeo do Azure Friday com Scott Hanselman e o gerente de engenharia Karthik Raman.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a id="ConfigureMultiRegionApplications"></a>Configurando aplicativos de várias regiões
Antes de nos aprofundarmos nos modos de failover, examinaremos como você pode configurar um aplicativo para aproveitar a disponibilidade de várias regiões e ser resiliente diante de failovers regionais.

* Primeiro, implante seu aplicativo em várias regiões
* Para garantir o acesso de baixa latência de todas as regiões nas quais seu aplicativo é implantado, configure a [lista de regiões preferenciais](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) correspondente para cada região por meio de um dos SDKs com suporte.

O trecho a seguir mostra como inicializar um aplicativo de várias regiões. Aqui, a conta do Azure Cosmos DB `contoso.documents.azure.com` está configurada com duas regiões – Oeste dos EUA e Europa Setentrional. 

* O aplicativo é implantado na região Oeste dos EUA (usando os Serviços de Aplicativos do Azure, por exemplo) 
* Configurado com `West US` como a primeira região preferencial para leituras de baixa latência
* Configurado com `North Europe` como a segunda região preferencial (para alta disponibilidade durante falhas regionais)

Na API do DocumentDB, essa configuração é parecida com o seguinte trecho:

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "memf7qfF89n6KL9vcb7rIQl6tfgZsRt5gY5dh3BIjesarJanYIcg2Edn9uPOUIVwgkAugOb2zUdCR2h0PTtMrA==",
    usConnectionPolicy);
```

O aplicativo também é implantado na região da Europa Setentrional, com a ordem das regiões preferenciais invertida. Ou seja, a região da Europa Setentrional é especificada primeiro para leituras de baixa latência. Em seguida, a região Oeste dos EUA é especificada como a segunda região preferencial para alta disponibilidade durante falhas regionais.

O diagrama de arquitetura a seguir mostra uma implantação de aplicativo de várias regiões em que o Cosmos DB e o aplicativo estão configurados para estarem disponíveis em quatro regiões geográficas do Azure.  

![Implantação de aplicativos globalmente distribuídos com o Azure Cosmos DB](./media/regional-failover/app-deployment.png)

Agora, vejamos como o serviço Cosmos DB lida com falhas regionais por meio de failovers automáticos. 

## <a id="AutomaticFailovers"></a>Failovers Automáticos
No caso raro de uma interrupção regional do Azure ou falha do data center, o Cosmos DB dispara automaticamente failovers de todas as contas do Cosmos DB com uma presença na região afetada. 

**O que acontece se uma região de leitura sofre uma interrupção?**

As contas do Cosmos DB com uma região de leitura em uma das regiões afetadas são automaticamente desconectadas de sua região de gravação e marcadas como offline. Os SDKs do Cosmos DB implementam um protocolo de descoberta regional que permite que eles detectem automaticamente quando uma região está disponível e redirecionem chamadas de leitura para a próxima região disponível na lista de regiões preferenciais. Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual. Nenhuma alteração é necessária no código do aplicativo para tratar de failovers regionais. Durante todo esse processo, as garantias de consistência continuam sendo cumpridas pelo Cosmos DB.

![Falhas de região de leitura no Azure Cosmos DB](./media/regional-failover/read-region-failures.png)

Depois que a região afetada se recupera da interrupção, todas as contas do Cosmos DB afetadas na região são recuperadas automaticamente pelo serviço. Depois, as contas do Cosmos DB que tinham uma região de leitura na região afetada serão sincronizadas automaticamente com a região de gravação atual e passarão a ficar online. Os SDKs do Cosmos DB descobrem a disponibilidade da nova região e avaliam se ela deve ser selecionada como a região de leitura atual com base na lista de regiões preferenciais configurada pelo aplicativo. Leituras subsequentes são redirecionadas para a região recuperada sem exigir nenhuma alteração ao código do aplicativo.

**O que acontece se uma região de gravação sofre uma interrupção?**

Se a região afetada for a região de gravação atual de determinada conta do Cosmos DB, essa região será marcada como offline automaticamente. Em seguida, uma região alternativa será promovida como a região de gravação de cada conta afetada do Cosmos DB. Você pode controlar por completo a ordem de seleção de região de suas contas do Cosmos DB por meio do portal do Azure ou de [forma programática](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Prioridades de failover para o Azure Cosmos DB](./media/regional-failover/failover-priorities.png)

Durante failovers automáticos, o Cosmos DB escolhe automaticamente a próxima região de gravação para determinada conta do Azure Cosmos DB com base na ordem de prioridade especificada. 

![Falhas de região de gravação no Azure Cosmos DB](./media/regional-failover/write-region-failures.png)

Depois que a região afetada se recupera da interrupção, todas as contas do Cosmos DB afetadas na região são recuperadas automaticamente pelo serviço. 

* As contas do Cosmos DB com sua região de gravação anterior na área afetada permanecerão em um modo offline com disponibilidade de leitura mesmo após a recuperação da região. 
* Você pode consultar essa região para computar quaisquer gravações não replicadas durante a interrupção por meio da comparação dos dados presentes nela com os dados disponíveis na região de gravação atual. Com base nas necessidades de seu aplicativo, você pode executar a mesclagem e/ou resolução de conflitos e gravar o conjunto final de alterações de volta na região de gravação atual. 
* Depois de concluir as alterações de mesclagem, você poderá colocar a região afetada online novamente, removendo e adicionando a região novamente à sua conta do Cosmos DB. Quando a região é adicionada novamente, você pode configurá-lo como a região de gravação, executando um failover manual por meio do Portal do Azure ou [programaticamente](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

## <a id="ManualFailovers"></a>Failovers Manuais

Além de failovers automáticos, a região de gravação atual de determinada conta do Cosmos DB pode ser alterada manualmente de modo dinâmico para uma das regiões de leitura existentes. Failovers manuais podem ser iniciados por meio do Portal do Azure ou [programaticamente](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Os failovers manuais garantem **zero perda de dados** e **zero perda de disponibilidade** e, na conta do Cosmos DB especificada, transferem normalmente o status de gravação da região de gravação antiga para a nova. Assim como ocorre em failovers automáticos, o SDK do Cosmos DB manipula automaticamente as alterações de região de gravação durante failovers manuais e garante que as chamadas são redirecionadas automaticamente para a nova região de gravação. Nenhuma alteração de configuração ou código é necessária em seu aplicativo para gerenciar failovers. 

![Failovers manuais no Azure Cosmos DB](./media/regional-failover/manual-failovers.png)

Estes são alguns dos cenários comuns em que o failover manual pode ser útil:

**Modelo siga o relógio**: se os aplicativos tiverem padrões de tráfego previsíveis com base na hora do dia, você poderá alterar periodicamente o status de gravação para a região geográfica mais ativa com base na hora do dia.

**Atualização de serviço**: determinada implantação de aplicativos distribuídos globalmente pode envolver o redirecionamento de tráfego para uma região diferente por meio do Gerenciador de Tráfego durante sua atualização de serviço planejada. Essa implantação de aplicativo agora pode usar o failover manual para manter o status de gravação para a região em que haverá tráfego ativo durante a janela de tempo da atualização de serviço.

**Simulações de BCDR (continuidade dos negócios e recuperação de desastre) e HADR (Alta disponibilidade e recuperação de desastres)**: a maioria dos aplicativos empresariais incluem testes de continuidade de negócios como parte do seu processo de desenvolvimento e lançamento. Testes de BCDR e HADR geralmente são uma etapa importante no que se refere a certificações de conformidade e garantia da disponibilidade do serviço no caso de interrupções regionais. Teste a prontidão para BCDR de seus aplicativos que usam o Cosmos DB para armazenamento disparando um failover manual de sua conta do Cosmos DB e/ou adicionando e removendo uma região de forma dinâmica.

Neste artigo, examinamos como os failovers manuais e automáticos funcionam no Cosmos DB e como você pode configurar suas contas e seus aplicativos do Cosmos DB para estarem disponíveis globalmente. Usando o suporte de replicação global do Cosmos DB, melhore a latência de ponta a ponta e garanta que eles estão altamente disponíveis, até mesmo no caso de falhas regionais. 

## <a id="NextSteps"></a>Próximas etapas
* Saiba mais sobre como o Cosmos DB dá suporte à [distribuição global](distribute-data-globally.md)
* Saiba mais sobre [consistência global com o Azure Cosmos DB](consistency-levels.md)
* Desenvolver com várias regiões usando o [API do DocumentDB](../cosmos-db/tutorial-global-distribution-documentdb.md) do Azure Cosmos DB
* Aprenda a criar [arquiteturas de gravador de várias regiões](multi-region-writers.md) com o Azure DocumentDB

