---
title: Configurar o codificador Haivision KB para enviar uma transmissão ativa da taxa de bits única para o Azure | Microsoft Docs
description: Este tópico mostra como configurar o codificador ativo Haivision KB para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 7bb3db4861842e145689682035adc3c691538adf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297792"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Usar o codificador Haivision KB para enviar uma transmissão ao vivo de taxa de bits única  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Este tópico mostra como configurar o [codificador ativo Havision KB](https://www.haivision.com/products/kb-series/) para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa. Para obter mais informações, consulte [trabalhando com canais habilitados a executar codificação ativa com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerenciar o AMS (Serviços de Mídia do Azure) com a ferramenta AMSE (Gerenciador de Serviços de Mídia da Azure). Essa ferramenta é executada apenas em PCs com Windows. Se você estiver no Mac ou Linux, use o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Pré-requisitos
*   Acesso a um codificador Haivision KB, executando o SW v5.01 ou superior.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Especifique um nome de canal; o campo de descrição é opcional. Nas Configurações do Canal, selecione **Standard** para a opção Codificação Ativa, com o Protocolo de Entrada definido para **RTMP**. Você pode deixar todas as outras configurações como estão. Verifique se a opção **Iniciar o novo canal agora** está marcada.
3. Clique em **Criar Canal**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> O canal pode levar até 20 minutos para ser iniciado.

## <a name="configure-the-haivision-kb-encoder"></a>Configurar o codificador Haivision KB
Neste tutorial, são usadas as configurações de saída abaixo. O restante desta seção descreve as etapas de configuração mais detalhadamente.

Vídeo:
-   Codec: H.264
-   Perfil: Alto (Nível 4.0)
-   Taxa de bits: 5.000 Kbps
-   Quadro-chave: 2 segundos (60 quadros)
-   Taxa de quadros: 30

Áudio:
-   Codec: AAC (LC)
-   Taxa de bits: 192 Kbps
-   Taxa de amostragem: 44,1 kHz

## <a name="configuration-steps"></a>Etapas da configuração
1.  Faça logon na interface do usuário do Haivision KB.
2.  Clique no **Botão de Menu** no centro de controle do canal e selecione **Adicionar canal**  
    ![Captura de tela 14-08-2017 às 09:15:09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Insira o **Nome do Canal** no campo Nome e clique em próximo.  
    ![Captura de tela 14-08-2017 às 09:15:07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Selecione a **Fonte de Entrada do Canal** da lista suspensa **Fonte de Entrada** e clique em próximo.
    ![Captura de tela 14-08-2017 às 09:20:44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  A partir da lista suspensa **Modelo do Codificador** escolha **H264-720-AAC-192** e clique em próximo.
    ![Captura de tela 14-08-2017 às 09:23:15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  A partir da lista suspensa **Selecionar Nova Saída** escolha **RTMP** e clique em próximo.  
    ![Captura de tela 14-08-2017 às 09:27:51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  A partir da janela **Saída do Canal** preencha as informações de fluxo do Azure. Cole o link **RTMP** da configuração inicial do canal na área **Servidor**. Na área **Nome de Saída** digite o nome do canal. Na área do Modelo de Nome de Fluxo, use o modelo RTMPStreamName_%video_bitrate% para nomear o fluxo.
    ![Captura de tela 14-08-2017 às 09:33:17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Clique em Próximo e, em seguida, em Concluído.
9.  Clique no **Botão Reproduzir** para iniciar o canal do codificador.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Reprodução de teste
Navegue até a ferramenta AMSE e clique com botão direito do mouse no canal a ser testado. No menu, passe o mouse sobre Reproduzir a Visualização e selecione Player de Mídia do Azure.

Se a transmissão for exibida no player, isso significa que o codificador foi corretamente configurado para se conectar ao AMS.

Se um erro for recebido, será necessário redefinir o canal e ajustar as configurações do codificador. Confira o artigo de solução de problemas para obter diretrizes.

## <a name="create-a-program"></a>Criar um programa
1.  Depois que a reprodução do canal for confirmada, crie um programa. Na guia Ativo na ferramenta AMSE, clique com o botão direito do mouse na área do programa e selecione Criar Novo Programa.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nomeie o programa e, se necessário, ajuste a Duração da Janela de Arquivo (cujo padrão é de quatro horas). Você também pode especificar um local de armazenamento ou deixar como o padrão.
2.  Marque a caixa Iniciar o Programa agora.
3.  Clique em Criar Programa.
4.  Quando o programa estiver em execução, confirme a reprodução clicando com o botão direito do mouse do programa e navegando até Reproduzir o(s) programa(s), em seguida, selecionando com o Player de Mídia do Azure.
5.  Depois de confirmar, clique novamente com botão direito no programa e selecione Copiar a URL de Saída para Área de Transferência (ou recupere essas informações na opção Informações e configurações do programa do menu).

A transmissão agora está pronta para ser inserida em um player ou distribuída para um público para a exibição ao vivo.

> [!NOTE]
> A criação do programa leva menos tempo do que a criação do canal.
