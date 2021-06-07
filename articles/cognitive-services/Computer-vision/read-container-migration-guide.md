---
title: Migração para os contêineres de Leitura v3.x
titleSuffix: Azure Cognitive Services
description: Saiba como migrar para os contêineres de OCR de Leitura v3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/29/2021
ms.author: aahi
ms.openlocfilehash: 1cc17306265e6e8ba2e7fb3f570d0017b006b84f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284678"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migrar para contêineres de OCR de Leitura v3.x

Caso esteja usando a versão 2 do contêiner de OCR de Leitura da Pesquisa Visual Computacional, use este artigo para saber mais sobre como atualizar seu aplicativo para usar a versão 3.x do contêiner. 


## <a name="configuration-changes"></a>Alterações de configuração

* `ReadEngineConfig:ResultExpirationPeriod` não é mais suportada. O contêiner de OCR de Leitura tem um trabalho cron integrado que remove os resultados e os metadados associados a uma solicitação após 48 horas.
* `Cache:Redis:Configuration` não é mais suportada. O Cache não é usado nos contêineres v3.x, portanto, não será necessário defini-lo.

## <a name="api-changes"></a>Alterações de API

O contêiner da Leitura v3.2 usa a versão 3 da API da Pesquisa Visual Computacional e tem os seguintes pontos de extremidade:

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

Confira o [guia de migração da API REST da Pesquisa Visual Computacional v3](./upgrade-api-versions.md) para obter informações detalhadas sobre como atualizar seus aplicativos para usar a versão 3 da API de Leitura baseada em nuvem. Essas informações também se aplicarão ao contêiner. Observe que as operações de sincronização são compatíveis somente com contêineres.

## <a name="memory-requirements"></a>Requisitos de memória

Os requisitos e as recomendações são baseados em parâmetros de comparação com uma solicitação por segundo, usando uma imagem de 8 MB de uma carta empresarial digitalizada que contém 29 linhas e 803 caracteres no total. A tabela a seguir descreverá a alocação mínima e recomendada de recursos para cada contêiner de OCR de Leitura.

|Contêiner  |Mínimo | Recomendado  |
|---------|---------|------|
|3\.2-preview da Leitura | 8 núcleos, 16 GB de memória         | 8 núcleos, 24 GB de memória |

Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

O núcleo e a memória correspondem às configurações `--cpus` e `--memory`, que são usadas como parte da opção Executar Comando do Docker.

## <a name="storage-implementations"></a>Implementações de armazenamento

>[!NOTE]
> O MongoDB não é mais compatível com as versões 3.x do contêiner. Em vez disso, os contêineres são compatíveis com o Armazenamento do Azure e com sistemas de arquivos offline.

| Implementação |    Argumento(s) de runtime necessário(s) |
|---------|---------|
|Nível de arquivo (padrão)    | Nenhum argumento de runtime é necessário. O diretório `/share` será usado. |
|Blob do Azure    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Implementações de fila

Na v3.x do contêiner, o RabbitMQ não é compatível no momento. As implementações de apoio compatíveis são:

| Implementação | Argumento(s) de Runtime | Uso pretendido |
|---------|---------|-------|
| Na memória (padrão) | Nenhum argumento de runtime é necessário. | Desenvolvimento e teste |
| Filas do Azure | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Produção |
| RabbitMQ    | Indisponível | Produção |

Para obter uma redundância adicionada, o contêiner de Leitura v3.x usa um temporizador de visibilidade para garantir que as solicitações possam ser processadas com êxito em caso de falha, durante a execução em uma configuração de vários contêineres. 

Defina o temporizador com `Queue:Azure:QueueVisibilityTimeoutInMilliseconds`, que definirá o tempo para uma mensagem ficar invisível quando outro trabalho a estiver processando. Para evitar que as páginas sejam processadas de modo redundante, recomendamos definir o período de tempo limite como 120 segundos. O valor padrão é 30 segundos.

| Valor padrão | Valor recomendado |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](computer-vision-resource-container-config.md) para configurações
* Examine [Visão geral de OCR](overview-ocr.md) para saber mais sobre como reconhecer um texto impresso e manuscrito
* Veja a [API de Leitura](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para obter detalhes sobre os métodos compatíveis com o contêiner.
* Veja as [Perguntas frequentes](FAQ.md) para resolver problemas relacionados à funcionalidade de Pesquisa Visual Computacional.
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)