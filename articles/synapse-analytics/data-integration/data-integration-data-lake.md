---
title: Ingestão de dados no Azure Data Lake Storage Gen2
description: Saiba como ingerir dados no Azure Data Lake Storage Gen2 no Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbc4f11b450a645002daedc800d4fed74ed37a3d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219565"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Ingerir dados em Azure Data Lake Storage Gen2 

Neste artigo, você aprenderá a ingerir dados de um local para outro em uma conta de armazenamento do Azure Data Lake Gen2 usando o Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**: Use o Azure Data Lake Gen 2 como uma *fonte* de armazenamento de dados. Se você não tiver uma conta de armazenamento, consulte [Criar uma conta de armazenamento do Azure](../../storage/common/storage-account-create.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) para obter as etapas para criar uma.

## <a name="create-linked-services"></a>Criar serviços vinculados

No Azure Synapse Analytics, um serviço vinculado é onde você define as informações de conexão com outros serviços. Nesta seção, você adicionará o Azure Synapse Analytics e o Azure Data Lake Gen 2 como serviços vinculados.

1. Abra a UX do Azure Synapse Analytics e vá para a guia **Gerenciar**.
1. Em **Conexões externas**, selecione **Serviços vinculados**.
1. Para adicionar um serviço vinculado, selecione **Novo**.
1. Selecione o bloco Azure Data Lake Storage Gen2 na lista e selecione **continuar**.
1. Insira as credenciais de autenticação. A chave de conta, a entidade de serviço e a identidade gerenciada são tipos de autenticação atualmente suportados. Selecione testar conexão para verificar se suas credenciais estão corretas. 
1. Selecione **Criar** quando terminar.

## <a name="create-pipeline"></a>Criar um pipeline

Um pipeline contém o fluxo lógico para uma execução de um conjunto de atividades. Nesta seção, você criará um pipeline que contém uma atividade de cópia que ingere dados do Azure Data Lake Gen 2 para um pool SQL dedicado.

1. Vá para a guia **orquestrar** . Selecione no ícone de adição ao lado do cabeçalho pipelines e selecione **pipeline**.
1. Em **Mover e Transformar** no painel atividades, arraste **Copiar dados** no painel da tela do pipeline.
1. Selecione na atividade de cópia e vá para a guia **origem** . Selecione **novo** para criar um novo conjunto de fonte de origem.
1. Selecione Azure Data Lake Storage Gen2 como seu armazenamento de dados e selecione continuar.
1. Selecione DelimitedText como seu formato e selecione continuar.
1. No painel definir propriedades, selecione o serviço vinculado ADLS que você criou. Especifique o caminho do arquivo dos dados de origem e especifique se a primeira linha tem um cabeçalho. Você pode importar o esquema do repositório de arquivos ou de um arquivo de exemplo. Selecione OK quando terminar.
1. Vá para a guia **coletor** . Selecione **novo** para criar um novo conjunto de coletas.
1. Selecione Azure Data Lake Storage Gen2 como seu armazenamento de dados e selecione continuar.
1. Selecione DelimitedText como seu formato e selecione continuar.
1. No painel definir propriedades, selecione o serviço vinculado ADLS que você criou. Especifique o caminho da pasta na qual você deseja gravar os dados. Selecione OK quando terminar.

## <a name="debug-and-publish-pipeline"></a>Depurar e publicar o pipeline

Depois de concluir a configuração do pipeline, você poderá efetuar uma execução de depuração antes de publicar seus artefatos para verificar se tudo está correto.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** na parte inferior da janela. 
1. Depois que o pipeline for executado corretamente, na barra de ferramentas superior, selecione **Publicar Tudo**. Esta ação publica as entidades (conjuntos de dados e pipelines) criadas por você anteriormente no Synapse Analytics.
1. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as mensagens de notificação, selecione o botão de sino no canto superior direito. 


## <a name="trigger-and-monitor-the-pipeline"></a>Acionar e monitorar o pipeline

Nesta etapa, você aciona manualmente o pipeline publicado na etapa anterior. 

1. Selecione **Adicionar gatilho** na barra de ferramentas e selecione **Disparar Agora**. Na página **Execução de Pipeline**, selecione **Concluir**.  
1. Vá para a guia **Monitorar** localizada na barra lateral esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar os links na coluna **Ações** para exibir detalhes da atividade e executar o pipeline novamente.
1. Selecione o link **Exibir atividades em execução** na coluna **Ações** para ver a atividade em execução associada à execução do pipeline. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. Para voltar ao modo de exibição Execuções de Pipeline, selecione **Execuções de Pipeline** na parte superior. Para atualizar a exibição, selecione **Atualizar**.
1. Verifique se os dados estão gravados corretamente no pool de SQL dedicado.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a integração de dados para o Azure Synapse Analytics, consulte o artigo [ingerir dados em um pool de SQL dedicado](data-integration-sql-pool.md) .