---
title: Suporte de idioma – QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas naturais e cultura com suporte do QnA Maker para sua base de dados de conhecimento. Não misture idiomas na mesma base de dados de conhecimento.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 1edd5ffc2578a27a53c7e9a46a4a5f1cf61331ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97605052"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Suporte a idiomas para um recurso QnA Maker e bases de dados de conhecimento

Este artigo descreve as opções de suporte de idioma para QnA Maker recursos e bases de dados de conhecimento. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Idioma do serviço é selecionado quando você cria a primeira base de dados de conhecimento no recurso. Todas as bases de dados de conhecimento adicionais no recurso devem estar no mesmo idioma. 

O idioma determina a relevância dos resultados QnA Maker fornece em resposta a consultas de usuário. O recurso QnA Maker e todas as bases de dados de conhecimento dentro desse recurso oferecem suporte a um único idioma. A linguagem única é necessária para fornecer os melhores resultados de resposta para uma consulta.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

No QnA Maker gerenciado, você obtém a opção de criar configurações de idioma no nível de base de dados de conhecimento individual. Essa configuração só pode ser habilitada com a base de dados de conhecimento do serviço. Depois de definido, as configurações de idioma não podem ser alteradas para o serviço. 

Se você selecionar configurações de idioma para ser específico da base de dados de conhecimento, poderá criar bases de dados de conhecimento de diferentes idiomas no próprio serviço. 

---

## <a name="single-language-per-resource"></a>Idioma único por recurso

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Considere o seguinte:

* Um serviço QnA Maker e todas as suas bases de dados de conhecimento oferecem suporte apenas a um idioma.
* O idioma é definido explicitamente quando a primeira base de dados de conhecimento do serviço é criada.
* O idioma é determinado a partir dos arquivos e URLs adicionados quando a base de dados de conhecimento é criada.
* O idioma não pode ser alterado para nenhuma outra base de dados de conhecimento no serviço.
* O idioma é usado pelo serviço de Pesquisa Cognitiva (classificador #1) e o serviço de QnA Maker (#2 de classificação) para gerar a melhor resposta a uma consulta.

# <a name="qnamaker-managed-preview"></a>[QnAMaker gerenciados (visualização)](#tab/v2)
![Configuração de idioma no QnA Maker gerenciado](../media/language-support/language-setting-managed.png)

Se você **não marcar a caixa de seleção para habilitar a configuração de idioma por base de dados de conhecimento**, considere o seguinte: 
* Um serviço QnA Maker e todas as suas bases de dados de conhecimento oferecerão suporte apenas a um idioma.
* O idioma é definido explicitamente quando a primeira base de dados de conhecimento do serviço é criada
* O idioma é determinado dos arquivos e URLs adicionados quando a base de dados de conhecimento é criada
* O idioma não pode ser alterado para nenhuma outra base de dados de conhecimento no serviço
* O idioma é usado pelo serviço de Pesquisa Cognitiva (#1 de classificação) e o serviço de QnA Maker (#2 de classificação) para gerar a melhor resposta a uma consulta

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Suporte a vários idiomas em um recurso QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)
Essa funcionalidade não tem suporte em nossa versão estável atual de GA (disponibilidade geral). Confira QnA Maker gerenciado para testar essa funcionalidade. 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)
* Ao criar a primeira base de dados de conhecimento em seu serviço, você tem a opção de habilitar a configuração de idioma por base de dados de conhecimento. Marque a caixa de seleção para criar bases de dados de conhecimento que pertencem a idiomas diferentes dentro de um serviço.
* A opção de configuração de idioma não pode ser modificada para o serviço, depois que a primeira base de dados de conhecimento é criada.
* Se você habilitar as configurações de idioma específicas de cada base de dados de conhecimento, em vez de ter um índice de teste para o serviço, você terá um índice de teste por base de dados de conhecimento. 

![Configuração de idioma no QnA Maker gerenciado](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Suporte a vários idiomas em uma base de dados de conhecimento

Se você precisar dar suporte a um sistema de base de dados de conhecimento, que inclui várias linguagens, você pode:

* Use o [serviço de Tradutor](../../translator/translator-info-overview.md) para converter uma pergunta em um único idioma antes de enviar a pergunta para sua base de dados de conhecimento. Isso permite que você se concentre na qualidade de uma única linguagem e na qualidade das perguntas e respostas alternativas.
* Crie um recurso QnA Maker e uma base de dados de conhecimento dentro desse recurso, para cada idioma. Isso permite que você gerencie perguntas alternativas separadas e texto de resposta que é mais nuance para cada idioma. Isso proporciona muito mais flexibilidade, mas requer um custo de manutenção muito maior quando as perguntas ou as respostas são alteradas em todos os idiomas.


## <a name="languages-supported"></a>Idiomas compatíveis

A lista a seguir contém os idiomas com suporte para um recurso de QnA Maker. 

| Idioma |
|--|
| Árabe |
| Armênia |
| Bangla |
| Basco |
| Búlgaro |
| Catalão |
| Chinês_Simplificado |
| Chinês_Tradicional |
| Croata |
| Tcheco |
| Dinamarquês |
| Holandês |
| Inglês |
| Estoniano |
| Finlandês |
| Francês |
| Galego |
| Alemão |
| Grego |
| Guzerate |
| Hebraico |
| Híndi |
| Húngaro |
| Islandês |
| Indonésio |
| Irlandês |
| Italiano |
| Japonês |
| canarim |
| Coreano |
| Letão |
| Lituano |
| Malaiala |
| Malaio |
| Norueguês |
| Polonês |
| Português |
| Panjabi |
| Romeno |
| Russo |
| Cirílico sérvio |
| sérvio Latino |
| Eslovaco |
| Esloveno |
| Espanhol |
| Sueco |
| Tâmil |
| Télugo |
| Tailandês |
| Turco |
| Ucraniano |
| Urdu |
| Vietnamita |

## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
QnA Maker depende dos [analisadores de linguagem do Azure pesquisa cognitiva](/rest/api/searchservice/language-support) para fornecer resultados.

Embora os recursos de Pesquisa Cognitiva do Azure estejam em par para os idiomas com suporte, QnA Maker tem um classificador adicional que fica acima dos resultados do Azure Search. Neste modelo de classificação, usamos alguns recursos semânticos e baseados em palavras especiais nos idiomas a seguir.

|Idiomas com classificação adicional|
|--|
|Chinês|
|Tcheco|
|Holandês|
|Inglês|
|Francês|
|Alemão|
|Húngaro|
|Italiano|
|Japonês|
|Coreano|
|Polonês|
|Português|
|Espanhol|
|Sueco|

Essa classificação adicional é um trabalho interno do classificador de QnA Maker.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Seleção de idioma](../index.yml)