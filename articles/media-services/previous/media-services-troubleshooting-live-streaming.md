---
title: Guia de solução de problemas da transmissão ao vivo | Microsoft Docs
description: Este tópico fornece sugestões sobre como solucionar os problemas da transmissão ao vivo.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f502e3228274840d23b9f52512280fc0d9f0553b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544687"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guia de solução de problemas da transmissão ao vivo  

Este artigo fornece sugestões sobre como solucionar alguns problemas da transmissão ao vivo.

## <a name="issues-related-to-on-premises-encoders"></a>Problemas relacionados aos codificadores locais
Esta seção fornece sugestões sobre como solucionar problemas relacionados aos codificadores locais que estão configurados para enviar uma transmissão de taxa de bits única para canais do AMS habilitados para a codificação ativa.

### <a name="problem-would-like-to-see-logs"></a>Problema: gostaria de ver logs
* **Problema em potencial**: não é possível localizar logs do codificador que poderiam ajudar com problemas de depuração.
  
  * **Telestream Wirecast**: normalmente, é possível encontrar os logs em C:\Users\{nome_do_usuário}\AppData\Roaming\Wirecast\ 
  * **Elemental Live**: você pode ver que há links para logs no portal de gerenciamento. Clique em **Estatísticas** e em **Logs**. Na página **Arquivos de Log** , você verá uma lista dos logs para todos os itens de LiveEvent; selecione o que corresponde à sua sessão atual. 
  * **Flash Media Live Encoder**: você pode encontrar o **Diretório de Log...** navegando até a guia **Log de Codificação**.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: não há nenhuma opção para gerar uma transmissão progressiva
* **Problema em potencial**: o codificador que está sendo usado não se desentrelaça automaticamente. 
  
    **Etapas de solução de problemas:** procure uma opção de desentrelaçamento na interface do codificador. Depois de habilitar o desentrelaçamento, verifique novamente as configurações de saída progressiva. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: várias configurações de saída de codificador foram tentadas, mas ainda não é possível se conectar.
* **Problema em potencial**: o canal de codificação do Azure não foi redefinido corretamente. 
  
    **Etapas de solução de problemas:** verifique se o codificador não está mais fazendo envios por push para o AMS, pare e redefina o canal. Quando estiver em execução novamente, tente conectar seu codificador com as novas configurações. Se isso ainda não corrigir o problema, tente criar um novo canal, pois, às vezes, os canais podem ser corrompidos após várias tentativas com falha.  
* **Problema em potencial**: o tamanho do GOP ou as configurações de quadro-chave não são ideais. 
  
    **Etapas de solução de problemas:** o tamanho do GOP ou o intervalo do quadro-chave recomendado é de dois segundos. Alguns codificadores calculam essa configuração em número de quadros, enquanto que outras usam segundos. Por exemplo:  na saída de 30 fps, o tamanho do GOP seria de 60 quadros, que é equivalente a 2 segundos.  
* **Problema em potencial**: portas fechadas estão bloqueando a transmissão. 
  
    **Etapas de solução de problemas:** ao transmitir via RTMP, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 1935 e 1936 estão abertas. 

> [!NOTE]
> Se, depois de seguir as etapas de solução de problemas, você ainda não conseguir realizar a transmissão, envie um tíquete de suporte usando o Portal do Azure.
> 
> 

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

