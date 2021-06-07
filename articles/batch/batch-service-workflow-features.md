---
title: Fluxo de trabalho e recursos do serviço do Lote
description: Conheça os recursos do serviço de Lote e o fluxo de serviço de alto nível do ponto de vista de um desenvolvedor.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 76a0b140f3bea4b07a6de632abbcbc3fd26e582d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85965205"
---
# <a name="batch-service-workflow-and-resources"></a>Fluxo de trabalho e recursos do serviço do Lote

Nesta visão geral dos componentes principais do serviço de Lote do Azure, vamos abordar o fluxo de trabalho de alto nível que os desenvolvedores do Lote podem usar para criar soluções de computação paralela em larga escala, junto com os recursos de serviço primário usados.

Se você estiver desenvolvendo um aplicativo de computador distribuído ou serviço que faz chamadas diretas da [API REST](/rest/api/batchservice/) ou estiver usando outro dos [SDKs de Lote](batch-apis-tools.md#batch-service-apis), usará muitos dos recursos analisados aqui.

> [!TIP]
> Para obter uma introdução de nível superior do serviço de Lote, consulte os [Fundamentos do Lote do Azure](batch-technical-overview.md). Confira também as [atualizações de serviço Lote](https://azure.microsoft.com/updates/?product=batch) mais recentes.

## <a name="basic-workflow"></a>Fluxo de trabalho básico

O seguinte fluxo de trabalho de alto nível é típico de quase todos os aplicativos e serviços que usam o serviço de Lote para o processamento de cargas de trabalho paralelas:

1. Carregue os **arquivos de dados** que você deseja processar em uma conta de [Armazenamento do Azure](../storage/index.yml). O Lote inclui suporte interno para acessar o armazenamento de Blobs do Azure, e as tarefas podem baixar esses arquivos para [nós de computação](nodes-and-pools.md#nodes) quando são executadas.
2. Carregue os **arquivos de aplicativo** que as tarefas executarão. Esses arquivos podem ser binários ou scripts e suas dependências e são executados pelas tarefas em seus trabalhos. As tarefas podem baixar esses arquivos de sua conta de armazenamento ou você pode usar o recurso dos [pacotes de aplicativos](nodes-and-pools.md#application-packages) de lote para a implantação e o gerenciamento dos aplicativos.
3. Crie um [pool](nodes-and-pools.md#pools) de nós de computação. Quando você cria um pool, pode especificar o número de nós de computação para o pool, seu tamanho e o sistema operacional. Quando cada tarefa no trabalho é executada, ela é atribuída para ser executada em um de nós no pool.
4. Crie um [trabalho](jobs-and-tasks.md#jobs). Um trabalho gerencia uma coleção de tarefas. Você associa cada trabalho a um pool específico no qual as tarefas do trabalho serão executadas.
5. Adicione [Tarefas](jobs-and-tasks.md#tasks) ao trabalho. Cada tarefa executa o aplicativo ou script que você carregou para processar os arquivos de dados que ele baixa de sua conta do Armazenamento. À medida que cada tarefa é concluída, pode carregar sua saída para o Armazenamento do Azure.
6. Monitore o andamento dos trabalhos e recupere a saída de tarefas do Armazenamento do Azure.

> [!NOTE]
> Você precisa de uma [Conta do Lote](accounts.md) para usar o serviço do Lote. A maioria das soluções do Lote também usa uma conta de [Armazenamento do Azure](../storage/index.yml) associada para o armazenamento e recuperação de arquivos.

## <a name="batch-service-resources"></a>Recursos do serviço de lote

Os tópicos a seguir discutem os recursos do Lote que habilitarão seus cenários de computação distribuída.

- [Contas do Lote e contas de armazenamento](accounts.md)
- [Nós e pools](nodes-and-pools.md)
- [Trabalhos e tarefas](jobs-and-tasks.md)
- [Arquivos e diretórios](files-and-directories.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
- Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](quick-run-dotnet.md) ou do [Python](quick-run-python.md). O artigo de início rápido orienta você por meio de um aplicativo de exemplo que usa o serviço em Lotes para executar uma carga de trabalho em vários nós de computação e que inclui o uso do Armazenamento do Azure para preparação e recuperação de um arquivo de carga de trabalho.
- Baixe e instale o [Batch Explorer](https://azure.github.io/BatchExplorer/) para usá-lo durante a instalação das suas soluções de lote. Use o Batch Explorer para ajudar a criar, depurar e monitorar os aplicativos do Lote do Azure.
- Consulte os recursos da comunidade incluindo o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), o [repositório da Comunidade de Lote](https://github.com/Azure/Batch), e o [fórum do Lote do Azure](/answers/topics/azure-batch.html).
