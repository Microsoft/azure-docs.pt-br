---
title: 'Tutorial: Usar Sessões de Depuração para corrigir conjuntos de habilidades'
titleSuffix: Azure Cognitive Search
description: As Sessões de depuração (versão prévia) são uma ferramenta do portal do Azure usada para localizar, diagnosticar e reparar problemas em um conjunto de habilidades.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509126"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Tutorial: Depurar um conjunto de habilidades usando as Sessões de Depuração

Os conjuntos de habilidades coordenam uma série de ações que analisam ou transformam conteúdo, em que a saída de uma habilidade se torna a entrada de outra. Quando as entradas dependem das saídas, erros nas definições do conjunto de habilidades e associações de campo podem resultar na perda de operações e de dados.

As **Sessões de depuração** no portal do Azure fornecem uma visualização holística de um conjunto de habilidades. Usando essa ferramenta, você pode fazer uma busca detalhada de etapas específicas para ver com facilidade onde uma ação pode estar falhando.

Neste artigo, você usará as **Sessões de depuração** para localizar e corrigir 1) uma entrada ausente e 2) mapeamentos de campo de saída. O tutorial é completo. Ele fornece dados para você indexar (dados de testes clínicos), uma coleção do Postman que cria objetos e instruções para usar as **Sessões de depuração** para localizar e corrigir problemas no conjunto de habilidades.

> [!Important]
> As sessões de depuração são uma versão prévia do recurso fornecida sem um contrato de nível de serviço e não são recomendadas para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, os seguintes pré-requisitos precisam estar em vigor:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/).

+ Um serviço do Azure Cognitive Search. [Crie um serviço](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar um serviço gratuito para este início rápido. 

+ Uma conta de Armazenamento do Azure com o [Armazenamento de Blobs](../storage/blobs/index.yml), usada para hospedar dados de exemplo e para persistir dados temporários criados durante uma sessão de depuração.

+ O [aplicativo da área de trabalho do Postman](https://www.getpostman.com/) e uma [coleção do Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) para criar objetos usando as APIs REST.

+ [Dados de exemplo (testes clínicos)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> Este guia de início rápido também usa os [Serviços Cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/) para a IA. Como a carga de trabalho é muito pequena, os Serviços Cognitivos são acionados nos bastidores para fornecer processamento gratuito para até 20 transações. Isso significa que você pode concluir este exercício sem precisar criar um recurso adicional dos Serviços Cognitivos.

## <a name="set-up-your-data"></a>Configurar seus dados

Esta seção cria o conjunto de dados de exemplo no Armazenamento de Blobs do Azure para que o indexador e o conjunto de habilidades tenham conteúdo com o qual trabalhar.

1. [Baixe dados de exemplo (clinical-trials-pdf-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) compostos por 19 arquivos.

1. [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou [localizar uma conta](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Escolha a mesma região do Azure Cognitive Search para evitar preços de largura de banda.

   + Escolha o tipo de conta StorageV2 (uso geral V2).

1. Navegue até as páginas dos serviços de Armazenamento do Azure no portal e crie um contêiner de Blob. A melhor prática é especificar o nível de acesso "particular". Dê ao contêiner o nome `clinicaltrialdataset`.

1. No contêiner, clique em **Carregar** para carregar os arquivos de exemplo baixados e descompactados na primeira etapa.

1. No portal, obtenha e salve a cadeia de conexão para o Armazenamento do Azure. Você precisará dela para as chamadas à API REST que indexam dados. Obtenha a cadeia de conexão em **Configurações** > **Chaves de acesso** no portal.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso" border="false":::

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="create-data-source-skillset-index-and-indexer"></a>Criar fonte de dados, conjunto de habilidades, índice e indexador

Nesta seção, o Postman e uma coleção fornecida são usados para criar o indexador, o índice, o conjunto de habilidades e a fonte de dados do Cognitive Search. Se não estiver familiarizado com o Postman, confira [este início rápido](search-get-started-rest.md).

Você precisará da [coleção do Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) criada para este tutorial concluir esta tarefa. 

1. Inicie o Postman e importe a coleção. Em **Arquivos** > **Novo**, selecione a coleção a ser importada.
1. Depois que a coleção for importada, expanda a lista de ações (...).
1. Clique em **Editar**.
1. Em Valor Atual, insira o nome de seu `searchService` (por exemplo, se o ponto de extremidade for `https://mydemo.search.windows.net`, o nome do serviço será "`mydemo`").
1. Insira o `apiKey` com a chave primária ou secundária do serviço de pesquisa.
1. Insira o `storageConnectionString` da página de chaves de sua conta de Armazenamento do Azure.
1. Insira o `containerName` para o contêiner criado na conta de armazenamento e clique em **Atualizar**.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="editar variáveis no Postman":::

A coleção contém quatro chamadas REST diferentes usadas para criar os objetos usados neste tutorial.

A primeira chamada cria a fonte de dados. `clinical-trials-ds`. A segunda chamada cria o conjunto de habilidades, `clinical-trials-ss`. A terceira chamada cria o índice, `clinical-trials`. A quarta e última chamada cria o indexador, `clinical-trials-idxr`.

+ Abra uma solicitação por vez e clique em **Enviar** para enviar cada uma delas ao serviço de pesquisa. 

Depois que todas as chamadas na coleção tiverem sido concluídas, feche o Postman e volte para o portal do Azure.

## <a name="check-results-in-the-portal"></a>Verificar os resultados no portal

O código de exemplo cria intencionalmente um índice com bug como consequência de problemas ocorridos durante a execução do conjunto de habilidades. O problema é a ausência de dados. 

1. No portal do Azure, na página de Visão geral do serviço de pesquisa, selecione a guia **Índices**. 
1. Selecione o índice `clinical-trials`.
1. Insira esta cadeia de caracteres de consulta: `$select=metadata_storage_path, organizations, locations&$count=true` para retornar campos para documentos específicos (identificados pelo campo `metadata_storage_path` exclusivo).
1. Clique em **Pesquisar** para executar a consulta, retornando os 19 documentos e mostrando valores vazios para "organizações" e "locais".

Esses campos devem ter sido populados por meio da [habilidades Reconhecimento de Entidade](cognitive-search-skill-entity-recognition.md) do conjunto de habilidades, usada para localizar organizações e locais em qualquer parte do conteúdo do blob. No próximo exercício, você usará a Sessão de depuração para determinar o que deu errado.

Outra maneira de investigar erros e avisos é por meio do portal do Azure.

1. Abra a guia **Indexadores** e selecione `clinical-trials-idxr`.
1. Observe que, embora o trabalho do indexador tenha sido bem-sucedido de modo geral, houve 57 avisos.
1. Clique em **Êxito** para ver os avisos (se houvessem ocorrido erros na maior parte, o link de detalhes seria **Falha**). Você verá uma longa lista com todos os avisos emitidos pelo indexador.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="exibir avisos":::

## <a name="start-your-debug-session"></a>Inicie a sessão de depuração

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="iniciar uma nova sessão de depuração":::

1. Na página de Visão geral da pesquisa, clique na guia **Sessões de depuração**.
1. Selecione **+ Nova sessão de depuração**.
1. Dê um nome à sessão. 
1. Conecte a sessão à sua conta de armazenamento. 
1. No modelo do Indexador, forneça o nome do indexador. O indexador tem referências à fonte de dados, ao conjunto de habilidades e ao índice.
1. Aceite a opção de documento padrão para o primeiro documento na coleção. 
1. **Salve** a sessão. Salvar a sessão iniciará o pipeline de enriquecimento de IA, conforme definido pelo conjunto de habilidades.

> [!Important]
> Uma sessão de depuração funciona apenas com um só documento. Você pode escolher qual documento deseja depurar ou simplesmente usar o primeiro.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Quando a sessão de depuração termina a inicialização, ela usa como padrão a guia **Aprimoramentos de IA**, destacando o **Grafo de Habilidades**. O Grafo de Habilidades fornece uma hierarquia visual do conjunto de habilidades e sua ordem de execução, sequencialmente e em paralelo.

## <a name="find-issues-with-the-skillset"></a>Encontrar problemas com o conjunto de habilidades

Todos os problemas relatados pelo indexador podem ser encontrados na guia **Erros/Avisos** adjacente. 

Observe que a guia **Erros/Avisos** fornecerá uma lista muito menor do que aquela exibida anteriormente, pois essa lista só detalha os erros de um documento. Assim como a lista exibida pelo indexador, você pode clicar em uma mensagem de aviso e ver os detalhes desse aviso.

Selecione **Erros/Avisos** para examinar as notificações. Você deverá ver três:

   + "Não foi possível mapear o campo de saída 'locations' para o índice de pesquisa. Verifique a propriedade 'outputFieldMappings' do indexador.
Valor ausente '/document/merged_content/locations'."

   + "Não foi possível mapear o campo de saída 'organizations' para o índice de pesquisa. Verifique a propriedade 'outputFieldMappings' do indexador.
Valor ausente '/document/merged_content/organizations'."

   + "Habilidade executada, mas pode haver resultados inesperados porque uma ou mais entradas de habilidades eram inválidas.
Entrada de habilidade opcional ausente. Nome: 'languageCode', Origem: '/document/languageCode'. Problemas de análise da linguagem de expressão: Valor ausente '/document/languageCode'."

   Muitas habilidades têm um parâmetro 'languageCode'. Ao inspecionar a operação, você pode ver que essa entrada de código de idioma está faltando em `Enrichment.NerSkillV2.#1`, que é a mesma habilidade Reconhecimento de Entidade que está tendo problemas com as saídas 'locations' e 'organizations'. 

Como as três notificações são sobre essa habilidade, a próxima etapa é depurá-la. Se possível, comece resolvendo primeiro os problemas de entrada antes de passar para os problemas de outputFieldMapping.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Nova sessão de depuração iniciada":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Corrigir o valor de entrada da habilidade ausente

Na guia **Erros/Avisos**, há um erro para uma operação rotulada `Enrichment.NerSkillV2.#1`. O detalhe desse erro explica que há um problema com o valor de entrada da habilidade '/document/languageCode '. 

1. Retorne à guia **Aprimoramentos de IA**.
1. Clique no **Grafo de Habilidades**.
1. Clique na habilidade rotulada **#1** para exibir seus detalhes no painel direito.
1. Localize a entrada de "languageCode".
1. Selecione o símbolo **</>** no início da linha e abra o Avaliador de Expressão.
1. Clique no botão **Avaliar** para confirmar que essa expressão está resultando em um erro. Ele confirmará que a propriedade "languageCode" não é uma entrada válida.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Avaliador de Expressão":::

Há duas maneiras de pesquisar esse erro na sessão. A primeira é examinar de onde a entrada é proveniente – qual habilidade na hierarquia deve produzir esse resultado? A guia Execuções no painel detalhes da habilidade deve exibir a origem da entrada. Se não houver uma origem, isso indicará um erro de mapeamento de campo.

1. Clique na guia **Execuções**.
1. Examine as ENTRADAS e localize "languageCode". Não há nenhuma origem para essa entrada listada. 
1. Alterne para o painel esquerdo para exibir a Estrutura de Dados Enriquecidos. Não há nenhum caminho mapeado correspondente a "languageCode".

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Estrutura de Dados Enriquecidos":::

Há um caminho mapeado para "language". Portanto, há um erro de digitação nas configurações da habilidade. Para corrigir essa expressão na habilidade #1, a expressão '/document/language' precisará ser atualizada.

1. Abra o Avaliador de Expressão **</>** para o caminho "language".
1. Copie a expressão. Feche a janela.
1. Vá para as Configurações de Habilidade da habilidade #1 e abra o Avaliador de Expressão **</>** para a entrada "languageCode".
1. Cole o novo valor, '/document/language', na caixa de Expressão e clique em **Avaliar**.
1. Ela deve exibir a entrada correta "en". Clique em Aplicar para atualizar a expressão.
1. Clique em **Salvar** no painel direito de detalhes da habilidade.
1. Clique em **Executar** no menu da janela da sessão. Isso iniciará outra execução do conjunto de habilidades usando o documento. 

Depois que a execução da sessão de depuração for concluída, clique na guia Erros/Avisos e ela mostrará que o erro chamado "Enrichment.NerSkillV2.#1" desapareceu. No entanto, ainda há dois avisos de que o serviço não pôde mapear campos de saída de organizações e locais para o índice de pesquisa. Há valores ausentes: '/document/merged_content/organizations' e '/document/merged_content/locations'.

## <a name="fix-missing-skill-output-values"></a>Corrigir valores de saída da habilidade ausente

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Erros e avisos":::

Há valores de saída ausentes de uma habilidade. Para identificar a habilidade com erro, vá para a Estrutura de Dados Enriquecidos, localize o nome do valor e examine sua Fonte de Origem. No caso dos valores de organizações e locais ausentes, eles são saídas do da habilidade #1. Abrir o Avaliador de Expressão </> para cada caminho exibirá as expressões listadas como '/document/content/organizations' e '/document/content/locations', respectivamente.

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Entidade de organizações do Avaliador de Expressão":::

A saída dessas entidades está vazia e não deveria estar vazia. Quais são as entradas que produzem esse resultado?

1. Vá para **Grafo de Habilidades** e selecione a habilidade #1.
1. Selecione a guia **Execuções** no painel de detalhes da habilidade à direita.
1. Abra o Avaliador de Expressão **</>** para a ENTRADA "text".

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Entrada da habilidade de texto":::

O resultado exibido para essa entrada não se parece com uma entrada de texto. Ele parece uma imagem envolvida por novas linhas. A falta de texto significa que nenhuma entidade pode ser identificada. Examinar a hierarquia do conjunto de habilidades mostra que o conteúdo é processado primeiro pela habilidade #6 (OCR) e, em seguida, passado para a habilidade #5 (Merge). 

1. Selecione a habilidade #5 (Merge) no **Grafo de Habilidades**.
1. Selecione a guia **Execuções** no painel de detalhes da habilidade à direita e abra o Avaliador de Expressão **</>** para as SAÍDAS "mergedText".

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Saída para a habilidade Merge":::

Aqui, o texto está emparelhado com a imagem. Examinando a expressão '/Document/merged_content', o erro nos caminhos "organizations" e "locations" para a habilidade #1 é visível. Em vez de usar '/document/content', ele deve usar '/document/merged_content' para as entradas de "text".

1. Copie a expressão da saída "mergedText" e feche a janela do Avaliador de Expressão.
1. Selecione a habilidade #1 no **Grafo de Habilidades**.
1. Selecione a guia **Configurações de Habilidade** no painel de detalhes da habilidade à direita.
1. Abra o Avaliador de Expressão **</>** para a entrada "text".
1. Cole a nova expressão na caixa. Clique em **Avaliar**.
1. A entrada correta com o texto adicionado deve ser exibida. Clique em **Aplicar** para atualizar as Configurações da Habilidade.
1. Clique em **Salvar** no painel direito de detalhes da habilidade.
1. Clique em **Executar** no menu da janela das sessões. Isso iniciará outra execução do conjunto de habilidades usando o documento.

Quando a execução do indexador for concluída, os erros ainda estarão lá. Volte para a habilidade #1 e investigue. A entrada da habilidade foi corrigida de 'content' para 'merger_content'. Quais são as saídas dessas entidades na habilidade?

1. Selecione a guia **Enriquecimentos de IA**.
1. Selecione o **Grafo de Habilidades** e clique na habilidade #1.
1. Navegue para as **Configurações de Habilidade** para encontrar "outputs".
1. Abra o Avaliador de Expressão **</>** para a entidade "organizations".

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Saída da entidade organizations":::

Avaliar o resultado da expressão fornece o resultado correto. A habilidade está trabalhando para identificar o valor correto da entidade "organizations". No entanto, o mapeamento de saída no caminho da entidade ainda está gerando um erro. Comparando o caminho de saída na habilidade com o caminho de saída no erro, a habilidade está gerando as saídas, organizações e locais sob o nó /document/content. Enquanto o mapeamento do campo de saída está esperando que os resultados sejam gerados no nó /document/merged_content. Na etapa anterior, a entrada mudou de '/document/content' para '/document/merged_content'. O contexto nas configurações da habilidade precisa ser alterado para garantir que a saída seja gerada com o contexto correto.

1. Selecione a guia **Enriquecimentos de IA**.
1. Selecione o **Grafo de Habilidades** e clique na habilidade #1.
1. Navegue para as **Configurações de Habilidade** para encontrar "context".
1. Clique duas vezes na configuração de "context" e edite-a para ler '/document/merged_content'.
1. Clique em **Salvar** no painel direito de detalhes da habilidade.
1. Clique em **Executar** no menu da janela das sessões. Isso iniciará outra execução do conjunto de habilidades usando o documento.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Correção de contexto na configuração da habilidade":::

Todos os erros foram resolvidos.

## <a name="commit-changes-to-the-skillset"></a>Confirmar alterações no conjunto de habilidades

Quando a sessão de depuração foi iniciada, o serviço de pesquisa criou uma cópia do conjunto de habilidades. Isso foi feito para proteger o conjunto de habilidades original em seu serviço de pesquisa. Agora que você concluiu a depuração de seu conjunto de habilidades, as correções podem ser confirmadas (substituindo o conjunto de habilidades original). 

Como alternativa, se não estiver pronto para confirmar as alterações, você poderá salvar a sessão de depuração e reabri-la mais tarde.

1. Clique em **Confirmar alterações** no menu principal das Sessões de depuração.
1. Clique em **OK** para confirmar que você deseja atualizar o conjunto de habilidades.
1. Feche a Sessão de depuração e selecione a guia **Indexadores**.
1. Abra seu 'clinical-trials-idxr'.
1. Clique em **redefinir**.
1. Clique em **Executar**. Clique em **OK** para confirmar.

Quando o indexador terminar de ser executado, deverá haver uma marca de seleção verde e a palavra Êxito ao lado do carimbo de data/hora da última execução na guia de **Histórico de execuções**. Para garantir que as alterações tenham sido aplicadas:

1. Na página de Visão geral da pesquisa, selecione a guia **Índice**.
1. Abra o índice 'clinical-trials' e, na guia Gerenciador de pesquisa, insira esta cadeia de caracteres de consulta: `$select=metadata_storage_path, organizations, locations&$count=true` para retornar campos para documentos específicos (identificados pelo campo `metadata_storage_path` exclusivo).
1. Clique em **Pesquisar**.

Os resultados devem mostrar que as organizações e locais agora estão preenchidos com os valores esperados.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Este tutorial abordou vários aspectos da definição e do processamento dos conjuntos de habilidades. Para saber mais sobre conceitos e fluxos de trabalho, confira seguintes os artigos:

+ [Como mapear campos de saída de conjuntos de habilidades para campos em um índice de pesquisa](cognitive-search-output-field-mapping.md)

+ [Conjuntos de habilidades no Azure Cognitive Search](cognitive-search-working-with-skillsets.md)

+ [Como configurar o cache para enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md)