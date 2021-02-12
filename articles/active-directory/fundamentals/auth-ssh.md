---
title: Autenticação SSH com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de integração SSH com o Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b77ab0832fa19149c270d6ba5a6641069548cbe
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172713"
---
# <a name="ssh"></a>SSH  

O Secure Shell (SSH) é um protocolo de rede que fornece criptografia para serviços de rede operacional com segurança em uma rede não segura. O SSH também fornece uma entrada de linha de comando, executa comandos remotos e transfere arquivos com segurança. Normalmente, ele é usado em sistemas baseados em UNIX, como o Linux®. O SSH substitui o protocolo Telnet, que não fornece criptografia em uma rede não segura. 

O Azure Active Directory (AD do Azure) fornece uma extensão de VM (máquina virtual) para sistemas baseados em® Linux em execução no Azure. 

## <a name="use-when"></a>Usar quando 

* Trabalhando com VMs baseadas em Linux® que exigem entrada remota

* Executando comandos remotos em sistemas baseados em® Linux

* Transferir arquivos com segurança em uma rede não segura

![diagrama do Azure AD com o protocolo SSH](./media/authentication-patterns/ssh-auth.png)

SSH com o Azure AD

## <a name="components-of-system"></a>Componentes do sistema 

* **Usuário**: inicia o cliente SSH para configurar uma conexão com as VMs do Linux® e fornece credenciais para autenticação.

* **Navegador da Web**: o componente com o qual o usuário interage. Ele se comunica com o provedor de identidade (Azure AD) para autenticar e autorizar o usuário com segurança.

* **Cliente SSH**: orienta o processo de configuração de conexão.

* **Azure ad**: autentica a identidade do usuário usando o fluxo do dispositivo e emite um token para as VMs do Linux.

* **VM do Linux**: aceita o token e fornece conexão bem-sucedida.

## <a name="implement-ssh-with-azure-ad"></a>Implementar SSH com o Azure AD 

* [Fazer logon em uma VM do Linux® com credenciais de Azure Active Directory-máquinas virtuais do Azure ](../../virtual-machines/linux/login-using-aad.md) 

* [Fluxo de código de dispositivo OAuth 2,0-plataforma de identidade da Microsoft ](../develop/v2-oauth2-device-code.md)

* [Integrar com o Azure Active Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

