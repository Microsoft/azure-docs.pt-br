---
title: Instalar o agente de provisionamento de nuvem Azure AD Connect usando o PowerShell
description: Saiba como instalar o agente de provisionamento de nuvem Azure AD Connect usando cmdlets do PowerShell.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613009"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Instalar o agente de provisionamento do Azure AD Connect usando cmdlets do PowerShell 
O guia a seguir mostra como instalar o agente de provisionamento do Azure AD Connect usando cmdlets do PowerShell.
 

## <a name="prerequisite"></a>Pré-requisito: 


>[!IMPORTANT]
>As seguintes instruções de instalação assumem que todos os [Pré-requisitos](how-to-prerequisites.md) foram atendidos.
>
> O Windows Server precisa ter o TLS 1,2 habilitado antes de instalar o agente de provisionamento do Azure AD Connect usando cmdlets do PowerShell. Para habilitar o TLS 1,2, você pode usar as etapas encontradas [aqui](how-to-prerequisites.md#tls-requirements).

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Instalar o agente de provisionamento do Azure AD Connect usando cmdlets do PowerShell 


 1. Entre no portal do Azure e, em seguida, vá para **Azure Active Directory**.
 2. No menu à esquerda, selecione **Azure ad Connect**.
 3. Selecione **Gerenciar aprovisionamento (versão prévia)**  > **Examinar todos os agentes**.
 4. Baixe o agente de provisionamento do Azure AD Connect do portal do Azure para um local.  

   ![Baixe o agente local](media/how-to-install/install-9.png)</br>
 5. Para fins dessas instruções, o agente foi baixado para a seguinte pasta: "C:\ProvisioningSetup". 
 6. Instalar o ProvisioningAgent no modo silencioso

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. Importar módulo PS do agente de provisionamento 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. Conectar-se ao AzureAD usando credenciais de administrador global, você pode personalizar esta seção para buscar a senha em um repositório seguro. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   $GlobalAdminCreds de Connect-AADCloudSyncAzureAD credenciais 

 9. Adicione a conta gMSA, forneça credenciais do administrador de domínio para criar a conta padrão do gMSA 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. Ou use o cmdlet acima como abaixo para fornecer uma conta gMSA criada previamente 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. Adicionar domínio 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. Ou use o cmdlet acima como abaixo para configurar os controladores de domínio preferenciais 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. Repita a etapa anterior para adicionar mais domínios, forneça os nomes de conta e os nomes de domínio dos respectivos domínios 
 14. Reinicie o serviço. 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Acesse o portal do Azure para criar a configuração de sincronização de nuvem.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Cmdlets do PowerShell gMSA do agente de provisionamento
Agora que você instalou o agente, você pode aplicar permissões mais granulares ao gMSA.  Confira [Azure ad Connect cmdlets do PowerShell gMSA do agente de provisionamento de nuvem](how-to-gmsa-cmdlets.md) para obter informações e instruções detalhadas sobre como configurar as permissões.

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [Azure AD Connect cmdlets do PowerShell gMSA do agente de provisionamento de nuvem](how-to-gmsa-cmdlets.md)
- [O que é Azure AD Connect a sincronização de nuvem?](what-is-cloud-sync.md)