---
title: "Tarefas permitidas em estados ou status diferentes nos Serviços BizTalk | Microsoft Docs"
description: "As ações/operações permitidas no status diferente de MABS: parar, iniciar, reiniciar, suspender, continuar, excluir, dimensionar, atualizar o backup e configuração de backup"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 918154852cf4ee1b80792eaddb588f5b0237da7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>O que você pode e não pode fazer usando o estado do Serviço BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Dependendo do estado atual do Serviço BizTalk, há operações que podem ou não serem executadas no Serviço BizTalk.

Por exemplo, você pode provisionar um novo Serviço BizTalk no portal clássico do Azure. Quando for concluído com êxito, o serviço BizTalk estará no estado `active`. No estado ativo, você pode parar, suspender e excluir o serviço BizTalk. Se você parar o serviço BizTalk e a interrupção falhar, o serviço BizTalk irá para um estado `StopFailed`. No estado `StopFailed`, você pode reiniciar o Serviço BizTalk. Se você tentar uma operação que não é permitida, como retomar, ocorrerá o seguinte erro:

`Operation not allowed`

## <a name="view-the-possible-states"></a>Exibir os possíveis estados

As tabelas a seguir listam as operações ou ações que podem ser executadas quando o Serviço BizTalk está em um estado específico. Um ✔ significa que a operação tem permissão enquanto estiver nesse estado. Uma entrada em branco significa que a operação não pode ser executada enquanto estiver nesse estado.

| Estado do serviço | Iniciar | Parar | Reiniciar | Suspender | Continuar | Excluir | Escala | Atualização <br/> Configuração | Backup |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Ativo |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Desabilitado |  |  |  |  |  | ✔ | |  |  | 
| Suspenso |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Parada | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Falha na atualização do serviço |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>Consulte também
* [Criar um Serviço BizTalk usando o Portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [O que você pode fazer nas guias painel, monitor e escala nos Serviços BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [O que você obtém com as edições Developer, Básico, Standard e Premium nos Serviços BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Como fazer backup e restaurar um Serviço BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Limitação explicada nos Serviços BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Recuperar os valores de nome e chave do emissor do Controle de Acesso e do Barramento de Serviços para o Serviço BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

