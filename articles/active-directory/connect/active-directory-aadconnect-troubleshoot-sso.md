---
title: "Azure AD Connect: solucionar problemas do Logon Único Contínuo | Microsoft Docs"
description: "Este tópico descreve como solucionar problemas do SSO Contínuo do Azure AD (Logon Único Contínuo do Azure Active Directory)."
services: active-directory
keywords: "o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2017
ms.author: billmath
ms.openlocfilehash: 7eea3621a52bf13dc44e89c342c503905ff24a0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Solucionar problemas do Logon Único Contínuo do Azure Active Directory

Este artigo ajuda você a localizar informações de solução de problemas comuns relacionados ao Logon Único Contínuo do Azure AD.

## <a name="known-issues"></a>Problemas conhecidos

- Em alguns casos, habilitar o SSO contínuo pode levar até 30 minutos.
- O suporte ao navegador Edge não está disponível.
- Ativação de licença em clientes do Office, especialmente em situações de computador compartilhado, gera prompts de entrada adicionais para usuários.
- O SSO Contínuo não funciona no modo de navegação particular no Firefox. e 
- O SSO contínuo não funciona no Internet Explorer quando o modo de Proteção Avançada está ativado.
- O SSO contínuo não funciona em navegadores de dispositivos móveis no iOS e no Android.
- Se você estiver sincronizando 30 ou mais florestas do AD, não será possível habilitar o SSO Contínuo usando o Azure AD Connect. Como alternativa, você poderá [habilitar manualmente](#manual-reset-of-azure-ad-seamless-sso) o recurso em seu locatário.
- Adicionar URLs do serviço Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) à zona "Sites confiáveis" em vez da zona "Intranet Local" **bloqueia a entrada dos usuários**.

## <a name="check-status-of-the-feature"></a>Verificar o status do recurso

Verifique se o recurso SSO ainda está **Habilitado** em seu locatário. Você pode verificar o status indo até a folha **Azure AD Connect** no [centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory - folha Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Motivos de falha de conexão no centro de administração do Azure Active Directory (é necessário licença Premium)

Se o locatário tiver uma licença do Azure AD Premium associada a ele, você também poderá analisar o [relatório de atividade de entrada](../active-directory-reporting-activity-sign-ins.md) no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory - relatório Entradas](./media/active-directory-aadconnect-sso/sso9.png)

Navegue até **Azure Active Directory** -> **Entradas** no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) e clique na atividade de entrada de um usuário específico. Procure o campo **CÓDIGO DE ERRO DE LOGON**. Faça o mapeamento do valor desse campo até um motivo da falha e uma resolução usando a tabela a seguir:

|Código de erro de logon|Motivo da falha no logon|Resolução
| --- | --- | ---
| 81001 | O tíquete Kerberos do usuário é muito grande. | Reduza as associações de grupo do usuário e tente novamente.
| 81002 | Não é possível validar o tíquete Kerberos do usuário. | Consulte a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81003 | Não é possível validar o tíquete Kerberos do usuário. | Consulte a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81004 | Falha na tentativa de autenticação Kerberos. | Consulte a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81008 | Não é possível validar o tíquete Kerberos do usuário. | Consulte a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81009 | "Não é possível validar o tíquete Kerberos do usuário. | Consulte a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81010 | O SSO Contínuo falhou porque o tíquete Kerberos do usuário expirou ou é inválido. | O usuário precisa entrar em um dispositivo ingressado no domínio dentro da rede corporativa.
| 81011 | Não foi possível localizar o objeto de usuário com base nas informações no tíquete Kerberos do usuário. | Use o Azure AD Connect para sincronizar as informações do usuário no Azure AD.
| 81012 | O usuário que está tentando entrar no Azure AD é diferente do usuário conectado no dispositivo. | Entre em um dispositivo diferente.
| 81013 | Não foi possível localizar o objeto de usuário com base nas informações no tíquete Kerberos do usuário. |Use o Azure AD Connect para sincronizar as informações do usuário no Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de verificação de solução de problemas

Use a lista de verificação a seguir para solucionar problemas de SSO Contínuo:

- Verifique se o recurso de SSO Contínuo está habilitado no Azure AD Connect. Se você não puder habilitar o recurso (por exemplo, devido a uma porta bloqueada), verifique se você cumpriu com todos os [pré-requisitos](active-directory-aadconnect-sso-quick-start.md#step-1-check-prerequisites).
- Verifique se ambas as URLs do Azure AD (https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net) fazem parte das configurações da zona de Intranet do usuário.
- Certifique-se de que o dispositivo corporativo seja ingressado no domínio do AD.
- Certifique-se de que o usuário faça logon no dispositivo usando uma conta de domínio do AD.
- Verifique se a conta do usuário é de uma floresta do AD na qual o SSO Contínuo foi configurado.
- Certifique-se de que o dispositivo esteja conectado à rede corporativa.
- Certifique-se de que a hora do dispositivo esteja sincronizada com a hora do Active Directory e a dos Controladores de Domínio e que tenham cinco minutos ou menos de diferença entre si.
- Liste os tíquetes Kerberos existentes no dispositivo usando o comando **klist** em um prompt de comando. Verifique se os tíquetes emitidos para a conta do computador `AZUREADSSOACCT` estão presentes. Os tíquetes Kerberos dos usuários são normalmente válidos durante 12 horas. Você pode ter configurações diferentes no seu Active Directory.
- Limpe os tíquetes Kerberos existentes do dispositivo usando o comando **klist purge** e tente novamente.
- Para determinar se há problemas relacionados a JavaScript, examine os logs do console do navegador (em "Ferramentas do Desenvolvedor").
- Examine também os [logs do controlador de domínio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Logs do controlador de domínio

Se a auditoria de êxito está habilitada no seu Controlador de Domínio, sempre que um usuário entra usando SSO Contínuo, uma entrada de segurança é registrada no log de eventos. Você pode encontrar esses eventos de segurança usando a seguinte consulta (procure o evento **4769** associado à conta do computador **AzureADSSOAcc$**):

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Redefinição manual do recurso

Se a solução de problemas não ajudar, você poderá redefinir manualmente o recurso em seu locatário. Siga estas etapas no servidor local em que você está executando o Azure AD Connect:

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Etapa 1: importar o módulo do PowerShell de SSO Contínuo

1. Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Em seguida, baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>Etapa 2: obter a lista de florestas do AD em que o SSO Contínuo foi habilitado

1. Execute o PowerShell como Administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Quando solicitado, insira as suas credenciais de Administrador global do locatário.
2. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas do AD (consulte a lista “Domínios”) em que esse recurso foi habilitado.

### <a name="step-3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>Etapa 3: desabilitar o SSO Contínuo para cada floresta do AD em que ele foi configurado

1. Chame `$creds = Get-Credential`. Quando solicitado, insira as credenciais de Administrador de Domínio da floresta do AD pretendida.
2. Chame `Disable-AzureADSSOForest -OnPremCredentials $creds`. Este comando remove a conta do computador `AZUREADSSOACCT` do Controlador de Domínio local dessa floresta do AD específica.
3. Repita as etapas anteriores para cada floresta do AD em que você configurou o recurso.

### <a name="step-4-enable-seamless-sso-for-each-ad-forest"></a>Etapa 4: habilitar o SSO Contínuo para cada floresta do AD

1. Chame `Enable-AzureADSSOForest`. Quando solicitado, insira as credenciais de Administrador de Domínio da floresta do AD pretendida.
2. Repita as etapas anteriores para cada floresta do AD em que você deseja configurar o recurso.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Etapa 5. Habilitar o recurso em seu locatário

Chame `Enable-AzureADSSO` e digite "true" no prompt `Enable: ` para ativar o recurso em seu locatário.
