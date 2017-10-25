---
title: "Configurar o codificador NewTek TriCaster para enviar uma transmissão ativa da taxa de bits única | Microsoft Docs"
description: "Este tópico mostra como configurar o codificador ativo TriCaster para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 42b012fb98bd0504c931ce391d63aecca8c3d311
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Usar o codificador NewTek TriCaster para enviar uma transmissão ao vivo de taxa de bits única
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Este tópico mostra como configurar o codificador ativo [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa. Para obter mais informações, consulte [Trabalhando com canais habilitados para executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerenciar o AMS (Serviços de Mídia do Azure) com a ferramenta AMSE (Gerenciador de Serviços de Mídia da Azure). Essa ferramenta é executada apenas em PCs com Windows. Se você estiver no Mac ou Linux, use o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Ao usar o Tricaster para o envio de um feed de contribuição aos canais AMS habilitados para codificação ativa, pode haver problemas de áudio/vídeo no evento ao vivo, caso você use determinados recursos do Tricaster, como corte rápido entre feeds ou alternância de/para imagens fixas. A equipe do AMS está trabalhando para corrigir esses problemas; até lá, não é recomendável usar esses recursos.
>
>

## <a name="prerequisites"></a>Pré-requisitos
* [Criar uma conta dos Serviços de Mídia do Azure](media-services-portal-create-account.md)
* Verifique se há um Ponto de Extremidade de Streaming em execução. Para obter mais informações, veja [Gerenciar Pontos de Extremidade de Transmissão em uma conta de Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md)
* Instale a versão mais recente da ferramenta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Inicie a ferramenta e conecte-se à sua conta do AMS.

## <a name="tips"></a>Dicas
* Sempre que possível, use uma conexão de Internet com fio.
* Uma boa regra geral ao determinar os requisitos de largura de banda é dobrar as taxas de bits de transmissão. Embora isso não seja um requisito obrigatório, isso ajuda a reduzir o impacto do congestionamento da rede.
* Ao usar codificadores baseados em software, feche todos os programas desnecessários.

## <a name="create-a-channel"></a>Criar um canal
1. Na ferramenta AMSE, navegue até a guia **Ao Vivo** e clique com o botão direito do mouse na área de canais. Selecione **Criar canal...** no menu.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Especifique um nome de canal; o campo de descrição é opcional. Nas Configurações do Canal, selecione **Standard** para a opção Codificação Ativa, com o Protocolo de Entrada definido para **RTMP**. Você pode deixar todas as outras configurações como estão.

    Verifique se a opção **Iniciar o novo canal agora** está marcada.

3. Clique em **Criar Canal**.

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> O canal pode levar até 20 minutos para ser iniciado.
>
>

Enquanto o canal é iniciado, você pode [configurar o codificador](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Lembre-se de que a cobrança começa assim que o Canal entra em um estado pronto. Para obter mais informações, veja [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_tricaster_rtmp></a>Configurar o codificador do NewTek TriCaster
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

### <a name="configuration-steps"></a>Etapas da configuração
1. Crie um novo projeto do **NewTek TriCaster** dependendo de qual fonte de entrada de vídeo está sendo usada.
2. Depois de entrar nesse projeto, encontre o botão **Transmissão** e clique no ícone de engrenagem ao lado dele para acessar o menu de configuração da transmissão.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Depois de abrir o menu, clique em **Novo** no título Conexão. Quando solicitado para selecionar o tipo de conexão, selecione **Adobe Flash**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Clique em **OK**.
5. Um perfil FMLE agora pode ser importado clicando na seta suspensa no **Perfil de Streaming** e navegando até **Procurar**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navegue até onde o perfil FMLE configurado foi salvo.
7. Selecione-o e pressione **OK**.

    Uma vez que o perfil foi carregado, vá para a próxima etapa.
8. Obtenha a URL de entrada do canal para atribuí-la ao **Ponto de extremidade RTMP**do Tricaster.

    Navegue de volta para a ferramenta AMSE e verifique o status de conclusão do canal. Depois do Estado ser alterado de **Iniciando** para **Executando**, você poderá obter a URL de entrada.

    Quando o canal estiver em execução, clique com o botão direito no nome dele, navegue até parar sobre **Copiar URL de entrada para área de transferência**, em seguida, selecione **URL da Entrada Principal**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Cole essas informações no campo **Local** sob **Flash Server** dentro do projeto Tricaster. Atribua também um nome de transmissão no campo **ID da transmissão** .

    Se as informações do fluxo forem adicionadas ao perfil FMLE, ele também poderá ser importado para esta seção clicando em **Importar Configurações**, navegando até o perfil FMLE salvo e clicando em **OK**. Os campos relevantes do Flash Server devem preencher as informações do FMLE.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Quando terminar, clique em **OK** na parte inferior da tela. Quando as entradas de áudio e vídeo no Tricaster estiverem prontas, comece a transmissão para o AMS clicando no botão **Transmissão** .

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Antes de clicar em **Transmissão**, você **deve** assegurar que o Canal está pronto.
> Além disso, lembre-se de não deixar o Canal em um estado pronto sem um feed de contribuição de entrada por mais de 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste
Navegue até a ferramenta AMSE e clique com botão direito do mouse no canal a ser testado. No menu, passe o mouse sobre **Reproduzir a Visualização** e selecione **Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Se a transmissão for exibida no player, isso significa que o codificador foi corretamente configurado para se conectar ao AMS.

Se um erro for recebido, será necessário redefinir o canal e ajustar as configurações do codificador. Veja o tópico [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.  

## <a name="create-a-program"></a>Criar um programa
1. Depois que a reprodução do canal for confirmada, crie um programa. Na guia **Ativo** na ferramenta AMSE, clique com o botão direito na área do programa e selecione **Criar Novo Programa**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Nomeie o programa e, se necessário, ajuste a **Duração da Janela de Arquivo** (cujo padrão é de 4 horas). Você também pode especificar um local de armazenamento ou deixar como o padrão.  
3. Marque a caixa **Iniciar o Programa agora** .
4. Clique em **Criar Programa**.  

    >[!NOTE]
    >A criação do programa leva menos tempo do que a criação do canal.
        
5. Quando o programa estiver em execução, confirme a reprodução clicando com o botão direito do programa e navegando até **Reproduzi o(s) programa(s)**, em seguida, selecionando **com o Azure Media Player**.  
6. Depois de confirmar, clique novamente com botão direito no programa e selecione **Copie a URL de Saída para Área de Transferência** (ou recupere essas informações na opção **Informações e configurações do programa** do menu).

A transmissão agora está pronta para ser inserida em um player ou distribuída para um público para a exibição ao vivo.  

## <a name="troubleshooting"></a>solução de problemas
Veja o tópico [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.

## <a name="next-step"></a>Próxima etapa
Revise os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
