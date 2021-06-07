---
title: Como usar marcas de tabela para treinar seu modelo de formulário personalizado – reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como usar efetivamente o rotulamento da marca de tabela supervisionada.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467782"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Usar marcas de tabela para treinar seu modelo de formulário personalizado

Neste artigo, você aprenderá a treinar seu modelo de formulário personalizado com marcas de tabela (rótulos). Alguns cenários exigem rótulos mais complexos do que simplesmente alinhar pares chave-valor. Esses cenários incluem a extração de informações de formulários com estruturas hierárquicas complexas ou a ocorrência de itens que não são detectados e extraídos automaticamente pelo serviço. Nesses casos, você pode usar marcas de tabela para treinar o modelo de formulário personalizado.

## <a name="when-should-i-use-table-tags"></a>Quando devo usar marcas de tabela?

Aqui estão alguns exemplos de quando usar marcas de tabela seria apropriado:

- Há dados que você deseja extrair apresentados como tabelas em seus formulários, e a estrutura das tabelas é significativa. Por exemplo, cada linha da tabela representa um item e cada coluna da linha representa um recurso específico desse item. Nesse caso, você pode usar uma marca de tabela onde uma coluna representa recursos e uma linha representa informações sobre cada recurso.
- Há dados que você deseja extrair que não são apresentados em campos de formulário específicos, mas semanticamente, os dados podem caber em uma grade bidimensional. Por exemplo, seu formulário tem uma lista de pessoas e inclui, um nome, um sobrenome e um endereço de email. Você gostaria de extrair essas informações. Nesse caso, você pode usar uma marca de tabela com primeiro nome, sobrenome e endereço de email como colunas e cada linha é preenchida com informações sobre uma pessoa da sua lista.

> [!NOTE]
> O reconhecedor de formulário localiza e extrai automaticamente todas as tabelas em seus documentos, independentemente de as tabelas estarem marcadas ou não. Portanto, você não precisa rotular todas as tabelas do formulário com uma marca de tabela e as marcas de tabela não precisam replicar a estrutura de uma tabela muito encontrada no formulário. As tabelas extraídas automaticamente pelo reconhecedor de formulário serão incluídas na seção pageResults da saída JSON.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Criar uma marca de tabela com ferramenta de teste de OCR de formulário (FOTT)
<!-- markdownlint-disable MD004 -->
* Determine se você deseja uma marca de tabela **dinâmica** ou **de tamanho fixo** . Se o número de linhas variar de documento para documento, use uma marca de tabela dinâmica. Se o número de linhas for consistente em seus documentos, use uma marca de tabela de tamanho fixo.
* Se a marca de tabela for dinâmica, defina os nomes de coluna e o tipo de dados e o formato de cada coluna.
* Se a tabela for de tamanho fixo, defina o nome da coluna, o nome da linha, o tipo de dados e o formato de cada marca.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Configurar uma marca de tabela":::

## <a name="label-your-table-tag-data"></a>Rotular dados de marca de tabela

* Se o seu projeto tiver uma marca de tabela, você poderá abrir o painel de rotulagem e preencher a marca como você rotularia os campos chave-valor.
:::image type="content" source="media/table-labeling.png" alt-text="Rótulo com marcas de tabela":::

## <a name="next-steps"></a>Próximas etapas

Siga nosso início rápido para treinar e usar seu modelo de reconhecedor de formulário personalizado:

> [!div class="nextstepaction"]
> [Treine com rótulos usando a ferramenta de rotulagem de exemplo](quickstarts/label-tool.md)

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](overview.md)
>
