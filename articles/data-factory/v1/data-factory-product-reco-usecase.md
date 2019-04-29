---
title: Caso de uso do Data Factory - recomendações de produtos
description: Saiba mais sobre um caso de uso implementado usando o Azure Data Factory junto com outros serviços.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4a3d1c513bcfb6449ca73d873c0dd9831c6fe01d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605689"
---
# <a name="use-case---product-recommendations"></a>Caso de uso - recomendações de produtos
O Azure Data Factory é um dos muitos serviços usados para implementar o Cortana Intelligence Suite de aceleradores de solução.  Consulte a página [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) para obter detalhes sobre este pacote. Neste documento, descrevemos um caso de uso comum que usuários do Azure já resolveram e implementaram usando o Azure Data Factory e outros serviços de componente do Cortana Intelligence.

## <a name="scenario"></a>Cenário
Revendedores online normalmente desejam estimular seus clientes a comprar produtos ao apresentar a esses clientes os produtos nos quais é mais provável que eles estejam interessados e, portanto, que têm mais probabilidade de comprar. Para fazer isso, os revendedores online devem personalizar a experiência online do seu usuário usando recomendações de produtos personalizadas para esse usuário específico. Essas recomendações personalizadas devem ser feitas com base em seus dados atuais e históricos de comportamento de compra, informações de produto, marcas introduzidas recentemente e dados de segmentação de produto e de cliente.  Além disso, eles podem oferecer ao usuário recomendações de produto do usuário com base na análise do comportamento geral de uso de todos os seus usuários combinados.

O objetivo desses revendedores é otimizar o processo para conversões de clique em venda pelo usuário e aumentar sua receita de vendas.  Eles alcançam essa conversão fornecendo recomendações de produtos contextuais, baseadas em comportamento, nas ações e nos interesses do cliente. Para esse caso, usamos revendedores online como um exemplo de empresas que desejam otimizar para seus clientes. No entanto, esses princípios se aplicam a qualquer empresa que deseje atrair seus clientes para seus produtos e serviços e aprimorar a experiência de compra dos clientes com recomendações de produto personalizadas.

## <a name="challenges"></a>Desafios
Existem muitos desafios enfrentados por revendedores online ao tentar implementar esse tipo de caso de uso. 

Primeiro, os dados de formas e tamanhos diferentes devem ser incluídos de várias fontes de dados, tanto locais quanto na nuvem. Esses dados incluem dados de produtos, dados históricos de comportamento do cliente e dados do usuário conforme o usuário pesquisa o site do revendedor online. 

Segundo, recomendações de produtos personalizadas devem ser calculadas e previstas de modo razoável e preciso. Além de produto, marca e os dados do navegador e de comportamento do cliente, os revendedores online também precisam incluir comentários fornecidos por clientes sobre compras passadas para que influenciem a determinação das melhores recomendações de produto para o usuário. 

Em terceiro lugar, as recomendações devem poder ser entregues imediatamente ao usuário para fornecer uma experiência perfeita de navegação e compra, além de fornecer as recomendações mais recentes e relevantes. 

Por fim, os revendedores devem avaliar a eficiência de sua abordagem acompanhando as medidas de vendas bem-sucedidas de conversão de clique, tanto em vendas suplementares quanto em vendas cruzadas, para então ajustar-se às suas recomendações futuras.

## <a name="solution-overview"></a>Visão geral da solução
Esse exemplo de caso de uso foi resolvido e implementado por usuários reais do Azure pelo uso do Azure Data Factory e outros serviços de componente de Cortana Intelligence, inclusive [HDInsight](https://azure.microsoft.com/services/hdinsight/) e [Power BI](https://powerbi.microsoft.com/).

O revendedor online usa um Armazenamento de Blob do Azure, um SQL Server local, um Banco de Dados SQL do Azure e um data mart para dados relacionais como suas opções de armazenamento de dados durante o fluxo de trabalho.  O armazenamento de blob contém dados de informações de produto, dados de comportamento do cliente e informações do produto. Os dados de informações de produto incluem informações sobre a marca do produto e um catálogo de produtos armazenados localmente em um SQL data warehouse. 

Todos os dados são combinados alimentados em um sistema de recomendação de produtos para fornecer recomendações personalizadas com base em ações e os interesses do cliente, enquanto o usuário procura produtos no catálogo existente no site. O cliente também vê produtos que estão relacionados ao produto está examinando com base nos padrões de uso geral do site, que não estão relacionadas a nenhum usuário específico.

![diagrama de casos de uso](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes de arquivos de log da Web brutos são gerados diariamente no site do revendedor online como arquivos semiestruturados. Os arquivos de log da Web brutos e as informações de catálogo do cliente e de produto são incluídos regularmente em um Armazenamento de Blobs do Azure, usando a movimentação de dados implantada globalmente do Data Factory como um serviço. Os arquivos de log brutos do dia são particionados (por ano e mês) no armazenamento de blobs para armazenamento a longo prazo.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) é usado para particionar os arquivos de log brutos no repositório de blob e processar os logs ingeridos em escala usando tanto scripts do Hive quanto do Pig. Os dados de log da Web particionados são processados para extrair as entradas necessárias para que um sistema de recomendação de aprendizado de máquina gere as recomendações de produtos personalizadas.

O sistema de recomendação usado para o aprendizado de máquina neste exemplo é uma plataforma de recomendação de aprendizado de máquina de software livre do [Apache Mahout](https://mahout.apache.org/).  Qualquer modelo personalizado ou do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) pode ser aplicado ao cenário.  O modelo Mahout é usado para prever a semelhança entre os itens no site com base nos padrões gerais de uso e para gerar as recomendações personalizadas com base no usuário individual.

Por fim, o conjunto de resultados de recomendações de produtos personalizadas é movido para um data mart para dados relacionais para consumo pelo site do revendedor.  O conjunto de resultados também pode ser acessado diretamente do armazenamento de blob por outro aplicativo ou movido para repositórios adicionais para outros consumidores e casos de uso.

## <a name="benefits"></a>Benefícios
Ao otimizar sua estratégia de recomendação de produtos e alinhá-la com as metas de negócios, a solução atendeu aos objetivos de marketing e gerenciamento de mercadorias do revendedor online. Além disso, eles conseguiram operacionalizar e gerenciar o fluxo de trabalho de recomendação de produtos de maneira eficiente, confiável e econômica. A abordagem facilitou para que eles possam atualizar seus modelos e ajustar sua eficácia com base em medidas de vendas bem-sucedidas de conversão de clique. Usando o Azure Data Factory, eles conseguiram abandonar seu gerenciamento de recursos de nuvem manual dispendioso e demorado e passar a usar o gerenciamento de recursos de nuvem sob demanda. Portanto, eles conseguiram economizar tempo, dinheiro e reduzir o tempo para implantação de soluções. Tornou-se fácil visualizar e solucionar problemas nas exibições de linhagem de dados e integridade do serviço operacional com a interface de usuário intuitiva de monitoramento e gerenciamento do Data Factory, disponível no Portal do Azure. Sua solução agora pode ser agendada e gerenciada de modo que os dados concluídos sejam produzidos e entregues aos usuários de maneira confiável e que os dados em dependências de processamento sejam gerenciados automaticamente, sem intervenção humana.

Ao fornecer essa experiência personalizada de compra, o revendedor online criou uma experiência de cliente mais competitiva, atraente e, consequentemente, haverá aumento da satisfação geral e das vendas.

