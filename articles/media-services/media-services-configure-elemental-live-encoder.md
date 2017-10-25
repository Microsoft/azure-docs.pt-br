---
title: "Configurar o codificador Elemental Live para enviar uma transmissão ativa de taxa de bits única | Microsoft Docs"
description: "Este tópico mostra como configurar o codificador Elemental Live para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para a codificação ativa."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
ms.openlocfilehash: 668a3ab46a70c0ee25fa87031d27c0f4333ec89c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Usar o codificador Elemental Live para enviar uma transmissão ao vivo de taxa de bits única
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Este tópico mostra como configurar o codificador [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live) para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para a codificação ativa.  Para obter mais informações, consulte [trabalhando com canais habilitados a executar codificação ativa com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerenciar o AMS (Serviços de Mídia do Azure) com a ferramenta AMSE (Gerenciador de Serviços de Mídia da Azure). Essa ferramenta é executada apenas em PCs com Windows. Se você estiver no Mac ou Linux, use o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Pré-requisitos
* Deve ter um conhecimento prático de uso da interface Web do Elemental Live para criar eventos ativos.
* [Criar uma conta dos Serviços de Mídia do Azure](media-services-portal-create-account.md)
* Verifique se há um Ponto de Extremidade de Streaming em execução. Para obter mais informações, veja [Gerenciar Pontos de Extremidade de Transmissão em uma conta de Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md).
* Instale a versão mais recente da ferramenta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Inicie a ferramenta e conecte-se à sua conta do AMS.

## <a name="tips"></a>Dicas
* Sempre que possível, use uma conexão de Internet com fio.
* Uma boa regra geral ao determinar os requisitos de largura de banda é dobrar as taxas de bits de transmissão. Embora isso não seja um requisito obrigatório, isso ajuda a reduzir o impacto do congestionamento da rede.
* Ao usar codificadores baseados em software, feche todos os programas desnecessários.

## <a name="elemental-live-with-rtp-ingest"></a>Elemental Live com ingestão de RTP
Esta seção mostra como configurar um codificador Elemental Live que faz uma transmissão ao vivo de taxa de bits única através de RTP.  Para saber mais, consulte [Fluxo MPEG TS por RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Criar um canal

1. Na ferramenta AMSE, navegue até a guia **Ao Vivo** e clique com o botão direito do mouse na área de canais. Selecione **Criar canal...** no menu.

    ![Elementares](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Especifique um nome de canal; o campo de descrição é opcional. Em Configurações de Canal, selecione **Padrão** para a opção de Codificação Ativa, com o Protocolo de Entrada definido como **RTP (MPEG-TS)**. Você pode deixar todas as outras configurações como estão.

    Verifique se a opção **Iniciar o novo canal agora** está marcada.

3. Clique em **Criar Canal**.

   ![Elementares](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> O canal pode levar até 20 minutos para ser iniciado.
>
>

Enquanto o canal é iniciado, você pode [configurar o codificador](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

> [!IMPORTANT]
> Lembre-se de que a cobrança começa assim que o Canal entra em um estado pronto. Para obter mais informações, veja [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

### <a id=configure_elemental_rtp></a>Configurar o codificador do Elemental Live
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

#### <a name="configuration-steps"></a>Etapas da configuração
1. Navegue até a interface Web **Elemental Live** e configure o codificador para transmissão **UDP/TS**.
2. Depois de criar um novo evento, role para baixo até os grupos de saída e adicione o grupo de saída **UDP/TS** .
3. Crie uma nova saída selecionando **Nova Transmissão** e clicando em **Adicionar Saída**.  

    ![Elementares](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > É recomendável que o evento Elementar tenha o código de tempo definido como "Relógio do Sistema" para ajudar o codificador a reconectar em caso de falha na transmissão.
   >
   >
4. Agora que a Saída foi criada, clique em **Adicionar Transmissão**. As configurações de saída agora podem ser configuradas.
5. Role para baixo até a “Transmissão 1” que você acabou de criar, clique na guia **Vídeo** à esquerda e expanda a seção de configurações **Avançadas**.

    ![Elementares](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Embora o Elemental Live tenha uma ampla variedade possibilidades de personalização disponíveis, as configurações a seguir são recomendadas para a introdução ao streaming para AMS.

   * Resolução: 1280 x 720
   * Taxa de quadros: 30
   * Tamanho de GOP: 60 quadros
   * Modo de entrelaçamento: Progressivo
   * Taxa de bits: 5000000 bits/s (Isso pode ser ajustado baseado nas limitações de rede)

    ![Elementares](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. Obter URL de entrada do canal.

    Navegue de volta para a ferramenta AMSE e verifique o status de conclusão do canal. Depois do Estado ser alterado de **Iniciando** para **Executando**, você poderá obter a URL de entrada.

    Quando o canal estiver em execução, clique com o botão direito no nome dele, navegue até parar sobre **Copiar URL de entrada para área de transferência**, em seguida, selecione **URL da Entrada Principal**.  

    ![Elementares](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. Cole essas informações no campo **Destino Principal** do Elementar. Todas as outras configurações podem permanecer como o padrão.

    ![Elementares](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Para redundância adicional, repita essas etapas com a URL de entrada secundária, criando uma guia separada de "Saída" para Streaming de UDP/TS.
3. Clique em **Criar** (se um novo evento foi criado) ou **Atualizar** (se estiver editando um evento já existente) e inicie o codificador.

> [!IMPORTANT]
> Antes de clicar em **Iniciar** na interface Web do Elemental Live, é **necessário** verificar se o Canal está pronto.
> Além disso, lembre-se de não deixar o Canal em um estado pronto sem um evento por mais de 15 minutos.
>
>

Após a transmissão estar em execução por 30 segundos, navegue de volta até a ferramenta AMSE e reproduza o teste.  

### <a name="test-playback"></a>Reprodução de teste

Navegue até a ferramenta AMSE e clique com botão direito do mouse no canal a ser testado. No menu, passe o mouse sobre **Reproduzir a Visualização** e selecione **Azure Media Player**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Se a transmissão for exibida no player, isso significa que o codificador foi corretamente configurado para se conectar ao AMS.

Se um erro for recebido, será necessário redefinir o canal e ajustar as configurações do codificador. Veja o tópico [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.   

### <a name="create-a-program"></a>Criar um programa
1. Depois que a reprodução do canal for confirmada, crie um programa. Na guia **Ativo** na ferramenta AMSE, clique com o botão direito na área do programa e selecione **Criar Novo Programa**.  

    ![Elementares](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. Nomeie o programa e, se necessário, ajuste a **Duração da Janela de Arquivo** (cujo padrão é de 4 horas). Você também pode especificar um local de armazenamento ou deixar como o padrão.  
3. Marque a caixa **Iniciar o Programa agora** .
4. Clique em **Criar Programa**.  

    >[!NOTE]
    > A criação do programa leva menos tempo do que a criação do canal.   
      
5. Quando o programa estiver em execução, confirme a reprodução clicando com o botão direito do programa e navegando até **Reproduzi o(s) programa(s)**, em seguida, selecionando **com o Azure Media Player**.  
6. Depois de confirmar, clique novamente com botão direito no programa e selecione **Copie a URL de Saída para Área de Transferência** (ou recupere essas informações na opção **Informações e configurações do programa** do menu).

A transmissão agora está pronta para ser inserida em um player ou distribuída para um público para a exibição ao vivo.  

## <a name="troubleshooting"></a>solução de problemas
Veja o tópico [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
