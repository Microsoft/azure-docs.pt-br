---
title: 'Azure Active Directory Connect: solucionar problemas do Logon Único Contínuo | Microsoft Docs'
description: Este tópico descreve como solucionar problemas do Logon Único Contínuo do Azure Active Directory
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eef58f6e84fb3b4dec947fa3614b6ec1043ff89e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644639"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Solucionar problemas do Logon Único Contínuo do Azure Active Directory

Este artigo ajuda você a localizar informações de solução de problemas comuns relacionados ao SSO Contínuo (Logon Único Contínuo) do Azure AD (Azure Active Directory).

## <a name="known-issues"></a>Problemas conhecidos

- Em alguns casos, habilitar o SSO contínuo pode levar até 30 minutos.
- Se você desabilitar e habilitar novamente o SSO Contínuo em seu locatário, os usuários não obterão a experiência de logon único até que seus tíquetes de Kerberos armazenados em cache, normalmente válidos por 10 horas, tenham se expirado.
- Se o SSO Contínuo for bem-sucedido, o usuário não terá a oportunidade de selecionar **Manter-me conectado**. Devido a esse comportamento, os [cenários de mapeamento do SharePoint e do onedrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) não funcionam.
- Microsoft 365 clientes Win32 (Outlook, Word, Excel e outros) com versões 16.0.8730. xxxx e superior têm suporte usando um fluxo não interativo. Não há suporte para outras versões; nessas versões, os usuários inserirão seus nomes de usuário, mas não senhas, para entrar. Para o OneDrive, você precisará ativar o [recurso de Configuração silenciosa do OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de logon silenciosa.
- O SSO Contínuo não funciona no modo de navegação particular no Firefox.
- O SSO contínuo não funciona no Internet Explorer quando o modo de Proteção Avançada está ativado.
- O SSO contínuo não funciona no modo de navegação particular no Microsoft Edge (Herdado).
- O SSO contínuo não funciona em navegadores de dispositivos móveis no iOS e no Android.
- Se um usuário fizer parte de muitos grupos no Active Directory, o tíquete Kerberos do usuário provavelmente será muito grande para processar e isso causará falha no SSO Contínuo. Solicitações de HTTPS do Azure AD podem ter cabeçalhos com um tamanho máximo de 50 KB; os tíquetes Kerberos precisam ser muito menores do que o limite para acomodar outros artefatos do Azure AD (tipicamente, 2 a 5 KB), como cookies. Nossa recomendação é reduzir as associações de grupo do usuário e tentar novamente.
- Se você estiver sincronizando 30 ou mais florestas do Active Directory, não será possível habilitar o SSO Contínuo usando o Azure AD Connect. Como alternativa, você poderá [habilitar manualmente](#manual-reset-of-the-feature) o recurso em seu locatário.
- Adicionar a URL de serviço do Azure AD ( `https://autologon.microsoftazuread-sso.com` ) à zona de sites confiáveis em vez da zona de intranet local *impede que os usuários entrem*.
- O SSO contínuo dá suporte aos tipos de criptografia AES256_HMAC_SHA1, AES128_HMAC_SHA1 e RC4_HMAC_MD5 para Kerberos. É recomendável que o tipo de criptografia para a conta AzureADSSOAcc $ seja definido como AES256_HMAC_SHA1 ou um dos tipos AES versus RC4 para segurança adicional. O tipo de criptografia é armazenado no atributo msDS-SupportedEncryptionTypes da conta no seu Active Directory.  Se o tipo de criptografia da conta AzureADSSOAcc $ for definido como RC4_HMAC_MD5 e você quiser alterá-lo para um dos tipos de criptografia AES, certifique-se de que você primeiro se sobreponha à chave de descriptografia do Kerberos da conta AzureADSSOAcc $, conforme explicado no [documento de perguntas frequentes](how-to-connect-sso-faq.md) sob a pergunta relevante; caso contrário, o SSO contínuo não ocorrerá.
-  Se você tiver mais de uma floresta com relação de confiança de floresta, habilitar o SSO em uma das florestas permitirá o SSO em todas as florestas confiáveis. Se você habilitar o SSO em uma floresta em que o SSO já está habilitado, receberá um erro informando que o SSO já está habilitado na floresta.

## <a name="check-status-of-feature"></a>Verificar o status do recurso

Verifique se o recurso SSO ainda está **Habilitado** em seu locatário. Você pode verificar o status acessando o painel **Azure AD Connect** no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory: painel do Azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Clique para ver todas as florestas do AD que foram habilitadas para SSO Contínuo.

![Centro de administração do Azure Active Directory: painel SSO Contínuo](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Motivos de falha de conexão no centro de administração do Azure Active Directory (é necessário uma licença Premium)

Se o locatário tiver uma licença do Azure AD Premium associada a ele, você também poderá analisar o [relatório de atividade de entrada](../reports-monitoring/concept-sign-ins.md) no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory: relatório Entradas](./media/tshoot-connect-sso/sso9.png)

Navegue até **Azure Active Directory**  >  **entradas** no [centro de administração do Azure Active Directory](https://aad.portal.azure.com/)e, em seguida, selecione uma atividade de entrada do usuário específico. Procure o campo **CÓDIGO DE ERRO DE LOGON**. Faça o mapeamento do valor desse campo até um motivo da falha e uma resolução usando a tabela a seguir:

|Código de erro de logon|Motivo da falha no logon|Resolução
| --- | --- | ---
| 81001 | O tíquete Kerberos do usuário é muito grande. | Reduza as associações de grupo do usuário e tente novamente.
| 81002 | Não é possível validar o tíquete Kerberos do usuário. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81003 | Não é possível validar o tíquete Kerberos do usuário. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81004 | Falha na tentativa de autenticação Kerberos. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81008 | Não é possível validar o tíquete Kerberos do usuário. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81009 | Não é possível validar o tíquete Kerberos do usuário. | Veja a [Lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81010 | O SSO Contínuo falhou porque o tíquete Kerberos do usuário expirou ou é inválido. | O usuário precisa entrar em um dispositivo ingressado no domínio dentro da rede corporativa.
| 81011 | Não foi possível localizar o objeto de usuário com base nas informações no tíquete Kerberos do usuário. | Use o Azure AD Connect para sincronizar as informações do usuário no Azure AD.
| 81012 | O usuário que está tentando entrar no Azure AD é diferente do usuário conectado no dispositivo. | O usuário precisa entrar em um dispositivo diferente.
| 81013 | Não foi possível localizar o objeto de usuário com base nas informações no tíquete Kerberos do usuário. |Use o Azure AD Connect para sincronizar as informações do usuário no Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de verificação de solução de problemas

Use a lista de verificação a seguir para solucionar problemas de SSO Contínuo:

- Verifique se o recurso de SSO Contínuo está habilitado no Azure AD Connect. Se você não puder habilitar o recurso (por exemplo, devido a uma porta bloqueada), verifique se você cumpriu com todos os [pré-requisitos](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites).
- Se você habilitou o [Ingresso no Azure AD](../devices/overview.md) e o SSO Contínuo em seu locatário, verifique se o problema não está com o Ingresso no Azure AD. O SSO por meio do Ingresso no Azure AD terá precedência sobre SSO Contínuo se o dispositivo estiver registrado no Azure AD e ingressado no domínio. Com o SSO por meio do Ingresso no Azure AD, o usuário verá um bloco de entrada com a informação "Conectado ao Windows".
- Verifique se a URL do Azure AD ( `https://autologon.microsoftazuread-sso.com` ) faz parte das configurações de zona da intranet do usuário.
- Certifique-se de que o dispositivo corporativo tenha ingressado no domínio do Active Directory. O dispositivo _não_ precisa ser [Ingressado no Azure AD](../devices/overview.md) para que o SSO Contínuo funcione.
- Certifique-se de que o usuário esteja conectado ao dispositivo por meio de uma conta de domínio do Active Directory.
- Verifique se a conta do usuário é de uma floresta do Active Directory na qual o SSO Contínuo foi configurado.
- Certifique-se de que o dispositivo esteja conectado à rede corporativa.
- Certifique-se de que a hora do dispositivo esteja sincronizada com a hora do Active Directory e dos Controladores de Domínio, e que tenham cinco minutos ou menos de diferença.
- Certifique-se de que a conta do computador `AZUREADSSOACC` está presente e habilitada em cada floresta do AD que você deseja que o SSO contínuo habilitado. Se a conta de computador tiver sido excluída ou estiver ausente, você poderá usar os [cmdlets do PowerShell](#manual-reset-of-the-feature) para recriá-las.
- Liste os tíquetes Kerberos existentes no dispositivo usando o comando `klist` em um prompt de comando. Certifique-se de que os tíquetes emitidos para a conta do computador `AZUREADSSOACC` estejam presentes. Os tíquetes Kerberos dos usuários são normalmente válidos durante 10 horas. Você pode ter configurações diferentes no Active Directory.
- Se você desabilitou e habilitou novamente o SSO Contínuo em seu locatário, os usuários não obterão a experiência de logon único até que seus tíquetes de Kerberos armazenados em cache tenham se expirado.
- Limpe os tíquetes Kerberos existentes do dispositivo usando o comando `klist purge` e tente novamente.
- Para determinar se há problemas relacionados a JavaScript, examine os logs do console do navegador (em **Ferramentas do Desenvolvedor**).
- Examine os [logs do controlador de domínio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Logs do controlador de domínio

Se a auditoria de êxito estiver habilitada em seu controlador de domínio, sempre que um usuário entrar usando SSO Contínuo, uma entrada de segurança será registrada no log de eventos. Encontre esses eventos de segurança usando a consulta a seguir. (Procure o evento **4769** associado à conta de computador **AzureADSSOAcc$**.)

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Redefinição manual do recurso

Se a solução de problemas não ajudar, você poderá redefinir manualmente o recurso em seu locatário. Execute estas etapas no servidor local em que você está executando o Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Etapa 1: importar o módulo do PowerShell de SSO Contínuo

1. Primeiro, baixe e instale o [PowerShell do Microsoft Azure AD](/powershell/azure/active-directory/overview).
2. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Etapa 2: Obter a lista de florestas do Active Directory em que o SSO Contínuo foi habilitado

1. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Quando solicitado, insira as suas credenciais de Administrador global do locatário.
2. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas do Active Directory (veja a lista “Domínios”) em que esse recurso foi habilitado.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Etapa 3: Desabilitar o SSO Contínuo para cada floresta do Active Directory onde você configurou o recurso

1. Chame `$creds = Get-Credential`. Quando solicitado, insira as credenciais de administrador de domínio da floresta do Active Directory pretendida.

   > [!NOTE]
   >O nome de usuário das credenciais do administrador de domínio deve ser inserido no formato de nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Usamos a parte de domínio do nome de usuário para localizar o controlador de domínio do administrador de domínio usando DNS.

   >[!NOTE]
   >A conta de administrador de domínio usada não deve ser um membro do grupo usuários protegidos. Nesse caso, a operação falhará.

2. Chame `Disable-AzureADSSOForest -OnPremCredentials $creds`. Este comando remove a conta do computador `AZUREADSSOACC` do controlador de domínio local dessa floresta do Active Directory específica.
3. Repita as etapas anteriores para cada floresta do Active Directory onde você configurou o recurso.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Etapa 4: Habilitar o SSO Contínuo para cada floresta do Active Directory

1. Chame `Enable-AzureADSSOForest`. Quando solicitado, insira as credenciais de administrador de domínio da floresta do Active Directory pretendida.

   > [!NOTE]
   >O nome de usuário das credenciais do administrador de domínio deve ser inserido no formato de nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Usamos a parte de domínio do nome de usuário para localizar o controlador de domínio do administrador de domínio usando DNS.

   >[!NOTE]
   >A conta de administrador de domínio usada não deve ser um membro do grupo usuários protegidos. Nesse caso, a operação falhará.

2. Repita as etapas anteriores para cada floresta do Active Directory onde você configurou o recurso.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Etapa 5. Habilitar o recurso em seu locatário

Para habilitar o recurso no locatário, chame `Enable-AzureADSSO -Enable $true`.
