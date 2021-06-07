---
title: Dimensionar e gerenciar habilidades personalizadas
titleSuffix: Azure Cognitive Search
description: Aprenda as ferramentas e técnicas para dimensionar com eficiência uma habilidade personalizada para a taxa de transferência máxima. Habilidades personalizadas invocam modelos ou lógicas de ia personalizados que você pode adicionar a um pipeline de indexação aprimorado do ia no Azure Pesquisa Cognitiva.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 4fdc222fa20aef6639bf6d5d485f7dcf6b6ca535
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641140"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Escale com eficiência uma habilidade personalizada

Habilidades personalizadas são APIs Web que implementam uma interface específica. Uma habilidade personalizada pode ser implementada em qualquer recurso publicamente endereçável. As implementações mais comuns para habilidades personalizadas são:
* Azure Functions para habilidades de lógica personalizada
* Webapps do Azure para habilidades de ia em contêineres simples
* Serviço kubernetes do Azure para habilidades mais complexas ou maiores.

## <a name="prerequisites"></a>Pré-requisitos

+ Examine a [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) para obter uma introdução à interface de entrada/saída que deve ser implementada por uma habilidade personalizada.

+ Configure seu ambiente. Você pode começar com [este tutorial de ponta a ponta](/azure/azure-functions/create-first-function-vs-code-python) para configurar a função do Azure sem servidor usando as extensões Visual Studio Code e Python.

## <a name="skillset-configuration"></a>Configuração do skillset

Configurar uma habilidade personalizada para maximizar a taxa de transferência do processo de indexação requer uma compreensão da habilidade, das configurações do indexador e de como a habilidade se relaciona com cada documento. Por exemplo, o número de vezes que uma habilidade é invocada por documento e a duração esperada por invocação.

### <a name="skill-settings"></a>Configurações de habilidades

Na [habilidade personalizada](cognitive-search-custom-skill-web-api.md) , defina os parâmetros a seguir.

1. Conjunto `batchSize` da habilidade personalizada para configurar o número de registros enviados para a habilidade em uma única invocação da habilidade.

2. Defina o `degreeOfParallelism` para calibrar o número de solicitações simultâneas que o indexador fará em sua habilidade.

3. Defina `timeout` como um valor suficiente para que a habilidade responda com uma resposta válida.

4. Na `indexer` definição, defina [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) como o número de documentos que devem ser lidos da fonte de dados e aprimorados simultaneamente.

### <a name="considerations"></a>Considerações

Definir essas variáveis para otimizar o desempenho dos indexadores requer determinar se suas habilidades são melhores com muitas solicitações simultâneas pequenas ou menos solicitações grandes. Algumas perguntas a serem consideradas são:

* Qual é a cardinalidade de invocação de habilidades? A habilidade é executada uma vez para cada documento, por exemplo, uma habilidade de classificação de documentos ou pode ser executada várias vezes por documento, uma habilidade de classificação de parágrafo?

* Em média, quantos documentos são lidos da fonte de dados para preencher uma solicitação de habilidade com base no tamanho do lote de habilidades? O ideal é que isso seja menor do que o tamanho do lote do indexador. Com tamanhos de lote maiores que 1 sua habilidade pode receber registros de vários documentos de origem. Por exemplo, se a contagem de lote do indexador for 5 e a contagem de lote de habilidades for 50 e cada documento gerar apenas cinco registros, o indexador precisará preencher uma solicitação de habilidades personalizada entre vários lotes do indexador.

* O número médio de solicitações que um lote de indexador pode gerar deve fornecer uma configuração ideal para os graus de paralelismo. Se sua infraestrutura que hospeda a habilidade não puder dar suporte a esse nível de simultaneidade, considere a possibilidade de discar os graus de paralelismo. Como prática recomendada, teste sua configuração com alguns documentos para validar suas escolhas nos parâmetros.

* Testando com uma amostra menor de documentos, avalie o tempo de execução de sua habilidade com o tempo total necessário para processar o subconjunto de documentos. O indexador gasta mais tempo criando um lote ou aguardando uma resposta de sua habilidade? 

* Considere as implicações de upstream de paralelismo. Se a entrada para uma habilidade personalizada for uma saída de uma habilidade anterior, todas as habilidades no Skills serão escaladas com eficiência para minimizar a latência?

## <a name="error-handling-in-the-custom-skill"></a>Tratamento de erros na habilidade personalizada

As habilidades personalizadas devem retornar um código de status de êxito HTTP 200 quando a habilidade for concluída com êxito. Se um ou mais registros em um lote resultarem em erros, considere retornar o código de vários status 207. A lista de erros ou avisos para o registro deve conter a mensagem apropriada.

Todos os itens em um lote que ocorrerão erros resultarão na falha do documento correspondente. Se você precisar que o documento tenha sucesso, retorne um aviso.

Qualquer código de status acima de 299 é avaliado como um erro e todos os aprimoramentos falham resultando em um documento com falha. 

### <a name="common-error-messages"></a>Mensagens de erro comuns

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Defina o parâmetro timeout na habilidade para permitir uma duração de execução mais longa.

* `Could not execute skill because Web Api skill response is invalid.` Indica a habilidade de não retornar uma mensagem no formato de resposta de habilidade personalizada. Isso pode ser o resultado de uma exceção não percebida na habilidade.

* `Could not execute skill because the Web Api request failed.` Provavelmente causado por erros ou exceções de autorização.

* `Could not execute skill.` Normalmente, o resultado da resposta da habilidade está sendo mapeada para uma propriedade existente na hierarquia do documento.

## <a name="testing-custom-skills"></a>Testando habilidades personalizadas

Comece testando sua habilidade personalizada com um cliente de API REST para validar:

* A habilidade implementa a interface de habilidade personalizada para solicitações e respostas

* A habilidade retorna um JSON válido com o `application/JSON` tipo MIME

* Retorna um código de status HTTP válido

Crie uma [sessão de depuração](cognitive-search-debug-session.md) para adicionar sua habilidade ao contextset e certifique-se de que ela produz um enriquecimento válido. Embora uma sessão de depuração não permita que você ajuste o desempenho da habilidade, ela permite que você verifique se a habilidade está configurada com valores válidos e retorna os objetos aprimorados esperados.

## <a name="best-practices"></a>Melhores práticas

* Embora as habilidades possam aceitar e retornar cargas maiores, considere limitar a resposta a 150 MB ou menos ao retornar JSON.

* Considere definir o tamanho do lote no indexador e a habilidade para garantir que cada lote de fonte de dados gere uma carga completa para sua habilidade.

* Para tarefas de longa execução, defina o tempo limite como um valor alto o suficiente para garantir que o indexador não faça erro ao processar documentos simultaneamente.

* Otimize o tamanho do lote do indexador, o tamanho do lote de habilidades e os graus de habilidade de paralelismo para gerar o padrão de carga que sua habilidade espera, menos solicitações grandes ou muitas solicitações pequenas.

* Monitore habilidades personalizadas com logs detalhados de falhas, pois você pode ter cenários em que solicitações específicas falham consistentemente como resultado da variabilidade dos dados.


## <a name="next-steps"></a>Próximas etapas
Parabéns! Sua habilidade personalizada agora é dimensionada diretamente para maximizar a taxa de transferência no indexador. 

+ [Habilidades de energia: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Adicionar uma habilidade personalizada a um pipeline de enriquecimento de ia](cognitive-search-custom-skill-interface.md)
+ [Adicionar uma habilidade de Azure Machine Learning](./cognitive-search-aml-skill.md)
+ [Usar sessões de depuração para testar alterações](./cognitive-search-debug-session.md)