---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 6d95e40623f17a39145778a2fc067dccc68fd872
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95561810"
---
## <a name="steps-to-enable-single-sign-on"></a>Etapas para habilitar o logon único
O provisionamento de nuvem funciona com o logon único.  No momento, não há uma opção para habilitar o SSO quando o agente é instalado, no entanto, você pode usar as etapas abaixo para habilitar o SSO e usá-lo. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Etapa 1: baixar e extrair Azure AD Connect arquivos
1.  Primeiro, baixe a versão mais recente do [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Abra um prompt de comando usando privilégios administrativos e navegue até o MSI que você acabou de baixar.
3.  Execute o seguinte:  `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Altere FilePath e extractfolder para corresponder ao caminho do arquivo e ao nome da sua pasta de extração.  O conteúdo agora deve estar na pasta de extração.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Etapa 2: importar o módulo do PowerShell de SSO contínuo

1. Baixe e instale o [PowerShell do Azure ad](/powershell/azure/active-directory/overview).
2. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Etapa 3: obter a lista de florestas de Active Directory nas quais o SSO contínuo foi habilitado

1. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Quando solicitado, insira as suas credenciais de Administrador global do locatário.
2. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas do Active Directory (veja a lista “Domínios”) em que esse recurso foi habilitado.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Etapa 4: Habilitar o SSO Contínuo para cada floresta do Active Directory

1. Chame `Enable-AzureADSSOForest`. Quando solicitado, insira as credenciais de administrador de domínio da floresta do Active Directory pretendida.

   > [!NOTE]
   >O nome de usuário das credenciais do administrador de domínio deve ser inserido no formato de nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Usamos a parte de domínio do nome de usuário para localizar o controlador de domínio do administrador de domínio usando DNS.

   >[!NOTE]
   >A conta de administrador de domínio usada não deve ser um membro do grupo usuários protegidos. Nesse caso, a operação falhará.

2. Repita as etapas anteriores para cada floresta do Active Directory onde você configurou o recurso.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Etapa 5. Habilitar o recurso em seu locatário

Para habilitar o recurso no locatário, chame `Enable-AzureADSSO -Enable $true`.