---
title: Migrar bases de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: Migrar uma base de dados de conhecimento requer a exportação de uma base de dados de conhecimento e a importação para outra.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: eac5e43c69cc09c5945316827a35f729c158d47a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431203"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de dados de conhecimento usando export-import

Migrar uma base de dados de conhecimento requer a exportação de uma base de dados de conhecimento e a importação para outra. 

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Configurar um novo [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrar uma base de dados de conhecimento do QnA Maker
1. Entre no [portal do QnA Maker](https://qnamaker.ai).
1. Selecione a base de dados de conhecimento que você deseja migrar.

1. Na página **Configurações**, selecione **Exportar base de dados de conhecimento** para baixar um arquivo .tsv que contém o conteúdo de sua base de dados de conhecimento: perguntas, respostas, metadados e nomes de fonte de dados da qual eles foram extraídos.

1. Selecione **Criar uma base de dados de conhecimento** no menu superior e crie uma base de dados de conhecimento vazia. 

    ![Definir fontes de dados](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Forneça um **nome** ao serviço. Nomes duplicados e caracteres especiais têm suporte.

1. Selecione **Criar**.

    ![Criar uma base de dados de conhecimento](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Nessa nova base de dados de conhecimento, abra a guia **Configurações** e selecione **Importar base de dados de conhecimento**. Isso importa as perguntas, as respostas e os metadados, e mantém os nomes das fontes de dados das quais eles foram extraídos.

   ![Importar base de dados de conhecimento](../media/qnamaker-how-to-migrate-kb/Import.png)

1. **Teste** a nova base de dados de conhecimento usando o painel de teste. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).
1. **Publicar** a base de dados de conhecimento. Saiba como [publicar a base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Use o ponto de extremidade no aplicativo ou código de bot. Veja aqui como [Criar um bot de QnA](../Tutorials/create-qna-bot.md).

    ![Valores do QnA Maker](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    Neste ponto, todo o conteúdo da base de dados de conhecimento – perguntas, respostas e metadados, juntamente com os nomes dos arquivos de origem e as URLs, são importados para a nova base de dados de conhecimento. 

## <a name="chat-logs-and-alterations"></a>Logs de chat e alterações
As alterações (sinônimos) que não diferenciam maiúsculas de minúsculas não são importadas automaticamente. Use as [APIs V2](https://aka.ms/qnamaker-v2-apis) para exportar as alterações da base de dados de conhecimento antiga e as [APIs V4](https://aka.ms/qnamaker-v4-apis) a fim de mover as alterações para a nova base de dados de conhecimento.

Não há nenhuma maneira de migrar os logs de chat, já que a nova base de dados de conhecimento usa o Application Insights para armazenar os logs de chat. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)
