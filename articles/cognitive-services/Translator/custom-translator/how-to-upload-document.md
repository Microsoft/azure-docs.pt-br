---
title: Como carregar um documento - conversor personalizado
titleSuffix: Azure Cognitive Services
description: Usando o recurso de upload de documentos, você pode carregar documento paralelo para seu treinamentos. Documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 7df7dd44716e98c74282bd0f0700ec7881ef31ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461272"
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

    -  Treinamento: Esses documentos serão usados para o conjunto de treinamento.
    -  Ajuste: Esses documentos serão usados para o conjunto de ajuste.
    -  Teste: Esses documentos serão usados para o conjunto de teste.
    -  Dicionário de Frases: Esses documentos serão usados para o dicionário de frases.
    -  Dicionário de Sentenças: Esses documentos serão usados para o dicionário de sentenças

    b.  Par de idiomas

    c.  Substituir o documento se existir: Marque essa caixa de seleção se desejar substituir os documentos existentes com o mesmo nome.

    d.  Preencha a seção relevante para dados paralelos ou dados de combinação.

    -  Dados em paralelo:
        -  Arquivo de origem: Selecione o arquivo do idioma de origem no computador local.
        -  Arquivo de destino: Selecione o arquivo do idioma de destino no computador local.
        -  Nome do documento: Usado somente se você estiver carregando arquivos paralelos.

    - Dados de caixa de combinação:
        -  Arquivo de Combinação: Selecione o arquivo de combinação no computador local. Seu arquivo de combinação tem ambas as suas sentenças de origem e de destino. [Convenção de nomenclatura](document-formats-naming-convention.md) é importante para os arquivos de caixa de combinação.

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
