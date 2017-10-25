---
title: "Saiba mais sobre a Limitação nos Serviços BizTalk | Microsoft Docs"
description: "Saiba mais sobre os limites de limitação e comportamentos de tempo de execução resultantes para os serviços BizTalk. A limitação é baseada no uso de memória e número de mensagens. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 145e7470bbc01c676a1fb5856c0f9a8726e667fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-throttling"></a>Serviços BizTalk: limitação

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Os Serviços BizTalk do Azure implementam a limitação do serviço com base em duas condições: uso de memória e número de mensagens simultâneas em processamento. Este tópico lista as limitações e descreve o comportamento da Execução quando ocorre uma condição de limitação.

## <a name="throttling-thresholds"></a>Limites da limitação
A tabela a seguir lista os limites e origem da limitação:

|  | Descrição | Limite baixo | Limite alto |
| --- | --- | --- | --- |
| Memória |% de memória total de sistema disponível/PageFileBytes. <p><p>O total de PageFileBytes disponível é aproximadamente 2 vezes a RAM do sistema. |60% |70% |
| Processamento de mensagem |Número de processamento de mensagens simultaneamente |40 vezes o número de núcleos |100 vezes o número de núcleos |

Quando um limite alto é atingido, os Serviços BizTalk do Azure começam a ser limitados. A limitação é interrompida quando um limite baixo é atingido. Por exemplo, seu serviço estiver usando 65% da memória do sistema. Nesta situação, o serviço não sofre limitação. Seu serviço começa usando 70% da memória do sistema. Nessa situação, o serviço começa a ser limitado e continua até que o serviço use 60% da memória do sistema (limite baixo).

Os Serviços BizTalk do Azure acompanham o status da limitação (estado normal versus limitado) e a duração da limitação.

## <a name="runtime-behavior"></a>Comportamento do tempo de execução
Quando os Serviços BizTalk do Azure entram em estado de limitação, o seguinte ocorre:

* A limitação ocorre por instância de função. Por exemplo:<br/>
  A InstânciadeFunçãoA está limitada. A InstânciadeFunçãoB não está limitada. Nesta situação, as mensagens da InstânciadeFunçãoB são processadas conforme o esperado. As mensagens na RoleInstanceA são descartadas e ocorre uma falha com o seguinte erro:<br/><br/>
  **O servidor está ocupado. Tente novamente.**<br/><br/>
* Nenhuma origem de pull pode pesquisar ou baixar uma mensagem. Por exemplo:<br/>
  um pipeline puxa as mensagens de uma origem de FTP externa. A instância de função que faz puxa entra em estado de limitação. Nessa situação, o pipeline interrompe o download de mensagens adicionais até que a instância de função saia da limitação.
* Uma resposta é enviada ao cliente para que ele reenvie a mensagem.
* Você deve aguardar até que a limitação seja resolvida. Especificamente, você deve aguardar até que o limite baixo seja atingido.

## <a name="important-notes"></a>Observações importantes
* A limitação não pode ser desabilitada.
* Os limites de limitação não podem ser modificados.
* A limitação é implementada em todo o sistema.
* O Servidor de Banco de Dados SQL do Azure também tem uma limitação interna.

## <a name="additional-azure-biztalk-services-topics"></a>Tópicos adicionais dos Serviços BizTalk do Azure
* [Instalando o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Tutoriais: Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Consulte também
* [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Serviços BizTalk: provisionamento usando o portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Serviços BizTalk: gráfico do status do provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Serviços BizTalk: guias Painel, Monitor e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Serviços BizTalk: backup e restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Serviços BizTalk: nome e chave do emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

