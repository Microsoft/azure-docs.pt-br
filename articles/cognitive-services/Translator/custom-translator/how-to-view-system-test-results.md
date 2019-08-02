---
title: Visualizar resultados de testes do sistema e implantação - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Quando seu treinamento for bem-sucedido, revise os testes do sistema para analisar seus resultados de treinamento. Se você estiver satisfeito com os resultados do treinamento, faça uma solicitação de implantação para o modelo treinado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f5e1a21f24fdd2458d4803d6ed1675455fa5037d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595606"
---
# <a name="view-system-test-results"></a>Exibir resultados de teste do sistema

Quando seu treinamento for bem-sucedido, revise os testes do sistema para analisar seus resultados de treinamento. Se você estiver satisfeito com os resultados do treinamento, faça uma solicitação de implantação para o modelo treinado.

## <a name="system-test-results-page"></a>Página de resultados de teste do sistema

Selecione um projeto, selecione a guia de modelos desse projeto, localize o modelo que deseja usar e, finalmente, selecione a guia de teste.

A guia teste mostra:

1.  **Resultados de Teste do sistema:** O resultado do processo de teste nos treinamentos. O processo de teste produz a pontuação do BLEU.

    **Contagem de sentença:** Sentenças paralelas quantas foram usadas no conjunto de teste.

     **Pontuação BLEU:** Pontuação BLEU gerada para um modelo após a conclusão do treinamento.

    **Status:** Indica se o processo de teste está concluído ou em andamento.

    ![Resultados de teste do sistema](media/how-to/how-to-system-test-results.png)

2.  Clique nos resultados do teste do sistema, e isso levará você a testar a página de detalhes do resultado. Esta página mostra a tradução automática de sentenças que faziam parte do conjunto de dados de teste.

3.  A tabela na página de detalhes do resultado do teste tem duas colunas - uma para cada idioma do par. A coluna do idioma de origem mostra a frase a ser traduzida. A coluna para o idioma de destino contém duas frases em cada linha.

    **Ref:** Essa frase é a conversão de referência da sentença origem conforme fornecido no conjunto de dados de teste.

    **MT:** Essa frase é a tradução automática da sentença origem feita pelo modelo compilado depois que o treinamento foi realizado.

    ![Comparação dos resultados do teste do sistema](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Teste para fazer o download

Clique no link Baixar Traduções para fazer o download de um arquivo zip. O zip contém as traduções automáticas de sentenças de origem no conjunto de dados de teste.

![Teste para fazer o download](media/how-to/how-to-system-test-download.png)

Este arquivo zip para fazer o download contém três arquivos.

1.  **custom.mt.txt:** Este arquivo contém traduções automáticas de frases do idioma de origem no idioma de destino feito pelo modelo treinado com os dados do usuário.

2.  **ref.txt:** Este arquivo contém traduções fornecidas pelo usuário de sentenças no idioma de origem no idioma de destino.

3.  **source.txt:** Este arquivo contém sentenças no idioma de origem.

    ![Resultados de testes do sistema descarregadas](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Implantar um modelo

Para solicitar uma implantação:

1.  Selecione um projeto, vá para a guia de modelos.

2. Para um modelo treinado com êxito, ela mostra o botão "Implantar", se não for implantado.

    ![Implantar modelo](media/how-to/how-to-deploy-model.png)

3.  Clique em implantar.
4.  Selecione a região onde você deseja que seu modelo seja **implantado** e clique em Salvar. Você pode selecionar **Implantado** para várias regiões.

    ![Implantar modelo](media/how-to/how-to-deploy-model-regions.png)

5.  Você pode ver o status do seu modelo na coluna "Status".

>[!Note]
>O tradutor personalizado dá suporte a 10 modelos implantados em um espaço de trabalho a qualquer momento.

## <a name="update-deployment-settings"></a>Atualizar configurações de implantação

Para atualizar configurações de implantação:

1.  Selecione um projeto, vá para a guia de **Modelos**.

2. Para um modelo implantado com êxito, ele mostra um botão **Atualização**.

    ![Implantar modelo](media/how-to/how-to-update-undeploy-model.png)

3.  Selecione **Atualização**.
4.  Selecione **Implantado** ou **Não Implantado** para as regiões onde você deseja seu modelo implantado ou não implantando, em seguida, clique em **Salvar**.

    ![Implantar modelo](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Se você selecionar **Não implantado** para todas as regiões, o modelo é desinstalado de todas as regiões e colocado em um estado não implantado. Agora está disponível para uso.

## <a name="next-steps"></a>Próximas etapas

- Comece a usar o modelo de tradução personalizado implantado por meio da [API de Tradução de Texto V3 da Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Saiba mais [como gerenciar configurações](how-to-manage-settings.md) para compartilhar seu espaço de trabalho, gerenciar a chave de assinatura.
- Aprenda [como migrar sua área de trabalho e projeto](how-to-migrate.md) de [Microsoft Translator Hub](https://hub.microsofttranslator.com)
