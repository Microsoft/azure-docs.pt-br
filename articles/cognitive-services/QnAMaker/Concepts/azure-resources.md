---
title: Recursos do Azure-QnA Maker
description: O QnA Maker usa várias fontes do Azure, cada uma com uma finalidade diferente. Entender como eles são usados individualmente permite que você planeje e selecione o tipo de preço correto ou saiba quando alterar o tipo de preço. Entender como eles são usados em combinação permite localizar e corrigir problemas quando eles ocorrem.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 2427fbdaa497ccb6d9a46330dcc6eb872e1d28ac
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214160"
---
# <a name="azure-resources-for-qna-maker"></a>Recursos do Azure para QnA Maker

O QnA Maker usa várias fontes do Azure, cada uma com uma finalidade diferente. Entender como eles são usados individualmente permite que você planeje e selecione o tipo de preço correto ou saiba quando alterar o tipo de preço. Entender como eles são usados _em combinação_ permite localizar e corrigir problemas quando eles ocorrem.

## <a name="resource-planning"></a>Planejamento de recursos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Ao desenvolver pela primeira vez uma base de dados de conhecimento QnA Maker, na fase de protótipo, é comum ter um único recurso de QnA Maker para teste e produção.

Ao passar para a fase de desenvolvimento do projeto, você deve considerar:

* Quantos idiomas seu sistema de base de dados de conhecimento irá manter?
* De quantas regiões você precisa que sua base de dados de conhecimento esteja disponível?
* Quantos documentos em cada domínio seu sistema irá manter?

Planeje ter um único recurso de QnA Maker conter todas as bases de dados de conhecimento que têm o mesmo idioma, a mesma região e a mesma combinação de domínio de assunto.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Ao desenvolver pela primeira vez uma base de dados de conhecimento gerenciada QnA Maker, na fase de protótipo, é comum ter um único recurso gerenciado de QnA Maker para teste e produção.

Ao passar para a fase de desenvolvimento do projeto, você deve considerar:

* Quantos idiomas seu sistema de base de dados de conhecimento irá manter?
* De quantas regiões você precisa que sua base de dados de conhecimento esteja disponível?
* Quantos documentos em cada domínio seu sistema irá manter?

---

## <a name="pricing-tier-considerations"></a>Considerações sobre o tipo de preço

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Normalmente, há três parâmetros que você precisa considerar:

* **A taxa de transferência que você precisa do serviço**:
    * selecione o [Plano de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/) apropriado para o serviço de aplicativo com base em suas necessidades. Você pode [escalar verticalmente](../../../app-service/manage-scale-up.md) ou reduzir verticalmente o Aplicativo.
    * Isso também deve influenciar sua seleção de SKU **pesquisa cognitiva** do Azure, veja mais detalhes [aqui](../../../search/search-sku-tier.md). Além disso, talvez seja necessário ajustar Pesquisa Cognitiva [capacidade](../../../search/search-capacity-planning.md) com réplicas.

* **Tamanho e o número de bases de dados de conhecimento**: escolha a [SKU de pesquisa do Azure](https://azure.microsoft.com/pricing/details/search/) apropriada para seu cenário. Normalmente, você decide o número de bases de dados de conhecimento de que precisa com base no número de domínios de assunto diferentes. Uma vez que o domínio do assunto (para um único idioma) deve estar em uma base de dados de conhecimento.

    Você pode publicar as bases de dados de conhecimento N-1 em uma camada específica, onde N representa os índices máximos permitidos na camada. Verifique também o tamanho máximo e o número de documentos permitidas por camada.

    Por exemplo, se a camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicada). O décimo quinto índice é usado para todas as bases de dados de conhecimento para criação e teste.

* **Número de documentos como fontes**: a SKU gratuita do serviço de gerenciamento do QnA Maker limita o número de documentos que você pode gerenciar por meio do portal e as APIs a três (cada uma com 1 MB de tamanho). O padrão de SKU não tem limites para o número de documentos que você pode gerenciar. Veja mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela a seguir fornece algumas diretrizes de alto nível.

|                            | Gerenciamento do QnA Maker | Serviço de Aplicativo | Pesquisa Cognitiva do Azure | Limitações                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Experimentação**        | SKU gratuito             | Camada Gratuita   | Camada Gratuita    | Publicar até 2 KB/s, tamanho de 50 MB  |
| **Ambiente de Desenvolvimento/Teste**   | SKU Standard         | Compartilhado      | Basic        | Publicar até 14 KBs, com tamanho de 2 GB    |
| **Ambiente de produção** | SKU Standard         | Basic       | Standard     | Publicar até 49 KBs, tamanho de 25 GB |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Normalmente, há três parâmetros que você precisa considerar:

* **A taxa de transferência que você precisa do serviço**:
    * O QnA Maker gerenciado (versão prévia) é um serviço gratuito, e a taxa de transferência está atualmente limitada a 10 TPS para APIs de gerenciamento e APIs de previsão.
    * Isso também deve influenciar sua seleção de SKU **pesquisa cognitiva** do Azure, veja mais detalhes [aqui](../../../search/search-sku-tier.md). Além disso, talvez seja necessário ajustar Pesquisa Cognitiva [capacidade](../../../search/search-capacity-planning.md) com réplicas.

* **Tamanho e o número de bases de dados de conhecimento**: escolha a [SKU de pesquisa do Azure](https://azure.microsoft.com/pricing/details/search/) apropriada para seu cenário. Normalmente, você decide o número de bases de dados de conhecimento de que precisa com base no número de domínios de assunto diferentes. Uma vez que o domínio do assunto (para um único idioma) deve estar em uma base de dados de conhecimento.

    Com o QnA Maker gerenciado (versão prévia), você tem a opção de configurar seu serviço de QnA Maker para KBs em uma única linguagem ou em vários idiomas. Você pode fazer essa seleção ao criar a primeira base de dados de conhecimento em seu serviço QnA Maker gerenciado (versão prévia).

    ![Seleção da base de dados de conhecimento multilíngue (visualização) QnA Maker gerenciada (versão prévia)](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    Você pode publicar N-1 bases de dados de conhecimento de uma única linguagem ou N/2 bases de dados de conhecimento de diferentes idiomas em uma determinada camada, em que N é o máximo de índices permitidos na camada. Verifique também o tamanho máximo e o número de documentos permitidas por camada.

    Por exemplo, se sua camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento do mesmo idioma (1 índice por base de dados de conhecimento publicado). O décimo quinto índice é usado para todas as bases de dados de conhecimento para criação e teste. Se você optar por ter bases de dados de conhecimento em idiomas diferentes, você só poderá publicar 7 bases de dados de conhecimento.

* **Número de documentos como fontes**: QnA Maker gerenciado (versão prévia) é um serviço gratuito e não há limites para o número de documentos que você pode adicionar como fontes. Veja mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela a seguir fornece algumas diretrizes de alto nível.

|                            |Pesquisa Cognitiva do Azure | Limitações                      |
| -------------------------- |------------ | -------------------------------- |
| **Experimentação**        |Camada Gratuita    | Publicar até 2 KB/s, tamanho de 50 MB  |
| **Ambiente de Desenvolvimento/Teste**   |Basic        | Publicar até 14 KBs, com tamanho de 2 GB    |
| **Ambiente de produção** |Standard     | Publicar até 49 KBs, tamanho de 25 GB |

---

## <a name="recommended-settings"></a>Configurações recomendadas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

|QPS de destino | Serviço de Aplicativo | Pesquisa Cognitiva do Azure |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 réplica   | S1, 1 réplica    |
| 50         | S3, 10 réplicas       | S1, 12 réplicas         |
| 80         | S3, 10 réplicas      |  S3, 12 réplicas  |
| 100         | P3V2, 10 réplicas  | S3, 12 réplicas, 3 partições   |
| 200 a 250         | P3V2, 20 réplicas | S3, 12 réplicas, 3 partições    |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

O QnA Maker gerenciado é um serviço gratuito, e a taxa de transferência está atualmente limitada a 10 transações por segundo para APIs de gerenciamento e APIs de previsão. Para direcionar 10 transações por segundo para seu serviço, recomendamos a SKU S1 (1 instância) do Azure Pesquisa Cognitiva.

---

## <a name="when-to-change-a-pricing-tier"></a>Quando alterar um tipo de preço

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

|Atualizar|Motivo|
|--|--|
|[Atualizar](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) o SKU de gerenciamento de QnA Maker|Você deseja ter mais pares de QnA ou fontes de documento na sua base de dados de conhecimento.|
|[Atualizar](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) o SKU do serviço de aplicativo e verificar Pesquisa Cognitiva camada e [criar réplicas de pesquisa cognitiva](../../../search/search-capacity-planning.md)|Sua base de dados de conhecimento precisa atender a mais solicitações do aplicativo cliente, como um bot de chat.|
|[Atualizar](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) o Serviço de Pesquisa Cognitiva do Azure|Você planeja ter muitas bases de dados de conhecimento.|

Obtenha as atualizações de tempo de execução mais recentes [atualizando seu serviço de aplicativo no portal do Azure](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

[Atualizar](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) o O serviço de Pesquisa Cognitiva do Azure quando você planeja ter muitas bases de dados de conhecimento.

---

## <a name="keys-in-qna-maker"></a>Chaves no QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Seu serviço de QnA Maker lida com dois tipos de chaves: chaves de **criação** e **chaves de ponto de extremidade de consulta** usadas com o tempo de execução hospedado no serviço de aplicativo.

Use essas chaves ao fazer solicitações ao serviço por meio de APIs.

![Gerenciamento de chaves](../media/authoring-key.png)

|Nome|Localização|Finalidade|
|--|--|--|
|Chave de criação/assinatura|[Azure portal](https://azure.microsoft.com/free/cognitive-services/)|essas chaves são usadas para acessar as [APIs do serviço de gerenciamento do QnA Maker](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Essas APIs permitem que você edite as perguntas e respostas em sua base de dados de conhecimento e publique sua base de dados de conhecimento. Essas chaves são criadas quando você cria um novo serviço de QnA Maker.<br><br>Localize essas chaves no recurso **Serviços cognitivas** na página **chaves e ponto de extremidade** .|
|Chave do ponto de extremidade da consulta|[Portal do QnA Maker](https://www.qnamaker.ai)|Essas chaves são usadas para consultar o ponto de extremidade da base de dados de conhecimento publicado para obter uma resposta para uma pergunta de usuário. Normalmente, você usa esse ponto de extremidade de consulta em seu bot de chat ou no código do aplicativo cliente que se conecta ao serviço de QnA Maker. Essas chaves são criadas quando você publica sua base de dados de conhecimento QnA Maker.<br><br>Localize essas chaves na página **configurações de serviço** . Localize essa página no menu do usuário no canto superior direito da página no menu suspenso.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Localizar chaves de criação no portal do Azure

Você pode exibir e redefinir as chaves de criação do portal do Azure, em que você criou o recurso de QnA Maker.

1. Vá para o recurso de QnA Maker na portal do Azure e selecione o recurso que tem o tipo de _Serviços cognitivas_ :

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vá para **chaves e ponto de extremidade**:

    ![Chave de assinatura gerenciada QnA Maker (versão prévia)](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>Localizar chaves de ponto de extremidade de consulta no portal de QnA Maker

O ponto de extremidade está na mesma região que o recurso porque as chaves de ponto de extremidade são usadas para fazer uma chamada para a base de dados de conhecimento.

As chaves de ponto de extremidade podem ser gerenciadas a partir do [portal do QnA Maker](https://qnamaker.ai).

1. Entre no portal de [QnA Maker](https://qnamaker.ai), acesse seu perfil e, em seguida, selecione **configurações de serviço**:

    ![Chave do ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Exibir ou redefinir suas chaves:

    > [!div class="mx-imgBorder"]
    > ![Gerenciador de chaves de ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Atualize suas chaves se você acreditar que elas foram comprometidas. Isso pode exigir que sejam feitas as alterações correspondentes no seu aplicativo cliente ou código bot.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Seu serviço gerenciado (versão prévia) QnA Maker lida com dois tipos de chaves: **chaves de criação** e chaves de **pesquisa cognitiva do Azure** usadas para acessar o serviço na assinatura do cliente.

Use essas chaves ao fazer solicitações ao serviço por meio de APIs.

![Visualização gerenciada do gerenciamento de chaves](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|Nome|Localização|Finalidade|
|--|--|--|
|Chave de criação/assinatura|[Azure portal](https://azure.microsoft.com/free/cognitive-services/)|essas chaves são usadas para acessar as [APIs do serviço de gerenciamento do QnA Maker](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Essas APIs permitem que você edite as perguntas e respostas em sua base de dados de conhecimento e publique sua base de dados de conhecimento. Essas chaves são criadas quando você cria um novo serviço de QnA Maker.<br><br>Localize essas chaves no recurso **Serviços cognitivas** na página **chaves e ponto de extremidade** .|
|Chave de administração de Pesquisa Cognitiva do Azure|[Azure portal](../../../search/search-security-api-keys.md)|Essas chaves são usadas para se comunicar com o serviço de pesquisa cognitiva do Azure implantado na assinatura do Azure do usuário. Quando você associa uma pesquisa cognitiva do Azure com o serviço QnA Maker gerenciado (versão prévia), a chave de administração é passada automaticamente para o serviço QnA Maker. <br><br>Você pode encontrar essas chaves no recurso **pesquisa cognitiva do Azure** na página **chaves** .|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Localizar chaves de criação no portal do Azure

Você pode exibir e redefinir as chaves de criação do portal do Azure, em que você criou o recurso QnA Maker gerenciado (versão prévia).

1. Vá para o QnA Maker recurso gerenciado (versão prévia) no portal do Azure e selecione o recurso que tem o tipo de *Serviços cognitivas* :

    ![Lista de recursos gerenciados QnA Maker (visualização)](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. Vá para **chaves e ponto de extremidade**:

    ![Chave de assinatura gerenciada QnA Maker (versão prévia)](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>Atualizar os recursos

Saiba como atualizar os recursos usados pela sua base de dados de conhecimento. QnA Maker gerenciado (versão prévia) é **gratuito** durante a visualização. 

---

## <a name="management-service-region"></a>Região do serviço de gerenciamento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

O serviço de gerenciamento do QnA Maker é usado somente para o portal de QnA Maker e para o processamento de dados inicial. Esse serviço está disponível apenas na região **oeste dos EUA** . Nenhum dado do cliente é armazenado neste serviço oeste dos EUA.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

No QnA Maker gerenciado (versão prévia), tanto o gerenciamento quanto os serviços de previsão estão colocalizados na mesma região. Atualmente QnA Maker gerenciado (versão prévia) está disponível no **sul EUA Central, Europa setentrional e leste da Austrália**.

---

## <a name="resource-naming-considerations"></a>Considerações sobre nomenclatura de recursos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

O nome do recurso para o recurso de QnA Maker, como `qna-westus-f0-b` , também é usado para nomear os outros recursos.

A portal do Azure criar janela permite que você crie um recurso de QnA Maker e selecione os tipos de preço para os outros recursos.

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure para a criação de recursos de QnA Maker](../media/concept-azure-resource/create-blade.png)

Depois que os recursos são criados, eles têm o mesmo nome, exceto para o recurso de Application Insights opcional, que postpends caracteres para o nome.

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure lista de recursos](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Crie um novo grupo de recursos ao criar um recurso de QnA Maker. Isso permite que você veja todos os recursos associados ao recurso de QnA Maker ao pesquisar por grupo de recursos.

> [!TIP]
> Use uma Convenção de nomenclatura para indicar os tipos de preço dentro do nome do recurso ou do grupo de recursos. Quando você recebe erros da criação de uma nova base de dados de conhecimento ou da adição de novos documentos, o limite de Pesquisa Cognitiva tipo de preço é um problema comum.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

O nome do recurso para o recurso QnA Maker gerenciado (versão prévia), como `qna-westus-f0-b` , também é usado para nomear os outros recursos.

O portal do Azure criar janela permite que você crie um recurso de QnA Maker gerenciado (versão prévia) e selecione os tipos de preço para os outros recursos.

> [!div class="mx-imgBorder"]
> ![Captura de tela de portal do Azure para a criação de recurso QnA Maker gerenciado (versão prévia) ](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) depois que os recursos são criados, eles têm o mesmo nome.

> [!div class="mx-imgBorder"]
> ![Captura de tela da listagem de recursos de portal do Azure QnA Maker gerenciada (versão prévia)](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

> [!TIP]
> Crie um novo grupo de recursos ao criar um recurso de QnA Maker. Isso permite que você veja todos os recursos associados ao recurso QnA Maker gerenciado (versão prévia) ao pesquisar por grupo de recursos.

> [!TIP]
> Use uma Convenção de nomenclatura para indicar os tipos de preço dentro do nome do recurso ou do grupo de recursos. Quando você recebe erros da criação de uma nova base de dados de conhecimento ou da adição de novos documentos, o limite de Pesquisa Cognitiva tipo de preço é um problema comum.

---

## <a name="resource-purposes"></a>Fins de recurso

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Cada recurso do Azure criado com QnA Maker tem uma finalidade específica:

* Recurso do QnA Maker
* Pesquisa Cognitiva recurso
* Serviço de Aplicativo
* Serviço de plano de aplicativo
* Serviço de Application Insights

### <a name="qna-maker-resource"></a>Recurso do QnA Maker

O recurso de QnA Maker fornece acesso às APIs de criação e publicação, bem como à segunda camada de classificação baseada em NLP (processamento de idioma natural) (Rank #2) dos pares de QnA em tempo de execução.

A segunda classificação aplica filtros inteligentes que podem incluir metadados e avisos de acompanhamento.

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker definições de configuração de recurso

Quando você cria uma nova base de dados de conhecimento no [portal de QnA Maker](https://qnamaker.ai), a configuração de **idioma** é a única que é aplicada no nível de recurso. Você seleciona o idioma ao criar a primeira base de dados de conhecimento para o recurso.

### <a name="cognitive-search-resource"></a>Pesquisa Cognitiva recurso

O recurso [pesquisa cognitiva](../../../search/index.yml) é usado para:

* Armazenar os pares de QnA
* Forneça a classificação inicial (Rank #1) dos pares de QnA em tempo de execução

#### <a name="index-usage"></a>Uso do índice

O recurso mantém um índice para atuar como o índice de teste e os índices restantes se correlacionam a uma base de dados de conhecimento publicada cada.

Um recurso com preço para conter 15 índices, manterá 14 bases de dados de conhecimento publicadas e um índice é usado para testar todas as bases de dados de conhecimento. Esse índice de teste é particionado pela base de dados de conhecimento para que uma consulta usando o painel de teste interativo use o índice de teste, mas apenas retorne os resultados da partição específica associada à base de dados de conhecimento específica.

#### <a name="language-usage"></a>Uso do idioma

A primeira base de dados de conhecimento criada no recurso QnA Maker é usada para determinar o conjunto de idiomas _único_ para o recurso pesquisa cognitiva e todos os seus índices. Você só pode ter _um conjunto de idiomas_ para um serviço de QnA Maker.

#### <a name="using-a-single-cognitive-search-service"></a>Usando um único serviço de Pesquisa Cognitiva

Se você criar um serviço QnA e suas dependências (como pesquisa) por meio do portal, um serviço de pesquisa será criado para você e vinculado ao serviço de QnA Maker. Depois que esses recursos forem criados, você poderá atualizar a configuração do serviço de aplicativo para usar um serviço de pesquisa existente anteriormente e remover o que você acabou de criar.

Saiba [como configurar](../How-To/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker para usar um recurso de serviço cognitiva diferente daquele criado como parte do processo de criação de recursos QnA Maker.

### <a name="app-service-and-app-service-plan"></a>Serviço de aplicativo e plano do serviço de aplicativo

O [serviço de aplicativo](../../../app-service/index.yml) é usado pelo aplicativo cliente para acessar as bases de dados de conhecimento publicadas por meio do ponto de extremidade de tempo de execução.

Para consultar a base de dados de conhecimento publicada, todas as bases de conhecimento publicadas usam o mesmo ponto de extremidade de URL, mas especificam a **ID da base de dados de conhecimento** dentro da rota

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application insights](../../../azure-monitor/app/app-insights-overview.md) é usado para coletar logs de chat e telemetria. Examine as consultas comuns do [Kusto](../how-to/get-analytics-knowledge-base.md) para obter informações sobre seu serviço.

### <a name="share-services-with-qna-maker"></a>Compartilhar serviços com o QnA Maker

QnA Maker cria vários recursos do Azure. Para reduzir o gerenciamento e beneficiar-se do compartilhamento de custos, use a tabela a seguir para entender o que você pode e não consegue compartilhar:

|Serviço|Compartilhar|Motivo|
|--|--|--|
|Serviços Cognitivos|X|Não é possível por design|
|Plano do Serviço de Aplicativo|✔|Espaço em disco fixo alocado para um plano do serviço de aplicativo. Se outros aplicativos que compartilham o mesmo plano do serviço de aplicativo usarem um espaço em disco significativo, a instância do serviço de aplicativo do QnAMaker encontrará problemas.|
|Serviço de Aplicativo|X|Não é possível por design|
|Application Insights|✔|Pode ser compartilhada|
|Serviço Search|✔|1. `testkb` é um nome reservado para o serviço QnAMaker; ele não pode ser usado por outros.<br>2. o mapa de sinônimos pelo nome `synonym-map` é reservado para o serviço QnAMaker.<br>3. o número de bases de dados de conhecimento publicadas é limitado pela camada de serviço de pesquisa. Se houver índices livres disponíveis, outros serviços poderão usá-los.|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Cada recurso do Azure criado com QnA Maker gerenciado (versão prévia) tem uma finalidade específica:

* Recurso do QnA Maker
* Pesquisa Cognitiva recurso

### <a name="qna-maker-resource"></a>Recurso do QnA Maker

O recurso gerenciado QnA Maker (versão prévia) fornece acesso às APIs de criação e publicação, hospeda o tempo de execução de classificação, bem como fornece telemetria.

### <a name="azure-cognitive-search-resource"></a>Recurso de Pesquisa Cognitiva do Azure

O recurso [pesquisa cognitiva](../../../search/index.yml) é usado para:

* Armazenar os pares de QnA
* Forneça a classificação inicial (Rank #1) dos pares de QnA em tempo de execução

#### <a name="index-usage"></a>Uso do índice

Você pode publicar N-1 bases de dados de conhecimento de uma única linguagem ou N/2 bases de dados de conhecimento de diferentes idiomas em uma determinada camada, em que N é o máximo de índices permitidos na camada de Pesquisa Cognitiva do Azure. Verifique também o tamanho máximo e o número de documentos permitidas por camada.

Por exemplo, se sua camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento do mesmo idioma (1 índice por base de dados de conhecimento publicado). O décimo quinto índice é usado para todas as bases de dados de conhecimento para criação e teste. Se você optar por ter bases de dados de conhecimento em idiomas diferentes, você só poderá publicar 7 bases de dados de conhecimento.

#### <a name="language-usage"></a>Uso do idioma

Com o QnA Maker gerenciado (versão prévia), você tem a opção de configurar seu serviço de QnA Maker para bases de dados de conhecimento em uma única linguagem ou em vários idiomas. Você faz essa escolha durante a criação da primeira base de dados de conhecimento em seu serviço de QnA Maker. Veja [aqui](#pricing-tier-considerations) como habilitar a configuração de idioma por base de dados de conhecimento.

---

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [base de dados de conhecimento](../How-To/manage-knowledge-bases.md) QnA Maker
* Entender um [ciclo de vida da base de dados de conhecimento](development-lifecycle-knowledge-base.md)
* Examinar [os limites](../limits.md) do serviço e da base de dados de conhecimento
