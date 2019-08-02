---
title: Codificadores de streaming dinâmicos recomendados pelos Serviços de Mídia – Azure | Microsoft Docs
description: Saiba mais sobre codificadores locais de transmissão ao vivo recomendados pelos Serviços de Mídia
services: media-services
keywords: codificação; codificadores; mídia
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/16/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 1f152f7f94c12beecf015d9389ed85cb310573bd
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297734"
---
# <a name="recommended-live-streaming-encoders"></a>Codificadores de transmissão ao vivo recomendados

Nos Serviços de Mídia do Azure, um [Evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) (canal) representa um pipeline para processamento de conteúdo de streaming dinâmico. O Evento ao vivo recebe fluxos de entrada dinâmicos em uma de duas maneiras.

* Um codificador dinâmico local envia um RTMP com múltiplas taxas de bits ou fluxo Smooth Streaming (MP4 fragmentado) para o evento ao vivo que não está habilitado para executar a codificação ativa com os Serviços de Mídia. Os fluxos ingeridos passam por eventos ao vivo sem nenhum processamento adicional. Esse método é chamado **passagem**. Um codificador dinâmico pode enviar um fluxo de taxa de bits único para um canal de passagem. Não recomendamos essa configuração, porque ela não possibilita o streaming de taxa de bits adaptável para o cliente.

  > [!NOTE]
  > O uso de um método de passagem é a maneira mais econômica de realizar uma transmissão ao vivo.
 
* Um codificador dinâmico local envia um fluxo de taxa de bits única para o evento ao vivo que está habilitado para executar a codificação ativa com os Serviços de Mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O evento ao vivo, em seguida, executa a codificação ativa do fluxo de entrada de taxa de bits única para um fluxo de vídeo de múltiplas taxas de bits (adaptável).

Para obter informações detalhadas sobre a codificação ativa com os Serviços de Mídia, consulte [Transmissão ao vivo com Serviços de Mídia v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Codificadores dinâmicos com RTMP de saída

Os Serviços de Mídias recomendam usar um dos seguintes codificadores dinâmicos que têm RTMP como saída. Os esquemas de URL com suporte são `rtmp://` ou `rtmps://`.

> [!NOTE]
> Ao transmitir via RTMP, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 1935 e 1936 estão abertas.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Alternador Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificadores dinâmicos que transmitem MP4 fragmentado

Os Serviços de Mídia recomendam usar um dos seguintes codificadores dinâmicos que têm Smooth Streaming com múltiplas taxas de bits (MP4 fragmentado) como saída. Os esquemas de URL com suporte são `http://` ou `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Mídia Hero ao vivo e Hero 4K (UHD/HEVC) do Excel

> [!TIP]
>  Se você estiver transmitindo eventos ao vivo em vários idiomas (por exemplo, uma faixa de áudio em inglês e uma faixa de áudio em espanhol), poderá fazer isso com o Media Excel Live Encoder configurado para enviar o feed ao vivo para um evento ao vivo de passagem.

## <a name="configuring-on-premises-live-encoder-settings"></a>Definir as configurações do codificador dinâmico local

Para obter informações sobre quais configurações são válidas para o seu tipo de evento ao vivo, consulte [Comparação de tipos de evento ao vivo](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Requisitos de reprodução

Para reproduzir conteúdo, os fluxos de áudio e de vídeo devem estar presentes. Não há suporte para a reprodução de fluxo somente de vídeo.

### <a name="configuration-tips"></a>Dicas de configuração

- Sempre que possível, use uma conexão de Internet com fio.
- Ao determinar os requisitos de largura de banda, dobre as taxas de bits de streaming. Embora não seja obrigatório, essa regra simples ajuda a atenuar o impacto do congestionamento de rede.
- Ao usar codificadores baseados em software, feche todos os programas desnecessários.
- A alteração da configuração do codificador após ela ter começado a efetuar push tem efeitos negativos sobre o evento. As alterações na configuração podem fazer o evento ficar instável. 
- Reserve bastante tempo para configurar seu evento. Para eventos em alta escala, recomendamos que você comece a configuração uma hora antes do evento.

## <a name="becoming-an-on-premises-encoder-partner"></a>Tornar-se um parceiro de codificador local

Como um parceiro de codificador local dos Serviços de Mídia do Azure, os Serviços de Mídia promovem seu produto, recomendando seu codificador aos clientes corporativos. Para tornar-se um parceiro de codificador local, é necessário verificar a compatibilidade do seu codificador local com os Serviços de Mídia. Para fazer isso, conclua as seguintes verificações.

### <a name="pass-through-live-event-verification"></a>Verificação de evento ao vivo de passagem

1. Na conta dos Serviços de Mídia, verifique se o **Ponto de Extremidade de Streaming** está em execução. 
2. Crie e inicie um evento ao vivo de **passagem**. <br/> Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador local para usar a URL para enviar o fluxo ao vivo com múltiplas taxas de bits para os Serviços de Mídia.
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
5. Crie um novo objeto de **ativo**.
6. Crie um **LiveOutput** e use o nome do ativo que você criou.
7. Crie um **Localizador de Streaming** com os tipos internos da **Política de Streaming**.
8. Liste os caminhos no **Localizador de Streaming** para retornar as URLs a serem usadas.
9. Obtenha o nome do host para o **Ponto de Extremidade de Streaming** do qual você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Execute o codificador dinâmico por aproximadamente 10 minutos.
12. Interrompa o Evento ao Vivo. 
13. Use um player como o [Player de Mídia do Azure](https://aka.ms/azuremediaplayer) para assistir ao ativo arquivado para verificar se a reprodução não tem falhas visíveis em nenhum nível de qualidade. Ou assista e valide por meio da URL de visualização durante a sessão ao vivo.
14. Registre a ID do ativo, a URL de streaming publicada para os arquivos ao vivo e as configurações e a versão usadas no codificador dinâmico.
15. Reinicie o estado do evento ao vivo após criar cada exemplo.
16. Repita as etapas de 5 a 15 para todas as configurações compatíveis com o codificador (com e sem sinalização de anúncios, legendas ou diferentes velocidades de codificação).

### <a name="live-encoding-live-event-verification"></a>Verificação do evento ao vivo de codificação ativa

1. Na conta dos Serviços de Mídia, verifique se o **Ponto de Extremidade de Streaming** está em execução. 
2. Crie e inicie o evento ao vivo de **codificação ativa**. <br/> Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador para enviar por push uma transmissão ao vivo de taxa de bits única para os Serviços de Mídia.
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
5. Crie um novo objeto de **ativo**.
6. Crie um **LiveOutput** e use o nome do ativo que você criou.
7. Crie um **Localizador de Streaming** com os tipos internos da **Política de Streaming**.
8. Liste os caminhos no **Localizador de Streaming** para retornar as URLs a serem usadas.
9. Obtenha o nome do host para o **Ponto de Extremidade de Streaming** do qual você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Execute o codificador dinâmico por aproximadamente 10 minutos.
12. Interrompa o Evento ao Vivo.
13. Use um player como o [Player de Mídia do Azure](https://aka.ms/azuremediaplayer) para assistir ao ativo arquivado para verificar se a reprodução não tem galhas visíveis em nenhum nível. Ou assista e valide por meio da URL de visualização durante a sessão ao vivo.
14. Registre a ID do ativo, a URL de streaming publicada para os arquivos ao vivo e as configurações e a versão usadas no codificador dinâmico.
15. Reinicie o estado do evento ao vivo após criar cada exemplo.
16. Repita as etapas de 5 a 15 para todas as configurações compatíveis com o codificador (com e sem sinalização de anúncios, legendas ou diferentes velocidades de codificação).

### <a name="longevity-verification"></a>Verificação de longevidade

Siga as mesmas etapas que as da [verificação de evento ao vivo de passagem](#pass-through-live-event-verification), exceto pela etapa 11. <br/>Em vez de dez minutos, execute o codificador dinâmico por uma semana ou mais. Use um player como o [Player de Mídia do Azure](https://aka.ms/azuremediaplayer) para assistir à transmissão ao vivo periodicamente (ou um ativo arquivado) para verificar se a reprodução não tem falhas visíveis.

### <a name="email-your-recorded-settings"></a>Envie suas configurações registradas por email

Por fim, envie por email as configurações registradas e os parâmetros dos arquivos ao vivo aos Serviços de Mídia do Azure em amshelp@microsoft.com como uma notificação de que todas as verificações de autoverificação foram aprovadas. Além disso, inclua suas informações de contato para quaisquer acompanhamentos. Contate a equipe dos Serviços de Mídia do Azure para tratar quaisquer questões sobre esse processo.

## <a name="next-steps"></a>Próximas etapas

[Transmissão ao vivo com os Serviços de Mídia v3](live-streaming-overview.md)
