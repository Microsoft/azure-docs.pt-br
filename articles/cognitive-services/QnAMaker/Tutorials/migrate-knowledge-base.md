---
title: Migrar bases de dados de conhecimento – QnA Maker
description: Migrar uma base de dados de conhecimento requer a exportação de uma base de dados de conhecimento e a importação para outra.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: c89ab375cb02824a08ff57e6b5278dd9299126ff
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350918"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de dados de conhecimento usando export-import

A migração é o processo de criação de uma nova base de dados de conhecimento a partir de uma base de dados de conhecimento existente. Você pode fazer isso por vários motivos:

* processo de backup e restauração
* Pipeline de CI/CD
* mover regiões

A migração de uma base de dados de conhecimento requer a exportação de uma base de dados de conhecimento existente e a importação para outra.

> [!NOTE]
> Siga as instruções abaixo para migrar sua base de dados de conhecimento existente para um novo QnA Maker gerenciado (versão prévia).

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.
* Configurar um novo [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrar uma base de dados de conhecimento do QnA Maker
1. Entre no [portal do QnA Maker](https://qnamaker.ai).
1. Selecione a base de dados de conhecimento de origem que você deseja migrar.

1. Na página **configurações** , selecione **Exportar base de dados de conhecimento** para baixar um arquivo. tsv que contém o conteúdo de sua base de dados de conhecimento de origem-perguntas, respostas, metadados, avisos de acompanhamento e os nomes da fonte de dados da qual eles foram extraídos. As IDs de QnA exportadas com as perguntas e respostas podem ser usadas para atualizar um par QnA específico usando a [API de atualização](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). A ID de QnA para um par de QnA específico permanece inalterada em várias operações de exportação.

1. Selecione **criar uma base de dados de conhecimento** no menu superior e, em seguida, crie uma base de dados de conhecimento _vazia_ . Ele está vazio porque, ao criá-lo, você não adicionará URLs ou arquivos. Eles são adicionados durante a etapa de importação, após a criação.

    Configure a base de dados de conhecimento. Defina o novo nome da base de dados de conhecimento apenas. Nomes duplicados e caracteres especiais têm suporte.

    Não selecione nada na etapa 4 porque esses valores serão substituídos quando você importar o arquivo.

1. Na etapa 5, selecione **criar**.

1. Nessa nova base de dados de conhecimento, abra a guia **Configurações** e selecione **Importar base de dados de conhecimento**. Isso importa as perguntas, as respostas, os metadados, os prompts de acompanhamento e retém os nomes das fontes de dados dos quais eles foram extraídos. **Os pares de QnA criados na nova base de dados de conhecimento devem ter a mesma ID de QnA, conforme presente no arquivo exportado**. Isso ajuda a criar uma réplica exata da base de dados de conhecimento.

   > [!div class="mx-imgBorder"]
   > [![Importar base de dados de conhecimento](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Teste** a nova base de dados de conhecimento usando o painel de teste. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).

1. **Publique** a base de dados de conhecimento e crie um bot de chat. Saiba como [publicar a base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Migre programaticamente uma base de dados de conhecimento de QnA Maker

O processo de migração está programaticamente disponível usando as seguintes APIs REST:

**Exportar**

* [Baixar API da base de dados de conhecimento](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**Importaçãoação**

* [Substituir API (recarregar com a mesma ID da base de dados de conhecimento)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [Criar API (carregar com nova ID da base de dados de conhecimento)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Logs de chat e alterações
As alterações (sinônimos) que não diferenciam maiúsculas de minúsculas não são importadas automaticamente. Use as [APIs v4](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) para mover as alterações na nova base de dados de conhecimento.

Não há nenhuma maneira de migrar os logs de chat, já que a nova base de dados de conhecimento usa o Application Insights para armazenar os logs de chat.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)