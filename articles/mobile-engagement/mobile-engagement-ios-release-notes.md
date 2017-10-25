---
title: "Notas de versão do SDK para iOS do Azure Mobile Engagement | Microsoft Docs"
description: "Atualizações e procedimentos mais recentes para o SDK do iOS para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 9bdaa57f9902373ccf796ff109332b64c66bf9e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Notas de versão do SDK do iOS no Azure Mobile Engagement

## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Corrigidas as notificações apagadas na tela de fundo.
* Corrigidos os avisos no XCode 9 sobre as APIs não chamadas na fila principal.
* Corrigida uma perda de memória em pesquisas Reach.
* Removido o suporte para iOS 6.X. Desta versão em diante, o destino da implantação do seu aplicativo deverá ser pelo menos o iOS 7.

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* Melhor entrega de log em segundo plano.

## <a name="400-09122016"></a>4.0.0 (12/09/2016)
* Notificação fixa não acionada em dispositivos com iOS 10.
* Substituir o XCode 7.

## <a name="324-06302016"></a>3.2.4 (30/06/2016)
* Agregação fixa entre logs técnicos e outros logs.

## <a name="323-06072016"></a>3.2.3 (07/06/2016)
* Foi corrigido o bug em que comentários de entrega não eram registrados quando o aplicativo estava em segundo plano.
* Envio de logs técnicos otimizado.

## <a name="322-04072016"></a>3.2.2 (07/04/2016)
* Correção de um bug no cancelamento da solicitação HTTP que, por vezes, leva a uma falha.

## <a name="321-12112015"></a>3.2.1 (11/12/2015)
* Corrigido o atraso quando uma nova instância do aplicativo é disparada por uma notificação com links profundos

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* Bitcode no SDK habilitado para fazê-lo funcionar com **Xcode 7**.
* Bugs corrigidos relacionados a notificações no aplicativo.
* Feitas notificações no aplicativo mais confiáveis no caso de pouca bateria e outros cenários.
* Removidos logs do console extra gerados pela biblioteca de terceiros.

## <a name="310-08262015"></a>3.1.0 (26/08/2015)
* Corrija o bug de compatibilidade do iOS 9 com uma biblioteca de terceiros. Ele estava causando falhas ao enviar resultados de pesquisas, informações do aplicativo ou dados adicionais.

## <a name="300-06192015"></a>3.0.0 (06/19/2015)
* O Mobile Engagement usa notificações por push silenciosas.
* Suporte removido para iOS 4.X. A partir de esta versão, o destino da implantação do seu aplicativo deve ter pelo menos o iOS 6.

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* A ID do dispositivo Mobile Engagement para dispositivos < iOS 6 agora se baseia em um GUID gerado no momento da instalação

## <a name="210-04242015"></a>2.1.0 (24/04/2015)
* Compatibilidade com Swift adicionada.
* Ao clicar em uma notificação, a URL de ação agora é executada logo após o aplicativo ser aberto.
* Arquivo de cabeçalho ausente adicionado no pacote SDK.
* Corrigiu um problema quando o gerador de relatórios de falha do Mobile Engagement foi desabilitado.

## <a name="200-02172015"></a>2.0.0 (17/02/2015)
* Versão Inicial do Azure Mobile Engagement
* A configuração appId/sdkKey é substituída por uma configuração de cadeia de conexão.
* Removida a API para enviar e receber mensagens XMPP arbitrárias de entidades XMPP arbitrárias.
* Removida a API para enviar e receber mensagens entre dispositivos.
* Aprimoramentos de segurança.
* Controle SmartAd removido.
