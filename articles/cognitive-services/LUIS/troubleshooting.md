---
title: Perguntas frequentes – LUIS
description: Este artigo contém as respostas para perguntas frequentes sobre o LUIS (Serviço Inteligente de Reconhecimento Vocal).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 05/06/2020
ms.openlocfilehash: e4de77a8a56ce21511ad099af59a77e10ab1815d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787529"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o LUIS (Serviço Inteligente de Reconhecimento Vocal)

Este artigo contém as respostas para perguntas frequentes sobre o LUIS (Serviço Inteligente de Reconhecimento Vocal).

## <a name="whats-new"></a>Novidades

[Saiba mais](whats-new.md) sobre as novidades do reconhecimento vocal (Luis).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Criação

### <a name="what-are-the-luis-best-practices"></a>Quais são as práticas recomendadas de LUIS?
Comece com o [Ciclo de Criação](luis-concept-app-iteration.md) e, em seguida, leia as [melhores práticas](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Qual é a melhor maneira de começar a criar o meu aplicativo de LUIS?

A melhor maneira de criar seu aplicativo é por meio de um [processo incremental](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Qual é uma boa prática para modelar as intenções do meu aplicativo? Devo criar intenções mais específicas ou mais genéricas?

Escolha intenções que não sejam tão gerais a ponto de sobreporem-se, nem tão específica que seja difícil para o LUIS distinguir entre intenções semelhantes. Criar intenções discriminantes específicas é uma das melhores práticas para modelagem de LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>É importante treinar a intenção None?

Sim, é bom treinar sua intenção **None** com mais enunciados conforme você adiciona mais rótulos a outras intenções. Uma boa proporção é 1 ou 2 rótulos adicionados a **None** para cada 10 rótulos adicionados a uma intenção. Essa proporção aumenta a capacidade discriminativa de LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Como poso corrigir erros de ortografia em enunciados?

Veja o tutorial [API de Verificação Ortográfica do Bing V7](luis-tutorial-bing-spellcheck.md). O LUIS impõe limites exigidos pela API de Verificação Ortográfica do Bing V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Como fazer para editar meu aplicativo de LUIS programaticamente?
Para editar seu aplicativo de LUIS programaticamente, use a [API de Criação](https://go.microsoft.com/fwlink/?linkid=2092087). Veja [Chamar a API de criação LUIS](./get-started-get-model-rest-apis.md) e [Compilar um aplicativo de LUIS programaticamente usando Node.js](./luis-tutorial-node-import-utterances-csv.md) para obter exemplos de como chamar a API de Criação. A API de Criação exige que você use uma [chave de criação](luis-how-to-azure-subscription.md#azure-resources-for-luis), em vez de uma chave de ponto de extremidade. A criação programática permite até um milhão de chamadas por mês e cinco transações por segundo. Para obter mais informações sobre as chaves que você usa com o LUIS, consulte [Gerenciar chaves](./luis-how-to-azure-subscription.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Em local está o recurso de Padrão que forneceu a correspondência da expressão regular?
O **Recurso de Padrão** anterior atualmente foi preterido, tendo sido substituído por **[Padrões](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Como fazer para usar uma entidade para extrair os dados corretos?
Veja [entidades](luis-concept-entity-types.md) e [extração de dados](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Variações de um enunciado de exemplo devem incluir pontuação?
use uma das seguintes soluções:
* Ignorar [Pontuação](luis-reference-application-settings.md#punctuation-normalization)
* Adicione as diferentes variações como exemplo declarações à intenção
* Adicione o padrão do exemplo expressão com a [sintaxe para ignorar](luis-concept-patterns.md#pattern-syntax) a pontuação.

### <a name="does-luis-currently-support-cortana"></a>O LUIS atualmente dá suporte à Cortana?

Os aplicativos pré-compilados da Cortana foram preteridos em 2017. Não há mais suporte para esses aplicativos.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Como fazer para transferir a propriedade de um aplicativo de LUIS?
Para transferir um aplicativo de LUIS para uma assinatura do Azure diferente, exporte o aplicativo de LUIS e importe-lo usando uma nova conta. Atualize a ID do aplicativo de LUIS no aplicativo cliente que o chama. O novo aplicativo pode retornar pontuações de LUIS ligeiramente diferentes que as do aplicativo original.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Uma entidade predefinida é marcada em um exemplo de expressão em vez de minha entidade personalizada. Como fazer corrigir isso?

No portal do LUIS, você pode rotular o texto para a entidade exata que você está interessado em extrair. Se o portal do LUIS não estiver mostrando a previsão de entidade correta, talvez seja necessário adicionar mais declarações e rotular a entidade dentro do texto ou adicionar um recurso.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Tentei importar um arquivo de aplicativo ou de versão, mas recebi um erro, o que aconteceu?

Leia mais sobre [erros de importação de versão](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Colaborando e contribuindo

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Como fazer fornecer aos colaboradores acesso ao LUIS com Azure Active Directory (Azure AD) ou controle de acesso baseado em função do Azure (RBAC do Azure)?

Confira [Recursos do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-resources) e [Usuário do locatário do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber como conceder acesso aos colaboradores.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Ponto de Extremidade

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Recebi um código de status de erro HTTP 403. Como corrigi-la?

Você obtém códigos de status de erro 403 e 429 ao exceder as transações por segundo ou transações por mês para o tipo de preço. Aumente seu tipo de preço ou use [contêineres](luis-container-howto.md)de reconhecimento vocal.

Quando você usar todas essas consultas de ponto de extremidade 1000 gratuitas ou exceder a cota de transações mensais do seu tipo de preço, você receberá um código de status de erro HTTP 403.

Para corrigir esse erro, você precisa [alterar seu tipo de preço](luis-how-to-azure-subscription.md#change-the-pricing-tier) para uma camada superior ou [criar um novo recurso](get-started-portal-deploy-app.md#create-the-endpoint-resource) e [atribuí-lo ao seu aplicativo](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

As soluções para esse erro incluem:

* No [portal do Azure](https://portal.azure.com), em seu recurso de reconhecimento vocal, no **tipo de preço gerenciamento de recursos->**, altere o tipo de preço para uma camada mais alta do TPS. Você não precisa fazer nada no portal de Reconhecimento vocal se o recurso já estiver atribuído ao seu aplicativo Reconhecimento vocal.
*  Se seu uso exceder o tipo de preço mais alto, adicione mais recursos Reconhecimento vocal com um balanceador de carga na frente deles. O [contêiner reconhecimento vocal](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Recebi um código de status de erro HTTP 429. Como corrigi-la?

Você obtém códigos de status de erro 403 e 429 ao exceder as transações por segundo ou transações por mês para o tipo de preço. Aumente seu tipo de preço ou use [contêineres](luis-container-howto.md)de reconhecimento vocal.

Esse código de status é retornado quando suas transações por segundo excedem seu tipo de preço.

As soluções incluem:

* Você pode [aumentar seu tipo de preço](luis-how-to-azure-subscription.md#change-the-pricing-tier), se não estiver na camada mais alta.
* Se seu uso exceder o tipo de preço mais alto, adicione mais recursos Reconhecimento vocal com um balanceador de carga na frente deles. O [contêiner reconhecimento vocal](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.
* Você pode portar suas solicitações de aplicativo cliente com uma [política de repetição](/azure/architecture/best-practices/transient-faults#general-guidelines) que você mesmo implementa quando obtém esse código de status.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Minha consulta de terminal retornou resultados inesperados. O que devo fazer?

Os resultados inesperados de previsão de consulta baseiam-se no estado do modelo publicado. Para corrigir o modelo, talvez seja necessário alterar, treinar e publicá-lo novamente.

A correção do modelo começa com o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md).

Você pode remover o treinamento não determinístico atualizando a [ API de configurações da versão do aplicativo ](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) para usar todos os dados de treinamento.

Examine as [práticas recomendadas](luis-concept-best-practices.md) para obter outras dicas.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Por que o LUIS adiciona espaços à consulta ao redor ou no meio das palavras?
O LUIS transforma em [token](luis-glossary.md#token) o enunciado com base na [cultura](luis-language-support.md#tokenization). O valor original e o valor em token estão disponíveis para [extração de dados](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Como fazer para criar e atribuir uma chave de ponto de extremidade de LUIS?
[Crie a chave do ponto de extremidade](luis-how-to-azure-subscription.md) no Azure para seu nível de [serviço](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). [Atribua a chave](luis-how-to-azure-subscription.md) na página de **[recursos do Azure](luis-how-to-azure-subscription.md)** . Não há uma API correspondente para essa ação. Em seguida, você deve alterar a solicitação HTTP para o ponto de extremidade para [usar a nova chave de ponto de extremidade](luis-how-to-azure-subscription.md).

### <a name="how-do-i-interpret-luis-scores"></a>Como fazer para interpretar as pontuações de LUIS?
Seu sistema deve usar a intenção de pontuação mais alta, independentemente do seu valor. Por exemplo, uma pontuação inferior a 0,5 (menor que 50%) não significa necessariamente que o LUIS tem confiança baixa. Fornecer mais dados de treinamento pode ajudar a aumentar a [Pontuação](luis-concept-prediction-score.md) da intenção mais provável.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Por que eu não vejo minhas ocorrências de ponto de extremidade no painel do meu aplicativo?
O total de pontos de extremidade no Painel do aplicativo é atualizado periodicamente, mas as métricas associadas à chave de ponto de extremidade de LUIS no portal do Azure são atualizadas com mais frequência.

Se você não vir ocorrências de ponto de extremidade atualizadas no Painel, entre no portal do Azure e localize o recurso associado à chave do ponto de extremidade do LUIS e, em seguida, abra **Métricas** para escolher a métrica **Total de Chamadas**. Se a chave de ponto de extremidade for usada para mais de um aplicativo de LUIS, a métrica no portal do Azure mostrará o número agregado de chamadas de todos os aplicativos de LUIS que a utilizam.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Há um comando get do PowerShell para a cota de ponto de extremidade?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

É possível usar um comando do PowerShell para ver a cota de ponto de extremidade:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Meu aplicativo de LUIS estava funcionando ontem, mas hoje estou recebendo erros 403. Eu não alterei o aplicativo. Como corrigi-la?
Siga estas [instruções](#how-do-i-create-and-assign-a-luis-endpoint-key) para criar uma chave de ponto de extremidade Luis e atribuí-la ao aplicativo. Em seguida, você deve alterar a solicitação HTTP do aplicativo cliente para o ponto de extremidade para [usar a nova chave de ponto de extremidade](luis-how-to-azure-subscription.md). Se você criou um novo recurso em uma região diferente, altere também a região da solicitação de cliente HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Como fazer para protejar o ponto de extremidade de LUIS?
Veja [Protegendo o ponto de extremidade](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Trabalhando dentro dos limites de LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Qual é o número máximo de intenções e entidades compatível com um aplicativo de LUIS?
Veja a referência de [limites](luis-limits.md).

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Quero compilar um aplicativo de LUIS com mais do que o número máximo de intenções. O que devo fazer?

Veja [Melhores práticas para intenções](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Quero compilar um aplicativo de LUIS com mais do que o número máximo de entidades. O que devo fazer?

Veja [Melhores práticas para entidades](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Quais são os limites de número e tamanho das listas de frases?
Para o tamanho máximo de uma [lista de frases](./luis-concept-feature.md), veja a referência de [limites](luis-limits.md).

### <a name="what-are-the-limits-on-example-utterances"></a>Quais são os limites nos enunciados de exemplo?
Veja a referência de [limites](luis-limits.md).

## <a name="testing-and-training"></a>Teste e treinamento

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Vejo alguns erros no painel de teste em lote para alguns dos modelos de meu aplicativo. Como posso solucionar esse problema?

Os erros indicam que há alguma discrepância entre os rótulos e as previsões de seus modelos. Para resolver o problema, realize uma ou ambas as seguintes tarefas:
* Para ajudar o LUIS a melhorar a discriminação entre intenções, adicione mais rótulos.
* Para ajudar o LUIS a aprender mais rápido, adicione recursos de lista de frases que introduzam vocabulário específico do domínio.

Veja o tutorial [Teste em lote](./luis-how-to-batch-test.md).

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Quando um aplicativo é exportado e reimportado em um novo aplicativo (com uma nova ID do aplicativo), as pontuações de previsão de LUIS são diferentes. Por que isso acontece?

Veja [Diferenças de previsão entre cópias do mesmo aplicativo](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Algumas declarações vão para a intenção errada depois que fiz alterações no meu aplicativo. O problema parece sumir aleatoriamente. Como corrigi-la?

Confira [Treinar com todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publicação de aplicativo

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Qual é a ID de locatário na janela "Adicionar uma chave ao seu aplicativo"?
No Azure, um locatário representa o cliente ou a organização que está associada a um serviço. Localize sua ID de locatário no portal do Azure na caixa **ID do Diretório** selecionando **Azure Active Directory** > **Gerenciar** > **Propriedades**.

![ID de locatário no portal do Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Por que há mais chaves de ponto de extremidade atribuídas ao meu aplicativo do que eu atribuí?
Cada aplicativo de LUIS tem a chave de criação/início na lista do ponto de extremidade como conveniência. Essa chave permite apenas algumas ocorrências de ponto de extremidade para que você possa experimentar o LUIS.

Se seu aplicativo existia antes da GA (disponibilidade geral) do LUIS, as chaves de ponto de extremidade do LUIS em sua assinatura são atribuídas automaticamente. Isso foi feito para facilitar a migração de GA. Quaisquer novas chaves de ponto de extremidade do LUIS no portal do Azure _não_ são atribuídas automaticamente aos LUIS.

## <a name="key-management"></a>Gerenciamento de chaves

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Como sei qual chave preciso, onde obtê-la e o que fazer com ela?

Consulte [criação e consulta de chaves de ponto de extremidade de previsão em Luis](luis-how-to-azure-subscription.md) para saber mais sobre as diferenças entre a chave de criação e a chave de tempo de execução de previsão.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Recebi um erro informando a ausência de cota. Como corrigi-la?

Consulte corrigir o código de status HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Eu preciso lidar com mais consultas de ponto de extremidade. Como faço isso?

Consulte corrigir o código de status HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Criei uma chave de criação, mas ela não está sendo exibida no portal do LUIS. O que aconteceu?

As chaves de criação estão disponíveis no portal do LUIS após [a migração para a experiência de chave de criação](luis-migration-authoring.md).

## <a name="app-management"></a>Gerenciamento de aplicativos

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Como fazer para baixar um log de enunciados do usuário?
Por padrão, o seu aplicativo de LUIS registra enunciados dos usuários. Para baixar um log de declarações que os usuários enviaram para seu aplicativo de LUIS, acesse **Meus Aplicativos** e selecione o aplicativo. Na barra de ferramentas contextual, selecione **Exportar Logs de Ponto de Extremidade**. O log é formatado como um arquivo CSV (valores separados por vírgulas).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Como eu desabilito o log de enunciados?
Você pode desligar o log de enunciados do usuário configurando `log=false` na URL do Ponto de Extremidade que seu aplicativo cliente usa para consultar o LUIS. No entanto, desativar o registro em log desabilita a habilidade do seu aplicativo de LUIS de sugerir enunciados ou melhorar o desempenho com base em [aprendizado ativo](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Se você definir `log=false` devido a questões de privacidade de dados, não poderá baixar um registro desses enunciados do usuário de LUIS nem usar esses enunciados para melhorar o seu aplicativo.

O registro em log é o único armazenamento de enunciados.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Por que eu não quero registrar em log todos os meus enunciados de ponto de extremidade?
Se você estiver usando o log para análise de previsão, não capture enunciados de teste em seu log.

## <a name="data-management"></a>Gerenciamento de dados

### <a name="can-i-delete-data-from-luis"></a>Posso excluir dados de LUIS?

* Você sempre pode excluir enunciados de exemplo usados para treinar o LUIS. Se você excluir um enunciado de exemplo do seu aplicativo de LUIS, ele será removido do serviço Web de LUIS e não estará disponível para exportação.
* Você pode excluir declarações da lista de usuários declarações que o LUIS sugere na página **revisar declarações do ponto de extremidade** . Excluir enunciados dessa lista impede que eles sejam sugeridos, mas não os exclui dos logs.
* Se você excluir uma conta, todos os aplicativos serão excluídos, junto com os respectivos logs e enunciados de exemplo. Os dados serão mantidos nos servidores por 60 dias antes de serem excluídos permanentemente.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Como a Microsoft gerencia dados que posso enviar para LUIS?

A [Central de confiabilidade](https://www.microsoft.com/trustcenter) explica suas opções de acesso nos serviços do Azure e gerenciamento de dados e de nossos compromissos.

## <a name="language-and-translation-support"></a>Suporte a idioma e tradução

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Eu tenho um aplicativo em um idioma e desejo criar um aplicativo paralelo em outro idioma. Qual é a maneira mais fácil de fazer isso?
1. Exporte seu aplicativo.
2. Traduza os enunciados rotulados no arquivo JSON do aplicativo exportado para o idioma de destino.
3. Talvez seja necessário alterar os nomes das intenções e das entidades ou deixá-los como estão.
4. Por fim, importe o aplicativo para ter um aplicativo de LUIS no idioma de destino.

## <a name="app-notification"></a>Notificação do aplicativo

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Porque eu recebi um email dizendo que estou quase sem cota?
Sua chave de criação/inicial tem permissão apenas para mil consultas de ponto de extremidade por um mês. Crie uma chave de ponto de extremidade de LUIS (gratuita ou paga) e use essa chave ao fazer consultas de ponto de extremidade. Se você estiver fazendo consultas de ponto de extremidade de um bot ou outro aplicativo cliente, precisará alterar a chave do ponto de extremidade LUIS lá.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Meu bot do LUIS não está funcionando. O que devo fazer?

O primeiro problema é descobrir se o problema está relacionado ao LUIS ou se ocorre fora do middleware do LUIS.

#### <a name="resolve-issue-in-luis"></a>Resolver problema no LUIS
Transmita o mesmo enunciado do [ponto de extremidade do LUIS](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) para o LUIS. Se você receber um erro, resolva o problema no LUIS até que o erro já não seja retornado. Os erros comuns incluem:

* `Out of call volume quota. Quota will be replenished in <time>.` - É um problema que indica a necessidade de mudar de uma chave de criação para uma [chave do ponto de extremidade](luis-how-to-azure-subscription.md) ou a necessidade de alterar [camadas de serviço](luis-how-to-azure-subscription.md#change-the-pricing-tier).

#### <a name="resolve-issue-in-azure-bot-service"></a>Resolver o problema no Serviço de Bot do Azure

Se você estiver usando o Serviço de Bot do Azure e o problema for que o **Teste no Webchat** retorna `Sorry, my bot code is having an issue`, verifique os logs:

1. No portal do Azure, na seção **Gerenciamento de bot**, escolha **Compilar** para seu bot.
1. Abra o editor de código online.
1. Na barra de navegação superior azul, escolha o nome do bot (o segundo item à direita).
1. Na lista suspensa resultante, escolha **Abrir o Console do Kudu**.
1. Escolha **LogFiles** e **Aplicativo**. Examine todos os arquivos de log. Se você não vir o erro na pasta do aplicativo, examine todos os arquivos de log em **LogFiles**.
1. Lembre-se de recompilar o projeto se você estiver usando uma linguagem compilada, por exemplo, C#.

> [!Tip]
> O console também pode instalar pacotes.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Resolva o problema durante a depuração no computador local com o Bot Framework.

Para saber mais sobre a depuração local de um bot, confira [Depurar um bot](/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrando LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Em que local meu aplicativo de LUIS é criado durante o processo de assinatura de bot do aplicativo Web do Azure?
Se você selecionar um modelo de LUIS e o botão **Selecionar** no painel de modelo, o painel da esquerda mudará para incluir o tipo de modelo e perguntará em que região criar o modelo de LUIS. No entanto, o processo de bot do aplicativo Web não cria uma assinatura de LUIS.

![Região do bot do aplicativo Web de modelo de LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Que regiões de LUIS são compatíveis com a preparação de fala do Bot Framework?
[Preparação de fala](/bot-framework/bot-service-manage-speech-priming) é compatível apenas com aplicativos LUIS na instância central (EUA).

## <a name="api-programming-strategies"></a>Estratégias de programação de API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Como fazer para obter de modo programático a região LUIS de um recurso?

Use o exemplo de LUIS para [encontrar região](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) de modo programático usando C# ou Node.Js.

## <a name="luis-service"></a>Serviço LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>O Reconhecimento vocal LUIS está disponível na nuvem privada ou local?

Sim, você poderá usar o [contêiner](luis-container-howto.md) do LUIS para esses cenários se tiver a conectividade necessária para medir o uso.

## <a name="migrating-to-the-next-version"></a>Migrando para a próxima versão

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Como fazer migrar para a API da visualização v3?

Consulte o [Guia de migração da API v2 para V3 para aplicativos Luis](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Crie anúncios de conferência 2019

Os seguintes recursos foram lançados na Conferência Build 2019:

* [Visualização do guia de migração da API V3](luis-migration-api-v3.md)
* [Painel de análise aprimorado](luis-how-to-use-dashboard.md)
* [Domínios predefinidos aprimorados](luis-reference-prebuilt-domains.md)
* [Entidades de lista dinâmica](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Vídeos:

* [Como usar a IA de conversa do Azure para preparar sua empresa para a próxima geração](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o LUIS, veja os seguintes recursos:
* [Perguntas do Stack Overflow marcadas com LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&uma página de perguntas para o MSDN Reconhecimento vocal Intelligent Services (LUIS)](/answers/topics/azure-language-understanding.html)