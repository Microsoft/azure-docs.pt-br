---
title: Ciclo de vida da base de dados de conhecimento – QnA Maker
description: O QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: e52e7151bc30a19bd6f6041d52effdd799a87c99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776962"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo de vida da base de dados de conhecimento no QnA Maker
O QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.

![Ciclo de criação](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento no QnA Maker
O ponto de extremidade de KB (base de dados de conhecimento) do QnA Maker fornece uma resposta de melhor correspondência a uma consulta do usuário com base no conteúdo da base de dados de conhecimento. A criação de uma base de dados de conhecimento é uma ação única para configurar um repositório de conteúdo de perguntas, respostas e metadados associados. Um KB pode ser criado rastreando o conteúdo pré-existente, como as seguintes fontes:

- Páginas de perguntas frequentes
- Manuais de produtos
- Q-um par

Saiba como [criar uma base de dados de conhecimento](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testar e atualizar a base de dados de conhecimento

A base de dados de conhecimento está pronta para testes, uma vez que é preenchida com conteúdo, seja editorialmente ou através de extração automática. O teste interativo pode ser feito no portal de QnA Maker, por meio do painel de **teste** . Você insere consultas de usuário comuns. Em seguida, você verifica se as respostas retornadas com a resposta correta e uma pontuação de confiança suficiente.


* **Para corrigir pontuações de baixa confiança**: Adicione perguntas alternativas.
* **Quando uma consulta retorna incorretamente a [resposta padrão](../How-to/change-default-answer.md)**: adicione novas respostas à pergunta correta.

Este loop estreito de atualização de teste continuará até que você esteja satisfeito com os resultados. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).

Para grandes KBs, use o teste automatizado com a [API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e a `isTest` Propriedade Body, que consulta a `test` base de dados de conhecimento em vez da base de dados de conhecimento publicada.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento
Quando terminar de testar a base de dados de conhecimento, você poderá publicá-la. A publicação envia por push a versão mais recente da base de dados de conhecimento testada para um índice do Azure Pesquisa Cognitiva dedicado que representa a base de dados de conhecimento **publicada** . Isso também cria um ponto de extremidade que pode ser chamado no aplicativo ou chat bot.

Devido à ação de publicação, quaisquer alterações adicionais feitas na versão de teste da base de dados de conhecimento deixarão a versão publicada não afetada. A versão publicada pode estar ativa em um aplicativo de produção.

Cada uma dessas bases de dados de conhecimento pode ser direcionada para testes separadamente. Usando as APIs, você pode direcionar a versão de teste da base de dados de conhecimento com a `isTest` Propriedade Body na chamada generateAnswer.

Saiba como [publicar a base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorar o uso
Para registrar os logs de chat do seu serviço, é necessário habilitar o Application Insights ao [criar o serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

É possível obter várias análises do uso do serviço. Saiba mais sobre como usar o Application Insights para obter [análises do serviço do QnA Maker](../How-To/get-analytics-knowledge-base.md).

De acordo com o que aprende-se com as análises, faça as [atualizações da base de dados de conhecimento](../How-To/edit-knowledge-base.md) apropriadas.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Controle de versão para dados em sua base de conhecimento

O controle de versão para dados é fornecido por meio dos recursos de importação/exportação na página **configurações** no portal de QnA Maker.

Você pode fazer backup de uma base de dados de conhecimento exportando a base de dados de conhecimento, em um `.tsv` `.xls` formato ou. Depois de exportado, inclua esse arquivo como parte da verificação de controle do código-fonte regular.

Quando precisar voltar para uma versão específica, você precisará importar esse arquivo do seu sistema local. Uma base de dados de conhecimento exportada **deve** ser usada apenas por meio de importação na página **configurações** . Ele não pode ser usado como uma fonte de dados de documento ou arquivo de URL. Isso substituirá as perguntas e respostas atualmente na base de dados de conhecimento pelo conteúdo do arquivo importado.

## <a name="test-and-production-knowledge-base"></a>Base de dados de conhecimento de teste e produção
Uma base de dados de conhecimento é o repositório de perguntas e conjuntos de respostas criados, mantidos e usados por meio de QnA Maker. Cada recurso de QnA Maker pode conter várias bases de dados de conhecimento.

Uma base de dados de conhecimento tem dois Estados: *teste* e *publicado*.

### <a name="test-knowledge-base"></a>Base de dados de conhecimento de teste

A *base de dados de conhecimento de teste* é a versão atualmente editada e salva. A versão de teste foi testada quanto à exatidão e, para fins de resposta. As alterações feitas na base de dados de conhecimento de teste não afetam o usuário final do seu aplicativo ou bot de bate-papo. A base de dados de conhecimento de teste é conhecida como `test` na solicitação HTTP. O `test` conhecimento está disponível com o painel de **teste** interativo do portal do QnA Maker.

### <a name="production-knowledge-base"></a>Base de dados de conhecimento de produção

A *base de dados de conhecimento publicada* é a versão que é usada no seu aplicativo ou bot de chat. A publicação de uma base de dados de conhecimento coloca o conteúdo de sua versão de teste em sua versão publicada. A base de dados de conhecimento publicada é a versão que o aplicativo usa por meio do ponto de extremidade. Verifique se o conteúdo está correto e bem testado. A base de dados de conhecimento publicada é conhecida como `prod` na solicitação HTTP.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Sugestões de aprendizado ativo](./active-learning-suggestions.md)