---
title: Aprimorar a sintetização com a Voz Personalizada – serviço de Fala
titleSuffix: Azure Cognitive Services
description: A Voz Personalizada é um conjunto de ferramentas online que permitem que você crie uma voz única e reconhecível para sua marca. Para começar, bastam alguns arquivos de áudio e as transcrições deles. Siga os links abaixo para começar a criar uma experiência de conversão de fala em texto personalizada.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 99af0ee46e2827ac8a5749d58bb19cf85f96aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577454"
---
# <a name="get-started-with-custom-voice"></a>Introdução à Voz Personalizada

A [Voz Personalizada](https://aka.ms/customvoice) é um conjunto de ferramentas online que permitem que você crie uma voz única e reconhecível para sua marca. Para começar, bastam alguns arquivos de áudio e as transcrições deles. Siga os links abaixo para começar a criar uma experiência de conversão de texto em fala personalizada.

## <a name="whats-in-custom-voice"></a>O que faz parte da Voz Personalizada?

Antes de começar a usar a Voz Personalizada, você precisará de uma conta do Azure e de uma assinatura do serviço de Fala. Após criar uma conta, você pode preparar dados, treinar e testar modelos, avaliar a qualidade da voz e, por fim, implantar um modelo de voz personalizada.

O diagrama a seguir realça as etapas da criação de um modelo de voz personalizada usando o [portal da Voz Personalizada](https://aka.ms/customvoice). Use os links para saber mais.

![Diagrama de arquitetura da Voz Personalizada](media/custom-voice/custom-voice-diagram.png)

1. [Assinar e criar um projeto](#set-up-your-azure-account) – crie uma conta do Azure e uma assinatura do serviço de Fala. Com essa assinatura unificada, você tem acesso à conversão de fala em texto, à conversão de texto em fala, à tradução de fala e ao portal da Voz Personalizada. Em seguida, usando a assinatura do serviço de Fala, crie seu primeiro projeto de Voz Personalizada.

2. [Carregar dados](how-to-custom-voice-create-voice.md#upload-your-datasets) – carregue dados (áudio e texto) usando o portal ou a API da Voz Personalizada. No portal, você pode investigar e avaliar pontuações de pronúncia e taxas de sinal para ruído. Para obter mais informações, confira [Como preparar dados para a Voz Personalizada](how-to-custom-voice-prepare-data.md).

3. [Treinar o modelo](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – use os dados para criar um modelo personalizado de voz com a conversão de texto em fala. Você pode treinar o modelo em diferentes idiomas. Depois de treinar, teste o modelo e, se estiver satisfeito com o resultado, implante-o.

4. [Implantar o modelo](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – crie um ponto de extremidade personalizado para o modelo de voz de conversão de texto em fala e use-o para sintetização de fala em seus produtos, ferramentas e aplicativos.

## <a name="custom-neural-voices"></a>Vozes neurais personalizadas

Atualmente, a Voz Personalizada dá suporte às camadas standard e neural. A Sintetização de Voz Personalizada permite que os usuários criem modelos de voz de qualidade mais elevada usando menos dados e fornece medidas para ajudar você a implantar a IA de maneira responsável. Recomendamos o uso da Sintetização de Voz Personalizada para desenvolver vozes mais realistas para interfaces de conversa mais naturais e para permitir que seus clientes e usuários finais se beneficiem da última tecnologia de conversão de texto em fala de maneira responsável. [Saiba mais sobre a Sintetização de Voz Personalizada](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

> [!NOTE]
> Como parte do compromisso da Microsoft com a criação de IA responsável, limitamos o uso da Sintetização de Voz Personalizada. Você poderá ter acesso à tecnologia somente após seus aplicativos serem analisados e você se comprometer a usá-los de acordo com nossos princípios de IA responsável. Saiba mais sobre nossa [política de limite de acesso](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) e [aplique-a aqui](https://aka.ms/customneural). Os [idiomas](language-support.md#customization) e as [regiões](regions.md#custom-voices) com suporte das versões padrão e neural da Voz Personalizada são diferentes. Confira os detalhes antes de começar.  

## <a name="set-up-your-azure-account"></a>Configurar uma conta do Azure

Uma assinatura do serviço de Fala é necessária para que você possa usar o portal de Fala Personalizada para criar um modelo personalizado. Siga estas instruções para criar uma assinatura do serviço de Fala no Azure. Se não tiver uma conta do Azure, crie uma.  

Após criar uma conta do Azure e uma assinatura do serviço de Fala, você precisará entrar no portal de Voz Personalizada e conectar sua assinatura.

1. Obtenha sua chave de assinatura do serviço de Fala do portal do Azure.
2. Entre no [Portal da Voz Personalizada](https://aka.ms/custom-voice).
3. Selecione sua assinatura e crie um projeto de fala.
4. Se quiser mudar para outra assinatura de Fala, use o ícone de engrenagem localizado na navegação superior.

> [!NOTE]
> Você precisa ter uma chave F0 ou S0 do serviço de Fala criada no Azure para usar o serviço. A Sintetização de Voz Personalizada dá suporte apenas à camada S0. 

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdo como dados, modelos, testes e pontos de extremidade são organizados em **Projetos** no portal da Voz Personalizada. Cada projeto é específico a um país/idioma e ao gênero da voz que você deseja criar. Por exemplo, você pode criar um projeto para uma voz feminina para os chatbots de seu call center que use o idioma inglês dos Estados Unidos ('en-US').

Para criar seu primeiro projeto, selecione a guia **Conversão de Texto em Fala/Voz Personalizada** e clique em **Novo projeto**. Siga as instruções fornecidas pelo assistente para criar o projeto. Depois de criar um projeto, você verá quatro guias: **Dados**, **Treinamento**, **Teste** e **Implantação**. Use os links fornecidos nas [Próximas etapas](#next-steps) para aprender a usar cada guia.

> [!IMPORTANT]
> O [Portal da Voz Personalizada](https://aka.ms/custom-voice) foi atualizado recentemente! Se você criou dados, modelos e testes e publicou pontos de extremidade no portal CRIS.ai ou com APIs, precisa criar um projeto no novo portal para se conectar a essas entidades antigas.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Como migrar para a Sintetização de Voz Personalizada

A camada de treinamento standard/não neural (adaptável, estatística, paramétrica e concatenativa) da Voz Personalizada está sendo preterida. O comunicado foi enviado para todas as assinaturas de Fala existentes antes de 28/02/2021. Durante o período de preterimento (01/03/2021 – 29/02/2024), os usuários existentes da camada standard poderão continuar usando seus modelos não neurais criados. Todos os novos usuários/recursos de fala devem ser movidos para a camada neural/Sintetização de Voz Personalizada. Após 29/02/2024, não haverá mais suporte para as vozes personalizadas padrão/não neurais. 

Se você está usando a Voz Personalizada não neural/padrão, migre imediatamente para a Sintetização de Voz Personalizada seguindo as etapas abaixo. A mudança para a Sintetização de Voz Personalizada ajudará você a desenvolver vozes mais realistas para interfaces de conversa ainda mais naturais e para permitir que seus clientes e usuários finais se beneficiem da última tecnologia de conversão de texto em fala de maneira responsável. 

1. Saiba mais sobre nossa [política de limite de acesso](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) e [aplique-a aqui](https://aka.ms/customneural). Observe que o acesso ao serviço de Sintetização de Voz Personalizada está sujeito ao exclusivo critério da Microsoft com base em nossos critérios de qualificação. Os clientes poderão ter acesso à tecnologia somente após o aplicativo ser analisado e eles se comprometerem a usá-lo de acordo com nossos [Princípios de IA responsável](https://microsoft.com/ai/responsible-ai) e com o [código de conduta](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 
2. Depois que seu aplicativo for aprovado, você terá acesso ao recurso de treinamento "neural". Faça logon no [portal da Voz Personalizada](https://speech.microsoft.com/customvoice) usando a mesma assinatura do Azure fornecida em seu aplicativo. 
    > [!IMPORTANT]
    > A fim de proteger o talento de voz e impedir o treinamento de modelos de voz com gravações não autorizadas ou sem a ciência do talento de voz, exigimos que o cliente carregue uma declaração gravada do talento de voz dando o consentimento dele. Ao preparar seu roteiro de gravação, lembre-se de incluir esta frase. “Eu, [informe o nome e o sobrenome], estou ciente de que as gravações de minha voz serão usadas pela [informe o nome da empresa] para criar e usar uma versão sintética de minha voz.”
    > Esta frase deve ser carregada na guia **Talento de Voz** como um arquivo de consentimento verbal. Ela será usada para confirmar que as gravações em seus conjuntos de dados de treinamento foram feitas pela mesma pessoa que deu o consentimento.
3. Após a criação do modelo de Sintetização de Voz Personalizada, implante-o em um novo ponto de extremidade. Para criar um ponto de extremidade de voz personalizada com seu modelo de voz neural, vá até **Conversão de Texto em Fala > Voz Personalizada > Implantação**. Selecione **Implantar modelo** e insira um **Nome** e uma **Descrição** para o ponto de extremidade personalizado. Em seguida, selecione o modelo de sintetização de voz personalizada que deseja associar ao ponto de extremidade e confirme a implantação.  
4. Atualize o código em seus aplicativos se você tiver criado um ponto de extremidade com um novo modelo. 

## <a name="next-steps"></a>Próximas etapas

- [Preparar dados de Voz Personalizada](how-to-custom-voice-prepare-data.md)
- [Criar uma Voz Personalizada](how-to-custom-voice-create-voice.md)
- [Guia: Gravar amostras de voz](record-custom-voice-samples.md)
