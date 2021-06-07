---
title: 'Tutorial: Criar um aplicativo de processamento de formulários com o AI Builder – Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você usará o AI Builder para criar e treinar um aplicativo de processamento de formulários.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: lajanuar
ms.openlocfilehash: 30c44ac60783132344cc018135d2c248f91f46bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467112"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Tutorial: Criar um aplicativo de processamento de formulários com o AI Builder

O [AI Builder](/ai-builder/overview) é uma funcionalidade do Power Platform que permite automatizar processos e prever resultados para melhorar o desempenho empresarial. Você pode usar o processamento de formulários do AI Builder para criar modelos de IA que identificam e extraem pares de chave-valor e dados de tabela de documentos de formulário.

> [!NOTE]
> Esse projeto também está disponível como um [módulo do Microsoft Learn](/learn/modules/get-started-with-form-processing/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um modelo de IA de processamento de formulários
> * Treinar seu modelo
> * Publicar o seu modelo para uso no Azure Power Apps ou no Power Automate

## <a name="prerequisites"></a>Pré-requisitos

* Um conjunto de pelo menos cinco formulários do mesmo tipo a serem usados para dados de treinamento/teste. Confira [Criar um conjunto de dados de treinamento](./build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este guia de início rápido, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2128080).
* Uma licença do Power Apps ou do Power Automate – confira o [Guia de Licenciamento](https://go.microsoft.com/fwlink/?linkid=2085130). A licença deve incluir o [Common Data Service](https://powerplatform.microsoft.com/common-data-service/).
* Um [complemento ou uma avaliação](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409) do AI Builder.


## <a name="create-a-form-processing-project"></a>Criar um projeto de processamento de formulários

1. Acesse o [Power Apps](https://make.powerapps.com/) ou o [Power Automate](https://flow.microsoft.com/signin) e entre com a sua conta da organização.
1. No painel esquerdo, selecione **AI Builder** > **Criar**.
1. Selecione o cartão **Processamento de Formulários**.
1. Digite um nome para o seu modelo.
1. Selecione **Criar**.

## <a name="upload-and-analyze-documents"></a>Carregar e analisar documentos

Na página **Adicionar documentos**, você precisa fornecer documentos de amostra para treinar o seu modelo para o tipo de formulário do qual você deseja extrair informações. Depois de carregar os seus documentos, o AI Builder analisa-os para verificar se são suficientes para treinar um modelo.

> [!NOTE]
> Atualmente, o AI Builder não dá suporte aos seguintes tipos de dados de entrada de processamento de formulários:
>
> - Tabelas complexas (tabelas aninhadas, cabeçalhos mesclados ou células e assim por diante)
> - Caixas de seleção ou botões de opção
> - Documentos PDF com mais de 50 páginas
> - PDFs preenchíveis
>
> Para obter mais informações sobre os requisitos para documentos de entrada, confira [requisitos de entrada](./overview.md#input-requirements).

### <a name="upload-your-documents"></a>Carregar os seus documentos

1. Selecione **Adicionar documentos**, selecione no mínimo cinco documentos e, em seguida, selecione **Carregar**.
1. Após a conclusão do upload, selecione **Fechar**.
1. Em seguida, selecione **Analisar**.

> [!NOTE] 
> Depois de carregar esses documentos, você ainda poderá remover alguns dos documentos ou carregar outros.

> [!div class="mx-imgBorder"]
> ![Captura de tela que realça o botão Adicionar documentos.](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Analisar os seus documentos

Durante a etapa de análise, o AI Builder examina os documentos que você carregou e detecta os campos e as tabelas. O tempo necessário para concluir essa operação depende do número de documentos fornecidos, mas, na maioria dos casos, ele deve levar apenas alguns minutos.

Quando a análise for concluída, selecione a miniatura para abrir a experiência de seleção de campo.

> [!IMPORTANT]
> Se a análise falhou, é provável que tenha sido porque o AI Builder não detectou texto estruturado nos seus documentos. Verifique se os documentos que você atualizou seguem os [requisitos de entrada](./overview.md#input-requirements).

## <a name="select-your-form-fields"></a>Selecione os seus campos de formulário

Na página de seleção de campo, você escolhe os campos que são importantes para você:

1. Para selecionar um campo, clique em um retângulo que indica um campo detectado no documento ou selecione vários campos clicando e arrastando. Você também pode selecionar campos no painel do lado direito.
1. Clique no nome do campo selecionado se desejar renomeá-lo para alinhá-lo às suas necessidades ou normalizar os rótulos extraídos.

    Quando você clicar em um campo detectado, as seguintes informações serão exibidas:

    - **Nome do campo**: O nome do rótulo do campo detectado.
    - **Valor do campo**: O valor do campo detectado.

> [!div class="mx-imgBorder"]
> ![página de marcação](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Rotular campos não detectados

Se o campo que você deseja rotular não foi detectado automaticamente pelo modelo, você pode desenhar um retângulo ao redor do valor e digitar um nome de rótulo na caixa de diálogo que aparece.

## <a name="train-your-model"></a>Treinar seu modelo

1. Selecione **Próximo** para verificar os campos de formulário selecionados. Se tudo estiver correto, selecione **Treinar** para treinar o seu modelo.

    > [!div class="mx-imgBorder"]
    > ![página de treinamento](./media/tutorial-ai-builder/summary-train-page.png)
1. Quando o treinamento for concluído, selecione **Acessar a página Detalhes** na tela **Treinamento completo**.
## <a name="quick-test-your-model"></a>Fazer um teste rápido do seu modelo

A página Detalhes permite que você teste o seu modelo antes de publicá-lo ou usá-lo:

1. Na página Detalhes, selecione **Teste rápido**.
2. Você pode arrastar e soltar um documento ou selecionar **Carregar do meu dispositivo** para carregar o arquivo de teste. O teste rápido deve levar apenas alguns segundos antes de exibir os resultados.
3. Você pode selecionar **Iniciar novamente** para executar outro teste ou **Fechar** se tiver terminado.

### <a name="troubleshooting-tips"></a>Dicas de solução de problemas

Se você estiver obtendo resultados inválidos ou pontuações de baixa confiança para determinados campos, tente as seguintes dicas:

- Treine novamente usando formulários com valores diferentes em cada campo.
- Treine novamente usando um conjunto maior de documentos de treinamento. Quanto mais documentos você marcar, mais o AI Builder aprenderá a reconhecer os campos.
- Você pode otimizar arquivos PDF selecionando apenas determinadas páginas para treinar. Use a opção **Imprimir** > **Imprimir em PDF** para selecionar determinadas páginas dentro do documento.

## <a name="publish-your-model"></a>Publicar o seu modelo

Se estiver satisfeito com o seu modelo, selecione **Publicar** para publicá-lo. Quando a publicação for concluída, o seu modelo será promovido como **Publicado** e estará pronto para ser usado.

> [!div class="mx-imgBorder"]
> ![página publicar modelo](./media/tutorial-ai-builder/model-page.png)

Depois de publicar o modelo de processamento de formulários, você poderá usá-lo em um [aplicativo de tela do Power Apps](/ai-builder/form-processor-component-in-powerapps) ou no [Power Automate](/ai-builder/form-processing-model-in-flow).

## <a name="next-steps"></a>Próximas etapas

Siga a documentação do AI Builder para usar um modelo de processamento de formulários.

* [Usar o componente processador de formulários no Power Apps](/ai-builder/form-processor-component-in-powerapps)
* [Usar um modelo de processamento de formulários no Power Automate](/ai-builder/form-processing-model-in-flow)