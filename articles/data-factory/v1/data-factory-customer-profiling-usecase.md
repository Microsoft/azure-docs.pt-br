---
title: "Caso de uso - Criação de perfil de cliente"
description: "Saiba como o Azure Data Factory é usado para criar um fluxo de trabalho orientado a dados (pipeline) para criar o perfil de clientes de jogos."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: e7acb9fbddc59803ed8cf6f1d7dcda0171998f7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-case---customer-profiling"></a>Caso de uso - Criação de perfil de cliente
O Azure Data Factory é um dos muitos serviços usados para implementar o Cortana Intelligence Suite de aceleradores de solução.  Para obter mais informações sobre o Cortana Intelligence, visite [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). Neste documento, descrevemos um caso de uso simples para ajudá-lo a começar a entender como o Azure Data Factory pode resolver problemas comuns de análise.

## <a name="scenario"></a>Cenário
A Contoso é uma empresa de jogos que cria os jogos para várias plataformas: PCs (computadores pessoais), dispositivos de mão e consoles de jogos. À medida que os usuários jogam, grandes volumes de dados de log são produzidos, controlando os padrões de uso, estilo de jogos e preferências do usuário.  Quando combinados com dados demográficos, regionais e dados de produto, a Contoso pode executar análises para orientá-los sobre como aprimorar a experiência dos jogadores e direcioná-los para atualizações e compras de jogos. 

O objetivo da Contoso é identificar oportunidades de venda/venda cruzada com base no histórico de jogos de seus jogadores e adicionar novos recursos atraentes para gerar o crescimento dos negócios e proporcionar uma melhor experiência para os clientes. Para esse caso, usamos uma empresa de jogos como um exemplo de uma empresa. A empresa deseja otimizar seus jogos com base no comportamento dos jogadores. Esses princípios se aplicam a qualquer empresa que deseje atrair seus clientes para seus produtos e serviços e aprimorar a experiência dos clientes.

Nesta solução, a Contoso deseja avaliar a eficiência de uma campanha de marketing lançada recentemente. Vamos começar com os logs de jogos brutos, processando e enriquecendo-os com os dados de localização geográfica, associando-os aos dados de referência de publicidade, por fim, copiando-os para um Banco de Dados SQL do Azure para analisar o impacto da campanha.

## <a name="deploy-solution"></a>Implantar Solução
Tudo o que você precisa para acessar e testar este caso de uso simples é uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/), uma [Conta de Armazenamento de Blobs do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account) e um [Banco de Dados SQL do Azure](../../sql-database/sql-database-get-started.md). Implante o pipeline de criação de perfil do cliente do bloco **Pipelines de exemplo** na página inicial de sua fábrica de dados.

1. Crie uma data factory ou abra uma existente. Consulte [Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL usando o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter as etapas para criar um data factory.
2. Na folha **DATA FACTORY** para a data factory em questão, clique no bloco **Pipelines de exemplo**.

    ![Bloco Pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)
3. Na folha **Pipelines de exemplo**, clique na **Criação de perfil do cliente** que você deseja implantar.

    ![Folha Pipelines de exemplo](./media/data-factory-samples/SampleTile.png)
4. Especifique definições de configuração para o exemplo. Por exemplo, o nome e a chave da sua conta de Armazenamento do Azure, o nome do Azure SQL Server, o banco de dados, a ID de usuário a e senha.

    ![Folha Exemplo](./media/data-factory-samples/SampleBlade.png)
5. Depois de terminar a especificação de configurações, clique em **Criar** para criar/implantar os pipelines de exemplo e serviços vinculados/tabelas usados pelos pipelines.
6. Você verá o status da implantação do bloco de exemplo em que você clicou anteriormente na folha **Pipelines de exemplo** .

    ![Status da Implantação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando você vir a mensagem **Implantação bem-sucedida** no bloco para o exemplo, feche a folha **Pipelines de exemplo**.  
8. Na folha **DATA FACTORY**, você verá que os pipelines, conjuntos de dados e serviços vinculados são adicionados à sua data factory.  

    ![Folha Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Visão geral da solução
Este caso de uso simples pode ser usado como um exemplo de como você pode usar o Azure Data Factory para ingestão, preparação, transformação, análise e publicação dos dados.

![Fluxos de trabalho completos](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Esta figura mostra como os pipelines de dados são exibidos no Portal do Azure após terem sido implantados.

1. O **PartitionGameLogsPipeline** lê os eventos brutos de jogos de um armazenamento de blob e cria partições com base no ano, mês e dia.
2. O **EnrichGameLogsPipeline** junta eventos de jogos particionados com dados de referência de código geográfico e enriquece os dados pelo mapeamento de endereços IP para os locais geográficos correspondentes.
3. O pipeline **AnalyzeMarketingCampaignPipeline** usa dados enriquecidos e processa-os com os dados de anúncio para criar a saída final, que contém a eficácia de uma campanha de marketing.

Neste exemplo, o Data Factory é usado para coordenar as atividades que copiam dados de entrada, transformam e processam os dados e fazem a saída de dados final para um Banco de Dados SQL do Azure.  Você também pode visualizar a rede de pipelines de dados, gerenciá-los e monitorar o status da interface do usuário.

## <a name="benefits"></a>Benefícios
Ao otimizar suas análises de perfil do usuário e o alinhamento com as metas de negócios, a empresa de jogos é capaz coletar rapidamente os padrões de uso e analisar a eficácia de suas campanhas de marketing.

