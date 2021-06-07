---
title: Implantações de idioma sem suporte – Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo mostra como implantar pares de idiomas sem suporte no Tradutor personalizado de serviços cognitivas do Azure.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: fb31388647fc6022a2e6670baf7b3e73f345c36a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898063"
---
# <a name="unsupported-language-deployments"></a>Implantações de linguagem sem suporte

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Com a próxima aposentadoria do Hub do Microsoft Translator, a Microsoft estará desimplantando todos os modelos atualmente implantados por meio do Hub. Muitos de vocês têm modelos implantados no Hub cujos pares de idiomas não têm suporte no Tradutor personalizado.  Não queremos que os usuários nessa situação não tenham um recurso para a tradução de seu conteúdo.

Agora temos um processo que permite que você implante seus modelos sem suporte por meio do tradutor personalizado.  Esse processo permite que você continue a converter conteúdo usando a API v3 mais recente.  Esses modelos serão hospedados até você optar por desimplantá-los ou o par de idiomas se tornar disponível no Tradutor personalizado.  Este artigo explica o processo de implantação de modelos com pares de idiomas sem suporte.

## <a name="prerequisites"></a>Pré-requisitos

Para que seus modelos sejam candidatos à implantação, eles devem atender aos seguintes critérios:
* O projeto que contém o modelo deve ter sido migrado do hub para o tradutor personalizado usando a ferramenta de migração.  O processo para migrar projetos e espaços de trabalho pode ser encontrado [aqui](how-to-migrate.md).
* O modelo deve estar no estado implantado quando a migração ocorrer.  
* O par de idiomas do modelo deve ser um par de idiomas sem suporte no Tradutor personalizado.  Os pares de idiomas nos quais um idioma tem suporte no ou em inglês, mas o par não inclui o inglês, são candidatos para implantações de idioma sem suporte.  Por exemplo, um modelo de Hub para um par de idiomas francês a alemão é considerado um par de idiomas sem suporte, embora francês a inglês e inglês para alemão sejam um par de idiomas com suporte.

## <a name="process"></a>Processo
Depois de migrar os modelos do Hub que são candidatos à implantação, você poderá encontrá-los acessando a página de **configurações** do seu espaço de trabalho e rolando até o final da página em que você verá uma seção de **treinamentos do Hub do tradutor sem suporte** .  Esta seção só aparecerá se você tiver projetos que atendam aos pré-requisitos mencionados acima.

![Captura de tela que realça a seção de treinamentos do Hub do tradutor sem suporte.](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Na página seleção de **treinamentos do Hub do tradutor sem suporte** , a guia **treinamentos não solicitados** contém modelos qualificados para implantação.  Selecione os modelos que você deseja implantar e envie uma solicitação.   Antes do prazo de implantação de 30 de abril, você pode selecionar quantos modelos desejar para a implantação.
 
![Captura de tela que mostra a guia treinamentos não solicitados.](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Depois de enviado, o modelo não estará mais disponível na guia **treinamentos não solicitados** e, em vez disso, aparecerá na guia **treinamentos solicitados** .  Você pode exibir os treinamentos solicitados a qualquer momento.

![Como migrar do Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>O que vem a seguir?

Os modelos selecionados para implantação são salvos depois que o Hub é encerrado e todos os modelos são desimplantados.  Você tem até 24 de maio para enviar solicitações de implantação de modelos sem suporte.  Implantaremos esses modelos em 15 de junho, em que eles poderão ser acessados por meio da API do Translator v3.  Além disso, eles estarão disponíveis por meio da API v2 até 1º de julho.  

Para obter mais informações sobre datas importantes na reprovação da verificação do Hub [aqui](https://www.microsoft.com/translator/business/hub/).
Depois de implantados, os encargos de hospedagem normais serão aplicados.  Consulte os [preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) para obter detalhes.  

Ao contrário dos modelos de Tradutor personalizado padrão, os modelos de Hub só estarão disponíveis em uma única região, portanto, os encargos de Hospedagem de várias regiões não serão aplicados.  Depois de implantado, você poderá desimplantar o modelo de Hub a qualquer momento por meio do projeto de Tradutor personalizado migrado.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo](how-to-train-model.md).
- Comece a usar o modelo de tradução personalizado implantado por meio da [API de Tradução de Texto V3 da Microsoft](../reference/v3-0-translate.md?tabs=curl).