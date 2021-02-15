---
title: Testar um modelo usando arquivos de áudio – Speech Studio
titleSuffix: Azure Cognitive Services
description: Neste guia de instruções, você usa o Speech Studio para testar o reconhecimento de fala em um arquivo de áudio.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: v-demjoh
ms.openlocfilehash: 572b3b3459e1d837130f3c987d45ee45629f37ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485069"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Testar um modelo usando um arquivo de áudio – Speech Studio

Neste guia de instruções, você usa o Speech Studio para converter a fala de um arquivo de áudio em texto. O Speech Studio permite testar, comparar, melhorar e implantar modelos de reconhecimento de fala usando texto relacionado, áudio com transcrições rotuladas por humanos e orientação de pronúncia que você fornece.

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar o Portal de Fala, [siga estas instruções para criar uma conta do Azure e assinar o serviço de fala](../custom-speech-overview.md#set-up-your-azure-account). Essa assinatura unificada fornece acesso à conversão de fala em texto, texto em fala e tradução de fala e ao portal de Fala Personalizada.

## <a name="download-an-audio-file"></a>Baixar um arquivo de áudio

Siga estas etapas para baixar um arquivo de áudio que contém a fala e empacotá-lo em um arquivo zip.

1. Baixe o **[exemplo de arquivo wav deste link](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** clicando com o botão direito do mouse no link e selecionando **Salvar link como**. Clique em **Salvar** para baixar o arquivo `whatstheweatherlike.wav`.
2. Usando um explorador de arquivos ou uma janela de terminal com uma ferramenta zip, crie um arquivo zip chamado `whatstheweatherlike.zip` que contenha o arquivo de `whatstheweatherlike.wav` que você baixou. No Windows, você pode abrir o Windows Explorer, navegar até a pasta `Downloads`, clicar com o botão direito do mouse em `whatstheweatherliike.wav`, clicar em **Enviar para**, clicar em **Pasta compactada (zipada)** e pressionar Enter para aceitar o nome de arquivo padrão.

## <a name="create-a-project-in-the-custom-speech-portal"></a>Criar um projeto no portal de Fala Personalizada

Siga estas etapas para criar um projeto contendo o zip de um arquivo de áudio.

1. Abra o [Speech Studio](https://speech.microsoft.com/) e clique em **Novo projeto**. Digite um nome para este projeto e clique em **Criar**. Seu projeto aparece na lista de Fala Personalizada.
2. Clique no nome do seu projeto. Na guia Dados, clique em **Carregar dados**.
3. O tipo de dados de fala usa como padrão **Somente áudio**, portanto, clique **Avançar**.
4. Dê um nome ao novo conjunto de dados de fala `MyZipOfAudio` e clique em **Avançar**.
5. Clique em **Procurar arquivos...** , navegue até o arquivo `whatstheweatherlike.zip` e clique em **Abrir**.
6. Clique no botão **Carregar** . O navegador carrega o arquivo zip no Speech Studio e o Speech Studio processa o conteúdo.

## <a name="test-a-model"></a>Testar um modelo

Depois que o Speech Studio processa o conteúdo do arquivo zip, você pode reproduzir o áudio de origem ao examinar a transcrição para procurar erros ou omissões. Siga estas etapas para examinar a qualidade da transcrição no navegador.

1. Clique na guia **Teste** e clique em **Adicionar teste**.
2. Neste teste, estamos inspecionando a qualidade de dados somente de áudio, então clique em **Avançar** para aceitar esse tipo de teste.
3. Dê um nome a esse teste `MyModelTest` e clique em **Avançar**.
4. Clique no botão de opção à esquerda de `MyZipOfAudio` e clique em **Avançar**.
5. O menu suspenso **Modelo 1** usa como padrão o modelo de reconhecimento mais recente, portanto, clique em **Criar**. Depois de processar o conteúdo de seu conjunto de dados de áudio, o status do teste será alterado para **Bem-sucedido**.
6. Clique em **MyModelTest**. Os resultados do reconhecimento de fala são exibidos. Clique no triângulo apontando para a direita dentro do círculo para ouvir o áudio e compare o que você ouve com o texto ao lado do círculo.

## <a name="download-detailed-results"></a>Baixar os resultados detalhados

Você pode baixar arquivos que descrevem transcrições em muito mais detalhes. Os arquivos incluem forma a lexical de fala em seus arquivos de áudio e arquivos JSON que contêm detalhes de confiança deslocamento, duração e transcrição sobre cada palavra. Siga estas etapas para ver esses arquivos.

1. Clique em **Download**.
2. Na caixa de diálogo Baixar, desmarque **Áudio** e clique em **Baixar**.
3. Descompacte o arquivo zip baixado e examine os arquivos extraídos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como melhorar a precisão do reconhecimento de fala ao lado de [Treinar um modelo personalizado](../how-to-custom-speech-test-and-train.md).