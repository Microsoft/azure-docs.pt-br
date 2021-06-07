---
title: 'Azure AD Connect: Autenticação da nuvem via distribuição em etapas | Microsoft Docs'
description: Este artigo explica como migrar da autenticação federada para a autenticação na nuvem usando uma distribuição em etapas.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c7f3de20ea3e86e3b56dc71d698354f7eaf782d
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709711"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>Migrar para a autenticação de nuvem usando a distribuição em etapas (versão prévia)

A distribuição em etapas permite que você teste seletivamente grupos de usuários com recursos de autenticação na nuvem, como a MFA (autenticação multifator) do Azure AD, acesso condicional, proteção de identidade para credenciais vazadas, governança de identidade e outros, antes de cortar seus domínios.  Este artigo discute como fazer essa mudança. No entanto, antes de começar a distribuição em etapas, você deve considerar as implicações caso uma ou mais das seguintes condições sejam verdadeiras:
    
-  Atualmente, você está usando um servidor de Autenticação Multifator local. 
-  Você está usando cartões inteligentes para a autenticação. 
-  Seu servidor atual oferece determinados recursos do tipo somente federação.

Antes de experimentar esse recurso, sugerimos que você leia nosso guia sobre como escolher o método de autenticação certo. Para obter mais informações, consulte a tabela “Métodos de comparação” em [Escolher o método de autenticação correto para sua solução de identidade híbrida do Azure Active Directory](./choose-ad-authn.md#comparing-methods).

Para obter uma visão geral do recurso, veja este vídeo chamado “Azure Active Directory: o que é distribuição em etapas?” :

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Pré-requisitos

-   Você ter um locatário do Azure Active Directory (Azure AD) com domínios federados.

-   Você ter decidido mover para uma de duas opções:
    - **Opção A**  -  *sincronização de hash de senha (sincronização)*  +  *SSO (logon único) contínuo*.  Para obter mais informações, consulte [o que é a sincronização de hash de senha](whatis-phs.md) e [o que é o SSO contínuo](how-to-connect-sso.md)
    - **Opção B**  -  *autenticação*  +  de passagem *SSO contínuo*.  Para obter mais informações, consulte [o que é autenticação de passagem](how-to-connect-pta.md)  
    
    Ainda que o *SSO contínuo* seja opcional, recomendamos permitir que ele obtenha uma experiência de entrada silenciosa para usuários que estejam executando computadores ingressados no domínio de dentro de uma rede corporativa.

-   Você ter configurado todas as políticas apropriadas de identidade visual do locatário e de acesso condicional necessárias para os usuários que estão sendo migrados para a autenticação na nuvem.

-   Se você planeja usar a autenticação multifator do Azure AD, recomendamos o uso [de registro combinado para redefinição de senha de autoatendimento (SSPR) e autenticação multifator](../authentication/concept-registration-mfa-sspr-combined.md) para que os usuários registrem seus métodos de autenticação uma vez. Observação: ao usar SSPR para redefinir a senha ou alterar a senha usando a página MyProfile durante a distribuição em etapas, Azure AD Connect precisa sincronizar o novo hash de senha, que pode levar até 2 minutos após a redefinição.

-   Para usar o recurso de distribuição em etapas, você precisará ser um administrador global em seu locatário.

-   Para habilitar o *SSO contínuo* em uma floresta específica do Active Directory, você precisa ser um administrador de domínio.

-  Se você estiver implantando o Azure AD híbrido ou o ingresso no Azure AD, deverá atualizar para a atualização do Windows 10 1903.


## <a name="supported-scenarios"></a>Cenários com suporte

Os cenários a seguir têm suporte para distribuição em etapas. O recurso só funciona para:

- Usuários que são provisionados no Azure AD ao usarem o Azure AD Connect. Ele não se aplica a usuários do tipo somente nuvem.

- Tráfegos de entrada do usuário em navegadores e clientes de *autenticação moderna*. Aplicativos ou serviços de nuvem que usem autenticação herdada voltarão para os fluxos de autenticação federada. Como exemplos, podemos ter o Exchange Online com autenticação moderna desativada ou o Outlook 2010, o qual não tem suporte para autenticação moderna.

- Atualmente, o tamanho do grupo está limitado a 50.000 usuários.  Caso tenha grupos maiores que 50.000 usuários, é recomendável dividir esses grupos em vários grupos para a distribuição em etapas.

- A junção híbrida do Windows 10 ou o Azure AD ingressam no token de atualização principal sem linha de visão para o servidor de Federação para o Windows 10 versão 1903 e mais recente, quando o UPN do usuário é roteável e o sufixo do domínio é verificado no Azure AD.

## <a name="unsupported-scenarios"></a>Cenários sem suporte

Os cenários a seguir não têm suporte para distribuição em etapas.

- Não há suporte para autenticação herdada, como POP3 e SMTP.

- Alguns aplicativos enviam o parâmetro de consulta “domain_hint” para o Azure AD durante a autenticação. Esses fluxos continuarão, e os usuários que estiverem habilitados para a distribuição em etapas continuarão a usar a federação para autenticação.

<!-- -->

- Os administradores podem distribuir a autenticação na nuvem usando grupos de segurança. Para evitar a latência de sincronização quando estiver usando grupos de segurança locais do Active Directory, recomendamos que você use grupos de segurança de nuvem. As seguintes condições se aplicam:

    - Você pode usar um máximo de 10 grupos por recurso. Ou seja, você pode usar 10 grupos cada para *sincronização de hash de senha*, *autenticação de passagem* e *SSO contínuo*.
    - *Não há suporte* para grupos aninhados. Esse escopo se aplica também à visualização pública.
    - *Não há suporte* para grupos dinâmicos na distribuição em etapas.
    - Os objetos de contato dentro do grupo não permitirão que o grupo seja adicionado.

- Ao adicionar um grupo de segurança pela primeira vez para a distribuição em etapas, você tem um limite de 200 usuários para evitar um tempo limite de UX (experiência do usuário). Depois de adicionar o grupo, você poderá adicionar mais usuários diretamente a ele conforme necessário.

- Enquanto os usuários estiverem em distribuição em etapas, quando EnforceCloudPasswordPolicyForPasswordSyncedUsers estiver habilitado, a política de expiração de senha será definida como 90 dias sem a opção de personalizá-la. 

- Aquisição híbrida do Windows 10 ou ingresso no token de atualização principal do Azure AD para a versão do Windows 10 com mais de 1903. Esse cenário voltará ao ponto de extremidade WS-Trust do servidor de Federação, mesmo se o usuário entrar estiver no escopo da distribuição em etapas.

- Aquisição híbrida do Windows 10 ou ingresso no token de atualização principal do Azure AD para todas as versões, quando o UPN local do usuário não é roteável. Esse cenário voltará ao ponto de extremidade WS-Trust enquanto estiver no modo de distribuição em etapas, mas deixará de funcionar quando a migração preparada for concluída e o logon do usuário não depender mais do servidor de Federação.

  >[!NOTE]
  >Você ainda precisa fazer a transferência final de federado para a autenticação na nuvem usando o Azure AD Connect ou PowerShell. A distribuição em etapas não alterna domínios de federados para gerenciado.  Para obter mais informações sobre a transferência de domínio, consulte [migrar da Federação para a sincronização de hash de senha](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso) e [migrar da Federação para a autenticação de passagem](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
  
## <a name="get-started-with-staged-rollout"></a>Introdução à distribuição em etapas

Para testar a *sincronização de hash de senha*, entre usando a distribuição em etapas e siga as instruções de preparação da próxima seção.

Para obter informações sobre quais cmdlets do PowerShell devem ser usados, consulte [Versão prévia do Azure AD 2.0](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Preparação para a sincronização de hash de senha

1. Habilite a *sincronização de hash de senha* na página [recursos opcionais](how-to-connect-install-custom.md#optional-features) no Azure ad Connect. 

   ![Captura de tela da página “Recursos opcionais” no Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Verifique se um ciclo de *sincronização de hash de senha* completo foi executado para que todos os hashes de senha dos usuários tenham sido sincronizados com o Azure AD. Para verificar o status da *sincronização de hash de senha*, você pode usar o diagnóstico do PowerShell em [Solucionar problemas de sincronização de hash de senha com a sincronização do Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

   ![Captura de tela do log de Solução de problemas do AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Caso queira testar a *autenticação de passagem*, entre usando a distribuição em etapas e habilite-a seguindo as instruções de preparação da próxima seção.

## <a name="pre-work-for-pass-through-authentication"></a>Preparação para autenticação de passagem

1. Identifique um servidor que esteja executando o Windows Server 2012 R2 ou posterior no qual você deseje que o agente de *autenticação de passagem* seja executado. 

   *Não* escolha o servidor do Azure AD Connect.  Verifique se o servidor está ingressado no domínio, se ele pode autenticar usuários selecionados com o Active Directory e se ele consegue se comunicar com o Azure AD em URLs e portas de saída. Para obter mais informações, consulte a seção “Etapa 1: verificar pré-requisitos” do [Início Rápido: logon único contínuo do Azure AD](how-to-connect-sso-quick-start.md).

1. [Baixe o agente de autenticação do Azure AD Connect](https://aka.ms/getauthagent) e instale-o no servidor. 

1. Para habilitar a [alta disponibilidade](how-to-connect-sso-quick-start.md), instale agentes de autenticação adicionais em outros servidores.

1. Verifique se você definiu suas [Configurações do Smart Lock](../authentication/howto-password-smart-lockout.md) adequadamente. Isso ajuda a garantir que as contas locais do Active Directory dos seus usuários não serão bloqueadas por atores inadequados.

É recomendável habilitar o *SSO contínuo* independentemente do método de entrada (*sincronização de hash de senha* ou *autenticação de passagem*) selecionado para a distribuição em etapas. Para habilitar o *SSO contínuo*, siga as instruções de preparação na próxima seção.

## <a name="pre-work-for-seamless-sso"></a>Preparação para SSO contínuo

Habilite o *SSO contínuo* nas florestas de Active Directory usando o PowerShell. Se você tiver mais de uma floresta Active Directory, habilite-a para cada floresta individualmente. O *SSO contínuo* é disparado somente para usuários selecionados para distribuição em etapas. Ele não afeta sua configuração de federação existente.

Para habilitar o *SSO contínuo*, faça o seguinte:

1. Entre no servidor do Azure AD Connect.

2. Vá para a pasta *% ProgramFiles% \\ Microsoft Azure Active Directory Connect* .

3. Importe o módulo do PowerShell com *SSO contínuo* executando o seguinte comando: 

   `Import-Module .\AzureADSSO.psd1`

4. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando abre um painel no qual você pode inserir as credenciais de administrador global do seu locatário.

5. Chame `Get-AzureADSSOStatus | ConvertFrom-Json`. Esse comando exibe uma lista de florestas do Active Directory (veja a lista “Domínios”) no qual esse recurso foi habilitado. Por padrão, ele é definido como “false” no nível do locatário.

   ![Exemplo de saída do Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Chame `$creds = Get-Credential`. No prompt, insira as credenciais de administrador de domínio da floresta do Active Directory pretendida.

7. Chame `Enable-AzureADSSOForest -OnPremCredentials $creds`. Esse comando cria a conta de computador AZUREADSSOACC do controlador de domínio local para a floresta do Active Directory necessária para o *SSO contínuo*.

8. O *SSO contínuo* requer que as URLs estejam na zona da intranet. Para implantar essas URLs usando políticas de grupo, consulte [Início Rápido: logon único contínuo do Azure AD](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Para obter uma explicação completa, você também pode baixar nossos [planos de implantação](https://aka.ms/SeamlessSSODPDownload) para o *SSO contínuo*.

## <a name="enable-staged-rollout"></a>Habilitar a distribuição em etapas

Para distribuir um recurso específico (*autenticação de passagem*, *sincronização de hash de senha* ou *SSO contínuo*) para um conjunto selecionado de usuários em um grupo, siga as instruções das próximas seções.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Habilitar uma distribuição em etapas de um recurso específico no seu locatário

Você pode distribuir uma das seguintes opções:

- **Opção A** - *sincronização de hash de senha* + *SSO contínuo*
- **Opção B** - *autenticação de passagem* + *SSO contínuo*
- **Sem suporte** - *sincronização de hash de senha* + *autenticação de passagem* + *SSO contínuo*

Faça o seguinte:

1. Para acessar a UX de versão prévia, entre no [portal do Azure AD](https://aka.ms/stagedrolloutux).

2. Selecione o link **Habilitar a distribuição em etapas para a entrada de usuário gerenciado (versão prévia)** .

   Por exemplo, caso queira habilitar a *Opção a*, deslize os controles de **Sincronização de hash de senha** e **Logon único contínuo** para **Ativado**, conforme mostrado nas imagens a seguir.

   ![A página do Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![A página “Habilitar os recursos de distribuição em etapas (versão prévia)”](./media/how-to-connect-staged-rollout/sr5.png)

3. Adicione os grupos ao recurso para habilitar a *autenticação de passagem* e o *SSO contínuo*. Para evitar um tempo limite da UX, verifique se os grupos de segurança contêm no máximo 200 membros inicialmente.

   ![A página “Gerenciar grupos para a sincronização de hash de senha (versão prévia)”](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Os membros de um grupo são habilitados automaticamente para a distribuição em etapas. Não há suporte para grupos aninhados e dinâmicos na distribuição em etapas.
   >Ao adicionar um novo grupo, os usuários no grupo (até 200 usuários para um novo grupo) serão atualizados para usar a autenticação gerenciada imediatamente. Editar um grupo (adicionar ou remover usuários) pode levar até 24 horas para que as alterações entrem em vigor.
   >O SSO contínuo será aplicado somente se os usuários estiverem no grupo de SSO contínuo e também em um grupo PTA ou PHS.

## <a name="auditing"></a>Auditoria

Habilitamos eventos de auditoria para as várias ações que executamos para fazer a distribuição em etapas:

- Evento de auditoria ao habilitar uma distribuição em etapas para a *sincronização de hash de senha*, a *autenticação de passagem* ou o *SSO contínuo*.

  >[!NOTE]
  >Um evento de auditoria é registrado quando o *SSO contínuo* for ativado usando a distribuição em etapas.

  ![O painel “Criar política de distribuição para recurso” – guia Atividade](./media/how-to-connect-staged-rollout/sr7.png)

  ![O painel “Criar política de distribuição para recurso” – guia Propriedades Modificadas](./media/how-to-connect-staged-rollout/sr8.png)

- Evento de auditoria quando um grupo for adicionado à *sincronização de hash de senha*, à *autenticação de passagem* ou ao *SSO contínuo*.

  >[!NOTE]
  >Um evento de auditoria é registrado quando um grupo for adicionado à *sincronização de hash de senha* para a distribuição em etapas.

  ![O painel “Adicionar um grupo ao recurso de distribuição” – guia Atividade](./media/how-to-connect-staged-rollout/sr9.png)

  ![O painel “Adicionar um grupo ao recurso de distribuição” – guia Propriedades Modificadas](./media/how-to-connect-staged-rollout/sr10.png)

- Evento de auditoria quando um usuário que tenha sido adicionado ao grupo for habilitado para a distribuição em etapas.

  ![O painel “Adicionar usuário ao recurso de distribuição” – guia Atividade](media/how-to-connect-staged-rollout/sr11.png)

  ![O painel “Adicionar usuário ao recurso de distribuição” – guia Destino(s)](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validação

Para testar a entrada com a *sincronização de hash de senha* ou a *autenticação de passagem* (entrada com nome de usuário e senha), faça o seguinte:

1. Na extranet, vá para a [página Aplicativos](https://myapps.microsoft.com) em uma sessão privada do navegador e, em seguida, insira o UPN (UserPrincipalName) da conta de usuário que foi selecionada para a distribuição em etapas.

   Os usuários que foram direcionados para a distribuição em etapas não serão redirecionados para a sua página de logon federada. Em vez disso, eles são solicitados a entrar na página de entrada com a identidade visual do locatário do Azure AD.

1. Verifique se a entrada é exibida com êxito no [Relatório de atividade de entrada do Azure AD](../reports-monitoring/concept-sign-ins.md); para isso, filtre usando o UPN.

Para testar a entrada com o *SSO contínuo*:

1. Na intranet, vá para a [página Aplicativos](https://myapps.microsoft.com) em uma sessão privada do navegador e, em seguida, insira o UPN da conta de usuário que foi selecionada para a distribuição em etapas.

   Os usuários que foram direcionados para a distribuição em etapas do *SSO contínuo* são apresentados com uma “Tentativa de conectar você...” antes que eles entrem silenciosamente.

1. Verifique se a entrada é exibida com êxito no [Relatório de atividade de entrada do Azure AD](../reports-monitoring/concept-sign-ins.md); para isso, filtre usando o UPN.

   Para rastrear as entradas de usuário que ainda ocorrem nos AD FS (Serviços de Federação do Active Directory) para usuários de distribuição em etapas selecionadas, siga as instruções em [Solução de problemas dos AD FS: eventos e registrar em log](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Consulte a documentação do fornecedor sobre como verificar isso em provedores de federação de terceiros.

## <a name="remove-a-user-from-staged-rollout"></a>Remover um usuário da distribuição em etapas

Remover um usuário do grupo desabilita a distribuição em etapas desse usuário. Para desabilitar o recurso de distribuição em etapas, deslize o controle de volta para **Desativado**.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Posso usar esse recurso na produção?**

A: Sim, você pode usar esse recurso em seu locatário de produção, mas recomendamos que você o teste primeiro em seu locatário de teste.

**P: Esse recurso pode ser usado para manter uma “coexistência” permanente, na qual alguns usuários usam a autenticação federada e outros usam a autenticação de nuvem?**

R: não, esse recurso foi projetado para testar a autenticação na nuvem. Após o teste bem-sucedido de alguns grupos de usuários, você deve passar para a autenticação na nuvem. Não recomendamos usar um estado misto permanente, pois essa abordagem pode levar a fluxos de autenticação inesperados.

**P: Posso usar o PowerShell para executar a distribuição em etapas?**

A: Sim. Para saber como usar o PowerShell para executar a distribuição em etapas, consulte [Versão prévia do Azure AD](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="next-steps"></a>Próximas etapas
- [Versão prévia do Azure AD 2.0](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout )
- [Alterar o método de entrada para a sincronização de hash de senha](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
- [Alterar o método de entrada para autenticação de passagem](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
