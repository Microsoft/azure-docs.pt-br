---
title: Configurar o codificador Telestream Wirecast para enviar uma transmissão ao vivo de taxa de bits única | Microsoft Docs
description: 'Este tópico mostra como configurar o codificador ativo Wirecast para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para a codificação ativa. '
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: a736270919f36cb7d4bbd4e686a98ddc57f402fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016824"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Usar o codificador Wirecast para enviar uma transmissão ao vivo de taxa de bits única

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Este artigo mostra como configurar o codificador ativo [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para a codificação ativa. Para obter mais informações, consulte [trabalhando com canais habilitados a executar codificação ativa com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerenciar o AMS (Serviços de Mídia do Azure) com a ferramenta AMSE (Gerenciador de Serviços de Mídia da Azure). Essa ferramenta é executada apenas em PCs com Windows. Se você estiver no Mac ou Linux, use o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Os codificadores devem dar suporte a TLS 1,2 ao usar protocolos RTMPS. Use o Wirecast versão 13.0.2 ou superior devido ao requisito de TLS 1,2.

## <a name="prerequisites"></a>Pré-requisitos
* [Criar uma conta dos serviços de mídia do Azure](media-services-portal-create-account.md)
* Verifique se há um Ponto de Extremidade de Streaming em execução. Para obter mais informações, veja [Gerenciar Pontos de Extremidade de Transmissão em uma conta de Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md)
* Instale a versão mais recente da ferramenta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Inicie a ferramenta e conecte-se à sua conta do AMS.

## <a name="tips"></a>Dicas
* Sempre que possível, use uma conexão de Internet com fio.
* Uma boa regra geral ao determinar os requisitos de largura de banda é dobrar as taxas de bits de transmissão. Embora isso não seja um requisito obrigatório, isso ajuda a reduzir o impacto do congestionamento da rede.
* Ao usar codificadores baseados em software, feche todos os programas desnecessários.

## <a name="create-a-channel"></a>Criar um canal
1. Na ferramenta AMSE, navegue até a guia **Ao Vivo** e clique com o botão direito do mouse na área de canais. Selecione **Criar canal...**  no menu.

    ![A captura de tela mostra criar canal selecionado em um menu.](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especifique um nome de canal; o campo de descrição é opcional. Nas Configurações do Canal, selecione **Standard** para a opção Codificação Ativa, com o Protocolo de Entrada definido para **RTMP**. Você pode deixar todas as outras configurações como estão.

    Verifique se a opção **Iniciar o novo canal agora** está marcada.

3. Clique em **Criar Canal**.

   ![Captura de tela mostra a caixa de diálogo criar um canal ao vivo.](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> O canal pode levar até 20 minutos para ser iniciado.
>
>

Enquanto o canal está sendo iniciado, você pode [Configurar o codificador](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> A cobrança começa assim que o Canal entra em um estado pronto. Para obter mais informações, veja [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Configurar o codificador do Telestream Wirecast
Neste tutorial, são usadas as configurações de saída abaixo. O restante desta seção descreve as etapas de configuração mais detalhadamente.

**Vídeo**:

* Codec: H.264
* Perfil: Alto (nível 4.0)
* Taxa de bits: 5.000 kbps
* Quadro-chave: 2 segundos (60 segundos)
* Taxa de quadros: 30

**Áudio**:

* Codec: AAC (LC)
* Taxa de bits: 192 kbps
* Taxa de amostragem: 44,1 kHz

### <a name="configuration-steps"></a>Etapas de configuração
1. Abra o aplicativo do Telestream Wirecast no computador que está sendo usado e configure a transmissão RTMP.
2. Configure a saída navegando até a guia **Saída** e selecionando **Configurações de Saída...**.

    Certifique-se de que o **Destino de Saída** está definido como **Servidor RTMP**.
3. Clique em **OK**.
4. Na página de configurações, defina o campo **Destino** como **Serviços de Mídia do Azure**.

    O perfil de Codificação é pré-selecionado para **Azure H.264 720p 16:9 (1280x720)**. Para personalizar essas configurações, selecione o ícone de engrenagem à direita da lista suspensa e escolha **Nova Predefinição**.

    ![Captura de tela mostra a caixa de diálogo escolher um modelo com BlobTrigger selecionado.](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Configure as predefinições do codificador.

    Nomeie a predefinição e verifique as seguintes configurações recomendadas:

    **Vídeo**

   * Codificador: MainConcept H.264
   * Quadros por segundo: 30
   * Taxa de bits média: 5.000 kbits/s (Pode ser ajustada com base nas limitações de rede)
   * Perfil: Principal
   * Quadro chave: a cada 60 quadros

     **Sonoro**

   * Taxa de bits de destino: 192 kbits/s
   * Taxa de amostragem: 44,100 kHz

     ![Captura de tela mostra a predefinição do codificador para AzureTest1.](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Pressione **Salvar**.

    O campo Codificação agora tem o perfil recém-criado disponível para seleção.

    Certifique-se de selecionar o novo perfil.
7. Obtenha a URL de entrada do canal para atribuí-la ao **Ponto de extremidade RTMP** do Wirecast.

    Navegue de volta para a ferramenta AMSE e verifique o status de conclusão do canal. Depois do Estado ser alterado de **Iniciando** para **Executando**, você poderá obter a URL de entrada.

    Quando o canal estiver em execução, clique com o botão direito no nome dele, navegue até parar sobre **Copiar URL de entrada para área de transferência**, em seguida, selecione **URL da Entrada Principal**.  

    ![Captura de tela mostra a opção de entrada de cópia U R L para a área de transferência para a entrada primária U R L.](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Na janela **Configurações de Saída** do Wirecast, cole essas informações no campo **Endereço** da seção de saída e atribua um nome de transmissão.

    ![Captura de tela mostra as configurações de saída.](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Selecione **OK**.
2. Na tela principal do **Wirecast**, confirme se as fontes de entrada de áudio e vídeo estão prontas e pressione **Transmissão** no canto superior esquerdo.

    ![Captura de tela mostra o botão fluxo de Wirecast.](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Antes de clicar em **Transmissão**, você **deve** assegurar que o Canal está pronto.
> Além disso, lembre-se de não deixar o Canal em um estado pronto sem um feed de contribuição de entrada por mais de 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue até a ferramenta AMSE e clique com botão direito do mouse no canal a ser testado. No menu, passe o mouse sobre **Reproduzir a Visualização** e selecione **Player de Mídia do Azure**.  

![A captura de tela mostra a reprodução da visualização com Player de Mídia do Azure opção selecionada.](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se a transmissão for exibida no player, isso significa que o codificador foi corretamente configurado para se conectar ao AMS.

Se um erro for recebido, será necessário redefinir o canal e ajustar as configurações do codificador. Confira o artigo de [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter diretrizes.  

## <a name="create-a-program"></a>Criar um programa
1. Depois que a reprodução do canal for confirmada, crie um programa. Na guia **Live** da ferramenta AMSE, clique com o botão direito do mouse na área do programa e selecione **criar novo programa**.  

    ![Captura de tela mostra a opção criar programa selecionada.](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Nomeie o programa e, se necessário, ajuste a **Duração da Janela de Arquivo** (cujo padrão é de quatro horas). Você também pode especificar um local de armazenamento ou deixar como o padrão.  
3. Marque a caixa **Iniciar o Programa agora** .
4. Clique em **Criar Programa**.  

   >[!NOTE]
   >A criação do programa leva menos tempo do que a criação do canal.
       
5. Quando o programa estiver em execução, confirme a reprodução clicando com o botão direito do mouse do programa e navegando até **Reproduzir o(s) programa(s)**, em seguida, selecionando **com o Player de Mídia do Azure**.  
6. Depois de confirmar, clique novamente com botão direito no programa e selecione **Copiar a URL de Saída para Área de Transferência** (ou recupere essas informações na opção **Informações e configurações do programa** do menu).

A transmissão agora está pronta para ser inserida em um player ou distribuída para um público para a exibição ao vivo.  

## <a name="troubleshooting"></a>Solução de problemas
Confira o artigo de [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter diretrizes.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
