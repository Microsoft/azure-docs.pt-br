---
title: O que é o Azure SQL Edge?
description: Saiba mais sobre o Azure SQL Edge
keywords: introdução ao SQL do Azure no Edge, o que é o SQL do Azure no Edge, visão geral do SQL do Azure no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a74e12a5e9bdd2dfdbc6ac07b66798e517f6f426
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395063"
---
# <a name="what-is-azure-sql-edge"></a>O que é o Azure SQL Edge?

O Azure SQL Edge é um mecanismo de banco de dados relacional otimizado voltado para implantações de IoT e IoT Edge. Ele oferece funcionalidades para a criação de uma camada de processamento e armazenamento de dados de alto desempenho para aplicativos e soluções de IoT. O SQL do Azure no Edge fornece recursos para transmitir, processar e analisar relacionais e não relacionais, como JSON, gráfico e dados de série temporal, o que o torna a escolha certa para uma variedade de aplicativos de IoT modernos.

O Azure SQL Edge foi criado com base nas versões mais recentes do [SQL Server mecanismo de banco de dados](/sql/sql-server/sql-server-technical-documentation), que fornece recursos de desempenho, segurança e processamento de consulta líderes do setor. Como o Azure SQL Edge é criado no mesmo mecanismo que [SQL Server](/sql/sql-server/sql-server-technical-documentation) e [SQL do Azure](../azure-sql/index.yml), ele fornece a mesma área de superfície de Programação Transact-SQL (T-SQL) que torna o desenvolvimento de aplicativos ou soluções mais fácil e rápido e torna a portabilidade do aplicativo entre IOT Edge dispositivos, data centers e a nuvem diretamente em frente.

O que é o vídeo do Azure SQL Edge no Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-SQL-Edge/player]

## <a name="deployment-models"></a>Modelos de implantação

O Azure SQL Edge dá suporte a dois modos de implantação.

- Implantação conectada por meio do Azure IoT Edge: o Azure SQL Edge está disponível no Azure Marketplace e pode ser implantado como um módulo para [Azure IOT Edge](../iot-edge/about-iot-edge.md). Para obter mais informações, consulte o artigo [Implantar o SQL do Azure no Edge](deploy-portal.md).<br>

![Diagrama de visão geral do SQL do Azure no Edge](media/overview/overview.png)

- Implantação desconectada: as imagens de contêiner do Azure SQL Edge podem ser extraídas do Hub do Docker e implantadas como um contêiner autônomo do Docker ou em um cluster kubernetes. Para obter mais informações, consulte [implantar o Azure SQL Edge com o Docker](disconnected-deployment.md) e [implantar um contêiner do Azure SQL Edge no kubernetes](deploy-kubernetes.md).

## <a name="editions-of-sql-edge"></a>Edições do SQL do Azure no Edge

O SQL no Edge está disponível com duas edições ou planos de software diferentes. Essas edições têm conjuntos de recursos idênticos e diferem apenas em termos de seus direitos de uso e da quantidade de CPU/memória que elas suportam.

   |**Plano**  |**Descrição**  |
   |---------|---------|
   |Desenvolvedor do SQL do Azure no Edge  |  No SKU somente de desenvolvimento, cada contêiner do SQL do Azure no Edge é limitado a até 4 núcleos e 32 GB de memória.  |
   |SQL do Azure no Edge    |  SKU de produção, cada contêiner do SQL Edge é limitado a até 8 núcleos e 64 GB de memória. |

## <a name="pricing-and-availability"></a>Preços e disponibilidade

O Azure SQL Edge já está disponível para o público geral. Para obter mais informações sobre os preços e a disponibilidade em regiões específicas, consulte [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/).

> [!IMPORTANT]
> Para entender as diferenças de recursos entre o SQL do Azure no Edge e o SQL Server, bem como as diferenças entre as diferentes opções do SQL do Azure no Edge, consulte [recursos com suporte do SQL do Azure no Edge](features.md).

## <a name="streaming-capabilities"></a>Recursos de streaming  

O SQL do Azure no Edge fornece recursos de streaming internos para análise em tempo real e processamento de eventos complexos. O recurso de streaming é criado usando as mesmas construções que o [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e recursos semelhantes, como [Azure Stream Analytics no IoT Edge](../stream-analytics/stream-analytics-edge.md).

O mecanismo de streaming para o SQL do Azure no Edge foi projetado para baixa latência, resiliência, uso eficiente da largura de banda e conformidade. 

Para obter mais informações sobre streaming de dados no SQL do Azure no Edge, consulte [Streaming de dados](stream-data.md).

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Recursos de Machine Learning e inteligência artificial

O SQL do Azure no Edge fornece recursos internos de machine learning e análise. Ele faz isso por meio da integração do tempo de execução de formato aberto chamado ONNX (Open Neural Network Exchange), que permite o intercâmbio de modelos de rede neural e de aprendizado profundo entre diferentes estruturas. Para saber mais sobre ONNX, clique [aqui](https://onnx.ai/). O tempo de execução do ONNX fornece a flexibilidade para desenvolver modelos na linguagem ou nas ferramentas de sua escolha, que poderão ser convertidas para o formato ONNX para execução no SQL do Azure no Edge. Para obter mais informações, consulte [Machine Learning e inteligência artificial com o ONNX no SQL no Edge](onnx-overview.md).

## <a name="working-with-azure-sql-edge"></a>Trabalhando com o SQL do Azure no Edge

O SQL do Azure no Edge torna o desenvolvimento e a manutenção de aplicativos mais fáceis e produtivos. Os usuários podem usar todas as ferramentas e habilidades conhecidas para criar ótimos aplicativos e soluções para as necessidades de IoT Edge deles. O usuário pode desenvolver no SQL do Azure no Edge usando ferramentas como:

- [O portal do Azure](https://portal.azure.com/): um aplicativo baseado na Web para gerenciar todos os serviços do Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/): um aplicativo cliente livre e que pode ser baixado para gerenciar qualquer infraestrutura SQL, do SQL Server ao Banco de Dados SQL.
- [SQL Server Data Tools no Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/): um aplicativo cliente livre que pode ser baixado para o desenvolvimento de bancos de dados relacionais do SQL Server, bancos de dados SQL, pacotes do Integration Services, modelos de dados do Analysis Services e relatórios do Reporting Services.
- [Azure Data Studio](/sql/azure-data-studio/what-is/): uma ferramenta gratuita e baixável de banco de dados multiplataforma para profissionais de dados que usam a família Microsoft de plataformas de dados locais e na nuvem em Windows, macOS e Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs): um editor de código gratuito de software livre que pode ser baixado para Windows, macOS e Linux. Ele dá suporte a extensões, incluindo a [extensão MSSQL](https://aka.ms/mssql-marketplace) para consultar Microsoft SQL Server, banco de dados SQL do Azure e Azure Synapse Analytics.


## <a name="next-steps"></a>Próximas etapas

- [Implantar o SQL do Azure no Edge por meio do portal do Azure](deploy-portal.md)
- [Machine Learning e inteligência artificial com o SQL no Edge](onnx-overview.md).
- [Compilar uma solução IoT de ponta a ponta com o SQL no Edge](tutorial-deploy-azure-resources.md)