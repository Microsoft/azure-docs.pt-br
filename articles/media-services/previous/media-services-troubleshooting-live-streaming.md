---
title: Guia de solução de problemas da transmissão ao vivo | Microsoft Docs
description: Este artigo fornece sugestões sobre como solucionar problemas de transmissão ao vivo dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1b7a7ec746f5400fe65e3e1db88ae61e97ae710a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009038"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guia de solução de problemas da transmissão ao vivo

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Este artigo fornece sugestões sobre como solucionar alguns problemas da transmissão ao vivo.

## <a name="issues-related-to-on-premises-encoders"></a>Problemas relacionados aos codificadores locais
Esta seção fornece sugestões sobre como solucionar problemas relacionados aos codificadores locais que estão configurados para enviar uma transmissão de taxa de bits única para canais do AMS habilitados para a codificação ativa.

### <a name="problem-would-like-to-see-logs"></a>Problema: gostaria de ver logs
* **Problema potencial**: não é possível localizar os logs do codificador que poderiam ajudar na depuração de problemas.
  
  * **Telestream Wirecast**: normalmente, você pode encontrar os logs em C:\Users\{nome_do_usuário\AppData\Roaming\Wirecast\ 
  * **Element Live**: você pode ver que há links para logs no portal de gerenciamento. Clique em **Estatísticas** e em **Logs**. Na página **Arquivos de Log** , você verá uma lista dos logs para todos os itens de LiveEvent; selecione o que corresponde à sua sessão atual. 
  * **Flash Media Live Encoder**: você pode encontrar o **Diretório de Log...** navegando para a guia **Log de Codificação**.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: não há nenhuma opção para gerar uma transmissão progressiva
* **Possível problema**: o codificador que está sendo usado não se desentrelaça automaticamente. 
  
    **Etapas de solução de problemas**: procure uma opção de desentrelaçamento na interface do codificador. Depois de habilitar o desentrelaçamento, verifique novamente as configurações de saída progressiva. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: várias configurações de saída de codificador foram tentadas, mas ainda não é possível se conectar.
* **Possível problema**: o canal de codificação do Azure não foi redefinido corretamente. 
  
    **Etapas de solução de problemas**: verifique se o codificador não está mais enviando para o AMS, pare-o e redefina o canal. Quando estiver em execução novamente, tente conectar seu codificador com as novas configurações. Se isso ainda não corrigir o problema, tente criar um novo canal, pois, às vezes, os canais podem ser corrompidos após várias tentativas com falha.  
* **Possível problema**: o tamanho do GOP ou as configurações de quadro-chave não são ideais. 
  
    **Etapas de solução de problemas**: o intervalo recomendado de tamanho do GOP ou de quadro-chave é de dois segundos. Alguns codificadores calculam essa configuração em número de quadros, enquanto que outras usam segundos. Por exemplo: na saída de 30 fps, o tamanho de GOP seria 60 quadros, o que é equivalente a 2 segundos.  
* **Possível problema**: as portas fechadas estão bloqueando a transmissão. 
  
    **Etapas de solução de problemas**: ao transmitir por RTMP, verifique as configurações de firewall e/ou proxy para confirmar que as portas de saída 1935 e 1936 estão abertas. 

> [!NOTE]
> Se, depois de seguir as etapas de solução de problemas, você ainda não conseguir realizar a transmissão, envie um tíquete de suporte usando o Portal do Azure.
> 
> 

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

