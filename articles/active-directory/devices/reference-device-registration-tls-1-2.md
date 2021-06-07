---
title: Imposição de TLS 1,2-serviço de registro de Azure Active Directory
description: Remover suporte para TLS 1,0 e 1,1 para o serviço Registro de Dispositivos do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89268750"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Impor o TLS 1,2 para o serviço de registro do Azure AD

O serviço de registro de dispositivo Azure Active Directory (AD do Azure) é usado para conectar dispositivos à nuvem com uma identidade de dispositivo. Atualmente, o serviço de Registro de Dispositivos do Azure AD dá suporte ao uso do protocolo TLS 1,2 para comunicações com o Azure. Para garantir a segurança e a melhor criptografia de classe, a Microsoft recomenda desabilitar o TLS 1,0 e 1,1. Este documento fornecerá informações sobre como garantir que os computadores usados para concluir o registro e se comunicar com o serviço de Registro de Dispositivos do Azure AD usam o TLS 1,2.

O protocolo TLS versão 1,2 é um protocolo de criptografia projetado para fornecer comunicações seguras. O protocolo TLS tem como objetivo principal fornecer privacidade e integridade dos dados. O TLS passou por muitas iterações com a versão 1,2 sendo definida na [RFC 5246 (link externo)](https://tools.ietf.org/html/rfc5246).

A análise atual de conexões mostra pouco uso de TLS 1,1 e 1,0, mas estamos fornecendo essas informações para que você possa atualizar quaisquer clientes ou servidores afetados, conforme necessário, antes que o suporte para TLS 1,1 e 1,0 seja encerrado. Se você estiver usando uma infraestrutura local para cenários híbridos ou Serviços de Federação do Active Directory (AD FS) (AD FS), certifique-se de que a infraestrutura possa dar suporte a conexões de entrada e saída que usam TLS 1,2.

## <a name="update-windows-servers"></a>Atualizar servidores Windows

Para servidores Windows que usam o serviço Registro de Dispositivos do Azure AD ou atuam como proxies, use as seguintes etapas para garantir que o TLS 1,2 esteja habilitado:

> [!IMPORTANT]
> Depois de atualizar o registro, você deve reiniciar o Windows Server para que as alterações entrem em vigor.

### <a name="enable-tls-12"></a>Habilitar o TLS 1.2

Verifique se as seguintes cadeias de caracteres de registro estão configuradas conforme mostrado:

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ cliente
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ servidor
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\. NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:00000001

## <a name="update-non-windows-proxies"></a>Atualizar proxies não Windows

Qualquer computador que atue como proxies entre dispositivos e o serviço de Registro de Dispositivos do Azure AD deve garantir que o TLS 1,2 esteja habilitado. Siga as diretrizes do seu fornecedor para garantir o suporte.

## <a name="update-ad-fs-servers"></a>Atualizar servidores AD FS

Qualquer servidor AD FS usado para se comunicar com o serviço Registro de Dispositivos do Azure AD deve garantir que o TLS 1,2 esteja habilitado. Consulte [Gerenciando protocolos SSL/TLS e conjuntos de codificação para AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) para obter informações sobre como habilitar/verificar essa configuração.

## <a name="client-updates"></a>Atualizações do cliente

Como todas as combinações de servidor cliente e navegador-servidor devem usar o TLS 1,2 para se conectar com o serviço de Registro de Dispositivos do Azure AD, talvez seja necessário atualizar esses dispositivos.

Os clientes a seguir são conhecidos por não oferecer suporte a TLS 1,2. Atualize seus clientes para garantir o acesso ininterrupto.

- Android versão 4,3 e anterior
- Firefox versão 5,0 e anterior
- Internet Explorer versões 8-10 no Windows 7 e versões anteriores
- Internet Explorer 10 em Windows Phone 8,0
- Safari versão 6.0.4 no OS X 10.8.4 e versões anteriores

## <a name="next-steps"></a>Próximas etapas

[Visão geral de TLS/SSL (SSP do Schannel)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)