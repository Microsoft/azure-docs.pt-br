---
title: Passo a passo de edição facial com o Azure Media Analytics | Microsoft Docs
description: Este tópico mostra instruções passo a passo sobre como executar um fluxo de trabalho de edição completo usando o AMSE (Explorador dos Serviços de Mídia do Azure) e o Azure Media Redactor Visualizer (ferramenta de código-fonte aberto).
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: rli; juliako;
ms.openlocfilehash: 3e4844c3174e41ca7f6f5667a2777aba11f70f11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60874823"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Passo a passo de edição facial com o Azure Media Analytics

## <a name="overview"></a>Visão geral

**Azure Media Redactor** é um MP (processador de mídia) do [Azure Media Analytics](media-services-analytics-overview.md) que oferece edição facial escalonável na nuvem. A edição facial permite que você modifique seu vídeo para desfocar rostos de pessoas selecionadas. Você pode querer usar o serviço de edição facial em cenários de segurança pública e de notícias veiculadas. Alguns minutos de vídeo com vários rostos podem levar horas para serem editados manualmente, mas, com esse serviço, o processo de edição facial exigirá apenas algumas etapas simples. Para saber mais, confira [este](https://azure.microsoft.com/blog/azure-media-redactor/)blog.

Para obter detalhes sobre o **Azure Media Redactor**, veja o tópico [Visão geral da edição de face](media-services-face-redaction.md).

Este tópico mostra instruções passo a passo sobre como executar um fluxo de trabalho de edição completo usando o AMSE (Explorador dos Serviços de Mídia do Azure) e o Azure Media Redactor Visualizer (ferramenta de código-fonte aberto).

Para saber mais, confira [este](https://azure.microsoft.com/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Fluxo de trabalho do Explorador dos Serviços de Mídia do Azure

A maneira mais fácil de começar a usar o Redactor é com a ferramenta de código-fonte aberto AMSE no GitHub. Você pode executar um fluxo de trabalho simplificado no modo **combinado** se não precisar de acesso ao json de anotação ou às imagens jpg da face.

### <a name="download-and-setup"></a>Download e configuração

1. Baixe a ferramenta AMSE [aqui](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Faça logon em sua conta dos Serviços de Mídia usando sua chave de serviço.

    Para obter as informações de chave e nome da conta, vá para o [Portal do Azure](https://portal.azure.com/) e selecione sua conta do AMS. Em seguida, selecione Configurações > Chaves. A janela Gerenciar chaves mostra o nome da conta e as chaves primária e secundária são exibidas. Copie os valores do nome da conta e a chave primária.

![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Primeira aprovação – modo de análise

1. Carregue seu arquivo de mídia em Ativo –> Carregar, ou arrastando e soltando. 
1. Clique com botão direito e processe o arquivo de mídia usando a Análise de Mídia –> Azure Media Redactor –> Modo de Análise. 


![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

A saída incluirá um arquivo json de anotações com dados de localização de face, bem como um jpg de cada face detectada. 

![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Segunda aprovação – modo de edição

1. Carregue seu ativo de vídeo original na saída da primeira aprovação e o defina como um ativo primário. 

    ![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Opcional) Carregue um arquivo “Dance_idlist.txt”, que inclui uma lista delimitada por nova linha das IDs que você deseja editar. 

    ![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Opcional) Faça as edições no arquivo annotations.json, por exemplo, aumentar os limites da caixa delimitadora. 
4. Clique com botão direito no ativo de saída da primeira aprovação, selecione o Redactor e execute com o modo **Editar**. 

    ![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Baixe ou compartilhe o ativo de saída editado final. 

    ![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Ferramenta de código-fonte aberto Azure Media Redactor Visualizador

Uma [ferramenta visualizadora](https://github.com/Microsoft/azure-media-redactor-visualizer) de código-fonte aberto que foi projetada para ajudar os desenvolvedores novatos no formato de anotações com análise e uso da saída.

Após a clonagem do repositório, para executar o projeto, você precisará baixar o FFMPEG no [site oficial](https://ffmpeg.org/download.html).

Se você for um desenvolvedor que está tentando analisar os dados de anotação de JSON, examine Models.MetaData para obter exemplos de código.

### <a name="set-up-the-tool"></a>Configurar a ferramenta

1.  Baixe e compile toda a solução. 

    ![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Baixe FFMPEG [aqui](https://ffmpeg.org/download.html). Este projeto foi desenvolvido originalmente com a versão be1d324 (2016-10-04) e com vinculação estática. 
3.  Copie ffmpeg.exe e ffprobe.exe na mesma pasta de saída que AzureMediaRedactor.exe. 

    ![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Execute AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Usar a ferramenta

1. Processe o vídeo em sua conta dos Serviços de Mídia do Azure com o MP do Redactor no modo Análise. 
2. Baixe o arquivo de vídeo original e a saída do trabalho Edição - Análise. 
3. Execute o aplicativo visualizador e escolha os arquivos acima. 

    ![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Visualize o arquivo. Selecione quais faces você gostaria de desfocar na barra lateral à direita. 
    
    ![Redação de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  O campo de texto inferior será atualizado com as IDs de face. Crie um arquivo chamado "idlist.txt" com essas IDs como uma lista delimitada por nova linha. 

    >[!NOTE]
    > O idlist.txt deve ser salvo em ANSI. É possível usar o bloco de notas para salvar em ANSI.
    
6.  Carregue esse arquivo no ativo de saída da etapa 1. Carregue o vídeo original também nesse ativo e defina-o como ativo primário. 
7.  Execute o trabalho de Edição nesse ativo com o modo de "Edição" para obter o último vídeo editado. 

## <a name="next-steps"></a>Próximas etapas 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Links relacionados
[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstrações do Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Anunciando a redação da face da Análise de Mídia do Azure](https://azure.microsoft.com/blog/azure-media-redactor/)
