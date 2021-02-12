---
title: Planeje seu aplicativo-QnA Maker
description: Saiba como planejar seu aplicativo QnA Maker. Entenda como QnA Maker funciona e interage com outros serviços do Azure e alguns conceitos da base de dados de conhecimento.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: bf5582016f74e67926c38111a3d8d2f468f3ac79
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987982"
---
# <a name="plan-your-qna-maker-app"></a>Planejar seu aplicativo QnA Maker

Para planejar seu aplicativo QnA Maker, você precisa entender como o QnA Maker funciona e interage com outros serviços do Azure. Você também deve ter uma noção sólida dos conceitos da base de dados de conhecimento.

## <a name="azure-resources"></a>Recursos do Azure

Cada [recurso do Azure](azure-resources.md#resource-purposes) criado com QnA Maker tem uma finalidade específica. Cada recurso tem sua própria finalidade, limites e [tipo de preço](azure-resources.md#pricing-tier-considerations). É importante entender a função desses recursos para que você possa usar esse conhecimento em seu processo de planejamento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

| Recurso | Finalidade |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) recurso | Criação e previsão de consulta |
| [Pesquisa cognitiva](azure-resources.md#cognitive-search-resource) recurso | Armazenamento e pesquisa de dados |
| Recurso do [serviço de aplicativo e recurso do serviço de plano de aplicativo](azure-resources.md#app-service-and-app-service-plan) | Ponto de extremidade de previsão da consulta |
| [Application insights](azure-resources.md#application-insights) recurso | Telemetria de previsão de consulta |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

| Recurso | Finalidade |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) recurso | Criação, ponto de extremidade de previsão de consulta e telemetria|
| [Pesquisa cognitiva](azure-resources.md#cognitive-search-resource) recurso | Armazenamento e pesquisa de dados |

---
### <a name="resource-planning"></a>Planejamento de recursos

A camada gratuita, `F0` , de cada recurso, funciona e pode fornecer a experiência de criação e previsão de consulta. Você pode usar essa camada para aprender a criação e a previsão de consultas. Ao mudar para um cenário de produção ou ao vivo, reavalie sua seleção de recursos.

#### <a name="qna-maker-resource"></a>Recurso do QnA Maker

Um único recurso de QnA Maker pode hospedar mais de uma base de dados de conhecimento. O número de bases de dados de conhecimento é determinado pela quantidade de índices com suporte do tipo de preço Pesquisa Cognitiva. Saiba mais sobre a [relação de índices com bases de dados de conhecimento](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Tamanho e taxa de transferência da base de dados de conhecimento

Quando você cria um aplicativo real, planeje recursos suficientes para o tamanho da sua base de dados de conhecimento e para suas solicitações de previsão de consulta esperadas.

Um tamanho da base de dados de conhecimento é controlado pelo:
* Limites de tipo de preço de [pesquisa cognitiva recurso](../../../search/search-limits-quotas-capacity.md)
* [Limites de QnA Maker](../limits.md)

A solicitação de previsão de consulta da base de dados de conhecimento é controlada pelo plano do aplicativo Web e pelo aplicativo Web. Consulte [as configurações recomendadas](azure-resources.md#recommended-settings) para planejar seu tipo de preço.

### <a name="resource-sharing"></a>Compartilhamento de recurso

Se você já tiver alguns desses recursos em uso, poderá considerar o compartilhamento de recursos. Veja quais recursos [podem ser compartilhados](azure-resources.md#share-services-with-qna-maker) com a compreensão de que o compartilhamento de recursos é um cenário avançado.

Todas as bases de dados de conhecimento criadas no mesmo recurso QnA Maker compartilham o mesmo ponto de extremidade de previsão de consulta de **teste** .

### <a name="understand-the-impact-of-resource-selection"></a>Entender o impacto da seleção de recursos

A seleção de recursos apropriada significa que sua base de dados de conhecimento responde a previsões de consulta com êxito.

Se sua base de dados de conhecimento não estiver funcionando corretamente, normalmente será um problema de gerenciamento de recursos impróprio.

A seleção de recursos inadequados requer investigação para determinar qual [recurso precisa ser alterado](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Bases de dados de conhecimento

Uma base de dados de conhecimento está diretamente vinculada ao recurso QnA Maker. Ele contém os pares de perguntas e respostas (QnA) que são usados para responder às solicitações de previsão de consulta.

### <a name="language-considerations"></a>Considerações sobre o idioma

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

A primeira base de dados de conhecimento criada em seu QnA Maker recurso define o idioma do recurso. Você só pode ter um idioma para um recurso QnA Maker.

Você pode estruturar seus recursos de QnA Maker por idioma ou pode usar o [Tradutor](../../translator/translator-info-overview.md) para alterar uma consulta de outro idioma para o idioma da base de dados de conhecimento antes de enviar a consulta para o ponto de extremidade de previsão de consulta.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Agora você pode ter bases de dados de conhecimento em diferentes idiomas dentro do mesmo QnA Maker recurso. Ao criar a primeira base de dados de conhecimento, você pode escolher se deseja usar o recurso para bases de dados de conhecimento em um único idioma ou em vários idiomas.

![Seleção da base de dados de conhecimento multilíngue (visualização) QnA Maker gerenciada (versão prévia)](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

> [!NOTE]
> Se você habilitar as configurações de idioma por base de dados de conhecimento, não poderá criar tantas bases de conhecimento em seu recurso de QnA Maker. Para obter [mais detalhes sobre as limitações de configurações de idioma](./azure-resources.md).

---

### <a name="ingest-data-sources"></a>Fontes de dados de ingestão

Você pode usar uma das seguintes [fontes de dados](../Concepts/data-sources-and-content.md) ingeridas para criar uma base de conhecimento:

* URL pública
* URL privada do SharePoint
* Arquivo

O processo de ingestão converte os [tipos de conteúdo com suporte](../reference-document-format-guidelines.md) para redução. Toda edição adicional da *resposta* é feita com a redução. Depois de criar uma base de dados de conhecimento, você pode editar [pares de QnA](question-answer-set.md) no portal de QnA Maker com a [criação de Rich Text](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Considerações de formato de dados

Como o formato final de um par de QnA é a redução, é importante entender o [suporte de redução](../reference-markdown-format.md).

As imagens vinculadas devem estar disponíveis de uma URL pública para serem exibidas no painel de teste do portal de QnA Maker ou em um aplicativo cliente. O QnA Maker não fornece autenticação para conteúdo, incluindo imagens.

### <a name="bot-personality"></a>Personalidade do bot

Adicione uma personalidade de bot à sua base de dados de conhecimento com o [Chit-Chat](../how-to/chit-chat-knowledge-base.md). Essa personalidade é fornecida com as respostas fornecidas em um determinado Tom de conversação, como *profissional* e *amigável*. Esse Chit é fornecido como um conjunto de conversação, que você tem controle total para adicionar, editar e remover.

Uma personalidade de bot é recomendada se o bot se conectar à sua base de dados de conhecimento. Você pode optar por usar o Chit-Chat em sua base de dados de conhecimento mesmo se você também se conectar a outros serviços, mas você deve examinar como o serviço de bot interage para saber se esse é o design de arquitetura correto para seu uso.

### <a name="conversation-flow-with-a-knowledge-base"></a>Fluxo de conversa com uma base de dados de conhecimento

O fluxo de conversa geralmente começa com uma saudação de um usuário, como `Hi` ou `Hello` . Sua base de dados de conhecimento pode responder com uma resposta geral, como `Hi, how can I help you` , e também pode fornecer uma seleção de avisos de acompanhamento para continuar a conversa.

Você deve projetar seu fluxo de conversação com um loop em mente para que um usuário saiba como usar o bot e não seja abandonado pelo bot na conversa. Os [prompts de acompanhamento](../how-to/multiturn-conversation.md) fornecem vinculação entre pares de QnA, que permitem o fluxo de conversação.

### <a name="authoring-with-collaborators"></a>Criando com colaboradores

Os colaboradores podem ser outros desenvolvedores que compartilham a pilha de desenvolvimento completa do aplicativo da base de dados de conhecimento ou podem estar limitados a apenas criar a base de dados de conhecimento.

A criação da base de dados de conhecimento dá suporte a várias permissões de acesso baseadas em função que você aplica no portal do Azure para limitar o escopo das habilidades de um colaborador.

## <a name="integration-with-client-applications"></a>Integração com aplicativos cliente

A integração com aplicativos cliente é realizada enviando uma consulta para o ponto de extremidade de tempo de execução de previsão. Uma consulta é enviada para sua base de dados de conhecimento específica com um SDK ou uma solicitação baseada em REST para o ponto de extremidade do aplicativo Web do QnA Maker.

Para autenticar uma solicitação de cliente corretamente, o aplicativo cliente deve enviar as credenciais corretas e a ID da base de dados de conhecimento. Se você estiver usando um serviço de bot do Azure, defina essas configurações como parte da configuração do bot no portal do Azure.

### <a name="conversation-flow-in-a-client-application"></a>Fluxo de conversa em um aplicativo cliente

O fluxo de conversa em um aplicativo cliente, como um bot do Azure, pode exigir funcionalidade antes e depois de interagir com a base de dados de conhecimento.

O aplicativo cliente dá suporte ao fluxo de conversa, seja fornecendo meios alternativos para lidar com avisos de acompanhamento ou incluindo Chit-Chit? Nesse caso, projete esses primeiros e verifique se a consulta do aplicativo cliente é tratada corretamente por outro serviço ou quando enviada à sua base de dados de conhecimento.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>Expedição entre QnA Maker e Reconhecimento vocal (LUIS)

Um aplicativo cliente pode fornecer vários recursos, apenas um dos quais é respondido por uma base de dados de conhecimento. Outros recursos ainda precisam compreender o texto de conversação e extrair o significado dele.

Uma arquitetura de aplicativo cliente comum é usar o QnA Maker e o [reconhecimento vocal (Luis)](../../LUIS/what-is-luis.md) juntos. O LUIS fornece a classificação de texto e extração para qualquer consulta, incluindo outros serviços. QnA Maker fornece respostas de sua base de dados de conhecimento.

Nesse cenário de [arquitetura compartilhada](../choose-natural-language-processing-service.md) , o despacho entre os dois serviços é realizado pela ferramenta de [expedição](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) do bot Framework.

### <a name="active-learning-from-a-client-application"></a>Aprendizado ativo de um aplicativo cliente

O QnA Maker usa o _aprendizado ativo_ para melhorar sua base de dados de conhecimento sugerindo perguntas alternativas para uma resposta. O aplicativo cliente é responsável por uma parte deste [aprendizado ativo](../How-To/use-active-learning.md). Por meio de prompts de conversação, o aplicativo cliente pode determinar se a base de dados de conhecimento retornou uma resposta que não é útil para o usuário e pode determinar uma resposta melhor. O aplicativo cliente precisa enviar essas informações de volta para a base de dados de conhecimento para melhorar a qualidade da previsão.

### <a name="providing-a-default-answer"></a>Fornecendo uma resposta padrão

Se sua base de dados de conhecimento não encontrar uma resposta, ela retornará a _resposta padrão_. Essa resposta é configurável na página **configurações** no portal QnA Maker ou nas [APIs](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Essa resposta padrão é diferente da resposta padrão do bot do Azure. Você configura a resposta padrão para o bot do Azure no portal do Azure como parte das definições de configuração. Ele é retornado quando o limite de pontuação não é atendido.

## <a name="prediction"></a>Previsão

A previsão é a resposta da sua base de dados de conhecimento e inclui mais informações do que apenas a resposta. Para obter uma resposta de previsão de consulta, use a [API GenerateAnswer](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Flutuações de Pontuação de previsão

Uma pontuação pode mudar com base em vários fatores:

* Número de respostas solicitadas em resposta a [GenerateAnswer](query-knowledge-base.md) com a `top` Propriedade
* Variedade de perguntas alternativas disponíveis
* Filtragem de metadados
* Consulta enviada para `test` ou `production` base de dados de conhecimento

Há uma [classificação de resposta de duas fases](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
- Classificação Pesquisa Cognitiva primeiro. Defina o número de _respostas permitidas_ em alta o suficiente para que as melhores respostas sejam retornadas por pesquisa cognitiva e, em seguida, passadas para o classificador de QnA Maker.
- Classificação de QnA Maker segundo. Aplique o personalização e o Machine Learning para determinar a melhor resposta.

### <a name="service-updates"></a>Atualizações de serviço

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Aplique as [atualizações de tempo de execução mais recentes](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) para gerenciar atualizações de serviço automaticamente.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

No QnA Maker gerenciado (versão prévia), o tempo de execução é gerenciado pelo próprio serviço QnA Maker. Portanto, as atualizações de serviço não são aplicáveis.

---

### <a name="scaling-throughput-and-resiliency"></a>Dimensionamento, taxa de transferência e resiliência

O dimensionamento, a taxa de transferência e a resiliência são determinados pelos [recursos do Azure](../how-to/set-up-qnamaker-service-azure.md), seus tipos de preço e qualquer arquitetura ao redor, como o [Gerenciador de tráfego](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Análise com Application Insights

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Todas as consultas à sua base de dados de conhecimento são armazenadas em Application Insights. Use nossas [principais consultas](../how-to/get-analytics-knowledge-base.md) para entender suas métricas.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Na implantação gerenciada, a telemetria é oferecida por meio do [serviço de Azure monitor](../../../azure-monitor/index.yml). Use nossas [principais consultas](../how-to/get-analytics-knowledge-base.md) para entender suas métricas.


---

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

O [ciclo de vida de desenvolvimento](development-lifecycle-knowledge-base.md) de uma base de dados de conhecimento é contínuo: editar, testar e publicar sua base de dados de conhecimento.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Desenvolvimento da base de dados de conhecimento de pares de QnA Maker

Seus pares de QnA devem ser projetados e desenvolvidos com base no uso do aplicativo cliente.

Cada par pode conter:
* Metadados-filtráveis ao consultar para permitir que você marque seus pares de QnA com informações adicionais sobre a origem, o conteúdo, o formato e a finalidade de seus dados.
* Prompts de acompanhamento – ajuda a determinar um caminho por meio de sua base de dados de conhecimento para que o usuário chegue à resposta correta.
* Perguntas alternativas – importante para permitir que a pesquisa corresponda à sua resposta de diferentes formas da pergunta. As [sugestões de aprendizado ativo são transformadas](../How-To/use-active-learning.md) em perguntas alternativas.

### <a name="devops-development"></a>Desenvolvimento de DevOps

Desenvolver uma base de dados de conhecimento para inserir em um pipeline do DevOps requer que a base de dados de conhecimento seja isolada durante o teste do lote.

Uma base de dados de conhecimento compartilha o índice de Pesquisa Cognitiva com todas as outras bases de dados de conhecimento no recurso de QnA Maker. Embora a base de dados de conhecimento seja isolada por partição, o compartilhamento do índice pode causar uma diferença na pontuação quando comparado à base de dados de conhecimento publicada.

Para ter a _mesma pontuação_ nas `test` bases de `production` conhecimento e, isole um recurso QnA Maker a uma única base de dados de conhecimento. Nessa arquitetura, o recurso só precisa residir, desde que o teste em lote isolado.

## <a name="next-steps"></a>Próximas etapas

* [Recursos do Azure](../how-to/set-up-qnamaker-service-azure.md)
* [Pares de pergunta e resposta](question-answer-set.md)
