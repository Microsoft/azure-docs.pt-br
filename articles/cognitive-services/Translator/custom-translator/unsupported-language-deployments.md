---
title: Implantações de idioma sem suporte - conversor personalizado
titleSuffix: Azure Cognitive Services
description: Como implantar os pares de idioma sem suporte no conversor personalizado.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 284a7e98c088edb73a2c770a4c14bb0a479a6621
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436064"
---
# <a name="unsupported-language-deployments"></a>Implantações de linguagem sem suporte

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Com a desativação futura do Microsoft Translator Hub, Microsoft será desimplantar todos os modelos implantados no momento por meio do Hub. Muitos de vocês têm modelos implantados no Hub cujos pares de idiomas não têm suporte no conversor personalizado.  Não queremos que os usuários nessa situação não tenham nenhum recurso para converter seu conteúdo.

Agora temos um processo que permite que você implante seus modelos sem suporte por meio da conversora personalizado.  Esse processo permite que você continue traduzir o conteúdo usando a API V3 mais recente.  Esses modelos serão hospedados até que você optar por desimplantá-los ou o par de idiomas seja disponibilizado no conversor personalizado.  Este artigo explica o processo para implantar modelos com pares de idiomas sem suporte.

## <a name="prerequisites"></a>Pré-requisitos

Para modelos de modo a ser candidatos para a implantação, eles devem atender aos seguintes critérios:
* O projeto que contém o modelo deve foram migrado do Hub para o conversor personalizado usando a ferramenta de migração.  O processo de migração de projetos e espaços de trabalho pode ser encontrado [aqui](how-to-migrate.md).
* O modelo deve estar no estado implantado quando ocorre a migração.  
* O par de linguagem do modelo deve ser um par de idioma sem suporte no conversor personalizado.  Pares de idiomas em que uma linguagem com suporte para ou do inglês, mas o par em si não inclui o inglês, são candidatos para implantações de idioma sem suporte.  Por exemplo, um modelo de Hub para francês para o par de idioma alemão é considerado um par de idioma com suporte embora francês para inglês e inglês do alemão são até mesmo idioma sem suporte par.

## <a name="process"></a>Process
Depois de migrar modelos do Hub que são candidatos para a implantação, você pode encontrá-los, vá para o **as configurações** página do seu espaço de trabalho e a rolagem até o final da página onde você verá um **sem suporte Treinamentos do tradutor Hub** seção.  Nesta seção só aparecerá se você tiver projetos que atendem aos pré-requisitos mencionados acima.

![Como migrar do Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Dentro de **treinamentos de Hub de tradução sem suporte** página de seleção, o **não solicitado treinamentos** guia contém os modelos que estão qualificados para a implantação.  Selecione os modelos que você deseja implantar e enviar uma solicitação.   Antes do prazo de implantação de 30 de abril, você pode selecionar tantos modelos quanto desejar para implantação.
 
![Como migrar do Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Depois de enviada, o modelo não estarão disponível na **reconhecimento treinamentos** guia e em vez disso, serão exibidos na **solicitado treinamentos** guia.  Você pode exibir seu treinamentos solicitados a qualquer momento.

![Como migrar do Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>O que vem a seguir?

Os modelos selecionados para implantação são salvas depois que o Hub está desativado e todos os modelos são cancelados.  Você tem até 24 de maio para enviar solicitações de implantação de modelos sem suporte.  Implantaremos esses modelos em 15 de junho no ponto em que eles poderão ser acessados por meio da API do Translator V3.  Além disso, ele serão disponibilizado por meio da API V2 até 1º de julho.  

Para obter mais informações sobre datas importantes na substituição da verificação de Hub [aqui](https://www.microsoft.com/translator/business/hub/).
Encargos de hospedagem de uma vez implantados, normais serão aplicada.  Ver [preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) para obter detalhes.  

Ao contrário de modelos padrão do conversor personalizado, os modelos de Hub só estará disponíveis em uma única região, portanto, não serão aplicados os encargos de hospedagem de várias regiões.  Uma vez implantado, você poderá desimplantar seu modelo de Hub a qualquer momento por meio do projeto migrado conversor personalizado.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo](how-to-train-model.md).
- Comece a usar o modelo de tradução personalizado implantado por meio da [API de Tradução de Texto V3 da Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
