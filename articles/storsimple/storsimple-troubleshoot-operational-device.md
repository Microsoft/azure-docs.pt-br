---
title: Solucionar problemas de um dispositivo StorSimple implantado | Microsoft Docs
description: Descreve como diagnosticar e corrigir os erros que ocorrem em um dispositivo StorSimple que esteja implantado e operacional.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/16/2016
ms.author: v-sharos
ms.openlocfilehash: 8d1b4905d0a24c8df9eb2c986459286909fd20dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Solucionar problemas de um dispositivo operacional do StorSimple
## <a name="overview"></a>Visão geral
Este artigo oferece orientações úteis de solução de problemas para resolver problemas de configuração que você poderá encontrar depois que seu dispositivo StorSimple estiver implantado e operacional. Descreve problemas comuns, possíveis causas e etapas recomendadas para ajudá-lo a resolver problemas que podem ocorrer durante a execução do Microsoft Azure StorSimple. Essas informações se aplicam ao dispositivo físico local StorSimple e ao dispositivo virtual StorSimple.

No final deste artigo, há uma lista de códigos de erro que você pode encontrar durante a operação do Microsoft Azure StorSimple, bem como procedimentos que você pode seguir para resolver os erros. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processo do assistente de instalação de dispositivos operacionais
Você pode usar o assistente de instalação ([Invoke-HcsSetupWizard][1]) para verificar a configuração do dispositivo e tomar uma ação corretiva, se necessário.

Quando você executa o assistente de instalação em um dispositivo previamente configurado e operacional, o fluxo do processo é diferente. Você pode alterar apenas as seguintes entradas:

* Endereço IP, máscara de sub-rede e gateway
* Servidor DNS primário
* Servidor NTP primário
* Configuração do proxy da Web opcional

O assistente de instalação não executa as operações relacionadas à coleta de senha e ao registro do dispositivo.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erros que ocorrem durante as execuções subsequentes do assistente de instalação
A tabela a seguir descreve os erros que você poderá encontrar ao executar o assistente de instalação em um dispositivo operacional, as possíveis causas dos erros e as ações recomendadas para resolvê-los. 

| Nº | Mensagem ou condição de erro | Possíveis causas | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1 |Erro 350032: este dispositivo já foi desativado. |Você verá esse erro se executar o assistente de instalação em um dispositivo que esteja desativado. |[Contate o Suporte da Microsoft Support](storsimple-contact-microsoft-support.md) para as próximas etapas. Um dispositivo desativado não pode ser colocado em serviço. Uma redefinição de fábrica pode ser necessária antes que seja possível ativar o dispositivo novamente. |
| 2 |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(Exception from HRESULT: 0x80070001) |A atualização do servidor DNS está falhando. As configurações de DNS são configurações globais e são aplicadas a todas as interfaces de rede habilitadas. |Habilite a interface e aplique as configurações de DNS novamente. Isso poderá afetar a rede para outras interfaces habilitadas, já que essas configurações são globais. |
| 3 |O dispositivo parece estar online no portal de serviço do StorSimple Manager, mas quando você tenta concluir a instalação mínima e salvar a configuração, a operação falha. |Durante a instalação inicial, o proxy da Web não foi configurado, mesmo havendo um servidor proxy real pronto. |Use o [cmdlet Test-HcsmConnection][2] para localizar o erro. [Contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md) se você não puder corrigir o problema. |
| 4 |Invoke-HcsSetupWizard: Value não está no intervalo esperado. |Uma máscara de sub-rede incorreta gera esse erro. As possíveis causas são:  <ul><li> A máscara de sub-rede está ausente ou vazia.</li><li>O formato de prefixo Ipv6 está incorreto.</li><li>A interface está habilitada para a nuvem, mas o gateway está ausente ou incorreto.</li></ul>Observe que DATA 0 será automaticamente habilitado para nuvem se configurado por meio do assistente de instalação. |Para determinar o problema, use a sub-rede 0.0.0.0 ou 256.256.256.256 e, em seguida, examine a saída. Insira os valores corretos para a máscara de sub-rede, o gateway e o prefixo Ipv6, como necessário. |

## <a name="error-codes"></a>Códigos do Erro
Os erros estão listados em ordem numérica.

| Número do erro | Texto do erro ou descrição | Ação recomendada do usuário |
|:--- |:--- |:--- |
| 10502 |Foi encontrado um erro ao acessar sua conta de armazenamento. |Aguarde alguns minutos e tente novamente. Se o erro persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. |
| 40017 |A operação de backup falhou pois um volume especificado na política de backup não foi encontrado no dispositivo. |Repita a operação de backup. Se o erro persistir, contate o Suporte da Microsoft. para as próximas etapas. |
| 40018 |A operação de backup falhou pois nenhum dos volumes especificados na política de backup foi encontrado no dispositivo. |Repita a operação de backup. Se o erro persistir, contate o Suporte da Microsoft. para as próximas etapas. |
| 390061 |O sistema está ocupado ou indisponível. |Aguarde alguns minutos e tente novamente. Se o erro persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. |
| 390143 |Ocorreu um erro com o código de erro 390143. Erro desconhecido. |Se o erro persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. |

## <a name="next-steps"></a>Próximas etapas
Se não for possível resolver o problema, [contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência. 

[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
