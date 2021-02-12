---
title: Melhorar a síntese com o serviço de fala de voz personalizado
titleSuffix: Azure Cognitive Services
description: Voz personalizada é um conjunto de ferramentas online que permitem que você crie uma voz de um tipo reconhecível para sua marca. Tudo o que é necessário para começar são alguns arquivos de áudio e as transcrições associadas. Siga os links abaixo para começar a criar uma experiência de fala em texto Personalizada.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: eff51c8568ce82c9d8d21bff7a2ba079c291679c
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007288"
---
# <a name="get-started-with-custom-voice"></a>Introdução à Voz Personalizada

[Voz personalizada](https://aka.ms/customvoice) é um conjunto de ferramentas online que permitem que você crie uma voz de um tipo reconhecível para sua marca. Tudo o que é necessário para começar são alguns arquivos de áudio e as transcrições associadas. Siga os links abaixo para começar a criar uma experiência personalizada de conversão de texto em fala.

## <a name="whats-in-custom-voice"></a>O que há em voz personalizada?

Antes de começar com a voz personalizada, você precisará de uma conta do Azure e uma assinatura do serviço de fala. Depois de criar uma conta, você pode preparar seus dados, treinar e testar seus modelos, avaliar a qualidade da voz e, por fim, implantar seu modelo de voz personalizado.

O diagrama a seguir realça as etapas para criar um modelo de voz personalizado usando o [portal de voz personalizado](https://aka.ms/customvoice). Use os links para saber mais.

![Diagrama de arquitetura de voz personalizada](media/custom-voice/custom-voice-diagram.png)

1. [Assinar e criar um projeto](#set-up-your-azure-account) -crie uma conta do Azure e crie uma assinatura do serviço de fala. Essa assinatura unificada fornece acesso a conversão de fala em texto, texto para fala, tradução de fala e portal de voz personalizado. Em seguida, usando sua assinatura do serviço de fala, crie seu primeiro projeto de voz personalizado.

2. [Carregar dados](how-to-custom-voice-create-voice.md#upload-your-datasets) – carregar dados (áudio e texto) usando o portal de voz personalizado ou a API de voz personalizada. No portal, você pode investigar e avaliar as pontuações de pronúncia e as taxas de sinal para ruído. Para obter mais informações, consulte [como preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md).

3. [Treine seu modelo](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – use seus dados para criar um modelo de voz de conversão de texto em fala personalizado. Você pode treinar um modelo em diferentes idiomas. Após o treinamento, teste seu modelo e, se estiver satisfeito com o resultado, você poderá implantar o modelo.

4. [Implantar seu modelo](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) -crie um ponto de extremidade personalizado para seu modelo de voz de conversão de texto em fala e use-o para síntese de fala em seus produtos, ferramentas e aplicativos.

## <a name="custom-neural-voices"></a>Vozes neurais personalizadas

A voz personalizada atualmente dá suporte às camadas Standard e neural. A voz neural personalizada permite que os usuários criem modelos de voz de qualidade mais alta enquanto exigem menos dados e fornece medidas para ajudá-lo a implantar o AI com responsabilidade. Recomendamos que você use a voz neural personalizada para desenvolver vozes mais realísticas para interfaces de conversa mais naturais e permitir que seus clientes e usuários finais se beneficiem da mais recente tecnologia de conversão de texto em fala, de uma maneira responsável. [Saiba mais sobre a voz neural personalizada](https://aka.ms/CNV-Transparency-Note). 

> [!NOTE]
> Como parte do compromisso da Microsoft em projetar a AI responsável, limitamos o uso de voz neural personalizada. Você pode obter acesso à tecnologia somente depois que seus aplicativos são revisados e você se compromete a usá-los em alinhamento com nossos princípios de ia responsáveis. Saiba mais sobre nossa [política sobre o limite de acesso](https://aka.ms/gating-overview) e aplique-a [aqui](https://aka.ms/customneural). As [linguagens](language-support.md#customization) e [regiões](regions.md#custom-voices) com suporte para a versão padrão e neural de voz personalizada são diferentes. Verifique os detalhes antes de começar.  

## <a name="set-up-your-azure-account"></a>Configurar sua conta do Azure

Uma assinatura de serviço de fala é necessária para que você possa usar o portal de Fala Personalizada para criar um modelo personalizado. Siga estas instruções para criar uma assinatura do serviço de fala no Azure. Se você não tiver uma conta do Azure, poderá se inscrever para uma nova.  

Depois de criar uma conta do Azure e uma assinatura do serviço de fala, você precisará entrar no portal de voz personalizado e conectar-se à sua assinatura.

1. Obtenha sua chave de assinatura do serviço de fala do portal do Azure.
2. Entre no portal de [voz personalizado](https://aka.ms/custom-voice).
3. Selecione sua assinatura e crie um projeto de fala.
4. Se você quiser mudar para outra assinatura de fala, use o ícone de engrenagem localizado na navegação superior.

> [!NOTE]
> Você deve ter uma chave de serviço de fala F0 ou S0 criada no Azure antes de poder usar o serviço. A voz neural personalizada só dá suporte à camada S0. 

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdo como dados, modelos, testes e pontos de extremidade são organizados em **projetos** no portal de voz personalizado. Cada projeto é específico de um país/idioma e do sexo da voz que você deseja criar. Por exemplo, você pode criar um projeto para uma voz fêmea para os bots de bate-papo do seu Call Center que usam o inglês na Estados Unidos (' en-US ').

Para criar seu primeiro projeto, selecione a guia **conversão de texto em fala/voz personalizada** e clique em **novo projeto**. Siga as instruções fornecidas pelo Assistente para criar seu projeto. Depois de criar um projeto, você verá quatro guias: **dados**, **treinamento**, **teste** e **implantação**. Use os links fornecidos nas [próximas etapas](#next-steps) para aprender a usar cada guia.

> [!IMPORTANT]
> O [portal de voz personalizado](https://aka.ms/custom-voice) foi atualizado recentemente! Se você criou dados, modelos, testes e pontos de extremidade publicados anteriores no portal do CRIS.ai ou com APIs, você precisa criar um novo projeto no novo portal para se conectar a essas entidades antigas.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Como migrar para uma voz neural personalizada

Se você estiver usando a voz personalizada não neural (ou padrão), considere migrar para a voz neural personalizada imediatamente seguindo as etapas abaixo. A mudança para a voz neural personalizada ajudará você a desenvolver vozes mais realísticas para interfaces de conversação ainda mais naturais e permitir que seus clientes e usuários finais se beneficiem da mais recente tecnologia de conversão de texto em fala, de uma maneira responsável. 

1. Saiba mais sobre nossa [política sobre o limite de acesso](https://aka.ms/gating-overview) e aplique-a [aqui](https://aka.ms/customneural). Observe que o acesso ao serviço de voz neural personalizado está sujeito à exclusiva critério da Microsoft com base em nossos critérios de qualificação. Os clientes podem obter acesso à tecnologia somente depois que seu aplicativo é revisado e eles confirmam usá-la em alinhamento com nossos [princípios de ia responsáveis](https://microsoft.com/ai/responsible-ai) e o [código de conduta](https://aka.ms/custom-neural-code-of-conduct). 
2. Depois que seu aplicativo for aprovado, você receberá o acesso ao recurso de treinamento "neural". Certifique-se de fazer logon no [portal de voz personalizado](https://speech.microsoft.com/customvoice) usando a mesma assinatura do Azure que você fornece em seu aplicativo. 
    > [!IMPORTANT]
    > Para proteger o talento de voz e impedir o treinamento de modelos de voz com gravação não autorizada ou sem a confirmação do talento de voz, exigimos que o cliente carregue uma declaração registrada dos talentos de voz dando seu consentimento. Ao preparar o script de gravação, certifique-se de incluir esta sentença. "I [declare seu nome e sobrenome] estou ciente de que as gravações da minha voz serão usadas por [estado o nome da empresa] para criar e usar uma versão sintética da minha voz".
    > Essa sentença deve ser carregada para a guia de **talento de voz** como um arquivo de consentimento verbal. Ele será usado para verificar se as gravações em seus conjuntos de seus de treinamento são feitas pela mesma pessoa que faz o consentimento.
3. Depois que o modelo de voz neural personalizado for criado, implante o modelo de voz em um novo ponto de extremidade. Para criar um novo ponto de extremidade de voz personalizado com seu modelo de voz neural, acesse **conversão de texto em fala > de voz personalizada > implantação**. Selecione **implantar modelo** e insira um **nome** e uma **Descrição** para o ponto de extremidade personalizado. Em seguida, selecione o modelo de voz neural personalizado que você deseja associar a esse ponto de extremidade e confirme a implantação.  
4. Atualize seu código em seus aplicativos se você tiver criado um novo ponto de extremidade com um novo modelo. 

## <a name="next-steps"></a>Próximas etapas

- [Preparar dados de voz personalizados](how-to-custom-voice-prepare-data.md)
- [Criar uma Voz Personalizada](how-to-custom-voice-create-voice.md)
- [Guia: Registre suas amostras de voz](record-custom-voice-samples.md)
