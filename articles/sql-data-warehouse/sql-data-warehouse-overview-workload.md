---
title: "Saiba mais sobre as operações do Azure SQL Data Warehouse | Microsoft Docs"
description: "A elasticidade do SQL Data Warehouse permite expandir, reduzir ou pausar o poder da computação usando uma escala deslizante de DWUs (Unidades de Data Warehouse). Este artigo explica as métricas de data warehouse e como elas se relacionam às DWUs. "
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: cadffa9c-589d-4db7-888a-1f202a753bc5
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 629ce22bf669a760d041bbd006b836d2da5d237b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-warehouse-workload"></a>Carga de trabalho do data warehouse
Uma carga de trabalho do data warehouse refere-se a todas as operações que são realizadas em um data warehouse. A carga de trabalho em um data warehouse abrange todo o processo de carregamento de dados, a execução de análise e relatórios, o gerenciamento de dados e a exportação de dados. O detalhamento e a amplitude desses componentes, muitas vezes, são proporcionais ao nível de maturidade do data warehouse.

## <a name="new-to-data-warehousing"></a>Novo no data warehouse?
Um data warehouse é uma coleção de dados que é carregada de uma ou mais fontes de dados e é usada para executar tarefas de business intelligence, como análise de dados e relatórios.

Os data warehouses são caracterizados por consultas que examinam grandes quantidades de linhas, grandes intervalos de dados e podem retornar resultados relativamente grandes para fins de análise e relatório. Os data warehouses também são caracterizados pelos carregamentos de dados relativamente grandes, em oposição a inserções/atualizações/exclusões pequenas no nível de transação.

* Um data warehouse funciona melhor quando os dados são armazenados de forma que otimizem as consultas que precisam examinar grandes números de linhas ou grandes intervalos de dados. Esse tipo de exame funciona melhor quando os dados são armazenados e pesquisados por colunas, e não por linhas.

> [!NOTE]
> O índice columnstore na memória, que usa o armazenamento de coluna, fornece até 10 vezes mais ganhos de compactação e 100 vezes mais ganhos de desempenho de consulta pelas árvores binárias tradicionais para consultas de análise e relatório. Consideramos os índices columnstore como o padrão para armazenamento e exame de dados volumosos em um data warehouse.
> 
> 

* Os requisitos de um data warehouse são diferentes dos requisitos de um sistema que é otimizado para OLTP (processamento de transação online). O sistema OLTP tem muitas operações de inserção, atualização e exclusão. Essas operações buscam linhas específicas na tabela. As buscas na tabela são mais bem executadas quando os dados são armazenados linha por linha. Os dados podem ser classificados e rapidamente pesquisados com uma abordagem de divisão e domínio chamada de árvore binária ou pesquisa na árvore B.

## <a name="data-loading"></a>Carregamento de dados
O carregamento de dados é uma grande parte da carga de trabalho do data warehouse. Normalmente, as empresas têm um sistema OLTP ocupado que rastreia alterações o dia todo quando os clientes estão gerando transações comerciais. Periodicamente, geralmente à noite durante uma janela de manutenção, as transações são movidas ou copiadas para o data warehouse. Depois que os dados estão no data warehouse, os analistas podem fazer análises e tomar decisões de negócios com base nos dados.

* Tradicionalmente, o processo de carregamento é chamado de ETL (Extração, Transformação e Carregamento). Geralmente, os dados precisam ser transformados para que sejam consistentes com outros dados no data warehouse. Anteriormente, as empresas usavam servidores ETL dedicados para realizar as transformações. Agora, com um processamento rápido massivamente paralelo, você pode carregar dados no SQL Data Warehouse primeiro e depois realizar as transformações. Esse processo é chamado ELT (Extração, Carregamento e Transformação) e está se tornando um novo padrão para a carga de trabalho do data warehouse.

> [!NOTE]
> Com o SQL Server 2016, agora você pode realizar a análise em tempo real em uma tabela OLTP. Isso não substitui a necessidade de um data warehouse para armazenar e analisar dados, mas é uma maneira de realizar análise em tempo real.
> 
> 

### <a name="reporting-and-analysis-queries"></a>Consultas de análise e relatórios
As consultas de análise e relatórios muitas vezes são classificadas em pequenas, médias e grandes com base em vários critérios, mas normalmente se baseiam no tempo. Na maioria dos data warehouses, há uma carga de trabalho mista de consultas de execução rápida e execução longa. Em cada caso, é importante determinar essa combinação e determinar sua frequência (por hora, diariamente, fim do mês, fim do trimestre e assim por diante). É importante entender que a carga de trabalho de consultas mistas, aliada à simultaneidade, leva ao planejamento adequado da capacidade para um data warehouse.

* O planejamento da capacidade pode ser uma tarefa complexa para uma carga de trabalho de consultas mistas, especialmente quando você precisa de um longo prazo de entrega para adicionar capacidade ao data warehouse. O SQL Data Warehouse elimina a urgência do planejamento de capacidade, uma vez que você pode expandir e reduzir a capacidade de computação a qualquer momento, e uma vez que a capacidade de armazenamento e computação é dimensionada de modo independente.

### <a name="data-management"></a>Gerenciamento de dados
O gerenciamento de dados é importante, especialmente quando você sabe que pode esgotar o espaço em disco em um futuro próximo. O data warehouse normalmente divide os dados em intervalos significativos, que são armazenados como partições em uma tabela. Todos os produtos baseados em SQL Server permitem que você mova partições para dentro e fora da tabela. Essa alternância de partição permite mover dados antigos para um armazenamento mais barato e manter os dados mais recentes disponíveis no armazenamento online.

* Os índices columnstore oferecem suporte a tabelas particionadas. Em índices columnstore, as tabelas particionadas são usadas para gerenciamento de dados e arquivamento. Em tabelas armazenadas linha por linha, as partições têm uma função mais importante no desempenho da consulta.  
* O PolyBase desempenha um papel importante no gerenciamento de dados. Usando o PolyBase, você tem a opção de arquivar dados antigos no armazenamento blob do Azure ou no Hadoop.  Isso proporciona muitas opções, uma vez que os dados permanecem online.  Pode levar mais tempo para recuperar dados do Hadoop, mas a compensação do tempo de recuperação pode superar o custo do armazenamento.

### <a name="exporting-data"></a>Exportando dados
Uma maneira de disponibilizar dados para relatórios e análise é enviar os dados do data warehouse aos servidores dedicados para execução de análise e relatórios. Esses servidores são chamados de data marts. Por exemplo, você pode pré-processar dados de relatório e, em seguida, exportá-los do data warehouse para muitos servidores ao redor do mundo, disponibilizando-os amplamente aos clientes e analistas.

* Para gerar relatórios, todas as noites você pode popular servidores de relatório somente leitura com um instantâneo dos dados diários. Isso fornece maior largura de banda aos clientes ao mesmo tempo que reduz os recursos de computação necessários no data warehouse. Quanto se trata de segurança, os data marts permitem que você reduza o número de usuários que têm acesso ao data warehouse.
* Para análise, você pode criar um cubo de análise no data warehouse e realizar a análise no data warehouse ou pré-processar os dados e exportá-los para o servidor de análise para análise futura.

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe um pouco sobre o SQL Data Warehouse, saiba como [criar um SQL Data Warehouse][create a SQL Data Warehouse] e [carregar dados de amostra][load sample data] rapidamente.

<!--Image references-->

<!--Article references-->
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
