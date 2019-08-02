---
title: Conectar-se a dados de eventos de segurança do Windows para o Azure Sentinel Preview | Microsoft Docs
description: Saiba como se conectar a dados de eventos de segurança do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 188febf090ddb3f685f9d3c3b94d822f15bbcfcb
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673775"
---
# <a name="connect-windows-security-events"></a>Conectar eventos de segurança do Windows 

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir todos os eventos de segurança dos servidores Windows conectados ao espaço de trabalho do Azure Sentinel. Essa conexão permite que você exibir painéis, criar alertas personalizados e melhorará a investigação. Isso fornece mais informações sobre a rede da sua organização e aprimora sua capacidade de operação de segurança.  Você pode selecionar quais eventos de fluxo:

- **Todos os eventos** -Windows todos os eventos de segurança e AppLocker.
- **Common** -um conjunto padrão de eventos para fins de auditoria. Uma trilha de auditoria de usuário completo está incluída nesse conjunto. Por exemplo, esse conjunto contém a entrada do usuário e os eventos (ID de evento 4634) de entrada do usuário. Incluímos ações de auditoria, como alterações de grupo de segurança, operações Kerberos do controlador de domínio de chave e outros eventos que são recomendados por organizações do setor.

Eventos que têm um volume muito baixo foram incluídos no conjunto Comum, pois a principal motivação para escolhê-los dentre todos os eventos era reduzir o volume e não filtrar eventos específicos.
- **Mínimo** -um pequeno conjunto de eventos que podem indicar possíveis ameaças. Ao habilitar essa opção, você não poderá ter uma trilha de auditoria completa.  Esse conjunto aborde apenas os eventos que podem indicar uma violação com êxito e eventos importantes que tenham um volume muito baixo. Por exemplo, este conjunto contém logon usuário bem-sucedidas e com falha (evento IDs 4624 e 4625), mas ele não contém informações que são importantes para auditoria, mas não é significativo para a detecção e tem um volume relativamente alto de saída. A maioria do volume de dados desse conjunto é o evento de criação de processo (ID de evento 4688) e de eventos de entrada.
- **Nenhum** -nenhuma segurança ou eventos do AppLocker.

> [!NOTE]
> 
> - Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando Sentinel do Azure.

A lista a seguir fornece uma análise completa da segurança e AppLocker identificações de evento para cada conjunto:

| Camada de dados | Indicadores de eventos coletados |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Comum | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Configurar o conector de eventos de segurança do Windows

Para integrar totalmente os seus eventos de segurança do Windows com o Azure Sentinel:

1. No portal do Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **eventos de segurança do Windows** lado a lado. 
1. Selecione quais tipos de dados que você deseja transmitir.
1. Clique em **Atualizar**.
6. Para usar o esquema relevante no Log Analytics para os eventos de segurança do Windows, pesquise **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Ele pode levar cerca de 20 minutos até que seus logs comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar eventos de segurança do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

