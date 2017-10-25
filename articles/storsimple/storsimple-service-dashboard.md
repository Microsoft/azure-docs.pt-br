---
title: "Painel do serviço do StorSimple Manager | Microsoft Docs"
description: "Descreve o painel de serviço do StorSimple Manager e explica como usá-lo para monitorar a integridade de sua solução StorSimple."
services: storsimple
documentationcenter: 
author: SharS
manager: carmonm
editor: 
ms.assetid: fb0f131d-d60b-45d7-ace2-56d0502e6627
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2016
ms.author: v-sharos
ms.openlocfilehash: 596431b7279b753ca4da838eb028cdde2022ce02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-dashboard"></a>Use o painel de serviço do StorSimple Manager
## <a name="overview"></a>Visão geral
A página do painel serviço do StorSimple Manager fornece uma exibição resumida de todos os dispositivos conectados ao serviço StorSimple Manager, destacando aqueles que precisam de atenção do administrador do sistema. Este tutorial apresenta a página do painel, explica seu conteúdo e função e descreve as tarefas que podem ser executadas nesta página.

![Painel de serviço](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

O painel de serviço do StorSimple Manager exibe as seguintes informações:

* Na área **gráfico** , você pode ver o gráfico relevante de métricas para seus dispositivos. É possível exibir o armazenamento primário (fixo localmente e em camadas) usado em todos os dispositivos, bem como o armazenamento em nuvem consumido pelos dispositivos durante determinado período. Use os controles no canto superior direito do gráfico para especificar uma escala de tempo de 1 semana, 1 mês, 3 meses ou 1 ano.
* A **visão geral de uso** mostra o armazenamento primário provisionado e consumido por todos os dispositivos relativos ao armazenamento total disponível em todos os dispositivos. **Provisionado** refere-se à quantidade de armazenamento que é preparado e alocado para uso, enquanto **Usado** refere-se ao uso de volumes conforme exibido pelos iniciadores conectados aos dispositivos.
* A área de **alertas** fornece um instantâneo de todos os alertas ativos em todos os dispositivos, agrupados por gravidade do alerta. Clicar no nível de gravidade abre a página **Alertas** , projetada para mostrar os alertas. Sobre a página **Alertas** , é possível clicar em um alerta individual para exibir detalhes adicionais sobre esse alerta, incluindo todas as ações recomendadas. Você também pode limpar o alerta se o problema foi resolvido.
* A área de **trabalhos** fornece um instantâneo dos trabalhos recentes em todos os dispositivos conectados ao seu serviço. Há links que você pode usar para examinar os trabalhos que estão atualmente em andamento, os que falharam nas últimas 24 horas ou aqueles que estão agendados para execução nas próximas 24 horas.
* A área de **visualização rápida** fornece informações úteis como o status do serviço, o número de dispositivos conectados ao serviço, local do serviço e detalhes da assinatura que está associada ao serviço. Também há um link para o log de operações. Clique no link para ver uma lista de todas as operações de serviço concluídas do StorSimple Manager.

Você pode usar a página do painel serviço do StorSimple Manager para iniciar as seguintes tarefas:

* Exibir ou regenerar a chave de registro.
* Alterar a chave de criptografia de dados do serviço.
* Exibir os logs de operação.

## <a name="view-or-regenerate-the-service-registration-key"></a>Exibir ou regenerar a chave de registro
A chave de registro é usada para registrar um dispositivo Microsoft Azure StorSimple com o serviço StorSimple Manager, para que o dispositivo seja exibido no Portal clássico do Azure para ações de gerenciamento adicionais. A chave é criada no primeiro dispositivo e compartilhada com o resto dos dispositivos.

Clicar em **Chave do registro** (na parte inferior da página) abre a caixa de diálogo **Chave de registro de serviço** em que você pode copiar a chave de registro de serviço atual para a área de transferência ou regenerar a chave de registro de serviço.

Regenerar a chave não afeta os dispositivos registrados anteriormente: afeta apenas os dispositivos registrados com o serviço depois que a chave é regenerada.

Para obter mais informações sobre como exibir e gerar a chave de registro de serviço, vá para [Obter a chave de registro de serviço](storsimple-manage-service.md#get-the-service-registration-key).

## <a name="change-the-service-data-encryption-key"></a>Alterar a chave de criptografia de dados do serviço
As chaves de criptografia de dados de serviço são usadas para criptografar dados confidenciais, como credenciais de conta de armazenamento, que são enviadas de seu serviço StorSimple Manager ao dispositivo StorSimple. Você precisará alterar essas chaves periodicamente se sua organização de TI tiver uma política de rotação de chave nos dispositivos de armazenamento. O processo de alteração de chave pode ser ligeiramente diferente dependendo se há um só ou vários dispositivos gerenciados pelo serviço StorSimple Manager.

A alteração da chave de criptografia de dados de serviço é um processo de três etapas:

1. Autorizar um dispositivo para alterar a chave de criptografia de dados de serviço no Portal clássico do Azure.
2. Usar o Windows PowerShell para StorSimple para iniciar a alteração da chave de criptografia de dados de serviço.
3. Se você tiver mais de um dispositivo StorSimple, atualize a chave de criptografia de dados de serviço nos outros dispositivos.

As etapas a seguir descrevem o processo de substituição da chave de criptografia de dados de serviço.

[!INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]

## <a name="view-the-operations-logs"></a>Exibir os logs de operações
Você pode exibir os logs de operação clicando no link de logs de operação disponível na área de **visualização rápida** do painel. Você será levado à página de serviços de gerenciamento, onde você poderá filtrar e consultar os logs específicos ao serviço do StorSimple Manager.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [solucionar problemas com um dispositivo StorSimple](storsimple-troubleshoot-operational-device.md).
* Saiba mais sobre como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

