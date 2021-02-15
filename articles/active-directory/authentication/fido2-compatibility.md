---
title: Suporte ao navegador de autenticação sem senha do FIDO2 | Azure Active Directory
description: Combinações de navegadores e sistemas operacionais dão suporte à autenticação sem senha do FIDO2 para aplicativos que usam Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0a8f96fabdff3543222077f5113b0bd602997b7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416903"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Suporte ao navegador de autenticação FIDO2 sem senha

Azure Active Directory permite que [as chaves de segurança FIDO2](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-keys) sejam usadas como um dispositivo com senha. A disponibilidade da autenticação FIDO2 para contas da Microsoft foi [anunciada em 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Conforme discutido no comunicado, determinados recursos e extensões opcionais para a especificação CTAP FIDO2 devem ser implementados para dar suporte à autenticação segura com contas da Microsoft e Azure Active Directory. O diagrama a seguir mostra quais combinações de navegadores e sistema operacional dão suporte à autenticação sem senha usando chaves de autenticação FIDO2 com Azure Active Directory.

## <a name="supported-browsers"></a>Navegadores com suporte

Esta tabela mostra o suporte para autenticar Azure Active Directory (Azure AD) e o MSA (contas da Microsoft). As contas da Microsoft são criadas por consumidores para serviços como Xbox, Skype ou Outlook.com. Os tipos de dispositivo com suporte incluem **USB**, a **NFC**(comunicação Near-campo) e o Bluetooth de baixa energia (**ble**).

|  | Chrome |  |  | Edge |  |  | Firefox |  |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![O Chrome dá suporte a USB no Windows para contas do AAD.][y] | ![O Chrome dá suporte a NFC no Windows para contas do AAD.][y] | ![O Chrome dá suporte a BLE no Windows para contas do AAD.][y] | ![O Edge dá suporte a USB no Windows para contas do AAD.][y] | ![O Edge dá suporte a NFC no Windows para contas do AAD.][y] | ![O Edge dá suporte a BLE no Windows para contas do AAD.][y] | ![O Firefox dá suporte a USB no Windows para contas do AAD.][y] | ![O Firefox oferece suporte a NFC no Windows para contas do AAD.][y] | ![O Firefox dá suporte a BLE no Windows para contas do AAD.][y] |
| **macOS**  | ![O Chrome dá suporte a USB no macOS para contas do AAD.][y] | ![O Chrome não oferece suporte a NFC no macOS para contas do AAD.][n] | ![O Chrome não oferece suporte a BLE no macOS para contas do AAD.][n] | ![O Edge dá suporte a USB no macOS para contas do AAD.][y] | ![O Edge não dá suporte a NFC no macOS para contas do AAD.][n] | ![O Edge não dá suporte a BLE no macOS para contas do AAD.][n] | ![O Firefox não dá suporte a USB no macOS para contas do AAD.][n] | ![O Firefox não oferece suporte a NFC no macOS para contas do AAD.][n] | ![O Firefox não oferece suporte a BLE no macOS para contas do AAD.][n] |
| **Linux**  | ![O Chrome dá suporte a USB no Linux para contas do AAD.][y] | ![O Chrome não oferece suporte a NFC no Linux para contas do AAD.][n] | ![O Chrome não oferece suporte a BLE no Linux para contas do AAD.][n] | ![O Edge não dá suporte a USB no Linux para contas do AAD.][n] | ![O Edge não dá suporte a NFC no Linux para contas do AAD.][n] | ![O Edge não dá suporte a BLE no Linux para contas do AAD.][n] | ![O Firefox não dá suporte a USB no Linux para contas do AAD.][n] | ![O Firefox não oferece suporte a NFC no Linux para contas do AAD.][n] | ![O Firefox não oferece suporte a BLE no Linux para contas do AAD.][n] |

## <a name="operating-system-versions-tested"></a>Versões do sistema operacional testadas

As informações na tabela acima foram testadas para as seguintes versões de sistema operacional.

| Sistema operacional | Última versão testada |
| --- | --- |
| Windows | Windows 10 20H2 1904 |
| macOS | OS X 11 Big Sur |
| Linux | Estação de trabalho Fedora 32 |

## <a name="next-steps"></a>Próximas etapas
[Habilitar entrada de chave de segurança sem senha (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
