---
title: "Guia de solução de problemas da transmissão ao vivo | Microsoft Docs"
description: "Este tópico fornece sugestões sobre como solucionar os problemas da transmissão ao vivo."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 3a7f6c1d-ce57-4fa4-a7a6-edb526b3ffbf
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: fa91baf7c494941fccf0e6ca38b930f3c2a521ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guia de solução de problemas da transmissão ao vivo
Este tópico fornece sugestões sobre como solucionar alguns problemas da transmissão ao vivo.

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
  
    **Etapas de solução de problemas**: o intervalo recomendado de tamanho do GOP ou de quadro-chave é de 2 segundos. Alguns codificadores calculam essa configuração em número de quadros, enquanto que outras usam segundos. Por exemplo: na saída de 30fps, o tamanho de GOP seria 60 quadros, o que é equivalente a 2 segundos.  
* **Possível problema**: as portas fechadas estão bloqueando a transmissão. 
  
    **Etapas de solução de problemas**: ao transmitir por RTMP, verifique as configurações de firewall e/ou proxy para confirmar que as portas de saída 1935 e 1936 estão abertas. Ao usar a transmissão RTP, confirme que a porta de saída 2010 está aberta. 

### <a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problema: ao configurar o codificador para a transmissão com o protocolo RTP, não há nenhum campo para inserir um nome de host.
* **Possível problema**: muitos codificadores RTP não permitem nomes de host, e será necessário adquirir um endereço IP.  
  
    **Etapas de solução de problemas**: para localizar o endereço IP, abra um prompt de comando em qualquer computador. Para fazer isso no Windows, abra o inicializador de Execução (WIN + R) e digite “cmd” para abrir.  
  
    Depois de aberto o prompt de comando, digite “Ping [Nome do Host do AMS]”. 
  
    O nome do host pode ser derivado pela omissão do número da porta da URL de Ingestão do Azure, como destacado no seguinte exemplo: 
  
    rtp://test2-amstest009.rtp.channel.mediaservices.windows.net:2010/ 
  
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

> [!NOTE]
> Se, depois de seguir as etapas de solução de problemas, você ainda não conseguir realizar a transmissão, envie um tíquete de suporte usando o Portal do Azure.
> 
> 

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

