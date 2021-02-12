---
title: Criação de conteúdo de áudio-serviço de fala
titleSuffix: Azure Cognitive Services
description: A criação de conteúdo de áudio é uma ferramenta online que permite que você personalize e ajuste a saída de conversão de texto em fala da Microsoft para seus aplicativos e produtos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7d4d1acac591bfe6ce40efcf6166357d43718363
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024952"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Melhorar a síntese com a ferramenta de criação de conteúdo de áudio

A [criação de conteúdo de áudio](https://aka.ms/audiocontentcreation) é uma ferramenta poderosa e fácil de usar que permite criar conteúdo de áudio altamente natural para uma variedade de cenários, como Audiobooks, difusões de notícias, narrações em vídeo e bots de bate-papo. Com a criação de conteúdo de áudio, você pode ajustar as vozes de conversão de texto em fala e criar experiências de áudio personalizadas de uma maneira eficiente e de baixo custo.

A ferramenta é baseada na [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). Ele permite que você ajuste os atributos de saída de conversão de texto em fala em síntese em tempo real ou em lote, como caracteres de voz, estilos de voz, velocidade de fala, pronúncia e Prosody.

Você pode ter acesso fácil a mais de 150 vozes predefinidas ao longo de 50 linguagens diferentes, incluindo as vozes de TTS do neural de última geração e sua voz personalizada se você tiver criado uma. 

Consulte o [tutorial em vídeo](https://www.youtube.com/watch?v=O1wIJ7mts_w) para a criação de conteúdo de áudio.

## <a name="how-to-get-started"></a>Como começar?

A criação de conteúdo de áudio é uma ferramenta gratuita, mas você pagará pelo serviço de fala do Azure que consumir. Para trabalhar com a ferramenta, você precisa fazer logon com uma conta do Azure e criar um recurso de fala. Para cada conta do Azure, você tem cotas de fala gratuitas mensais que incluem 500.000 caracteres para vozes de TTS de neural (por mês), 5 milhões caracteres para vozes padrão e personalizadas (por mês) e 1 serviço de Hospedagem de ponto de extremidade de voz personalizado (por mês). A quantia mensal alocada geralmente é suficiente para uma pequena equipe de conteúdo de cerca de 3-5 pessoas. Aqui estão as etapas para criar uma conta do Azure e obter um recurso de fala. 

### <a name="step-1---create-an-azure-account"></a>Etapa 1-criar uma conta do Azure

Para trabalhar com a criação de conteúdo de áudio, você precisa ter um [conta Microsoft](https://account.microsoft.com/account) e uma [conta do Azure](https://azure.microsoft.com/free/ai/). Siga estas instruções para [Configurar a conta](./overview.md#try-the-speech-service-for-free). 

[Portal do Azure](https://portal.azure.com/) é o local centralizado para gerenciar sua conta do Azure. Você pode criar o recurso de fala, gerenciar o acesso ao produto e monitorar tudo, desde aplicativos Web simples até implantações de nuvem complexas. 

### <a name="step-2---create-a-speech-resource"></a>Etapa 2 – criar um recurso de fala

Depois de se inscrever na conta do Azure, você precisará criar um recurso de fala em sua conta do Azure para acessar os serviços de fala. Veja as instruções sobre [como criar um recurso de fala](./overview.md#create-the-azure-resource). 

São necessários alguns instantes para a implantação do novo recurso de Fala. Depois que a implantação for concluída, você poderá iniciar a jornada de criação de conteúdo de áudio. 

 >[!NOTE]
   > Se você planeja usar vozes neurais, certifique-se de criar seu recurso em [uma região que ofereça suporte a vozes neurais](regions.md#standard-and-neural-voices).
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Etapa 3 – faça logon na criação de conteúdo de áudio com sua conta do Azure e o recurso de fala

1. Depois **de obter a** conta do Azure e o recurso de fala, você pode fazer logon na criação de conteúdo de [áudio](https://aka.ms/audiocontentcreation) clicando em introdução.
2. A página de **recursos de fala** será mostrada a você. Selecione o recurso de fala no qual você deseja trabalhar. Clique em **ir para Studio** para iniciar a criação de áudio. Você também pode criar um novo recurso de fala clicando em **criar novo**. Quando você fizer logon na ferramenta de criação de conteúdo de áudio pela próxima vez, iremos vinculá-lo diretamente aos arquivos de trabalho de áudio no recurso de fala atual. 
3. Você pode modificar o recurso de fala a qualquer momento com a opção **configurações** , localizada na barra de navegação superior.

## <a name="how-to-use-the-tool"></a>Como usar a ferramenta?

Este diagrama mostra as etapas necessárias para ajustar as saídas de conversão de texto em fala. Use os links abaixo para saber mais sobre cada etapa.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Um diagrama das etapas necessárias para ajustar as saídas de conversão de texto em fala":::


1. Escolha o recurso de fala no qual você deseja trabalhar.
2. [Crie um arquivo de ajuste de áudio](#create-an-audio-tuning-file) usando texto sem formatação ou scripts de SSML. Digite ou carregue seu conteúdo na criação de conteúdo de áudio.
3. Escolha a voz e o idioma para o conteúdo do script. A criação de conteúdo de áudio inclui todas as [vozes de texto em fala da Microsoft](language-support.md#text-to-speech). Você pode usar o padrão, neural ou sua própria voz personalizada.
   >[!NOTE]
   > O acesso restrito está disponível para vozes neurais personalizadas, que permitem criar vozes de alta definição semelhantes à fala de som natural. Para obter mais detalhes, consulte o [processo de retenção](./text-to-speech.md).

4. Clique no ícone de **reprodução** (um triângulo) para visualizar a saída de síntese padrão. Em seguida, aumente a saída ajustando pronúncia, quebra, pitch, taxa, intonation, estilo de voz e muito mais. Para obter uma lista completa de opções, consulte [linguagem de marcação de síntese de fala](speech-synthesis-markup.md). Aqui está um [vídeo](https://www.youtube.com/watch?v=O1wIJ7mts_w) para mostrar como ajustar a saída de fala com a criação de conteúdo de áudio. 
5. Salve e [exporte seu áudio ajustado](#export-tuned-audio). Ao salvar a faixa de ajuste no sistema, você pode continuar a trabalhar e iterar na saída. Quando estiver satisfeito com a saída, você poderá criar uma tarefa de criação de áudio com o recurso exportar. Você pode observar o status da tarefa de exportação e baixar a saída para uso com seus aplicativos e produtos.

## <a name="create-an-audio-tuning-file"></a>Criar um arquivo de ajuste de áudio

Há duas maneiras de colocar seu conteúdo na ferramenta de criação de conteúdo de áudio.

**Opção 1:**

1. Clique no ícone **novo arquivo** no canto superior direito para criar um novo arquivo de ajuste de áudio.
2. Digite ou cole o conteúdo na janela de edição. Os caracteres para cada arquivo são de até 20.000. Se o seu script tiver mais de 20.000 caracteres, você poderá usar a opção 2 para dividir automaticamente o conteúdo em vários arquivos. 
3. Não se esqueça de salvar.

**Opção 2:**

1. Clique em **carregar** para importar um ou mais arquivos de texto. Há suporte para texto sem formatação e SSML. Se o arquivo de script tiver mais de 20.000 caracteres, divida o arquivo por parágrafos, por caractere ou por expressões regulares. 
3. Ao carregar seus arquivos de texto, certifique-se de que o arquivo atende a esses requisitos.

   | Propriedade | Valor/observações |
   |----------|---------------|
   | Formato de arquivo | Texto sem formatação (.txt)<br/> Texto de SSML (. txt)<br/> Não há suporte para arquivos zip |
   | Formato de codificação | UTF-8 |
   | Nome do arquivo | Cada arquivo deve ter um nome exclusivo. Não há suporte para duplicatas. |
   | Comprimento do texto | A limitação de caracteres do arquivo de texto é 20.000. Se os arquivos excederem a limitação, divida os arquivos com as instruções na ferramenta. |
   | Restrições de SSML | Cada arquivo SSML pode conter apenas uma única parte de SSML. |

**Exemplo de texto sem formatação**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**Exemplo de texto de SSML**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportar áudio ajustado

Depois de revisar sua saída de áudio e ficar satisfeito com seu ajuste e ajuste, você poderá exportar o áudio.

1. Clique em **Exportar** para criar uma tarefa de criação de áudio. É recomendável **exportar para a biblioteca de áudio** , pois ela dá suporte à saída de áudio longa e a experiência de saída de áudio completa. Você também pode baixar o áudio para o disco local diretamente, mas somente os primeiros 10 minutos estão disponíveis.
2. Escolha o formato de saída para o áudio ajustado. Uma lista de formatos com suporte e taxas de exemplo está disponível abaixo.
3. Você pode exibir o status da tarefa na guia **exportar tarefa** . Se a tarefa falhar, consulte a página informações detalhadas para obter um relatório completo.
4. Quando a tarefa for concluída, o áudio estará disponível para download na guia **biblioteca de áudio** .
5. Clique em **Download**. Agora você está pronto para usar seu áudio ajustado personalizado em seus aplicativos ou produtos.

**Formatos com suporte de áudio**

| Formatar | taxa de amostragem de 16 kHz | taxa de amostragem de 24 kHz |
|--------|--------------------|--------------------|
| WAV | riff-16kHz-16 bits-mono-PCM | riff-24kHz-16 bits-mono-PCM |
| mp3 | Audio-16kHz-128kbitrate-mono-mp3 | Audio-24kHz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Como adicionar/remover usuários de criação de conteúdo de áudio?

Se mais de um usuário quiser usar a criação de conteúdo de áudio, você poderá compartilhar sua conta e senha do Azure com o usuário ou conceder acesso ao usuário à assinatura do Azure e ao recurso de fala. Se você adicionar um usuário a uma assinatura do Azure, o usuário poderá acessar todos os recursos na assinatura do Azure. Mas se você adicionar apenas um usuário a um recurso de fala, o usuário só terá acesso ao recurso de fala e não poderá acessar outros recursos nessa assinatura do Azure. Um usuário com acesso ao recurso de fala pode usar a criação de conteúdo de áudio.

### <a name="add-users-to-a-speech-resource"></a>Adicionar usuários a um recurso de fala

Siga estas etapas para adicionar um usuário a um recurso de fala para que eles possam usar a criação de conteúdo de áudio.

1. Procure **Serviços cognitivas** no [portal do Azure](https://portal.azure.com/), selecione o recurso de fala ao qual você deseja adicionar usuários.
2. Clique em **Controle de acesso (IAM)** . Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Guia atribuição de função":::
1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função. Na lista suspensa função, selecione a função de **usuário serviço cognitiva** . Se você quiser fornecer a propriedade do usuário desse recurso de fala, poderá selecionar a função **proprietário** .
1. Na lista, selecione um usuário. Se você não vir o usuário na lista, poderá digitar na caixa Selecionar para pesquisar o diretório em busca de nomes de exibição e endereços de email. Se o usuário não estiver nesse diretório, você poderá inserir o [conta Microsoft](https://account.microsoft.com/account) do usuário (que é confiável pelo Azure Active Directory).
1. Clique em **Salvar** para atribuir a função. Após alguns instantes, o usuário recebe a função de usuário de serviço cognitiva no escopo do recurso de fala.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Caixa de diálogo Adicionar função":::

1. Os usuários que você adicionar receberão um email de convite. Depois que eles clicarem em **aceitar convite**  >  **Accept para ingressar no Azure**, eles poderão usar a [criação de conteúdo de áudio](https://aka.ms/audiocontentcreation).

Os usuários que estão no mesmo recurso de fala verão o trabalho deles no estúdio de criação de conteúdo de áudio. Se você quiser que cada usuário individual tenha um local de trabalho exclusivo e privado na criação de conteúdo de áudio, [crie um novo recurso de fala](#step-2---create-a-speech-resource) para cada usuário e dê a cada usuário o acesso exclusivo ao recurso de fala. 

### <a name="remove-users-from-a-speech-resource"></a>Remover usuários de um recurso de fala
1. Procure **Serviços cognitivas** no portal do Azure, selecione o recurso de fala do qual você deseja remover os usuários.
2. Clique em **Controle de acesso (IAM)** . Clique na guia **atribuições de função** para exibir todas as atribuições de função para este recurso de fala.
3. Selecione os usuários que você deseja remover e clique em **remover**  >  **OK**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Botão Remover":::

### <a name="enable-users-to-grant-access"></a>Habilitar os usuários a conceder acesso
Se você quiser que um dos usuários forneça acesso a outros usuários, será necessário conceder ao usuário a função de proprietário do recurso de fala e definir o usuário como o leitor de diretório do Azure. 
1. Adicione o usuário como o proprietário do recurso de fala. Consulte [como adicionar usuários a um recurso de fala](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Campo de proprietário da função":::
1. Selecione o menu recolhido no canto superior esquerdo. Clique em **Azure Active Directory** e em **usuários**.
1. Pesquise o conta Microsoft do usuário e vá para a página de detalhes do usuário. Clique em **funções atribuídas**.
1. Clique em **Adicionar atribuições** de  ->  **diretório leitores**.

## <a name="see-also"></a>Confira também

* [API de áudio longo](./long-audio-api.md)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Estúdio de Fala](https://speech.microsoft.com)
