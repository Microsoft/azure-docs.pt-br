---
title: 'Azure AD Connect: Logon Único Contínuo – perguntas frequentes | Microsoft Docs'
description: Respostas às perguntas frequentes sobre o Logon Único Contínuo do Azure Active Directory.
services: active-directory
keywords: o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dac4b461d4506015f0ef374eae37f67c445791d
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107864"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Logon Único Contínuo do Azure Active Directory: Perguntas frequentes

Neste artigo abordamos perguntas frequentes sobre o Logon Único Contínuo do Azure Active Directory (SSO contínuo). Continue verificando para ver novo conteúdo.

**P: Com quais métodos de entrada o SSO Contínuo funcionam**

O SSO Contínuo pode ser combinado com o método de entrada de [Sincronização de Hash de Senha](how-to-connect-password-hash-synchronization.md) ou de [Autenticação de Passagem](how-to-connect-pta.md). No entanto esse recurso não pode ser usado com os Serviços de Federação do Active Directory (ADFS).

**P: O SSO Contínuo é um recurso gratuito?**

O SSO Contínuo é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.

**P: O SSO Contínuo está disponível na [nuvem do Microsoft Azure Alemanha](https://www.microsoft.de/cloud-deutschland) e na [nuvem do Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?**

O SSO contínuo está disponível para a nuvem do Azure Government. Para detalhes, confira [Considerações de identidade híbrida para o Azure Government](./reference-connect-government-cloud.md).

**P: Quais aplicativos aproveitam a capacidade do parâmetro `domain_hint` ou `login_hint` do SSO Contínuo?**

Veja abaixo uma lista parcial de aplicativos que enviam esses parâmetros para o Microsoft Azure Active Directory e, portanto, fornecem aos usuários uma experiência de logon silenciosa usando o SSO contínuo (isto é, os usuários não precisam inserir seus nomes de usuário ou senha):

| Nome do aplicativo | URL do aplicativo a ser usada |
| -- | -- |
| Painel de acesso | https:\//myapps.microsoft.com/contoso.com |
| Outlook na Web | https:\//outlook.office365.com/contoso.com |
| Portais do Office 365 | https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

Além disso, os usuários terão uma experiência de logon silenciosa se um aplicativo enviar solicitações de entrada para pontos de extremidade do Azure AD configurados como locatários, ou seja, https:\//login.microsoftonline.com/contoso.com/<..> ou https:\//login.microsoftonline.com/<tenant_ID>/<..> – em vez do ponto de extremidade comum do Azure AD – ou seja, https:\//login.microsoftonline.com/common/<...>. Abaixo está uma lista parcial de aplicativos que fazem esses tipos de solicitações de entrada.

| Nome do aplicativo | URL do aplicativo a ser usada |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Portal do Azure | https:\//portal.azure.com/contoso.com |

Nas tabelas acima, substitua "contoso.com" por seu nome de domínio para obter as URLs do aplicativo certo para o seu locatário.

Se desejar que outros aplicativos usem nossa experiência de logon silenciosa, informe isso na seção de comentários.

**P: O SSO Contínuo dá suporte ao `Alternate ID` como o nome de usuário, em vez de `userPrincipalName`?**

Sim. O SSO Contínuo dá suporte ao `Alternate ID` como o nome de usuário quando configurado no Azure AD Connect, conforme mostrado [aqui](how-to-connect-install-custom.md). Não há suporte para todos os aplicativos Microsoft 365 `Alternate ID` . Consulte a documentação específica do aplicativo para obter o demonstrativo de suporte.

**P: Qual é a diferença entre a experiência de logon único fornecida pelo [Ingresso no Azure AD](../devices/overview.md) e o SSO Contínuo?**

O [Ingresso no Azure AD](../devices/overview.md) fornece o SSO aos usuários se os dispositivos deles estiverem registrados no Azure AD. Esses dispositivos não precisam, necessariamente, ser ingressados no domínio. O SSO é fornecido com o uso de *tokens de atualização primários* ou *PRTs* e não Kerberos. A experiência do usuário é melhor em dispositivos Windows 10. SSO acontece automaticamente no navegador Microsoft Edge. Ele também funciona no Chrome com o uso de uma extensão de navegador.

Você pode usar tanto o Ingresso no Azure AD quanto o SSO Contínuo em seu locatário. Esses dois recursos são complementares. Se os dois recursos forem ativados, o SSO do Ingresso no Azure AD terá precedência sobre o SSO Contínuo.

**P: Desejo registrar dispositivos que não sejam Windows 10 com o Azure AD, sem o uso do AD FS. Posso usar o SSO Contínuo em vez disso?**

Sim, esse cenário precisa da versão 2.1 ou posterior do [cliente de ingresso no local de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

**P: Como posso sobrepor a chave de descriptografia de Kerberos a `AZUREADSSO` conta de computador?**

É importante sobrepor frequentemente a chave de descriptografia de Kerberos a `AZUREADSSO` conta de computador (que representa o AD do Azure) criada na floresta do AD do seu local.

>[!IMPORTANT]
>É altamente recomendável sobrepor a chave de descriptografia do Kerberos pelo menos a cada 30 dias.

Siga estas etapas no servidor local em que você está executando o Azure AD Connect:

   > [!NOTE]
   >Você precisará de ambas as credenciais de administrador de domínio e de administrador global para as etapas abaixo.
   >Se você não for um administrador de domínio e tiver recebido permissões pelo administrador de domínio, deverá chamar `Update-AzureADSSOForest -OnPremCredentials $creds -PreserveCustomPermissionsOnDesktopSsoAccount`

   **Etapa 1. Obter lista de florestas do AD em que o SSO Contínuo foi habilitado**

   1. Primeiro, baixe e instale o [PowerShell do Microsoft Azure AD](/powershell/azure/active-directory/overview).
   2. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
   4. Execute o PowerShell como um Administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir suas credenciais de Administrador Global do locatário.
   5. Chame `Get-AzureADSSOStatus | ConvertFrom-Json`. Esse comando fornece a lista de florestas do AD (consulte a lista “Domínios”) em que esse recurso foi habilitado.

   **Etapa 2. Atualizar a chave de descriptografia de Kerberos em cada floresta do AD que foi configurado**

   1. Chame `$creds = Get-Credential`. Quando solicitado, insira as credenciais de Administrador de Domínio da floresta do AD pretendida.

   > [!NOTE]
   >O nome de usuário das credenciais do administrador de domínio deve ser inserido no formato de nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Usamos a parte de domínio do nome de usuário para localizar o controlador de domínio do administrador de domínio usando DNS.

   >[!NOTE]
   >A conta de administrador de domínio usada não deve ser um membro do grupo usuários protegidos. Nesse caso, a operação falhará.

   2. Chame `Update-AzureADSSOForest -OnPremCredentials $creds`. Esse comando atualiza a chave de descriptografia do Kerberos para a `AZUREADSSO` conta de computador nessa floresta do AD específico e a atualiza no AD do Azure.
   
   3. Repita as etapas anteriores para cada floresta do AD em que você configurou o recurso.
   
  >[!NOTE]
   >Se você estiver atualizando uma floresta, que não seja a Azure AD Connect uma, verifique se a conectividade com o servidor de catálogo global (TCP 3268 e TCP 3269) está disponível.

   >[!IMPORTANT]
   >Certifique-se de _não_ executar o `Update-AzureADSSOForest` comando mais de uma vez. Caso contrário, o recurso deixará de funcionar até o momento em que os tíquetes Kerberos dos usuários expirarem e forem reemitidos pelo Active Directory local.

**P: Como faço para desabilitar o SSO Contínuo?**

   **Etapa 1. Desabilitar o recurso no locatário**

   **Opção A: desabilitar usando o Azure AD Connect**
    
   1. Execute o Azure AD Connect, escolha **Alterar página de entrada do usuário** e clique em **Avançar**.
   2. Desmarque a opção **Habilitar logon único**. Continue com o assistente.

   Após a conclusão do assistente, o SSO Contínuo é desabilitado no locatário. No entanto, uma mensagem será exibida na tela, informando o seguinte:

   “O logon único agora está desabilitado, mas há etapas manuais adicionais a serem realizadas para concluir a limpeza. Saiba mais”

   Para concluir o processo de limpeza, siga as etapas 2 e 3 no servidor local em que você está executando o Azure AD Connect.

   **Opção B: desabilitar usando o PowerShell**

   Siga estas etapas no servidor local em que o Azure AD Connect está sendo executado:

   1. Primeiro, baixe e instale o [PowerShell do Microsoft Azure AD](/powershell/azure/active-directory/overview).
   2. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
   4. Execute o PowerShell como um Administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir suas credenciais de Administrador Global do locatário.
   5. Chame `Enable-AzureADSSO -Enable $false`.
   
   Neste ponto, o SSO contínuo está desabilitado, mas os domínios permanecerão configurados no caso de você desejar habilitar o SSO contínuo de volta. Se você quiser remover completamente os domínios da configuração de SSO contínuo, chame o seguinte cmdlet depois de concluir a etapa 5 acima: `Disable-AzureADSSOForest -DomainFqdn <fqdn>` .

   >[!IMPORTANT]
   >Desabilitar o SSO Contínuo usando o PowerShell não alterará o estado no Azure AD Connect. O SSO Contínuo aparecerá como habilitado na página **Alterar entrada do usuário**.

   **Etapa 2. Obter lista de florestas do AD em que o SSO Contínuo foi habilitado**

   Siga as tarefas de 1 a 4 abaixo, se você desabilitou o SSO Contínuo usando o Azure AD Connect. Se você desabilitou o SSO Contínuo usando o PowerShell, vá para a tarefa 5 abaixo.

   1. Primeiro, baixe e instale o [PowerShell do Microsoft Azure AD](/powershell/azure/active-directory/overview).
   2. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
   4. Execute o PowerShell como um Administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir suas credenciais de Administrador Global do locatário.
   5. Chame `Get-AzureADSSOStatus | ConvertFrom-Json`. Esse comando fornece a lista de florestas do AD (consulte a lista “Domínios”) em que esse recurso foi habilitado.

   **Etapa 3. Exclua manualmente a conta do computador `AZUREADSSO` de cada floresta do AD que você vir listada.**

## <a name="next-steps"></a>Próximas etapas

- [**Início Rápido**](how-to-connect-sso-quick-start.md) – colocar o SSO Contínuo do Azure AD em funcionamento.
- [**Aprofundamento técnico**](how-to-connect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Solução de problemas**](tshoot-connect-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
