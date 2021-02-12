---
title: Considerações do navegador do sistema do Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as considerações para usar os navegadores do sistema no Xamarin Android com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4230a194fb18587a209c100a39b0924e6170502d
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063460"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Considerações do navegador do sistema do Xamarin Android para uso do MSAL.NET

Este artigo discute o que você deve considerar ao usar o navegador do sistema no Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).

A partir do MSAL.NET 2.4.0 Preview, o MSAL.NET dá suporte a navegadores diferentes do Chrome. Ele não requer mais que o Chrome seja instalado no dispositivo Android para autenticação.

Recomendamos que você use navegadores que ofereçam suporte a guias personalizadas. Aqui estão alguns exemplos desses navegadores:

| Navegadores com suporte para guias personalizadas | Nome do pacote |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Produtor kiwis | com. kiwibrowser. browser|
|Corajoso | com. corajoso. browser|

Além de identificar navegadores que oferecem suporte a guias personalizadas, nossos testes indicam que alguns navegadores que não dão suporte a guias personalizadas também funcionam para autenticação. Esses navegadores incluem Opera, Opera Mini, inbrowser e Maxthon. 

## <a name="tested-devices-and-browsers"></a>Dispositivos e navegadores testados
A tabela a seguir lista os dispositivos e navegadores que foram testados quanto à compatibilidade de autenticação.

| Dispositivo | Navegador     |  Resultado  | 
| ------------- |:-------------:|:-----:|
| Huawei/um + | Chrome\* | Aprovado|
| Huawei/um + | Edge\* | Aprovado|
| Huawei/um + | Firefox\* | Aprovado|
| Huawei/um + | Corajoso\* | Aprovado|
| Um + | Ecosia\* | Aprovado|
| Um + | Produtor kiwis\* | Aprovado|
| Huawei/um + | Opera | Aprovado|
| Huawei | OperaMini | Aprovado|
| Huawei/um + | Navegador | Aprovado|
| Um + | Maxthon | Aprovado|
| Huawei/um + | DuckDuckGo | Usuário cancelou a autenticação|
| Huawei/um + | Navegador de UC | Usuário cancelou a autenticação|
| Um + | Dolphin | Usuário cancelou a autenticação|
| Um + | Navegador CM | Usuário cancelou a autenticação|
| Huawei/um + | Nenhum instalado | Exceção AndroidActivityNotFound|

\* Dá suporte a guias personalizadas

## <a name="known-issues"></a>Problemas conhecidos

Se o usuário não tiver um navegador habilitado no dispositivo, o MSAL.NET gerará uma `AndroidActivityNotFound` exceção.  
  - **Mitigação**: peça ao usuário para habilitar um navegador em seu dispositivo. Recomendamos um navegador que dê suporte a guias personalizadas.

Se a autenticação falhar (por exemplo, se a autenticação for iniciada com DuckDuckGo), MSAL.NET retornará `AuthenticationCanceled MsalClientException` . 
  - **Problema de raiz**: um navegador que dá suporte a guias personalizadas não foi habilitado no dispositivo. A autenticação foi iniciada com um navegador que não pôde concluir a autenticação. 
  - **Mitigação**: peça ao usuário para habilitar um navegador em seu dispositivo. Recomendamos um navegador que dê suporte a guias personalizadas.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações e exemplos de código, consulte [escolhendo entre um navegador da Web incorporado e um navegador do sistema no Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) e [incorporado versus interface do usuário da Web do sistema](msal-net-web-browsers.md#embedded-vs-system-web-ui).  