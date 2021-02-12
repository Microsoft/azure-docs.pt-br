---
title: Inspecionar a qualidade de dados para o serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Fala Personalizada fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando dados de áudio com o resultado de reconhecimento correspondente. Você pode reproduzir áudio carregado e determinar se o resultado de reconhecimento fornecido está correto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: erhopf
ms.openlocfilehash: 87bc411903604a7068f346604853a2a61c9a6f1c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499179"
---
# <a name="inspect-custom-speech-data"></a>Inspecionar dados da Fala Personalizada

> [!NOTE]
> Esta página pressupõe que você tenha lido [preparar dados de teste para fala personalizada](./how-to-custom-speech-test-and-train.md) e ter carregado um DataSet para inspeção.

Fala Personalizada fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando dados de áudio com o resultado de reconhecimento correspondente. No [portal de fala personalizada](https://speech.microsoft.com/customspeech), você pode reproduzir áudio carregado e determinar se o resultado de reconhecimento fornecido está correto. Essa ferramenta ajuda a inspecionar a qualidade do modelo de fala-para-texto de linha de base da Microsoft, inspecionar um modelo personalizado treinado ou comparar a transcrição por dois modelos.

Neste documento, você aprenderá a inspecionar visualmente a qualidade do modelo de fala-a-texto de linha de base da Microsoft e/ou modelos personalizados que você tenha treinado. Você também aprenderá a usar o editor de transcrição online para criar e refinar conjuntos de linhas de áudio rotulados.

## <a name="create-a-test"></a>Criar um teste

Siga estas instruções para criar um teste:

1. Entre no portal de [fala personalizada](https://speech.microsoft.com/customspeech).
2. Navegue até **conversão de fala em texto > Fala Personalizada > [nome do projeto] > teste**.
3. Clique em **Adicionar teste**.
4. Selecione **inspecionar qualidade (dados somente de áudio)**. Dê ao teste um nome, uma descrição e selecione seu conjunto de dado de áudio.
5. Selecione até dois modelos que você gostaria de testar.
6. Clique em **Criar**.

Depois que um teste tiver sido criado com êxito, você poderá ver como um modelo transcreve o conjunto de testes de áudio especificado ou comparar os resultados de dois modelos lado a lado.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Comparações de modelo lado a lado

Quando o status do teste for _bem-sucedido_, clique no nome do item de teste para ver os detalhes do teste. Essa página de detalhes lista todos os declarações em seu conjunto de seus conjuntos de anotações e mostra os resultados de reconhecimento dos dois modelos que você está comparando.

Para ajudar a inspecionar a comparação lado a lado, você pode alternar vários tipos de erro, incluindo inserção, exclusão e substituição. Ao ouvir o áudio e comparar os resultados de reconhecimento em cada coluna (mostrando a transcrição com rótulo humano e os resultados de dois modelos de fala em texto), você pode decidir qual modelo atende às suas necessidades e onde são necessárias melhorias.

O teste de modelo lado a lado é útil para validar qual modelo de reconhecimento de fala é melhor para um aplicativo. Para uma medida objetiva de precisão, exigindo áudio transcrita, siga as instruções encontradas em [avaliar exatidão](how-to-custom-speech-evaluate-data.md).

## <a name="online-transcription-editor"></a>Editor de transcrição online

O editor de transcrição online permite que você trabalhe facilmente com transcrições de áudio no Fala Personalizada. Os principais casos de uso do editor são os seguintes: 

* Você só tem dados de áudio, mas deseja criar um áudio preciso, além de conjuntos com rótulos humanos, do zero para usar no treinamento do modelo.
* Você já tem áudio + conjuntos de valores com rótulo humano, mas há erros ou defeitos na transcrição. O editor permite que você modifique rapidamente as transcrições para obter a melhor precisão de treinamento.

O único requisito para usar o editor de transcrição é ter dados de áudio carregados (somente áudio ou áudio + transcrição).

### <a name="import-datasets-to-editor"></a>Importar conjuntos de valores para o editor

Para importar dados para o editor, primeiro navegue até **Fala Personalizada > [seu projeto] > editor**.

![Guia Editor](media/custom-speech/custom-speech-editor-detail.png)

Em seguida, use as etapas a seguir para importar dados.

1. Clique em **importar dados**
1. Criar um novo conjunto de dados e dar a ele uma descrição
1. Selecione conjuntos de os. Há suporte para várias seleções e você pode selecionar somente os dados de áudio, bem como os dados de áudio e de rótulo humano.
1. Para dados somente de áudio, você pode opcionalmente usar os modelos padrão para gerar automaticamente a transcrição do computador após a importação para o editor
1. Clique em **importar**

Depois que os dados tiverem sido importados com êxito, você poderá clicar nos conjuntos e iniciar a edição.

> [!TIP]
> Você também pode importar conjuntos de valores para o editor diretamente, selecionando conjuntos de os e clicando em **exportar para o editor**

### <a name="edit-transcription-by-listening-to-audio"></a>Editar transcrição ouvindo áudio

Depois que o carregamento de dados for bem-sucedido, clique em cada nome de item para ver os detalhes dos dados. Você também pode usar **Previous** e **Next** para mover entre cada arquivo.

A página de detalhes lista todos os segmentos em cada arquivo de áudio e você pode clicar no expressão desejado. Para cada expressão, você pode reproduzir o áudio e examinar as transcrições e editar as transcrições se encontrar erros de inserção, exclusão ou substituição. Consulte a [avaliação de dados como](how-to-custom-speech-evaluate-data.md) para obter mais detalhes sobre tipos de erro.

![Página do editor](media/custom-speech/custom-speech-editor.png)

Depois de fazer edições, clique no botão **salvar** .

### <a name="export-datasets-from-the-editor"></a>Exportar conjuntos de os do editor

Para exportar os conjuntos de dados de volta para a guia **Data** , navegue até a página de detalhes de dados e clique no botão **Exportar** para exportar todos os arquivos como um novo DataSet. Você também pode filtrar os arquivos por hora da última edição, durações de áudio, etc. para selecionar parcialmente os arquivos desejados. 

![Exportar dados](media/custom-speech/custom-speech-editor-export.png)

Os arquivos exportados para dados serão usados como um conjunto de dados totalmente novo e não afetarão nenhuma das entidades existentes/de treinamento/teste.

## <a name="next-steps"></a>Próximas etapas

- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
- [Treinar seu modelo](how-to-custom-speech-train-model.md)
- [Melhorar o modelo](./how-to-custom-speech-evaluate-data.md)
- [Implantar o seu modelo](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar dados de teste para Fala Personalizada](./how-to-custom-speech-test-and-train.md)