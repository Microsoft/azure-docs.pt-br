---
title: Analisar sua mídia usando o Portal do Azure | Microsoft Docs
description: Este tópico aborda como processar sua mídia com processadores de mídia (MPs) da Análise de Mídia usando o Portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: d3917f65d8be08d6355013393f6c6675ea6c7fc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61131710"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analisar sua mídia usando o Portal do Azure 
> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Visão geral
A Análise dos Serviços de Mídia do Azure é uma coleção de componentes de fala e visão (em escala empresarial, conformidade, segurança e alcance global) que facilitam para as organizações e empresas obterem ideias práticas de seus arquivos de vídeo. Para maiores detalhes da Análise de Serviços de Mídia do Azure consulte [esse](media-services-analytics-overview.md) tópico. 

Este tópico aborda como processar sua mídia com processadores de mídia (MPs) da Análise de Mídia usando o Portal do Azure. Os MPs da Análise de Mídia produzem arquivos MP4 ou arquivos JSON. Se um processador de mídia produzir um arquivo MP4, você poderá baixar o arquivo progressivamente. Se um processador de mídia produzir um arquivo JSON, você poderá baixar o arquivo do Armazenamento de Blobs do Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Escolha um ativo que você deseja analisar
1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Na janela **Configurações**, selecione **Ativos**.  
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Selecione o ativo que você deseja analisar e pressione o botão **Analisar**.
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Na janela **Processar ativo de mídia com Análise de Mídia**, selecione o processador. 
   
    O restante do artigo explica porque e como usar cada processador. 
5. Pressione **Criar** para iniciar um trabalho.

## <a name="azure-media-indexer"></a>Indexador de Mídia do Azure
O processador de mídia do **Azure Media Indexer** permite tornar conteúdo e arquivos de mídia pesquisáveis, bem como gerar faixas de legendagem oculta. Esta seção fornece alguns detalhes sobre as opções que você especifica para esse MP.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Linguagem
O idioma natural a ser reconhecido no arquivo de multimídia. Por exemplo, inglês ou espanhol. 

### <a name="captions"></a>Legendas
Você pode escolher um formato de legenda que será gerado do seu conteúdo. Um trabalho de indexação pode gerar arquivos de legenda oculta nos seguintes formatos:  

* **SAMI**
* **TTML**
* **WebVTT**

Arquivos de CC (Legenda Oculta) nesses formatos podem ser usados para tornar os arquivos de áudio e vídeo acessíveis para pessoas com deficiência auditiva.

### <a name="aib-file"></a>Arquivo AIB
Selecione esta opção se você deseja gerar o Arquivo de blob de índice de áudio para uso com o IFilter personalizado do SQL Server. Para saber mais, confira [este](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Palavras-chave
Selecione esta opção se você gostaria de gerar um arquivo XML de palavras-chave. Esse arquivo contém palavras-chave extraídas do conteúdo de fala, com informações de frequência de compensação.

### <a name="job-name"></a>Nome do trabalho
Um nome amigável que permite identificar o trabalho. [Esse](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o progresso de um trabalho. 

### <a name="output-file"></a>Arquivo de saída
Um nome amigável que permite identificar o conteúdo de saída. 

### <a name="speed"></a>Velocidade
Especifique a velocidade com a qual acelerar o vídeo de entrada. A saída é uma representação estabilizada e de lapso de tempo do vídeo de entrada.

### <a name="job-name"></a>Nome do trabalho
Um nome amigável que permite identificar o trabalho. [Esse](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o progresso de um trabalho. 

### <a name="output-file"></a>Arquivo de saída
Um nome amigável que permite identificar o conteúdo de saída. 

## <a name="azure-media-face-detector"></a>Detector de Rostos em Mídias do Azure
O MP (processador de mídia) **Azure Media Face Detector** permite que você conte e acompanhe movimentos, e até mesmo meça a participação e a reação do público por meio de expressões faciais. Este serviço contém dois recursos: 

* **Detecção facial**
  
    A detecção facial localiza e acompanha as faces humanas em um vídeo. Várias faces podem ser detectadas e, consequentemente, acompanhadas à medida que se movimentam, com os metadados de hora e local retornados em um arquivo JSON. Durante o acompanhamento, esse recurso tentará atribuir uma ID consistente à mesma face enquanto a pessoa se movimenta pela tela, mesmo se for obstruída ou deixar brevemente o quadro.
  
  > [!NOTE]
  > Esses serviços não realizam o reconhecimento facial. Uma pessoa que sair do enquadramento ou ficar obstruída por muito tempo receberá uma nova ID quando retornar.
  > 
  > 
* **Detecção de emoções**
  
    A Detecção de Emoções é um componente opcional do Face Detection Media Processor que retorna uma análise sobre vários atributos emocionais das faces detectadas, incluindo felicidade, tristeza, medo, irritação e muito mais. 

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modo de detecção
Um dos modos a seguir pode ser usado pelo processador:

* detecção facial
* detecção de emoções faciais
* detecção de emoção de agregada

### <a name="job-name"></a>Nome do trabalho
Um nome amigável que permite identificar o trabalho. [Esse](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o progresso de um trabalho. 

### <a name="output-file"></a>Arquivo de saída
Um nome amigável que permite identificar o conteúdo de saída. 

## <a name="azure-media-motion-detector"></a>Detector de Movimento em Mídias do Azure
O MP (processador de mídia) **Azure Media Motion Detector** permite a identificação eficiente de seções de interesse em um vídeo longo e rotineiro. A detecção de movimento pode ser usada em sequências de imagens estáticas para identificar seções do vídeo onde ocorrem movimentos. Ela gera um arquivo JSON contendo metadados com carimbos de hora e a região delimitadora onde o evento ocorreu.

Essa tecnologia, destinada à segurança de feeds de vídeo, é capaz de categorizar o movimento em eventos relevantes e falsos positivos, como mudanças de iluminação e sombras. Isso permite a geração de alertas de segurança por meio de feeds da câmera, sem gerar incontáveis eventos irrelevantes, além de permitir também a extração de momentos de interesse dos vídeos de vigilância extremamente longos.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Miniaturas de Vídeo de Mídia do Azure
Esse processador pode ajudá-lo a criar resumos de vídeos de longa duração com a seleção automática de snippets interessantes do vídeo de origem. Isso será útil quando você desejar fornecer uma visão geral rápida do que esperar de um vídeo de longa duração. Para obter informações detalhadas e exemplos, confira [Usar miniaturas de vídeo da Mídia do Azure para criar um resumo de vídeo](media-services-video-summarization.md)

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome do trabalho
Um nome amigável que permite identificar o trabalho. [Esse](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o progresso de um trabalho. 

### <a name="output-file"></a>Arquivo de saída
Um nome amigável que permite identificar o conteúdo de saída. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Este processador ajuda a detectar possível conteúdo adulto e erótico em vídeos. O processador detecta automaticamente capturas e quadros-chave no vídeo. Ele classifica os quadros-chave de conteúdo adulto ou erótico possíveis e sugere análises com base em limites padrão. Para obter informações detalhadas e exemplos, consulte [Usar Azure Media Content Moderator para moderar o vídeo](media-services-content-moderation.md)

![Moderar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Version 
Use "2.0".

### <a name="mode"></a>Mode
A versão 2.0 ignora a configuração `Mode`.

## <a name="next-steps"></a>Próximas etapas
Exibir os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
