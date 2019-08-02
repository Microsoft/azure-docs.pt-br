---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ebe112103bc3eb30239e80095db9bb91a33bebf3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172117"
---
## <a name="troubleshooting-update-failures"></a>Solucionando problemas de falhas na atualização
**E se você receber uma notificação informando que as verificações de pré-atualização falharam?**

Se uma pré-verificação falhar, certifique-se de que já observou a barra de notificação detalhada na parte inferior da página. Ela explica os motivos da falha da pré-verificação. Por exemplo, você receberá uma notificação sobre as falhas na verificação da integridade do controlador e da integridade do componente de hardware. Acesse **Monitorar > Integridade do hardware**. Você precisa ter certeza de que ambos os controladores estão em boas condições e online. Também é preciso ter certeza de que todos os componentes de hardware no dispositivo StorSimple sejam mostrados como íntegros nessa folha. Em seguida, você pode tentar instalar as atualizações. Se não for possível corrigir os problemas de componente de hardware, você precisará contatar o Suporte da Microsoft para saber das próximas etapas.

**E se você receber uma mensagem de erro “Não foi possível instalar atualizações” e a recomendação for consultar o guia de solução de problemas de atualização para determinar a causa da falha?**

Uma causa provável para isso pode ser a falta de conectividade com os servidores Microsoft Update. Essa é uma verificação manual que precisa ser executada. Se você perder a conectividade com o servidor de atualização, o trabalho de atualização falhará. É possível verificar a conectividade usando o seguinte cmdlet na interface do Windows PowerShell do dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Execute o cmdlet em ambos os controladores.

Se você verificou que há conectividade e o problema continuar, contate o Suporte da Microsoft para saber das próximas etapas.

**E se você receber uma falha na atualização ao atualizar o seu dispositivo para Atualização 4 e ambos os controladores estiverem executando a Atualização 4?**

Iniciando a Atualização 4, se os dois controladores estiverem executando a mesma versão de software e se houver falha na atualização, os controladores não entrarão no modo de recuperação. Essa situação pode ocorrer se o hotfix de software do dispositivo (1º atualização da ordem) for aplicado em ambos os controladores com êxito, mas outros hotfixes (2ª ordem e 3ª ordem) ainda estão a ser aplicados. Iniciando a Atualização 4, os controladores entrarão no modo de recuperação somente se os dois controladores estiverem executando versões diferentes do software. 

Se o usuário vê uma falha na atualização, quando ambos os controladores estão executando a Atualização 4, recomendamos que aguarde alguns minutos e, em seguida, tente atualizar novamente. Se a repetição falhar, ele deverá contatar o Suporte da Microsoft.
