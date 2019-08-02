---
title: Saiba mais sobre os codificadores recomendados pelos Serviços de Mídia do Azure | Microsoft Docs
description: Saiba mais sobre os codificadores recomendados pelos serviços de mídia
services: media-services
keywords: codificação; codificadores; mídia
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: fc481129e652c6dacd15a5a6d039a9118393e8f1
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854239"
---
# <a name="recommended-on-premises-encoders"></a>Codificadores locais recomendados
Ao fazer uma transmissão ao vivo com os Serviços de Mídia do Azure, você pode especificar como deseja que o canal receba o fluxo de entrada. Se você optar por usar um codificador local com um canal de codificação ativa, o codificador deverá enviar por push um fluxo de taxa de bits única de alta qualidade como saída. Se você optar por usar um codificador local com um canal de passagem, o codificador deverá enviar por push um fluxo de múltiplas taxas de bits como saída com todas as qualidades de saída desejadas. Para obter mais informações, consulte [transmissão ao vivo com codificadores locais](media-services-live-streaming-with-onprem-encoders.md).

Os Serviços de Mídia do Azure recomendam o uso de um dos seguintes codificadores dinâmicos que têm RTMP como saída:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Alternador Studio (iOS)

Os Serviços de Mídia do Azure recomendam o uso de um dos seguintes codificadores ao vivo que possuem MP4 com taxa de bits múltipla (Smooth Streaming) como saída:
- Mídia Hero ao vivo e Hero 4K (UHD/HEVC) do Excel
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> Um codificador dinâmico pode enviar um fluxo de taxa de bits única para um canal de passagem, mas essa configuração não é recomendada porque não permite um streaming de taxa de bits adaptável para o cliente.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Como se tornar um parceiro de codificador local
Como um parceiro de codificador local dos serviços de mídia do Azure, os serviços de mídia promovem seu produto, recomendando o codificador para clientes corporativos. Para se tornar um parceiro de codificador local, você deve verificar a compatibilidade do codificador local com os serviços de mídia. Para fazer isso, conclua as seguintes verificações:

Verificação de canal de passagem
1. Criar ou acessar sua conta dos Serviços de Mídia do Azure
2. Criar e iniciar um canal de **passagem**
3. Configure seu codificador para enviar por push um fluxo ao vivo de múltiplas taxas de bits.
4. Criar um evento ao vivo publicado
5. Executar o codificador dinâmico por aproximadamente 10 minutos
6. Parar o evento ao vivo
7. Crie, inicie um ponto de extremidade Streaming, use um player como o [Azure Media Player](https://aka.ms/azuremediaplayer) para assistir ao recurso arquivado para garantir que a reprodução não tenha falhas visíveis em todos os níveis de qualidade (Ou, alternativamente, assista e valide por meio do URL de visualização durante o sessão ao vivo antes do passo 6)
8. Registrar a ID do Ativo, a URL de streaming publicada para o arquivo morto dinâmico e as configurações e a versão usada do codificador dinâmico
9. Redefinir o estado do canal depois de criar cada amostra
10. Repita os passos 3 a 9 para todas as configurações suportadas pelo seu codificador (com e sem sinalização de anúncios / legendas / diferentes velocidades de codificação)

Verificação de canal de codificação ativa
1. Criar ou acessar sua conta dos Serviços de Mídia do Azure
2. Criar e iniciar um canal de **codificação ativa**
3. Configure seu codificador para enviar por push um fluxo ao vivo de taxa de bits única.
4. Criar um evento ao vivo publicado
5. Executar o codificador dinâmico por aproximadamente 10 minutos
6. Parar o evento ao vivo
7. Crie, inicie um ponto de extremidade Streaming, use um player como o [Azure Media Player](https://aka.ms/azuremediaplayer) para assistir ao recurso arquivado para garantir que a reprodução não tenha falhas visíveis em todos os níveis de qualidade (Ou, alternativamente, assista e valide por meio do URL de visualização durante o sessão ao vivo antes do passo 6)
8. Registrar a ID do Ativo, a URL de streaming publicada para o arquivo morto dinâmico e as configurações e a versão usada do codificador dinâmico
9. Redefinir o estado do canal depois de criar cada amostra
10. Repita os passos 3 a 9 para todas as configurações suportadas pelo seu codificador (com e sem sinalização de anúncios / legendas / várias velocidades de codificação)

Verificação de longevidade
1. Criar ou acessar sua conta dos Serviços de Mídia do Azure
2. Criar e iniciar um canal de **passagem**
3. Configure seu codificador para enviar por push um fluxo ao vivo de múltiplas taxas de bits.
4. Criar um evento ao vivo publicado
5. Executar o codificador dinâmico por uma semana ou mais
6. Use um player como o [Azure Media Player](https://aka.ms/azuremediaplayer) para assistir ao streaming ao vivo de tempos em tempos (ou recurso arquivado) para garantir que a reprodução não tenha falhas visíveis
7. Parar o evento ao vivo
8. Registrar a ID do Ativo, a URL de streaming publicada para o arquivo morto dinâmico e as configurações e a versão usada do codificador dinâmico

Por fim, envie as configurações e os parâmetros de arquivo morto dinâmico registrados para os Serviços de Mídia enviando um email para amsstreaming@microsoft.com. Após o recebimento, os Serviços de Mídia executam testes de verificação nas amostras no codificador dinâmico. Entre em contato com os Serviços de Mídia em caso de dúvidas sobre esse processo.
