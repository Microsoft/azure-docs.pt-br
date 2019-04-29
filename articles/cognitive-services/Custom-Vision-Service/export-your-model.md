---
title: Exporte seu modelo para celular - Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como exportar seu modelo para uso na criação de aplicativos móveis.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 405b6ebd06091536749751a94362d8c4a6495dbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605421"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exportar seu modelo para uso com dispositivos móveis

O Serviço de Visão Personalizada permite que os classificadores sejam exportados para execução offline. É possível inserir o classificador exportado para um aplicativo e executá-lo localmente em um dispositivo para classificação em tempo real.

O Serviço de Visão Personalizada dá suporte às seguintes exportações:

* __Tensorflow__ para __Android__.
* __CoreML__ para __iOS11__.
* __ONNX__ para __Windows ML__.
* Um __contêiner__ do Windows ou Linux. O contêiner inclui um modelo Tensorflow e um código de serviço para usar a API do Serviço de Visão Personalizada. 

> [!IMPORTANT]
> O Serviço de Visão Personalizada exporta apenas domínios __compactos__. Os modelos gerados por domínios compactos são otimizados para as restrições de classificação em tempo real em dispositivos móveis. Classificadores compilados com um domínio compacto podem ser ligeiramente menos precisos que um domínio padrão com a mesma quantidade de dados de treinamento.
>
> Para obter informações sobre como aprimorar os classificadores, consulte o documento [Aprimorar o classificador](getting-started-improving-your-classifier.md).

## <a name="convert-to-a-compact-domain"></a>Converter para um domínio compacto

> [!NOTE]
> As etapas nesta seção serão aplicáveis somente se houver um classificador existente que não esteja definido como domínio compacto.

Para converter o domínio de um classificador existente, execute as seguintes etapas:

1. Na [página Visão personalizada](https://customvision.ai), selecione o ícone de __Página Inicial__ para exibir uma lista dos projetos. Também é possível usar [https://customvision.ai/projects](https://customvision.ai/projects) para consultar os projetos.

    ![Imagem do ícone de página inicial e lista de projetos](./media/export-your-model/projects-list.png)

2. Selecione um projeto e, em seguida, selecione o ícone de __Engrenagem__ no canto superior direito da página.

    ![Imagem do ícone de engrenagem](./media/export-your-model/gear-icon.png)

3. Na seção __Domínios__, selecione um domínio __compacto__. Selecione __Salvar Alterações__ para salvar as alterações.

    ![Imagem da seleção de domínios](./media/export-your-model/domains.png)

4. Na parte superior da página, selecione __Treinar__ para treinar novamente usando o novo domínio.

## <a name="export-your-model"></a>Exportar seu modelo

Para exportar o modelo após o retreinamento, use as seguintes etapas:

1. Acesse a guia **Desempenho** e selecione __Exportar__. 

    ![Imagem do ícone de exportação](./media/export-your-model/export.png)

    > [!TIP]
    > Se a entrada __Exportar__ não estiver disponível, a iteração selecionada não utilizará um domínio compacto. Use a seção __Iterações__ desta página para selecionar uma iteração que usa um domínio compacto e, em seguida, selecione __Exportar__.

2. Selecione o formato de exportação e, em seguida, selecione __Exportar__ para baixar o modelo.

## <a name="next-steps"></a>Próximas etapas

Integre o modelo exportado em um aplicativo. Vários aplicativos de exemplo estão disponíveis:

* Uma exemplo para [usar o modelo do CoreML exportado em um aplicativo iOS](https://go.microsoft.com/fwlink/?linkid=857726) para classificação de imagem em tempo real com Swift
* Exemplo de aplicativo iOS para [usar o modelo do CoreML exportado com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) para classificação de imagem em tempo real 
* Exemplo para [usar o modelo do Tensorflow exportado em um aplicativo Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) para classificação de imagens em tempo real 
* [Usar o modelo do Tensorflow com Windows ](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-model-python)
* Exemplo para [usar o modelo do ONNX exportado com Windows Machine Learning](https://azure.microsoft.com/resources/samples/cognitive-services-onnx-customvision-sample/)
