---
title: Como carregar um documento - conversor personalizado
titleSuffix: Azure Cognitive Services
description: O recurso de upload de documento carrega documentos paralelos (dois documentos em que um é a origem e o outro é a tradução) no serviço.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 83cce31d3eee9b5cca7b54e996e41a270fec0030
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896387"
---
# <a name="upload-a-document"></a>Upload de um documento

Em [Tradutor Personalizado](https://portal.customtranslator.azure.ai), você pode enviar documentos paralelos para treinar seus modelos de tradução. [Documentos paralelos](what-are-parallel-documents.md) são pares de documentos em que um é uma tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.

Antes de carregar seus documentos, examine as [diretrizes de convenção de nomenclatura e formatos de documento](document-formats-naming-convention.md) para garantir que seu arquivo de formato tenha suporte no Tradutor Personalizado.

## <a name="how-to-upload-document"></a>Como carregar o documento?

No portal [ Custom Translator ](https://portal.customtranslator.azure.ai), clique na guia “Documents” para ir para a página de documentos.

![Link de upload de documento](media/how-to/how-to-upload-1.png)


1.  Clique no botão carregar arquivos na página de documentos.

    ![Carregar a página de documento](media/how-to/how-to-upload-2.png)

2.  Na caixa de diálogo, preencha as seguintes informações:

    a.  Tipo de documento:

    -  Treinamento: Estes documentos serão usados para o conjunto de treinamento.
    -  Sintonia: Estes documentos serão usados para o ajuste do ajuste.
    -  Teste: Estes documentos serão usados para o conjunto de testes.
    -  Dicionário de Frases: Estes documentos serão usados para o dicionário de frases.
    -  Dicionário de sentença: Esses documentos serão usados para o dicionário de frase

    b.  Par de idiomas

    c.  Substituir documento, se existir: Marque essa caixa de seleção se desejar substituir documentos existentes com o mesmo nome.

    d.  Preencha a seção relevante para dados paralelos ou dados de combinação.

    -  Dados em paralelo:
        -  Arquivo de origem: selecione o arquivo de idioma de origem em seu computador local.
        -  Arquivo de destino: selecione o arquivo de idioma de destino do seu computador local.
        -  Nome do documento: usado somente se você estiver carregando arquivos paralelos.

    - Dados de caixa de combinação:
        -  Combo de Arquivo: Selecione o arquivo de combinação do seu computador local. Seu arquivo de combinação tem ambas as suas sentenças de origem e de destino. [Convenção de nomenclatura](document-formats-naming-convention.md) é importante para os arquivos de caixa de combinação.

    e.  Clique em carregar

    ![Carregar a caixa de diálogo de documento](media/how-to/how-to-upload-dialog.png)

3.  Neste ponto, estamos processando seus documentos e tentando extrair sentenças. Você pode clicar em "Visualizar progresso do envio" para verificar o status de seus documentos conforme eles são processados.

    ![Carregar caixa de diálogo de processamento de documentos](media/how-to/how-to-upload-processing-dialog.png)

4.  Esta página exibirá o status e os erros de cada arquivo no seu upload. Você pode ver o status do upload passado a qualquer momento clicando na guia "Histórico de uploads".

    ![Carregar a caixa de diálogo de histórico de documento](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Exibir o histórico de upload

Na página de histórico de uploads, você pode ver o histórico de todos os detalhes dos uploads de documentos, como tipo de documento, par de idiomas, status de upload, etc.

1. No portal [Tradutor Personalizado](https://portal.customtranslator.azure.ai), clique na guia Histórico de uploads para visualizar o histórico.

    ![Carregar a guia Histórico](media/how-to/how-to-upload-history-1.png)

2. Esta página mostra o status de todos os seus últimos envios. Ele exibe os uploads do mais recente para o menos recente. Para cada upload, ele mostra o nome do documento, o status do upload, a data de envio, o número de arquivos enviados, o tipo de arquivo enviado e o par de idiomas do arquivo.

    ![Carregar a página de histórico](media/how-to/how-to-document-history-2.png)

3. Clique em qualquer registro de histórico de upload. Na página de detalhes do histórico de uploads, você pode visualizar os arquivos enviados como parte do upload, status de upload do arquivo, idioma do arquivo e mensagem de erro (se houver algum erro no upload).

## <a name="next-steps"></a>Próximas etapas

- Use a [página de detalhes do documento](how-to-view-document-details.md) para revisar a lista de sentenças extraídas.
- [Como treinar um modelo](how-to-train-model.md).
