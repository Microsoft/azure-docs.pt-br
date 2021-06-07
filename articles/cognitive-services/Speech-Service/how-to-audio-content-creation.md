---
title: Criação de Conteúdo de Áudio – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: A Criação de Conteúdo de Áudio é uma ferramenta online que permite personalizar e ajustar a saída de conversão de texto em fala da Microsoft para aplicativos e produtos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7e43c472c939049e5dfd4ec4df909a3178ef8e2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553253"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Aprimorar a sintetização com a ferramenta Criação de Conteúdo de Áudio

A [Criação de Conteúdo de Áudio](https://aka.ms/audiocontentcreation) é uma ferramenta avançada e fácil de usar que permite criar conteúdo de áudio altamente natural para diversos cenários, como audiolivros, transmissão de notícias, narrações de vídeos e bots de bate-papo. Com a Criação de Conteúdo de Áudio, é possível ajustar as vozes de conversão de texto em fala e criar experiências de áudio personalizadas de maneira eficiente e barata.

A ferramenta é baseada em [SSML (Linguagem de Marcação de Síntese de Fala)](speech-synthesis-markup.md). Portanto, permite ajustar os atributos de saída de conversão de texto em fala em síntese em tempo real ou em lote, como caracteres de voz, estilos de voz, velocidade de fala, pronúncia e prosódia.

É possível acessar facilmente mais de 150 vozes predefinidas em quase 50 linguagens diferentes, incluindo vozes de TTS neural de última geração e sua voz personalizada, caso você tenha criado uma. 

Confira o [tutorial em vídeo](https://www.youtube.com/watch?v=O1wIJ7mts_w) da Criação de Conteúdo de Áudio.

## <a name="how-to-get-started"></a>Como começar?

A Criação de Conteúdo de Áudio é uma ferramenta gratuita, mas você paga para usar o serviço de Fala do Azure. Para trabalhar com a ferramenta, é preciso fazer logon com uma conta do Azure e criar um recurso de fala. Para cada conta do Azure, você tem cotas de fala mensais e gratuitas que incluem 500 mil caracteres para vozes de TTS neural (por mês), 5 milhões de caracteres para vozes padrão e personalizadas (por mês) e 1 serviço de hospedagem de ponto de extremidade de voz personalizada (por mês). A quantidade mensal alocada geralmente é suficiente para uma equipe de conteúdo pequena de três a cinco pessoas. Veja nas etapas a seguir como criar uma conta do Azure e obter um recurso de fala. 

### <a name="step-1---create-an-azure-account"></a>Etapa 1 – Criar uma conta do Azure

Para trabalhar com a Criação de Conteúdo de Áudio, é preciso ter uma [conta Microsoft](https://account.microsoft.com/account) e uma [conta do Azure](https://azure.microsoft.com/free/ai/). Siga estas instruções para [configurar a conta](./overview.md#try-the-speech-service-for-free). 

O [portal do Azure](https://portal.azure.com/) é a central de gerenciamento da conta do Azure. É possível criar o recurso de fala, gerenciar o acesso ao produto e monitorar tudo, desde aplicativos Web simples a implantações de nuvem complexas. 

### <a name="step-2---create-a-speech-resource"></a>Etapa 2 – Criar um recurso de fala

Depois de criar uma conta do Azure, é preciso criar um recurso de fala nela para acessar os serviços de Fala. Veja as instruções de [como criar um recurso de fala](./overview.md#create-the-azure-resource). 

São necessários alguns instantes para a implantação do novo recurso de Fala. Depois da implantação, comece seu percurso de Criação de Conteúdo de Áudio. 

 >[!NOTE]
   > Se você planeja usar vozes neurais, crie o recurso em uma [região compatível com vozes neurais](regions.md#neural-and-standard-voices).
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Etapa 3 – Fazer logon na Criação de Conteúdo de Áudio com a conta do Azure e o recurso de fala

1. Depois de obter a conta do Azure e o recurso de fala, faça logon na [Criação de Conteúdo de Áudio](https://aka.ms/audiocontentcreation) clicando em **Começar**.
2. A página **Recurso de Fala** será exibida. Selecione o recurso de fala em que você deseja trabalhar. Clique em **Ir para o Estúdio** para começar a criar áudio. Também é possível criar um recurso de fala aqui clicando em **Criar**. Quando você fizer logon novamente na ferramenta de Criação de Conteúdo de Áudio, será diretamente vinculado aos arquivos de trabalho de áudio no recurso de fala atual. 
3. É possível modificar o recurso de fala a qualquer momento com a opção **Configurações**, localizada na barra de navegação superior.

## <a name="how-to-use-the-tool"></a>Como usar a ferramenta?

Veja neste diagrama as etapas necessárias para ajustar as saídas de conversão de texto em fala. Use os links abaixo para saber mais sobre cada etapa.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Diagrama de etapas necessárias para ajustar as saídas de conversão de texto em fala":::


1. Escolha o recurso de fala em que você deseja trabalhar.
2. [Crie um arquivo de ajuste de áudio](#create-an-audio-tuning-file) usando texto sem formatação ou scripts de SSML. Digite ou faça upload do conteúdo na Criação de Conteúdo de Áudio.
3. Escolha a voz e a linguagem do conteúdo do script. A Criação de Conteúdo de Áudio inclui todas as [vozes de conversão de texto em fala da Microsoft](language-support.md#text-to-speech). É possível usar a voz padrão, neural ou personalizada.
   >[!NOTE]
   > O acesso restrito está disponível para a Sintetização de Voz Personalizada, que permite criar vozes de alta definição semelhantes à fala de som natural. Para saber mais detalhes, confira [processo de retenção](./text-to-speech.md).

4. Clique no ícone de **reprodução** (um triângulo) para visualizar a saída de sintetização padrão. Em seguida, aprimore a saída ajustando a pronúncia, a interrupção, o tom, a taxa, a entonação, o estilo da voz e muito mais. Para ver uma lista completa de opções, confira [Linguagem de Marcação de Síntese de Fala](speech-synthesis-markup.md). Assista a este [vídeo](https://www.youtube.com/watch?v=O1wIJ7mts_w) para saber como ajustar a saída de fala com a Criação de Conteúdo de Áudio. 
5. Salve e [exporte o áudio ajustado](#export-tuned-audio). Quando você salva faixa de ajuste no sistema, pode continuar trabalhando e iterando na saída. Quando a saída estiver pronta, gere uma tarefa de criação de áudio com o recurso de exportação. É possível observar o status da tarefa de exportação e baixar a saída para usar com seus aplicativos e produtos.

## <a name="create-an-audio-tuning-file"></a>Criar um arquivo de ajuste de áudio

Existem duas maneiras de colocar seu conteúdo na ferramenta de Criação de Conteúdo de Áudio.

**Opção 1:**

1. Clique no ícone **Novo arquivo** no canto superior direito para criar um arquivo de ajuste de áudio.
2. Digite ou cole o conteúdo na janela de edição. Cada arquivo pode ter até 20 mil caracteres. Se o script tiver mais que isso, use a Opção 2 para dividir automaticamente o conteúdo em vários arquivos. 
3. Não se esqueça de salvar.

**Opção 2:**

1. Clique em **Carregar** para importar um ou mais arquivos de texto. Há compatibilidade com texto sem formatação e SSML. Se o arquivo de script tiver mais de 20 mil caracteres, faça a divisão dele por parágrafos, por caractere ou por expressões regulares. 
3. Ao carregar os arquivos de texto, verifique se eles estão de acordo com estes requisitos.

   | Propriedade | Valor/observações |
   |----------|---------------|
   | Formato de arquivo | Texto sem formatação (.txt)<br/> Texto de SSML (.txt)<br/> Não há compatibilidade com arquivos zip |
   | Formato de codificação | UTF-8 |
   | Nome do arquivo | Cada arquivo precisa ter um nome exclusivo. Não há compatibilidade com duplicatas. |
   | Tamanho do texto | O arquivo de texto tem um limite de 20 mil caracteres. Se os arquivos tiverem mais que isso, faça a divisão deles seguindo as instruções na ferramenta. |
   | Restrições de SSML | Cada arquivo SSML só pode conter uma parte de SSML. |

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

Depois de analisar a saída de áudio e ficar satisfeito com o ajuste, exporte o áudio.

1. Clique em **Exportar** para criar uma tarefa de criação de áudio. É recomendável **exportar para a biblioteca de áudio**, pois ela é compatível com a saída de áudio longa e a experiência completa de saída de áudio. Também é possível baixar o áudio diretamente para o disco local, mas somente os primeiros dez minutos ficam disponíveis.
2. Escolha o formato de saída do áudio ajustado. Veja a seguir uma lista de formatos compatíveis e taxas de amostragem.
3. É possível ver o status da tarefa na guia **Exportar tarefa**. Se a tarefa falhar, confira a página de informações detalhadas para ver um relatório completo.
4. Quando a tarefa for concluída, o áudio ficará disponível para download na guia **Biblioteca de Áudio**.
5. Clique em **Download**. Agora você pode usar o áudio ajustado personalizado em aplicativos ou produtos.

**Formatos com suporte de áudio**

| Formatar | Taxa de amostragem de 16 kHz | Taxa de amostragem de 24 kHz |
|--------|--------------------|--------------------|
| WAV | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Como adicionar/remover usuários da Criação de Conteúdo de Áudio?

Se mais de um usuário quiser usar a Criação de Conteúdo de Áudio, permita o acesso dele à assinatura do Azure e ao recurso de fala. Se você adicionar um usuário a uma assinatura do Azure, ele poderá acessar todos os recursos nela. Mas se você adicionar somente um usuário ao recurso de fala, ele só acessará o recurso de fala e não poderá acessar outros recursos na assinatura do Azure. Um usuário com acesso ao recurso de fala pode usar a Criação de Conteúdo de Áudio.

### <a name="add-users-to-a-speech-resource"></a>Adicionar usuários a um recurso de fala

Siga estas etapas para adicionar um usuário ao recurso de fala para usar a Criação de Conteúdo de Áudio.

1. Pesquise **Serviços Cognitivos** no [portal do Azure](https://portal.azure.com/) e selecione o recurso de fala ao qual você quer adicionar usuários.
2. Clique em **Controle de acesso (IAM)** . Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Guia Atribuição de função":::
1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função. Na lista suspensa Função, selecione a função **Usuário dos Serviços Cognitivos**. Para dar ao usuário a propriedade do recurso de fala, selecione a função **Proprietário**.
1. Na lista, selecione um usuário. Se o usuário não estiver na lista, digite na caixa Selecionar para pesquisar no diretório os nomes de exibição e os endereços de email. Se o usuário não estiver no diretório, insira a [conta Microsoft](https://account.microsoft.com/account) do usuário (na qual o Azure Active Directory confia).
1. Clique em **Salvar** para atribuir a função. Após alguns instantes, o usuário é atribuído à função Usuário dos Serviços Cognitivos no escopo do recurso de fala.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Caixa de diálogo Adicionar função":::

1. Os usuários que você adicionar receberão um email de convite. Depois de clicarem em **Aceitar convite** > **Aceitar entrar no Azure**, eles poderão usar a [Criação de Conteúdo de Áudio](https://aka.ms/audiocontentcreation).

Os usuários que estão no mesmo recurso de fala verão o trabalho dos outros no estúdio da Criação de Conteúdo de Áudio. Se você quiser que cada usuário tenha um local de trabalho exclusivo e particular na Criação de Conteúdo de Áudio, [crie um recurso de fala](#step-2---create-a-speech-resource) para cada usuário e permita acesso exclusivo ao recurso de fala. 

### <a name="remove-users-from-a-speech-resource"></a>Remover usuários de um recurso de fala
1. Pesquise **Serviços Cognitivos** no portal do Azure e selecione o recurso de fala em que você quer remover usuários.
2. Clique em **IAM (Controle de Acesso)**. Clique na guia **Atribuições de funções** para ver todas as atribuições de função do recurso de fala em questão.
3. Selecione os usuários que você quer remover e clique em **Remover** > **OK**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Botão Remover":::

### <a name="enable-users-to-grant-access"></a>Habilitar os usuários a permitir acesso
Se você quiser que um dos usuários permita o acesso de outros usuários, precisará atribuir a ele a função de proprietário do recurso de fala e defini-lo como leitor do diretório do Azure. 
1. Adicione o usuário como proprietário do recurso de fala. Confira [como adicionar usuários a um recurso de fala](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Campo Proprietário da função":::
1. Selecione o menu recolhido no canto superior esquerdo. Clique em **Azure Active Directory** e em **Usuários**.
1. Pesquise a conta Microsoft do usuário e acesse a página de detalhes dele. Clique em **Funções atribuídas**.
1. Clique em **Adicionar atribuições** -> **Leitores de Diretório**.

## <a name="see-also"></a>Confira também

* [API de áudio longo](./long-audio-api.md)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Estúdio de Fala](https://speech.microsoft.com)
