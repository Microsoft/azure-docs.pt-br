---
title: Desabilitar PTA ao usar Azure AD Connect "não configurar" | Microsoft Docs
description: Este artigo descreve como desabilitar o PTA com o recurso Azure AD Connect "não configurar".
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26112b1e799cbde3145e7137c686b4b336db4bab
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919928"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>Desabilitar PTA ao usar Azure AD Connect

Se você estiver usando a autenticação de passagem com Azure AD Connect e tiver definido como **"não configurar"**, poderá desabilitá-lo. 

>[!NOTE]
>Se você já tiver o PHS habilitado, desabilitar o PTA resultará no fallback do locatário para PHS.

A desabilitação de PTA pode ser feita usando os cmdlets a seguir. 

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos são necessários:
- Qualquer computador Windows que tenha o agente PTA instalado. 
- O agente deve estar na versão 1.5.1742.0 ou posterior. 
- Uma conta de administrador global do Azure para executar os cmdlets do PowerShell para desabilitar o PTA.

>[!NOTE]
> Se o agente for mais antigo, talvez ele não tenha os cmdlets necessários para concluir esta operação. Você pode obter um novo agente no portal do Azure para instalá-lo em qualquer computador Windows e fornecer credenciais de administrador. (A instalação do agente não afeta o status do PTA na nuvem)

> [!IMPORTANT]
> Se você estiver usando a nuvem do Azure governamental, terá que passar o parâmetro ENVIRONMENTname com o valor a seguir. 
>
>| Nome do ambiente | Nuvem |
>| - | - |
>| AzureUSGovernment | Gov dos EUA|


## <a name="to-disable-pta"></a>Para desabilitar o PTA
Em uma sessão do PowerShell, use o seguinte para desabilitar o PTA:
1. PS C:\Arquivos de Programas\microsoft Azure AD Connect agente de autenticação> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` ou `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` ou `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Se você não tiver acesso a um agente

Se você não tiver um computador agente, poderá usar o comando a seguir para instalar um agente.

1. Baixe o agente de autenticação mais recente de portal.azure.com.
2. Instalar o recurso: `.\AADConnectAuthAgentSetup.exe` ou `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Próximas etapas

- [Entrada do usuário com autenticação de passagem do Azure Active Directory](how-to-connect-pta.md)
