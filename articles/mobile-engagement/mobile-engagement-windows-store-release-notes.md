---
title: "Notas de versão do SDK de Aplicativos do Windows Universal no Azure Mobile Engagement | Microsoft Docs"
description: "Azure Mobile Engagement - Notas de versão do SDK do Windows Universal"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: de938ce5-93d5-4218-9e33-10eef102bd61
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: dc5529a9e8f4eba867732f719ca8fff718c00d5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-sdk-release-notes"></a>Notas de versão do SDK de aplicativos do Windows Universal
## <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Aprimoramentos de estabilidade.

## <a name="340-04192016"></a>3.4.0 (19/04/2016)
* Melhorias de sobreposição de alcance.
* Adicionada a API "TestLogLevel" para habilitar/desabilitar/filtrar logs de console emitidos pelo SDK.
* Corrigidas as notificações na atividade visando a primeira atividade que não eram exibidas no início do Aplicativo.

## <a name="331-02182016"></a>3.3.1 (18/02/2016)
* Corrigidos os conflitos entre o conteúdo HTML do comunicado da Web e a página HTML do SDK.
* Aprimoramentos de estabilidade.

## <a name="330-01222016"></a>3.3.0 (22/01/2016)
* Corrigida a formatação com falha nos aplicativos UWP em execução no modo de versão.
* Corrigida a margem de 1px em notificações para aplicativos Universal 8.1.
* Esquemas ms-appx e ms-appdata disponíveis em urls de ação.
* Aprimoramentos de estabilidade.

## <a name="320-11202015"></a>3.2.0 (20/11/2015)
* Suporte adicionado para aplicativos da Plataforma Universal do Windows do Windows 10.
* Recurso de compartilhamento de canais de push adicionado para resolver conflitos de canal (agora compatível com os Hubs de Notificação do Azure).
* Falha corrigida ao solicitar a ID do dispositivo logo após a inicialização.
* Melhorias de logs do console.
* Falha corrigida ao analisar algumas exceções não tratadas.

## <a name="310-05212015"></a>3.1.0 (21/05/2015)
* A ID do dispositivo Mobile Engagement agora se baseia em um GUID gerado no momento da instalação.

## <a name="301-04292015"></a>3.0.1 (29/04/2015)
* Correção de um bug que afeta a inicialização do SDK em alguns aplicativos do Windows Phone WinRT.

## <a name="300-04032015"></a>3.0.0 (03/04/2015)
* Introdução do SDK do Mobile Engagement para aplicativo Universal (Windows e Windows Phone WinRT).
* Ícone de notificação padrão atualizado.
* Envia comentários de ação de notificação do sistema quando uma notificação é clicada.
* Correção de notificação de sistema que, às vezes, é repetida no aplicativo após ser clicada.

## <a name="200-02172015"></a>2.0.0 (17/02/2015)
* Versão Inicial do Azure Mobile Engagement
* A configuração appId/sdkKey é substituída por uma configuração de cadeia de conexão.
* Aprimoramentos de segurança.

