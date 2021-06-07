---
title: Configurar o aplicativo assistente de voz no Azure Percept Studio
description: Configurar o aplicativo assistente de voz no Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 8f22379049b74428787b738af832802081be7bf8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022884"
---
# <a name="managing-your-voice-assistant"></a>Gerenciando seu assistente de voz

Este artigo descreve como configurar a palavra-chave e os comandos do seu aplicativo de assistente de voz no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Para obter orientação sobre como configurar sua palavra-chave no Hub IoT em vez do portal, consulte este [artigo de instruções](./how-to-configure-voice-assistant.md).

Se você ainda não criou um aplicativo de assistente de voz, consulte [criar um assistente de voz sem código com o Azure Percept Studio e o áudio do Azure Percept](./tutorial-no-code-speech.md).

## <a name="keyword-configuration"></a>Configuração de palavra-chave

Uma palavra-chave é uma palavra ou frase curta usada para ativar um assistente de voz. Por exemplo, "Ei Cortana" é a palavra-chave do assistente da Cortana. A ativação por voz permite que os usuários comecem a interagir com seu produto totalmente livre, simplesmente falando com a palavra-chave. À medida que o seu produto escuta a palavra-chave continuamente, todo o áudio é processado localmente no dispositivo até que ocorra uma detecção para garantir que os dados do usuário permaneçam o mais privados possível.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configuração na janela de demonstração do assistente de voz

1. Clique em **alterar** ao lado de **palavra-chave personalizada** na página demonstração.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Captura de tela da janela de demonstração de hotelaria.":::

    Se você não tiver a página de demonstração aberta, navegue até a página do dispositivo (veja abaixo) e clique em **testar seu assistente de voz** em **ações** para acessar a demonstração.

1. Selecione uma das palavras-chave disponíveis e clique em **salvar** para aplicar as alterações.

1. As três luzes de LED no dispositivo de áudio do Azure Percept mudarão para azul brilhante (sem piscar) quando a configuração estiver concluída e o assistente de voz estiver pronto para uso.

### <a name="configuration-within-the-device-page"></a>Configuração na página do dispositivo

1. Na página Visão geral do [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819), clique em **dispositivos** no painel de menu à esquerda.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Captura de tela da página de visão geral do Azure Percept Studio com dispositivos realçados.":::

1. Selecione o dispositivo ao qual seu aplicativo de assistente de voz foi implantado.

1. Abra a guia **fala** .

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Captura de tela da página do dispositivo de borda com a guia fala realçada.":::

1. Clique em **alterar** ao lado de **palavra-chave**.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Captura de tela das ações da solução de fala disponíveis.":::

1. Selecione uma das palavras-chave disponíveis e clique em **salvar** para aplicar as alterações.

1. As três luzes de LED no dispositivo de áudio do Azure Percept mudarão para azul brilhante (sem piscar) quando a configuração estiver concluída e o assistente de voz estiver pronto para uso.

## <a name="create-a-custom-keyword"></a>Criar uma palavra-chave personalizada

Com o [Speech Studio](https://speech.microsoft.com/), você pode criar uma palavra-chave personalizada para seu assistente de voz. Leva até 30 minutos para treinar um modelo básico de palavra-chave personalizada.

Siga a [documentação do Speech Studio](../cognitive-services/speech-service/custom-keyword-basics.md) para obter orientação sobre como criar uma palavra-chave personalizada. Uma vez configurado, sua nova palavra-chave estará disponível no portal de Santa Cruz do projeto para uso com seu aplicativo assistente de voz.

## <a name="commands-configuration"></a>Configuração de comandos

Os comandos personalizados facilitam a criação de aplicativos avançados de comando de voz otimizados para experiências de interação de voz primeiro. Os comandos personalizados são mais adequados para os cenários de conclusão de tarefas ou de comando e controle.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configuração na janela de demonstração do assistente de voz

1. Clique em **alterar** ao lado de **comando personalizado** na página demonstração. Se você não tiver a página de demonstração aberta, navegue até a página do dispositivo (veja abaixo) e clique em **testar seu assistente de voz** em **ações** para acessar a demonstração.

1. Selecione um dos comandos personalizados disponíveis e clique em **salvar** para aplicar as alterações.

### <a name="configuration-within-the-device-page"></a>Configuração na página do dispositivo

1. Na página Visão geral do [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819), clique em **dispositivos** no painel de menu à esquerda.

1. Selecione o dispositivo ao qual seu aplicativo de assistente de voz foi implantado.

1. Abra a guia **fala** .

1. Clique em **alterar** ao lado de **comando**.

1. Selecione um dos comandos personalizados disponíveis e clique em **salvar** para aplicar as alterações.

## <a name="create-custom-commands"></a>Criar comandos personalizados

Com o [Speech Studio](https://speech.microsoft.com/), você pode criar comandos personalizados para o assistente de voz executar.

Siga a [documentação do Speech Studio](../cognitive-services/speech-service/quickstart-custom-commands-application.md) para obter orientação sobre como criar comandos personalizados. Uma vez configurados, seus novos comandos estarão disponíveis no Azure Percept Studio para uso com seu aplicativo assistente de voz.

## <a name="next-steps"></a>Próximas etapas

Depois de criar um aplicativo de assistente de voz, tente desenvolver uma [solução de visão sem código](./tutorial-nocode-vision.md) com seu Percept do Azure DK.