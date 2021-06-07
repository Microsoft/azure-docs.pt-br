---
title: Criar um assistente de voz com o Azure Percept DK e o Áudio do Azure Percept
description: Saiba como criar e implantar uma solução de fala sem código no Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 76333e11916641be71c72ce6142cd59b496a17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023156"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Criar um assistente de voz com o Azure Percept DK e o Áudio do Azure Percept

Neste tutorial, você criará um assistente de voz de um modelo para usar com o Azure Percept DK e o Áudio do Azure Percept. A demonstração do assistente de voz é executada no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) e contém uma seleção de objetos virtuais controlados por voz. Para controlar um objeto, diga sua palavra-chave, que é uma palavra ou frase curta que ativa seu dispositivo, seguida por um comando. Cada modelo responde a um conjunto de comandos específicos.

Este guia orientará você pelo processo de configuração de seus dispositivos, criando um assistente de voz e os recursos de [Serviços de Fala](../cognitive-services/speech-service/overview.md) necessários, testando seu assistente de voz, configurando sua palavra-chave e criando palavras-chaves personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- Áudio do Azure Percept
- Alto-falante ou fones de ouvido que podem ser conectados à entrada de áudio de 3,5 mm (opcional)
- [Assinatura do Azure](https://azure.microsoft.com/free/)
- [Experiência de instalação do Azure Percept DK](./quickstart-percept-dk-set-up.md): você conectou o kit de desenvolvimento a uma rede Wi-Fi, criou um Hub IoT e conectou o kit de desenvolvimento ao Hub IoT
- [Configuração do Áudio do Azure Percept](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>Criar um assistente de voz usando um modelo disponível

1. Navegar para o [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Abra a guia **Demonstrações e tutoriais**.

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Captura de tela da home page do portal do Azure.":::

1. Clique em **Experimentar modelos do assistente de voz** em **Tutoriais e demonstrações de fala**. Isso abrirá uma janela no lado direito da tela.

1. Nessa janela, faça o seguinte:

    1. No menu suspenso **Hub IoT**, selecione o Hub IoT ao qual seu devkit está conectado.

    1. No menu suspenso **Dispositivo**, selecione seu devkit.

    1. Selecione um dos modelos disponíveis do assistente de voz.

    1. Clique na caixa de seleção **Eu concordo com os termos e condições deste projeto**.

    1. Clique em **Criar**.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Captura de tela da criação do modelo de assistente de voz.":::

1. Depois de clicar em **Criar**, o portal abre outra janela para criar o recurso de tema de fala. Nessa janela, faça o seguinte:

    1. Selecione a assinatura do Azure na caixa **Assinatura**.

    1. Selecione o grupo de recursos preferencial no menu suspenso **Grupo de recursos**. Se você quiser criar um grupo de recursos para usar com o assistente de voz, clique em **Criar** no menu suspenso e siga os prompts.

    1. Para o **Prefixo do aplicativo**, insira um nome. Esse será o prefixo do projeto e o nome de comando personalizado.

    1. Em **Região**, selecione a região na qual os recursos serão implantados.

    1. Em **Tipo de preço de previsão do LUIS**, selecione **Standard** (a camada gratuita não dá suporte a solicitações de fala).

    1. Selecione o botão **Criar**. Os recursos do aplicativo de assistente de voz serão implantados em sua assinatura.

        > [!WARNING]
        > **NÃO** feche a janela até que o portal conclua a implantação do recurso. O fechamento prematuro da janela pode resultar em um comportamento inesperado do assistente de voz. Depois que o recurso tiver sido implantado, a demonstração será exibida.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Captura de tela da janela de seleção de grupo de recursos e assinatura.":::

## <a name="test-out-your-voice-assistant"></a>Testar o assistente de voz

Para interagir com o assistente de voz, diga a palavra-chave seguida por um comando. Quando o SoM Ear reconhecer sua palavra-chave, o dispositivo emitirá um alarme (que você poderá ouvir se um alto-falante ou fone de ouvido estiver conectado) e os LEDs piscarão em azul. Os LEDs mudarão para azul em rápida intermitência enquanto o comando for processado. A resposta do assistente de voz para o comando será impressa em texto na janela de demonstração e emitida de maneira audível por meio de fones de ouvido/alto-falantes. A palavra-chave padrão (listada ao lado de **Palavra-chave Personalizada**) é definida como "Computer", e cada modelo tem um conjunto de comandos compatíveis que permitem que você interaja com objetos virtuais na janela de demonstração. Por exemplo, se você estiver usando a demonstração de hospedagem ou saúde, diga "Computador, ligar TV" para ligar a TV virtual.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Captura de tela da janela de demonstração de hotelaria.":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Comandos de demonstração de hotelaria e saúde

Ambas as demonstrações de saúde e hotelaria têm TVs virtuais, luzes, persianas e termostatos conectados com os quais você pode interagir. Os seguintes comandos (e variações adicionais) são compatíveis:

* "Ligar/desligar luzes."
* "Ligar/desligar TV."
* "Ligar/desligar ar-condicionado."
* "Abrir/fechar as persianas".
* "Definir a temperatura para X graus". (X é a temperatura desejada, por exemplo, 24.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Captura de tela da janela de demonstração de saúde.":::

### <a name="automotive-demo-commands"></a>Comandos de demonstração automotiva

A demonstração automotiva tem um aquecedor de assento, um descongelador e um termostato virtuais com os quais você pode interagir. Os seguintes comandos (e variações adicionais) são compatíveis:

* "Ligar/desligar descongelador."
* "Ligar/desligar aquecedor de assento."
* "Definir a temperatura para X graus". (X é a temperatura desejada, por exemplo, 24.)
* "Aumentar/diminuir a temperatura em Y graus."


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Captura de tela da janela de demonstração automotiva.":::

### <a name="inventory-demo-commands"></a>Comandos de demonstração de inventário

A demonstração de inventário tem uma seleção de caixas azuis, amarelas e verdes virtuais com as quais é possível interagir, junto com um aplicativo de inventário virtual. Os seguintes comandos (e variações adicionais) são compatíveis:

* "Adicionar/remover X caixas". (X é o número de caixas, por exemplo, 4.)
* "Solicitar/enviar X caixas".
* "Quantas caixas estão em estoque?"
* "Contar caixas Y". (Y é a cor das caixas, por exemplo, amarelo.)
* "Enviar tudo o que está em estoque".


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Captura de tela da janela de demonstração de inventário.":::

## <a name="configure-your-keyword"></a>Configurar sua palavra-chave

Você pode personalizar a palavra-chave do seu aplicativo de assistente de voz.

1. Clique em **alterar** ao lado de **Palavra-chave Personalizada** na janela de demonstração.

1. Selecione uma das palavras-chave disponíveis. Você poderá escolher entre uma seleção de palavras-chave de amostra e as palavras-chave personalizadas que você tenha criado.

1. Clique em **Save** (Salvar).

### <a name="create-a-custom-keyword"></a>Criar uma palavra-chave personalizada

Você pode criar sua própria palavra-chave para o aplicativo de voz. O treinamento para a palavra-chave personalizada pode ser concluído em apenas alguns minutos.

1. Clique em **+ Criar Palavra-chave Personalizada** próximo à parte superior da janela de demonstração. 

1. Insira a palavra-chave desejada, a qual pode ser uma única palavra ou uma frase curta.

1. Selecione o seu **Recurso de fala** (que está listado próximo a **Comando Personalizado** na janela de demonstração e contém o prefixo do seu aplicativo).

1. Clique em **Save** (Salvar). 

## <a name="create-a-custom-command"></a>Criar um comando personalizado

O portal também fornece funcionalidade para a criação de comandos personalizados com recursos de fala existentes. "Comando personalizado" refere-se ao próprio aplicativo de assistente de voz, não a um comando específico dentro do aplicativo existente. Ao criar um comando personalizado, você cria um projeto de fala, que você precisa continuar a desenvolver no [Speech Studio](https://speech.microsoft.com/).

Para criar um comando personalizado de dentro da janela de demonstração, clique em **+ Criar Comando Personalizado** na parte superior da página e faça o seguinte:

1. Insira um nome para o comando personalizado.

1. Insira uma descrição do projeto (opcional).

1. Selecione o idioma preferido.

1. Selecione o recurso de fala.

1. Selecione o recurso LUIS.

1. Selecione seu recurso de criação do LUIS ou crie um.

1. Clique em **Criar**.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Captura de tela da janela de criação de comandos personalizados.":::

Depois de criar um comando personalizado, você precisa ir para o [Speech Studio](https://speech.microsoft.com/) para continuar a desenvolvê-lo. Se você abrir o Speech Studio e não vir o comando personalizado listado, siga estas etapas:

1. No painel de menu à esquerda no Azure Percept Studio, clique em **Fala** em **Projetos de IA**.

1. Selecione a guia **Comandos**.

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Captura de tela da lista de comandos personalizados disponíveis para edição.":::

1. Selecione o comando personalizado que você deseja desenvolver. Isso abre o projeto no Speech Studio.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Captura de tela da tela inicial do Speech Studio.":::

Para obter mais informações sobre como desenvolver comandos personalizados, confira a [Documentação do Serviço de Fala](../cognitive-services/speech-service/custom-commands.md).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>O assistente de voz foi criado, mas não responde a comandos

Verifique as luzes do LED na placa intermediária:

* Três luzes azuis sólidas indicam que o assistente de voz está pronto e aguardando a palavra-chave.
* Se o LED central (L02) estiver branco, o devkit terá concluído a inicialização e precisará ser configurado com uma palavra-chave.
* Se o LED central (L02) estiver piscando em branco, significa que o SoM de áudio ainda não concluiu a inicialização. A inicialização pode demorar alguns minutos para ser concluída.

Para obter mais informações sobre os indicadores de LED, confira o [artigo sobre o LED](./audio-button-led-behavior.md).

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>O assistente de voz não responde a uma palavra-chave personalizada criada no Speech Studio

Isso poderá ocorrer se o módulo de fala estiver desatualizado. Siga estas etapas para atualizar o módulo de fala para a versão mais recente:

1. Clique em **Dispositivos** no painel de menu à esquerda da home page do Azure Percept Studio.

1. Localize e selecione o dispositivo.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Captura de tela da lista de dispositivos no Azure Percept Studio.":::

1. Na janela do dispositivo, selecione a guia **Fala**.

1. Verifique a versão do módulo de fala. Se uma atualização estiver disponível, você verá um botão de **Atualização** ao lado do número de versão.

1. Clique em **Atualizar** para implantar a atualização do módulo de fala. O processo de atualização geralmente leva de 2 a 3 minutos para ser concluído.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de trabalhar com seu aplicativo de assistente de voz, siga estas etapas para limpar os recursos de fala implantados durante este tutorial:

1. No [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos** no painel de menu à esquerda ou digite isso na barra de pesquisa.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Captura de tela da home page do portal do Azure mostrando o painel de menu à esquerda e os Grupos de Recursos.":::

1. Selecione o grupo de recursos.

1. Selecione todos os seis recursos que contêm o prefixo do aplicativo e clique no ícone **Excluir** no painel de menu superior.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Captura de tela dos recursos de fala selecionados para exclusão.":::

1. Para confirmar a exclusão, digite **sim** na caixa de confirmação, verifique se você selecionou os recursos corretos e clique em **Excluir**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Captura de tela da janela de confirmação de exclusão.":::

> [!WARNING]
> Isso removerá todas as palavras-chave personalizadas criadas com os recursos de fala que você está excluindo, e a demonstração do assistente de voz deixará de funcionar.

## <a name="next-steps"></a>Próximas etapas

Agora que você criou uma solução de fala sem código, tente criar uma [solução de pesquisa visual sem código](./tutorial-nocode-vision.md) para o Azure Percept DK.