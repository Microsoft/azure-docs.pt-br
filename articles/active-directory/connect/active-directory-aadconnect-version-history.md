---
title: "Azure AD Connect: histórico de liberação de versão | Microsoft Docs"
description: "Este artigo lista todas as versões do Azure AD Connect e do Azure AD Sync"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2017
ms.author: billmath
ms.openlocfilehash: 6e526e10ac5e3307aeefcdd22840a3e6a6ec843d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: histórico de lançamento de versão
A equipe do Azure AD (Azure Active Directory) atualiza regularmente o Azure AD Connect com novos recursos e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.

Este artigo foi projetado para ajudá-lo a controlar as versões que foram lançadas e compreender se você precisa atualizar para a versão mais recente ou não.

>[!IMPORTANT]
>Começando com o build 1.1.484, o Azure AD Connect introduziu um bug de regressão que requer permissões de sysadmin para atualizar o banco de dados SQL.  Esse bug ainda está presente no build mais recente, 1.1.614.  Se você estiver atualizando para esse build, precisará de permissões de sysadmin.  Permissões de DBO não são suficientes.  Se você tentar atualizar a conexão do Azure AD sem ter permissões de sysadmin, a atualização falhará e o Azure AD Connect deixará de funcionar corretamente depois.  A Microsoft está ciente desse problema e está trabalhando para corrigi-lo.

Esta é uma lista de tópicos relacionados:


Tópico |  Detalhes
--------- | --------- |
Etapas para atualizar do Azure AD Connect | Métodos diferentes para [atualizar de uma versão anterior para a versão mais recente](active-directory-aadconnect-upgrade-previous-version.md) do Azure AD Connect.
Permissões necessárias | Para obter permissões necessárias para aplicar uma atualização, veja [contas e permissões](./active-directory-aadconnect-accounts-permissions.md#upgrade).
Baixar| [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="116140"></a>1.1.614.0
Status: 5 de setembro de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Problemas conhecidos
* Há um problema conhecido com a atualização do Azure AD Connect que está afetando os clientes que habilitaram o [Logon Único Contínuo](active-directory-aadconnect-sso.md). Após a atualização do Azure AD Connect, o recurso é exibido como desabilitado no assistente, mesmo que na realidade o recurso permaneça habilitado. Uma correção para esse problema será fornecida em futuras versões. Os clientes que se preocupam com esse problema de exibição podem corrigi-lo manualmente habilitando o Logon Único Contínuo no assistente.

#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigido um problema que impeda que o Azure AD Connect atualizasse as regras de declarações no ADFS local, habilitando o recurso [msDS-ConsistencyGuid as Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor). O problema ocorre se você tentar habilitar o recurso para uma implantação existente do Azure AD Connect com ADFS configurado como o método de entrada. O problema ocorre porque o assistente não solicita as credenciais do ADFS antes de tentar atualizar as regras de declarações no ADFS.
* Corrigido um problema que causava falha na instalação do Azure AD Connect se a floresta do AD local estava com o NTLM desabilitado. O problema era devido ao assistente do Azure AD Connect não fornecer credenciais totalmente qualificadas ao criar os contextos de segurança necessários para a autenticação Kerberos. Isso faz com que a autenticação Kerberos falhe e que o assistente do Azure AD Connect volte a usar o NTLM.

### <a name="azure-ad-connect-sync"></a>Sincronização do Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigido um problema em que a nova regra de sincronização não pode ser criada se o atributo Tag não está populado.
* Corrigido um problema que fazia com que o Azure AD Connect se conectasse ao AD local para a sincronização de senha usando NTLM, mesmo que o Kerberos estivesse disponível. Esse problema ocorria se a topologia do AD local tinha um ou mais controladores de domínio restaurados de um backup.
* Corrigido um problema que fazia com que as etapas de sincronização completa ocorressem desnecessariamente após a atualização. Em geral, a execução das etapas de sincronização completa é necessária após a atualização se houver alterações em regras de sincronização para uso imediato. O problema ocorreu devido a um erro na lógica de detecção de alteração que detectava incorretamente uma alteração ao encontrar a expressão de regra de sincronização com caracteres de nova linha. Os caracteres de nova linha são inseridos na expressão de regra de sincronização para melhorar a legibilidade.
* Corrigido um problema que pode fazer com que o servidor do Azure AD Connect não funcione corretamente após a Atualização Automática. Esse problema afeta os servidores do Azure AD Connect com a versão 1.1.443.0 (ou anterior). Para obter detalhes sobre o problema, consulte o artigo [O Azure AD Connect não está funcionando corretamente após uma atualização automática](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Corrigido um problema que pode fazer com que a atualização automática seja repetida a cada 5 minutos quando erros são encontrados. Com a correção, a Atualização Automática faz novas tentativas com retirada exponencial quando são encontrados erros.
* Corrigido um problema em que o evento de sincronização de senha 611 é mostrado incorretamente nos logs de eventos de aplicativo do Windows como **informativo** em vez de **erro**. O evento 611 é gerado sempre que a sincronização de senha encontra um problema. 
* Corrigido um problema no assistente do Azure AD Connect que permite que o recurso de write-back de grupo seja habilitado sem selecionar uma unidade organizacional necessária para write-back de grupo.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Adicionada uma tarefa de solução de problemas para o assistente do Azure AD Connect em Tarefas Adicionais. Os clientes podem aproveitar essa tarefa para solucionar problemas relacionados à sincronização de senha e coletar diagnósticos em geral. No futuro, a tarefa de Solução de Problemas será estendida para incluir outros problemas relacionados à sincronização de diretório.
* O Azure AD Connect agora dá suporte a um novo modo de instalação chamado **Usar Banco de Dados Existente**. Esse modo de instalação permite que os clientes instalem o Azure AD Connect, que especifica um banco de dados ADSync. Para obter mais informações sobre esse recurso, consulte o artigo [Usar um banco de dados existente](active-directory-aadconnect-existing-database.md).
* Para maior segurança, o Azure AD Connect agora assume como padrão o uso do TLS 1.2 para se conectar ao Azure AD para sincronização de diretório. Anteriormente, o padrão era o TLS 1.0.
* Quando o Agente de Sincronização de Senha do Azure AD Connect é iniciado, ele tenta se conectar a um ponto de extremidade bem conhecido do Azure AD para sincronização de senha. Após uma conexão bem-sucedida, ele é redirecionado para um ponto de extremidade específico da região. Anteriormente, o Agente de Sincronização de Senha armazenava em cache o ponto de extremidade específico da região até que ele fosse reiniciado. Agora, o agente limpa o cache e faz uma nova tentativa com o ponto de extremidade conhecido quando encontra um problema de conexão com o ponto de extremidade específico da região. Essa alteração assegura que a sincronização de senha possa fazer failover para um ponto de extremidade específico da região diferente quando o ponto de extremidade específico da região em cache não está mais disponível.
* Para sincronizar as alterações de uma floresta do AD local, uma conta do AD DS é necessária. Você pode (i) criar a conta do AD DS você mesmo e fornecer a credencial dela para o Azure AD Connect ou (ii) fornecer uma credencial de admin corporativo e permitir que o Azure AD Connect crie a conta do AD DS para você. Anteriormente, (i) era a opção padrão no assistente do Azure AD Connect. Agora, (ii) é a opção padrão.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Adicionado suporte para a Nuvem do Microsoft Azure Governamental e Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>Gerenciamento dos AD FS
#### <a name="fixed-issues"></a>Problemas corrigidos
* O cmdlet Initialize-ADSyncNGCKeysWriteBack no módulo do PowerShell de preparação do AD estava realizando ACL no contêiner do registro de dispositivo incorretamente em lista de acesso e, portanto, herdaria somente as permissões existentes.  Isso foi atualizado para que a conta de serviço de sincronização tenha as permissões corretas.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* A tarefa Verificar Logon do ADFS do AAD Connect foi atualizada para que ele verifique logons em relação ao Microsoft Online e não apenas a recuperação de token do ADFS.
* Ao configurar um novo farm de AD FS usando o AAD Connect, a página solicitando credenciais do AD FS foi movida para que isso agora ocorra antes que seja solicitado ao usuário que forneça servidores ADFS e WAP.  Isso permite que o AAD Connect verifique se a conta especificada tem as permissões corretas.
* Durante a atualização do AAD Connect, não teremos mais falha em uma atualização se a relação de confiança do AAD do ADFS falhar em atualizar.  Se isso acontecer, será mostrada uma mensagem de aviso apropriada ao usuário e ele deverá prosseguir para redefinir a relação de confiança por meio da tarefa adicional do AAD Connect.

### <a name="seamless-single-sign-on"></a>Logon Único Contínuo
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigido um problema que fazia com que o assistente do Azure AD Connect retornasse um erro se você tentasse habilitar o [Logon Único Contínuo](active-directory-aadconnect-sso.md). A mensagem de erro é *"Falha na configuração do Agente de Autenticação do Microsoft Azure AD Connect".* Esse problema afetava clientes existentes que tinham atualizado manualmente para a versão prévia dos Agentes de Autenticação para [Autenticação de Passagem](active-directory-aadconnect-sso.md) com base nas etapas descritas neste [artigo](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Status: 23 de julho de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema corrigido

* Foi corrigido um problema que fazia com que a regra de sincronização integrada "Out to AD – User ImmutableId" fosse removida:

  * O problema ocorre quando o Azure AD Connect é atualizado ou quando a opção de tarefa *Atualizar configuração de sincronização* no assistente do Azure AD Connect é usada para atualizar a configuração da sincronização do Azure AD Connect.
  
  * Essa regra de sincronização é aplicável aos clientes que habilitaram o [msDS-ConsistencyGuid como recurso de âncora de origem](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). Esse recurso foi introduzido na versão 1.1.524.0 e posteriores. Quando a regra de sincronização é removida, o Azure AD Connect não consegue mais preencher o atributo ms-DS-ConsistencyGuid do AD local com o valor do atributo ObjectGuid. Isso não impede que novos usuários sejam provisionados no Azure AD.
  
  * A correção garante que a regra de sincronização não seja mais removida durante a atualização ou durante a alteração de configuração, desde que o recurso esteja habilitado. Para clientes existentes que tenham sido afetados por esse problema, a correção também garantirá que a regra de sincronização seja adicionada novamente após a atualização para esta versão do Azure AD Connect.

* Foi corrigido um problema que fazia com que as regras de sincronização integradas tivessem o valor de precedência menores que 100:

  * Em geral, os valores de precedência de 0 a 99 são reservados para regras de sincronização personalizadas. Durante a atualização, os valores de precedência para regras de sincronização integradas são atualizados para acomodar as alterações de regra de sincronização. Devido a esse problema, as regras de sincronização integradas podem ser atribuídas com valores de precedência que sejam menores que 100.
  
  * A correção impede que o problema ocorra durante a atualização. No entanto, ela não restaura os valores de precedência dos clientes existentes que tenham sido afetados pelo problema. Uma correção adicional será fornecida no futuro para ajudar com a restauração.

* Foi corrigido um problema em que a [tela Filtragem de domínio e UO](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering), no assistente do Azure AD Connect, mostrava a opção *Sincronizar todos os domínios e UOs* como selecionada, mesmo que a filtragem baseada em UO estivesse habilitada.

*   Foi corrigido um problema que fazia com que a [tela Configurar partições de diretório](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager retornasse um erro se o botão *Atualizar* fosse clicado. A mensagem de erro é *"Foi encontrado um erro ao atualizar domínios: não é possível converter objeto do tipo 'System.Collections.ArrayList' no tipo 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject'."* O erro ocorre quando um novo domínio do AD é adicionado a uma floresta existente do AD e você tenta atualizar o Azure AD Connect usando o botão Atualizar.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* O [recurso de Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) foi expandido para dar suporte aos clientes com as seguintes configurações:
  * Você habilitou o recurso write-back de dispositivo.
  * Você habilitou o recurso write-back de grupo.
  * A instalação não é uma configuração Express ou uma atualização de DirSync.
  * Você tem mais de 100 mil objetos no metaverso.
  * Você está se conectando a mais de uma floresta. A instalação expressa se conecta somente a uma floresta.
  * A conta do Conector AD não é mais a conta MSOL_ padrão.
  * O servidor está definido como em modo de preparo.
  * Você habilitou o recurso write-back de usuário.
  
  >[!NOTE]
  >A expansão de escopo do recurso de Atualização automática afeta os clientes com o Azure AD Connect build 1.1.105.0 e posteriores. Se você não quiser que seu servidor do Azure AD Connect seja atualizado automaticamente, será necessário executar o seguinte cmdlet em seu servidor do Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre habilitar/desabilitar a Atualização automática, consulte o artigo [Azure AD Connect: atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Status: não será liberada. As alterações deste build estão incluídas na versão 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema corrigido

* Foi corrigido um problema que fazia com que a regra de sincronização integrada "Out to AD – User ImmutableId" fosse removida quando a configuração de filtragem baseada em UO era atualizada. Essa regra de sincronização é exigida pelo [msDS-ConsistencyGuid como recurso de âncora de origem](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).

* Foi corrigido um problema em que a [tela Filtragem de domínio e UO](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering), no assistente do Azure AD Connect, mostrava a opção *Sincronizar todos os domínios e UOs* como selecionada, mesmo que a filtragem baseada em UO estivesse habilitada.

*   Foi corrigido um problema que fazia com que a [tela Configurar partições de diretório](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager retornasse um erro se o botão *Atualizar* fosse clicado. A mensagem de erro é *"Foi encontrado um erro ao atualizar domínios: não é possível converter objeto do tipo 'System.Collections.ArrayList' no tipo 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject'."* O erro ocorre quando um novo domínio do AD é adicionado a uma floresta existente do AD e você tenta atualizar o Azure AD Connect usando o botão Atualizar.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* O [recurso de Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) foi expandido para dar suporte aos clientes com as seguintes configurações:
  * Você habilitou o recurso write-back de dispositivo.
  * Você habilitou o recurso write-back de grupo.
  * A instalação não é uma configuração Express ou uma atualização de DirSync.
  * Você tem mais de 100 mil objetos no metaverso.
  * Você está se conectando a mais de uma floresta. A instalação expressa se conecta somente a uma floresta.
  * A conta do Conector AD não é mais a conta MSOL_ padrão.
  * O servidor está definido como em modo de preparo.
  * Você habilitou o recurso write-back de usuário.
  
  >[!NOTE]
  >A expansão de escopo do recurso de Atualização automática afeta os clientes com o Azure AD Connect build 1.1.105.0 e posteriores. Se você não quiser que seu servidor do Azure AD Connect seja atualizado automaticamente, será necessário executar o seguinte cmdlet em seu servidor do Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre habilitar/desabilitar a Atualização automática, consulte o artigo [Azure AD Connect: atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Status: julho de 2017

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema corrigido
* Foi corrigido um problema com o cmdlet Initialize-ADSyncDomainJoinedComputerSync que fazia com que o domínio verificado configurado no objeto de ponto de conexão de serviço existente fosse alterado, mesmo que ele ainda fosse um domínio válido. Esse problema ocorre quando seu locatário do Azure AD tem mais de um domínio verificado, os quais podem ser usados para configurar o ponto de conexão de serviço.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* O Write-back de Senha já está disponível para versão prévia com a nuvem do Microsoft Azure Governamental e com o Microsoft Cloud Alemanha. Para obter mais informações sobre o suporte do Azure AD Connect para as diferentes instâncias de serviço, consulte o artigo [Azure AD Connect: considerações especiais para instâncias](active-directory-aadconnect-instances.md).

* O cmdlet Initialize-ADSyncDomainJoinedComputerSync agora tem um novo parâmetro opcional chamado AzureADDomain. Esse parâmetro permite especificar o domínio verificado a ser usado para configurar o ponto de conexão de serviço.

### <a name="pass-through-authentication"></a>Autenticação de Passagem

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* O nome do agente necessário para Autenticação de passagem foi alterado de *Conector de Proxy de Aplicativo do Microsoft Azure AD* para *Agente de autenticação do Microsoft Azure AD Connect*.

* A habilitação da Autenticação de passagem não habilita a Sincronização de hash de senha por padrão.


## <a name="115530"></a>1.1.553.0
Status: junho de 2017

> [!IMPORTANT]
> Foram introduzidas alterações de regra de esquema e sincronização nesse build. O Serviço de Sincronização do Azure AD Connect disparará as etapas de Importação completa e Sincronização completa após a atualização. Detalhes das alterações são descritos abaixo. Para adiar temporariamente as etapas de Importação completa e Sincronização completa após a atualização, consulte o artigo [Como adiar a sincronização completa após a atualização](active-directory-aadconnect-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Sincronização do Azure AD Connect

#### <a name="known-issue"></a>Problema conhecido
* Há um problema que afeta os clientes que estejam usando a [filtragem baseada em UO](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) com a sincronização do Azure AD Connect. Quando você navega até a [página Filtragem de domínio e UO](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no assistente do Azure AD Connect, espera-se o seguinte comportamento:
  * Se a filtragem baseada em UO estiver habilitada, a opção **Sincronizar domínios e UOs selecionados** será selecionada.
  * Caso contrário, a opção **Sincronizar todos os domínios e UOs** será selecionada.

O problema que surge é que a **opção Sincronizar todos os domínios e UOs** fica sempre selecionada quando você executa o assistente.  Isso ocorre mesmo se a filtragem baseada em UO tiver sido configurada anteriormente. Antes de salvar qualquer alteração de configuração do AAD Connect, verifique se a **opção Sincronizar domínios e UOs selecionados está selecionada** e verifique se todas as UOs que precisam ser sincronizadas estão habilitadas novamente. Caso contrário, a filtragem baseada em UO será desabilitada.

#### <a name="fixed-issues"></a>Problemas corrigidos

* Foi corrigido um problema com Write-back de Senha que permitia que um administrador do Azure AD redefinisse a senha de uma conta de usuário privilegiado do AD local. O problema ocorre quando o Azure AD Connect recebe a permissão de Redefinição de senha sobre a conta privilegiada. O problema é corrigido nesta versão do Azure AD Connect não permitindo que um administrador do Azure AD redefina a senha de uma conta de usuário privilegiado arbitrária do AD local, a menos que o administrador seja o proprietário daquela conta. Para obter mais informações, consulte o [Security Advisory 4033453](https://technet.microsoft.com/library/security/4033453).

* Foi corrigido um problema relacionado ao recurso [msDS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) em que o Azure AD Connect não faz write-back no atributo msDS-ConsistencyGuid do AD local. O problema ocorre quando há várias florestas do AD locais adicionadas ao Azure AD Connect e a *opção Existem identidades de usuários em vários diretórios* está selecionada. Quando essa configuração é usada, as regras de sincronização resultantes não preenchem o atributo sourceAnchorBinary no metaverso. O atributo sourceAnchorBinary é usado como o atributo de origem para o atributo msDS-ConsistencyGuid. Como resultado, o write-back no atributo ms-DSConsistencyGuid não ocorre. Para corrigir o problema, as seguintes regras de sincronização foram atualizadas para garantir que o atributo sourceAnchorBinary no metaverso seja sempre populado:
  * In from AD – InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * In from AD - User AccountEnabled.xml
  * In from AD - User Common.xml
  * In from AD - User Join SOAInAAD.xml

* Anteriormente, mesmo se o recurso [msDS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) não estivesse habilitado, a regra de sincronização "Out to AD – User ImmutableId" ainda seria adicionada ao Azure AD Connect. O efeito seria benigno e não faria com que o write-back do atributo msDS-ConsistencyGuid ocorresse. Para evitar confusão, foi adicionada uma lógica para garantir que a regra de sincronização seja adicionada somente quando o recurso estiver habilitado.

* Foi corrigido um problema que causava a falha na sincronização de hash de senha com o evento de erro 611. Esse problema ocorria depois que um ou mais controladores de domínio eram removidos do AD local. No final de cada ciclo de sincronização de senha, o cookie de sincronização emitido pelo AD local continha IDs de invocação dos controladores de domínio removidos com valor de USN (números de sequência de atualização) igual a 0. O Gerenciador de Sincronização de Senha não é capaz de persistir o cookie de sincronização que tenha valor de USN igual a 0 e falha com o evento de erro 611. Durante o próximo ciclo de sincronização, o Gerenciador de Sincronização de Senha reutiliza o último cookie de sincronização persistido que não tenha o valor de USN igual a 0. Isso faz com que as mesmas alterações de senha sejam sincronizadas. Com essa correção, o Gerenciador de Sincronização de Senha persiste corretamente o cookie de sincronização.

* Anteriormente, mesmo que a Atualização automática tivesse sido desabilitada usando o cmdlet Set-ADSyncAutoUpgrade, o processo de Atualização automática continuava a verificar atualizações periodicamente e dependia do instalador baixado para ser desabilitado. Com essa correção, o processo de Atualização automática não verificará mais atualizações periodicamente. A correção será aplicada automaticamente quando o instalador de atualização desta versão do Azure AD Connect for executada uma vez.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Anteriormente, o recurso [msDS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) estava disponível somente para novas implantações. Agora ele está disponível para as implantações existentes. Mais especificamente:
  * Para acessar o recurso, inicie o assistente Azure AD Connect e escolha a opção *Atualizar âncora de origem*.
  * Essa opção só está visível para as implantações existentes que estejam usando o objectGuid como atributo sourceAnchor.
  * Ao configurar a opção, o assistente validará o estado do atributo msDS-ConsistencyGuid em seu Active Directory local. Se o atributo não estiver configurado em nenhum objeto de usuário no diretório, o assistente usará o msDS-ConsistencyGuid como o atributo sourceAnchor. Se o atributo estiver configurado em um ou mais objetos de usuário no diretório, o assistente concluirá que o atributo está sendo usado por outros aplicativos e não é adequado como atributo sourceAnchor, não permitindo que a alteração da Âncora de origem prossiga. Se você tiver certeza de que o atributo não é usado pelos aplicativos existentes, precisará contatar o Suporte para obter informações sobre como suprimir o erro.

* Específico ao atributo **userCertificate** nos objetos de dispositivo, o Azure AD Connect agora procura por valores de certificados necessários para [Conectar dispositivos ingressados no domínio ao Azure AD para a experiência do Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) e filtra o restante antes da sincronização com o Azure AD. Para habilitar esse comportamento, a regra de sincronização integrada "Out to AAD – Device Join SOAInAD" foi atualizada.

* Agora o Azure AD Connect dá suporte ao write-back do atributo **cloudPublicDelegates** do Exchange Online no atributo **publicDelegates** do AD local. Isso habilita o cenário em que uma caixa de correio do Exchange Online possa obter direitos de SendOnBehalfTo para usuários com caixa de correio do Exchange local. Para dar suporte a esse recurso, uma nova regra de sincronização integrada "Out to AD – User Exchange Hybrid PublicDelegates writeback" foi adicionada. Essa regra de sincronização só é adicionada ao Azure AD Connect quando o recurso Exchange Híbrido está habilitado.

*   Agora o Azure AD Connect dá suporte à sincronização do atributo **altRecipient** por meio do Azure AD. Para dar suporte a essa alteração, as seguintes regras de sincronização integradas foram atualizadas para incluir o fluxo de atributo necessário:
  * Entrada do AD – usuário do Exchange
  * Out to AAD – User ExchangeOnline
  
* O atributo **cloudSOAExchMailbox** no metaverso indica se um determinado usuário tem a caixa de correio do Exchange Online ou não. A definição desse atributo foi atualizada para incluir RecipientDisplayTypes adicionais do Exchange Online, como caixas de correio de Equipamentos e de Sala de conferência. Para habilitar essa alteração, a definição do atributo cloudSOAExchMailbox, que se encontra na regra de sincronização integrada "In from AAD – User Exchange Hybrid", foi atualizada de:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... para o seguinte:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Foi adicionado o seguinte conjunto de funções compatíveis com X509Certificate2 para a criação de expressões de regra de sincronização, a fim de lidar com valores de certificado no atributo userCertificate:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Selecionar|
    |CertKeyAlgorithmParams|CertHashString|Where|
    |||With|

* As seguintes alterações de esquema foram introduzidas para permitir que os clientes criem regras de sincronização personalizadas para transmitir sAMAccountName, domainNetBios e domainFQDN para objetos de grupo, bem como transmitir distinguishedName para objetos de usuário:

  * Os atributos a seguir foram adicionados ao esquema do MV:
    * Grupo: AccountName
    * Grupo: domainNetBios
    * Grupo: domainFQDN
    * Pessoa: distinguishedName

  * Os atributos a seguir foram adicionados ao esquema do Azure AD Connector:
    * Grupo: OnPremisesSamAccountName
    * Grupo: NetBiosName
    * Grupo: DnsDomainName
    * Usuário: OnPremisesDistinguishedName

* O script do cmdlet ADSyncDomainJoinedComputerSync agora tem um novo parâmetro opcional chamado AzureEnvironment. O parâmetro é usado para especificar a região na qual o locatário do Azure Active Directory correspondente está hospedado. Os valores válidos incluem:
  * AzureCloud (padrão)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* O Editor de regra de sincronização foi atualizado para usar Join (em vez de Provision) como o valor padrão do tipo de link durante a criação da regra de sincronização.

### <a name="ad-fs-management"></a>Gerenciamento dos AD FS

#### <a name="issues-fixed"></a>Problemas corrigidos

* As seguintes URLs são novos pontos de extremidade do Web Services Federation, introduzidos pelo Azure AD para aumentar a resiliência contra interrupção de autenticação e serão adicionadas à configuração de confiança da entidade de replicação do AD FS local:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Foi corrigido um problema que fazia com que o AD FS gerasse um valor de declaração incorreto para IssuerID. O problema ocorre se houver vários domínios verificados no locatário do Azure AD e o sufixo do domínio do atributo userPrincipalName usado para gerar a declaração IssuerID tiver pelo menos 3 níveis de profundidade (por exemplo, johndoe@us.contoso.com). O problema é resolvido ao atualizar a expressão regular usada pelas regras de declaração.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Anteriormente, o recurso Certificate Management do AD FS, fornecido pelo Azure AD Connect, só podia ser usado com farms do AD FS gerenciados por meio do Azure AD Connect. Agora, você pode usar o recurso com farms do AD FS que não sejam gerenciados com o Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Lançamento: maio de 2017

> [!IMPORTANT]
> Foram introduzidas alterações de regra de esquema e sincronização nesse build. O Serviço de Sincronização do Azure AD Connect disparará as etapas de Importação Completa e Sincronização Completa após a atualização. Detalhes das alterações são descritos abaixo.
>
>

**Problemas corrigidos:**

Sincronização do Azure AD Connect

* Corrigido um problema que fazia a Atualização Automática ocorrer no servidor do Azure AD Connect, mesmo que o cliente tivesse desabilitado o recurso usando o cmdlet Set-ADSyncAutoUpgrade. Com essa correção, o processo de Atualização Automática no servidor ainda verifica se há atualizações periodicamente, mas o instalador baixado respeita a configuração de Atualização Automática.
* Durante a atualização in-loco do DirSync, o Azure AD Connect cria uma conta de serviço do Azure AD para ser usada pelo Azure AD Connector para sincronizar com o Azure AD. Depois da criação da conta, o Azure AD Connect autentica-se com o Azure AD usando a conta. Às vezes, a autenticação falha devido a questões transitórias que, por sua vez, fazem a atualização in-loco de DirSync falhar com o erro *"Ocorreu um erro ao executar tarefa Configurar AAD Sync: AADSTS50034: para entrar nesse aplicativo, a conta deve ser adicionada ao diretório xxx.onmicrosoft.com"*. Para melhorar a resiliência da atualização do DirSync, o Azure AD Connect agora tenta novamente realizar a etapa de autenticação.
* Havia um problema com o build 443 que fazia a atualização in-loco do DirSync ser bem-sucedida, mas os perfis de execução necessários para a sincronização do diretório não eram criados. A lógica de reparo está incluída nesse build do Azure AD Connect. Quando o cliente atualiza para esse build, o Azure AD Connect detecta os perfis de execução ausentes e cria-os.
* Corrigido um problema que fazia o processo de Sincronização de Senha não ser inicializado com a ID de Evento 6900 e o erro *"Um item com a mesma chave já foi adicionado"*. Esse problema ocorre se você atualiza a configuração de filtragem da UO para incluir a partição de configuração do AD. Para corrigir esse problema, o processo de Sincronização de Senha agora sincroniza alterações de senha de partições de domínio do AD apenas. Partições não de domínio, como a partição de configuração, são ignoradas.
* Durante a instalação do Expresso, o Azure AD Connect cria uma conta do AD DS local para ser usada pelo AD Connector para comunicação com o AD local. Anteriormente, a conta era criada com o sinalizador PASSWD_NOTREQD definido no atributo user-Account-Control e uma senha aleatória era definida na conta. Agora, o Azure AD Connect remove explicitamente o sinalizador PASSWD_NOTREQD depois que a senha é definida na conta.
* Corrigido um problema que faz com que a atualização do DirSync falhe com o erro *"Ocorreu um deadlock no SQL Server que está tentando adquirir um deadlock de aplicativo"* quando o atributo mailNickname é encontrado no esquema do AD local, mas não está limitado à classe de objeto de Usuário do AD.
* Corrigido um problema que fazia o recurso de write-back do Dispositivo ser desabilitado automaticamente quando um administrador estava atualizando a configuração de sincronização do Azure AD Connect usando o assistente do Azure AD Connect. Isso é causado pela execução de verificação de pré-requisito pelo assistente para a configuração de write-back do Dispositivo existente no AD local e a falha na verificação. A correção será ignorar a verificação se o write-back do Dispositivo já tiver sido habilitado anteriormente.
* Para configurar a filtragem da UO, você pode usar o assistente do Azure AD Connect ou o Synchronization Service Manager. Anteriormente, se você usasse o Assistente do Azure AD Connect para configurar a filtragem de UO, novas UOs criadas posteriormente seriam incluídas para sincronização de diretórios. Se você não quisesse incluir novas UOs, precisaria configurar a filtragem de UO usando o Synchronization Service Manager. Agora, você pode obter o mesmo comportamento usando o assistente do Azure AD Connect.
* Corrigido um problema que fazia os procedimentos armazenados exigidos pelo Azure AD Connect serem criados sob o esquema do administrador de instalação, em vez de no esquema do dbo.
* Corrigido um problema que fazia com que o atributo TrackingId retornado pelo Azure AD fosse omitido nos Logs de Evento do Servidor do AAD Connect. O problema ocorria quando o Azure AD Connect recebia uma mensagem de redirecionamento do Azure AD e do Azure AD Connect não conseguia conectar-se ao ponto de extremidade fornecido. O TrackingId é usado pelos Engenheiros de Suporte para correlacionar com logs no lado do serviço durante a solução de problemas.
* Quando o Azure AD Connect recebe o erro LargeObject do Azure AD, o Azure AD Connect gera um evento com o EventID 6941 e a mensagem *“O objeto provisionado é muito grande. Restrinja o número de valores de atributo nesse objeto.”* Ao mesmo tempo, o Azure AD Connect também gera um evento enganoso com EventID 6900 e mensagem *"Microsoft.Online.Coexistence.ProvisionRetryException: não é possível comunicar-se com o serviço Microsoft Azure Active Directory"*. Para minimizar a confusão, Azure AD Connect não gera mais este último evento quando o erro LargeObject é recebido.
* Corrigido um problema que fazia com que o Synchronization Service Manager parasse de responder ao tentar atualizar a configuração do conector de LDAP genérico.

**Novos recursos/melhorias:**

Sincronização do Azure AD Connect
* Alterações de Regra de Sincronização – as seguintes alterações de regra de sincronização foram implementadas:
  * Atualizado o conjunto de regras de sincronização padrão para não exportar os atributos **userCertificate** e **userSMIMECertificate** se os atributos tivessem mais de 15 valores.
  * Os atributos **employeeID** e **msExchBypassModerationLink** do AD agora são incluídos no conjunto de regras de sincronização padrão.
  * O atributo **photo** do AD foi removido do conjunto de regras de sincronização padrão.
  * Adicionado **preferredDataLocation** ao esquema de Metaverso e ao esquema do AAD Connector. Os clientes que desejam atualizar qualquer um dos atributos no Azure AD podem implementar regras de sincronização personalizadas para fazer isso. Para obter mais informações sobre o atributo, consulte a seção do artigo [Sincronização do Azure AD Connect: como fazer uma alteração à configuração padrão – habilitar a sincronização de PreferredDataLocation](active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-preferreddatalocation).
  * Adicionado **userType** ao esquema do Metaverso e ao esquema do AAD Connector. Os clientes que desejam atualizar qualquer um dos atributos no Azure AD podem implementar regras de sincronização personalizadas para fazer isso.

* Agora o Azure AD Connect habilita automaticamente o uso do atributo ConsistencyGuid como o atributo de Âncora de origem para objetos do AD local. Além disso, o Azure AD Connect preenche o atributo ConsistencyGuid com o valor do atributo objectGuid se ele estiver vazio. Esse recurso é aplicável apenas à nova implantação. Para obter mais informações sobre esse recurso, consulte a seção do artigo [Azure AD Connect: Conceitos de design – usando msDS-ConsistencyGuid como sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).
* O novo cmdlet de solução de problemas Invoke-ADSyncDiagnostics foi adicionado para ajudar a diagnosticar problemas relacionados à Sincronização de Hash de Senha. Para obter informações sobre como usar o cmdlet, consulte o artigo [Solucionar problemas de sincronização de senha com a sincronização do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-synchronization).
* O Azure AD Connect agora dá suporte à sincronização dos objetos de Pasta Pública Habilitados para Email do AD local para o Azure AD. Você pode habilitar o recurso usando o assistente do Azure AD Connect em Recursos Opcionais. Para obter mais informações sobre esse recurso, consulte o artigo [Suporte do Bloqueio do Edge Baseado nos Diretórios do Office 365 para Pastas Públicas locais Habilitadas para Email](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* O Azure AD Connect exige uma conta do AD DS para sincronizar do AD local. Anteriormente, se você instalava o Azure AD Connect usando o modo Expresso, era possível fornecer as credenciais de uma conta do Admin Corporativo e o Azure AD Connect criaria a conta necessária do AD DS. No entanto, para uma instalação personalizada e para adicionar florestas a uma implantação existente, era necessário fornecer a conta do AD DS. Agora você também tem a opção de fornecer as credenciais de uma conta do Admin Corporativo durante uma instalação personalizada e permitir que o Azure AD Connect crie a conta do AD DS necessária.
* O Azure AD Connect agora dá suporte a SQL AOA. Você deve habilitar o SQL AOA antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect detecta se a instância do SQL fornecida está habilitada para SQL AOA ou não. Se o SQL AOA estiver habilitado, o Azure AD Connect descobrirá melhor se o AOA SQL está configurado para usar replicação síncrona ou replicação assíncrona. Ao configurar o Ouvinte do Grupo de Disponibilidade, é recomendável definir a propriedade RegisterAllProvidersIP como 0. Isso ocorre porque o Azure AD Connect atualmente usa o SQL Native Client para conectar-se ao SQL, e o SQL Native Client não dá suporte ao uso da propriedade MultiSubNetFailover.
* Se você estiver usando o LocalDB como o banco de dados para o servidor do Azure AD Connect e tiver atingido seu limite de tamanho de 10 GB, o Serviço de Sincronização não será mais iniciado. Anteriormente, você precisava executar a operação ShrinkDatabase no LocalDB para recuperar espaço suficiente do banco de dados para o Serviço de Sincronização iniciar. Depois disso, você pode usar o Synchronization Service Manager para excluir o histórico de execução para recuperar mais espaço do banco de dados. Agora, você pode usar cmdlet Start-ADSyncPurgeRunHistory para limpar os dados do histórico de execução do LocalDB para recuperar espaço do banco de dados. Além disso, esse cmdlet dá suporte ao modo offline (especificando o parâmetro -offline), que pode ser usado quando o Synchronization Service não estiver em execução. Observação: o modo offline poderá ser usado apenas se o Synchronization Service não estiver em execução e o banco de dados usado for LocalDB.
* Para reduzir a quantidade de espaço de armazenamento necessária, o Azure AD Connect agora compacta os detalhes do erro de sincronização antes de armazená-los em bancos de dados SQL/LocalDB. Ao atualizar de uma versão anterior do Azure AD Connect para esta versão, o Azure AD Connect executa uma compactação única em detalhes do erro de sincronização existentes.
* Anteriormente, depois de atualizar a configuração de filtragem da UO, você precisava executar manualmente uma importação Completa para garantir que os objetos existentes fossem incluídos/excluídos adequadamente da sincronização de diretório. Agora, o Azure AD Connect aciona automaticamente a importação Completa durante o próximo ciclo de sincronização. Além disso, a importação Total deve ser aplicada apenas aos AD Connectors afetados pela atualização. Observação: essa melhoria é aplicável apenas a atualizações de filtragem de UO feitas usando o assistente do Azure AD Connect. Não é aplicável à atualização de filtragem de UO feita usando o Synchronization Service Manager.
* Anteriormente, filtragem baseada em Grupo dava suporte apenas a objetos de Usuários, Grupos e Contato. Agora, a filtragem baseada em Grupo também dá suporte a objetos de Computador.
* Anteriormente, você podia excluir dados do Espaço Conector sem desabilitar o agendador de sincronização do Azure AD Connect. Agora, o Synchronization Service Manager bloqueia a exclusão de dados do Espaço Conector quando ele detecta que o agendador está habilitado. Além disso, um aviso é retornado para informar os clientes sobre uma possível perda de dados se os dados do espaço conector forem excluídos.
* Anteriormente, você precisava desabilitar a transcrição do PowerShell para o assistente do Azure AD Connect ser executado corretamente. Esse problema foi parcialmente resolvido. Você poderá habilitar a transcrição do PowerShell se estiver usando o assistente do Azure AD Connect para gerenciar a configuração de sincronização. Você deverá desabilitar a transcrição do PowerShell se estiver usando o assistente do Azure AD Connect para gerenciar a configuração do ADFS.



## <a name="114860"></a>1.1.486.0
Lançamento: abril de 2017

**Problemas corrigidos:**
* Corrigido o problema em que o Azure AD Connect não será instalado com êxito na versão localizada do Windows Server.

## <a name="114840"></a>1.1.484.0
Lançamento: abril de 2017

**Problemas conhecidos:**

* Essa versão do Azure AD Connect não será instalada com êxito se as seguintes condições forem todas verdadeiras:
   1. Você está executando a atualização DirSync in-loco ou a nova instalação do Azure AD Connect.
   2. Você está usando uma versão localizada do Windows Server em que o nome do grupo de Administradores interno no servidor não é "Administradores".
   3. Você pode usar o SQL Server 2012 Express LocalDB padrão instalado com o Azure AD Connect ou usar seu próprio SQL completo.

**Problemas corrigidos:**

Sincronização do Azure AD Connect
* Corrigido um problema em que o agendador de sincronização ignora a etapa de sincronização inteiro se um ou mais conectores estiver sem o perfil de execução para essa etapa de sincronização. Por exemplo, você adicionou manualmente um conector usando Soynchronization Service Manager sem criar um perfil de execução de Importação Delta para ele. Essa correção garante que o agendador de sincronização continua a executar Importação Delta para outros conectores.
* Corrigido um problema em que o Serviço de Sincronização imediatamente para de processar um perfil de execução quando ele encontra um problema com uma das etapas de execução. Essa correção garante que o Serviço de Sincronização ignora essa etapa de execução e continua processando o restante. Por exemplo, você tem um perfil de execução de Importação Delta para seu conector do AD com várias etapas de execução (uma para cada domínio do AD local). O Serviço de Sincronização executará a Importação Delta com os outros domínios do AD, mesmo que um deles tenha problemas de conectividade de rede.
* Corrigido um problema que faz com que a atualização do Azure AD Connector seja ignorada durante a Atualização Automática.
* Corrigido um problema que faz com que o Azure AD Connect determine incorretamente se o servidor é um controlador de domínio durante a instalação o que, por sua vez, faz com que a atualização do DirSync falhe.
* Corrigido um problema que faz com que a atualização in-loco de DirSync não crie perfis de execução para o Azure AD Connector.
* Corrigido um problema em que a interface de usuário do Synchronization Service Manager não responde ao tentar configurar o Generic LDAP Connector.

Gerenciamento dos AD FS
* Corrigido um problema em que o assistente do Azure AD Connect falha se o nó primário do AD FS foi movido para outro servidor.

SSO da Área de Trabalho
* Corrigido um problema no assistente do Azure AD Connect em que a tela de entrada não permite habilitar o recurso de SSO da Área de Trabalho se você escolheu a Sincronização de Senha como a opção de Entrada durante a nova instalação.

**Novos recursos/melhorias:**

Sincronização do Azure AD Connect
* Azure AD Connect Sync agora oferecre suporte para uso da Conta de Serviço Virtual, Conta de Serviço Gerenciado e Conta de Serviço Gerenciado de Grupo como sua conta de serviço. Isso se aplica à nova instalação do Azure AD Connect, somente. Ao instalar o Azure AD Connect:
    * Por padrão, o assistente do Azure AD Connect criará uma Conta de Serviço Virtual e utilizará como sua conta de serviço.
    * Se você estiver instalando em um controlador de domínio, o Azure AD Connect reverterá para o comportamento anterior onde criará uma conta de usuário de domínio e utilizará como sua conta de serviço.
    * É possível substituir o comportamento padrão fornecendo uma das seguintes opções:
      * Uma Conta de Serviço Gerenciado de Grupo
      * Uma Conta de Serviço Gerenciado
      * Uma conta de usuário do domínio
      * Uma conta de usuário local
* Anteriormente, se você atualizasse para uma nova versão do Azure AD Connect contendo atualizações de conexão ou alterações na regra de sincronização, o Azure AD Connect dispararia um ciclo de sincronização completo. Agora, o Azure AD Connect dispara seletivamente a etapa de Importação Completa somente para conectores com atualização e a etapa de Sincronização Completa somente para conectores com alterações de regra de sincronização.
* Anteriormente, o limite de exclusão de exportação só se aplicava às exportações disparadas pelo Agendador de Sincronização. Agora, o recurso é estendido para incluir exportações disparadas manualmente pelo cliente usando o Synchronization Service Manager.
* No seu locatário do Azure AD, há uma configuração de serviço que indica se o recurso de Sincronização de Senha está habilitado para o seu locatário ou não. Anteriormente, era fácil que configuração de serviço fosse configurado incorretamente pelo Azure AD Connect quando havia um servidor de preparo e um ativo. Agora, o Azure AD Connect tentará manter a configuração de serviço consistente com o servidor ativo do Azure AD Connect, somente.
* O assistente do Azure AD Connect agora detecta e retorna um aviso se o AD local não tiver Lixeira do AD ativada.
* Anteriormente, Exportar para o Azure AD expirava e falhava se o tamanho combinado dos objetos no lote excedesse um determinado limite. Agora, o Serviço de Sincronização tentará reenviar os objetos em lotes menores se o problema for encontrado.
* O aplicativo de Gerenciamento de Chaves de Serviço de Sincronização foi removido do Menu Iniciar do Windows. O Gerenciamento de chave de criptografia continuará a oferecer suporte por meio da interface de linha de comando usando miiskmu.exe. Para obter informações sobre o gerenciamento de chave de criptografia, consulte o artigo [Abandonando a chave de criptografia de sincronização do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Anteriormente, se você alterava a senha da conta de serviço de sincronização do Azure AD Connect, o Serviço de Sincronização não poderia iniciar corretamente até que você tivesse abandonado a chave de criptografia e reinicializado a senha da conta do serviço de sincronização do Azure AD Connect. Agora, isso não é mais necessário.

SSO da Área de Trabalho

* O assistente do Azure AD Connect não exige mais porta 9090 para ser aberta na rede ao configurar a Autenticação de Passagem e SSO de Área de Trabalho. Somente a porta 443 é exigida. 

## <a name="114430"></a>1.1.443.0
Lançamento: março de 2017

**Problemas corrigidos:**

Sincronização do Azure AD Connect
* Correção de um problema que causa uma falha do assistente do Azure AD Connect se o nome de exibição do Conector do Azure AD não contém o domínio onmicrosoft.com inicial atribuído ao locatário do Azure AD.
* Correção de um problema que causa uma falha do assistente do Azure AD Connect ao estabelecer uma conexão com o banco de dados SQL quando a senha da Conta de Serviço de Sincronização contém caracteres especiais, como apóstrofo, dois-pontos e espaço.
* Correção de um problema que faz com que o erro “A dimagem tem uma âncora diferente da imagem” ocorra em um servidor do Azure AD Connect no modo de preparo, após a exclusão temporária de um objeto do AD local da sincronização e sua inclusão novamente na sincronização.
* Correção de um problema que faz com que o erro “O objeto localizado pelo DN é um fantasma” ocorra em um servidor do Azure AD Connect no modo de preparo, após a exclusão temporária de um objeto do AD local da sincronização e sua inclusão novamente na sincronização.

Gerenciamento dos AD FS
* Correção de um problema no qual o assistente do Azure AD Connect não atualiza a configuração do AD FS nem define as declarações corretas no objeto de confiança de terceira parte confiável após a configuração de um ID de Logon Alternativo.
* Correção de um problema no qual o assistente do Azure AD Connect não consegue manipular corretamente os servidores do AD FS cujas contas de serviço são configuradas usando o formato userPrincipalName, em vez do formato sAMAccountName.

Autenticação de Passagem
* Correção de um problema que causa uma falha do assistente do Azure AD Connect se a Autenticação de Passagem é selecionada, mas o registro de seu conector falha.
* Correção de um problema que faz com que o assistente do Azure AD Connect ignore as verificações de validação no método de conexão selecionado quando o recurso SSO da Área de Trabalho está habilitado.

Redefinição de senha
* Correção de um problema que pode fazer com que o servidor do Azure AAD Connect não tente se reconectar se a conexão for interrompida por um firewall ou proxy.

**Novos recursos/melhorias:**

Sincronização do Azure AD Connect
* Agora, o cmdlet Get-ADSyncScheduler retorna uma nova propriedade booliana chamada SyncCycleInProgress. Se o valor retornado for verdadeiro, isso significa que há um ciclo de sincronização agendado em andamento.
* A pasta de destino de armazenamento dos logs de instalação e configuração do Azure AD Connect foi movida de %localappdata%\AADConnect para %programdata%\AADConnect, a fim de melhorar a acessibilidade aos arquivos de log.

Gerenciamento dos AD FS
* Adição de suporte para atualização do Certificado SSL do Farm do AD FS.
* Adição de suporte para gerenciamento do AD FS 2016.
* Agora é possível especificar uma gMSA (Conta de Serviço Gerenciado de Grupo) existente durante a instalação do AD FS.
* Agora é possível configurar o SHA-256 como o algoritmo de hash de assinatura para o objeto de confiança de terceira parte confiável do Azure AD.

Redefinição de senha
* Introdução de aprimoramentos para permitir que o produto funcione em ambientes com regras de firewall mais rígidas.
* Aumento na confiabilidade de conexão com o Barramento de Serviço do Azure.

## <a name="113800"></a>1.1.380.0
Lançamento: dezembro de 2016

**Problema corrigido:**

* Foi corrigido o problema em que a regra de declaração de issuerid para AD FS (Serviços de Federação do Active Directory) está ausente desta compilação.

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Lançamento: dezembro de 2016

**Problema conhecido:**

* A regra de declaração issuerid para o AD FS está ausente dessa compilação. A regra de declaração de issuerid é necessária se você está associando vários domínios ao Azure AD (Azure Active Directory). Se estiver usando o Azure AD Connect para gerenciar a implantação local do AD FS, atualizar para essa compilação removerá a regra de declaração issuerid existente da configuração do AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar regra de declaração issuerid, confira este artigo sobre [Multiple Domain Support for Federating with Azure AD](active-directory-aadconnect-multiple-domains.md) (Suporte a vários domínios para federação com o Azure AD).

**Problema corrigido:**

* Se a porta 9090 não estiver aberta para a conexão de saída, a atualização ou a instalação do Azure AD Connect falhará.

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Lançamento: dezembro de 2016

**Problemas conhecidos:**

* A regra de declaração issuerid para o AD FS está ausente dessa compilação. A regra de declaração issuerid será necessária se você estiver federando vários domínios com o Azure AD. Se estiver usando o Azure AD Connect para gerenciar a implantação local do AD FS, atualizar para essa compilação removerá a regra de declaração issuerid existente da configuração do AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar a regra de declaração issuerid, confira este artigo sobre [Suporte a vários domínios para federação com o Azure AD](active-directory-aadconnect-multiple-domains.md).
* A porta 9090 deve estar aberta para a saída para que a instalação seja concluída.

**Novos recursos:**

* Autenticação de Passagem (Visualização).

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Lançamento: novembro de 2016

**Problema conhecido:**

* A regra de declaração issuerid para o AD FS está ausente dessa compilação. A regra de declaração issuerid será necessária se você estiver federando vários domínios com o Azure AD. Se estiver usando o Azure AD Connect para gerenciar a implantação local do AD FS, atualizar para essa compilação removerá a regra de declaração issuerid existente da configuração do AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar a regra de declaração issuerid, confira este artigo sobre [Suporte a vários domínios para federação com o Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problemas corrigidos:**

* Algumas vezes, instalar o Azure Connect AD falha porque não é possível criar uma conta de serviço local cuja senha satisfaça o nível de complexidade especificado pela política de senha da organização.
* Foi corrigido um problema em que as regras de associação não são avaliadas novamente quando um objeto no espaço do conector simultaneamente fica fora do escopo de uma regra de junção e tornam-se em escopo para outro. Isso pode ocorrer se você tiver duas ou mais regras de junção cujas condições de junção são mutuamente exclusivas.
* Corrigido um problema em que regras de sincronização de entrada (do Azure AD) que não contêm regras de união não são processadas se elas tiverem valores de precedência inferiores daqueles que contém regras de associação.

**Aperfeiçoamentos:**

* Adicionado suporte para instalar o Azure AD Connect no Windows Server 2016 Standard ou posterior.
* Foi adicionado suporte para usar o SQL Server 2016 como banco de dados remoto para o Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Lançamento: agosto de 2016

**Problemas corrigidos:**

* As alterações no intervalo de sincronização não ocorrerão até após a conclusão do próximo ciclo de sincronização.
* O assistente do Azure AD Connect não aceita uma conta do Azure AD cujo nome de usuário começa com um sublinhado (\_).
* Falha do Assistente do Azure AD Connect ao autenticar a conta do Azure AD se a senha da conta contém muitos caracteres especiais. A mensagem de erro "Não foi possível validar as credenciais. Ocorreu um erro inesperado.” é retornado.
* Desinstalar o servidor de preparo desabilita a sincronização de senha no locatário do Azure AD e causa a falha da sincronização de senha com o servidor ativo.
* A sincronização de senha falha em casos incomuns quando não há hash de senha armazenado no usuário.
* Quando o servidor do Azure AD Connect está habilitado para o modo de preparo, o write-back de senha não é temporariamente desabilitado.
* O assistente do Azure AD Connect não mostra a sincronização de senha real e a configuração de write-back de senha quando o servidor está no modo de preparo. Ele sempre os mostra como desabilitados.
* As alterações de configuração na sincronização de senha e no write-back de senha não são mantidas pelo Assistente do Azure AD Connect quando o servidor está no modo de preparo.

**Aperfeiçoamentos:**

* Cmdlet Start-ADSyncSyncCycle atualizado para indicar se pode iniciar um novo ciclo de sincronização com êxito ou não.
* Cmdlet Stop-ADSyncSyncCycle adicionado para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
* Cmdlet Stop-ADSyncScheduler atualizado para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
* Ao configurar [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md) no Assistente do Azure AD Connect, o atributo do Azure AD do tipo "Cadeia de caracteres Teletex" pode ser selecionado.

## <a name="111890"></a>1.1.189.0
Lançamento: junho de 2016

**Problemas corrigidos e aperfeiçoamentos:**

* O Azure AD Connect agora pode ser instalado em um servidor compatível com FIPS.
  * Para sincronização de senha, confira [Sincronização de senha e FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips).
* Corrigido um problema em que um nome NetBIOS não pôde ser resolvido para o FQDN no Active Directory Connector.

## <a name="111800"></a>1.1.180.0
Lançamento: maio de 2016

**Novos recursos:**

* Avisa e ajuda a verificar os domínios se você não fizer isso antes de executar o Azure AD Connect.
* Adicionado suporte ao [Microsoft Cloud Alemanha](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Adicionado suporte à infraestrutura em [nuvem do Microsoft Azure Governamental](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) mais recente com novos requisitos de URL.

**Problemas corrigidos e aperfeiçoamentos:**

* Adicionada filtragem ao Editor de regra de sincronização para tornar mais fácil localizar as regras de sincronização.
* Desempenho aprimorado ao excluir um espaço de conector.
* Corrigido um problemas quando o mesmo objeto foi excluído e adicionado na mesma execução (chamado excluir/adicionar).
* Uma regra de sincronização desabilitada não pode mais reabilitar objetos e atributos incluídos na atualização ou na atualização de esquema de diretório.

## <a name="111300"></a>1.1.130.0
Lançamento: abril de 2016

**Novos recursos:**

* Suporte adicionado para atributos com valores múltiplos para [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md).
* Suporte adicionado para mais variações de configuração de [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) serem consideradas qualificadas para atualização.
* Adicionados alguns cmdlets para o [agendador personalizado](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Lançamento: março de 2016

**Problemas corrigidos:**

* Foi garantido que a Instalação expressa não possa ser usada no Windows Server 2008 (pré-R2), já que a sincronização de senha não tem suporte neste sistema operacional.
* A atualização do DirSync com uma configuração de filtro personalizado não funcionou conforme o esperado.
* Ao atualizar para uma versão mais recente e se não houver alterações na configuração, a importação/sincronização completa não deverá ser agendada.

## <a name="111100"></a>1.1.110.0
Lançamento: fevereiro de 2016

**Problemas corrigidos:**

* A atualização de versões anteriores não funcionará se a instalação não estiver na pasta padrão C:\Arquivos de Programas.
* Se você instalar e desmarcar **Iniciar o processo de sincronização...** no fim do assistente de instalação, executar o assistente de instalação uma segunda vezs não habilitará o agendador.
* O agendador não funciona conforme o esperado em servidores em que o formato de data/hora en-US não é usado. Ele também bloqueará `Get-ADSyncScheduler` para retornar as horas corretas.
* Se você instalou uma versão anterior do Azure AD Connect com o ADFS como a opção de entrada e atualização, não será possível executar o assistente de instalação novamente.

## <a name="111050"></a>1.1.105.0
Lançamento: fevereiro de 2016

**Novos recursos:**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) para clientes de configurações Expressas.
* Suporte para o administrador global usando a Autenticação Multifator do Azure e o Privileged Identity Management no assistente de instalação.
  * Você precisará permitir que o proxy também permita o tráfego para https://secure.aadcdn.microsoftonline-p.com se usar a Autenticação Multifator.
  * Você precisa adicionar https://secure.aadcdn.microsoftonline-p.com à lista de sites confiáveis para que a Autenticação Multifator funcione corretamente.
* Permita alterar o método de logon do usuário após a instalação inicial.
* Permita [Filtragem de Domínio e UO](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no assistente de instalação. Isso também permite conectar-se às florestas em que nem todos os domínios estão disponíveis.
* O [Agendador](active-directory-aadconnectsync-feature-scheduler.md) é interno ao mecanismo de sincronização.

**Recursos promovidos da visualização para GA:**

* [Write-back de dispositivo](active-directory-aadconnect-feature-device-writeback.md).
* [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md).

**Novos recursos de visualização:**

* O novo intervalo de ciclo de sincronização padrão é de 30 minutos. Costumava ser três horas para todas as versões anteriores. Adiciona suporte para alterar o comportamento do [agendador](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemas corrigidos:**

* A página Verificar domínios DNS nem sempre reconheceu os domínios.
* Solicita credenciais de administrador de domínio ao configurar o AD FS.
* As contas locais do AD não são reconhecidas pelo assistente de instalação se estiverem localizadas em um domínio com uma árvore DNS diferente do domínio raiz.

## <a name="1091310"></a>1.0.9131.0
Lançamento: dezembro de 2015

**Problemas corrigidos:**

* A sincronização de senha poderá não funcionar quando você alterar as senhas no AD DS (Active Directory Domain Services), mas funcionará quando você definir uma senha.
* Quando você tiver um servidor proxy, a autenticação do Azure AD poderá falhar durante a instalação ou se uma atualização for cancelada na página de configuração.
* A atualização de uma versão anterior do Azure AD Connect com uma instância completa do SQL Server falhará se você não for um SA (administrador do sistema) do SQL Server.
* A atualização de uma versão anterior do Azure AD Connect com um SQL Server remoto mostra o erro "Não é possível acessar o banco de dados SQL do ADSync".

## <a name="1091250"></a>1.0.9125.0
Lançamento: novembro de 2015

**Novos recursos:**

* Pode reconfigurar a relação de confiança entre o ADFS e o AD do Azure.
* Pode atualizar o esquema do Active Directory e gerar regras de sincronização.
* Pode desabilitar uma regra de sincronização.
* Pode definir "AuthoritativeNull" como um novo literal em uma regra de sincronização.

**Novos recursos de visualização:**

* [Azure AD Connect Health para sincronização](../connect-health/active-directory-aadconnect-health-sync.md).
* Suporte para sincronização de senha dos [Serviços de Domínio do AD do Azure](../active-directory-passwords-update-your-own-password.md) .

**Novo cenário com suporte:**

* Dá suporte a várias organizações local do Exchange. Para obter mais informações, confira [Implantações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Problemas corrigidos:**

* Problemas de sincronização de senha:
  * Um objeto movido de fora do escopo para dentro do escopo não terá sua senha sincronizada. Isso inclui a UO e a filtragem de atributo.
  * Selecionar uma nova UO para incluir na sincronização não exige uma sincronização de senha completa.
  * Quando um usuário desabilitado é habilitado, a senha não é sincronizada.
  * A fila de repetição de senha é infinita e o limite anterior de 5.000 objetos a ser retirado foi removido.
* Não é possível se conectar ao Active Directory com o nível funcional de floresta do Windows Server 2016.
* Não é possível alterar o grupo que é usado para filtragem de grupo após a instalação inicial.
* Não cria mais um novo perfil do usuário no servidor do Azure AD Connect para cada usuário que faz uma alteração de senha com write-back de senha habilitada.
* Não é possível usar valores Inteiros Longos em escopos de regras de sincronização.
* A caixa de seleção "write-back de dispositivo" permanecerá desabilitada se houver controladores de domínio inacessíveis.

## <a name="1086670"></a>1.0.8667.0
Lançamento: agosto de 2015

**Novos recursos:**

* O assistente de instalação do Azure AD Connect agora está localizado para todos os idiomas do Windows Server.
* Suporte adicionado para desbloqueio de contas ao usar o gerenciamento de senhas do AD do Azure.

**Problemas corrigidos:**

* O assistente de instalação do Azure AD Connect falha se outro usuário continuar a instalação em vez da pessoa que iniciou a instalação inicialmente.
* Se uma desinstalação anterior do Azure AD Connect falhar ao desinstalar o Azure AD Connect Sync corretamente, não será possível reinstalá-lo.
* Não será possível instalar o Azure AD Connect usando a Instalação expressa se o usuário não estiver no domínio raiz da floresta ou se uma versão diferente do inglês do Active Directory for usada.
* Se o FQDN da conta de usuário do Active Directory não puder ser resolvido, é mostrada a mensagem enganosa de erro "Falha ao confirmar o esquema".
* Se a conta usada no Active Directory Connector for alterada fora do assistente, o assistente falhará em execuções posteriores.
* O Azure AD Connect às vezes não pode instalar em um controlador de domínio.
* Não é possível habilitar e desabilitar o "Modo de preparo" se os atributos de extensão forem adicionados.
* O write-back de senha falha em algumas configurações devido a uma senha incorreta no Active Directory Connector.
* DirSync não poderá ser atualizado se um DN (nome diferenciado) for usado na filtragem de atributos.
* Uso excessivo de CPU ao usar a redefinição de senha.

**Recursos de visualização removidos:**

* O recurso de visualização [Write-back de usuário](active-directory-aadconnect-feature-preview.md#user-writeback) foi temporariamente removido com base nos comentários de nossos clientes da visualização. Ela será adicionada novamente mais tarde, depois que abordarmos os comentários fornecidos.

## <a name="1086410"></a>1.0.8641.0
Lançamento: junho de 2015

**Versão inicial do Azure AD Connect.**

Nome alterado de Azure AD Sync para Azure AD Connect.

**Novos recursos:**

* Instalação das [configurações expressas](active-directory-aadconnect-get-started-express.md)
* Pode [configurar o AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* É possível [atualizar do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Apresentação do [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode)

**Novos recursos de visualização:**

* [Write-back de usuário](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Write-back de grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Write-back de dispositivo](active-directory-aadconnect-feature-device-writeback.md)
* [Extensões de diretório](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Lançamento: maio de 2015

**Novo Requisito:**

* O Azure AD Sync agora requer a instalação do .NET Framework versão 4.5.1.

**Problemas corrigidos:**

* O write-back de senha do Azure AD está falhando com um erro de conectividade do Barramento de Serviço do Azure.

## <a name="104910413"></a>1.0.491.0413
Lançamento: abril de 2015

**Problemas corrigidos e aperfeiçoamentos:**

* O Active Directory Connector não processa exclusões corretamente se a Lixeira estiver habilitada e se existirem vários domínios na floresta.
* O desempenho das operações de importação foi aprimorado para o Active Directory Connector do Azure.
* Quando um grupo excedeu o limite de associação (por padrão, o limite é definido como 50.000 objetos), o grupo foi excluído do Azure Active Directory. Com o novo comportamento, o grupo não é excluído, um erro é gerado e novas alterações de associação não são exportadas.
* Não é possível provisionar um novo objeto se uma exclusão de preparo com o mesmo DN já estiver presente no espaço do conector.
* Alguns objetos são marcados para sincronização durante uma sincronização delta, embora não haja alteração preparada no objeto.
* Forçar uma sincronização de senha também remove a lista preferencial do DC.
* CSExportAnalyzer tem problemas com alguns estados de objetos.

**Novos recursos:**

* Um ingresso agora pode se conectar ao tipo de objeto "ANY" na MV.

## <a name="104850222"></a>1.0.485.0222
Lançamento: fevereiro de 2015

**Aperfeiçoamentos:**

* Desempenho aprimorado de importação.

**Problemas corrigidos:**

* A Sincronização de Senha respeita o atributo cloudFiltered usado pela filtragem de atributo. Os objetos filtrados não estão mais no escopo de sincronização de senha.
* Em raras situações em que a topologia tem muitos controladores de domínio, a sincronização de senha não funciona.
* "Servidor-parado" ao importar do Conector do AD do Azure depois que o gerenciamento de dispositivo tiver sido habilitado no AD do Azure/Intune.
* Ingressar FSPs (Entidades de Segurança Externa) de vários domínios na mesma floresta causa um erro de ingresso ambíguo.

## <a name="104751202"></a>1.0.475.1202
Lançamento: dezembro de 2014

**Novos recursos:**

* Agora há suporte para a sincronização de senhas com filtragem baseada em atributo. Para obter mais informações, confira [Sincronização de senha com filtragem](active-directory-aadconnectsync-configure-filtering.md).
* O atributo msDS-ExternalDirectoryObjectID será gravado de volta no Active Directory. Esse recurso adiciona suporte a aplicativos do Office 365. Ele usa OAuth2 para acessar caixas de correio Locais e Online em uma Implantação Híbrida do Exchange.

**Problemas de atualização corrigidos:**

* Uma versão mais recente do que o assistente de logon está disponível no servidor.
* Um caminho de instalação personalizada foi usado para instalar o Azure AD Sync.
* Um critério de associação personalizado inválido bloqueará a atualização.

**Outras correções:**

* Modelos do Office Pro Plus corrigidos.
* Foram corrigidos os problemas de instalação causados por nomes de usuário que começam com um traço.
* Foi corrigida a perda da configuração sourceAnchor ao executar o assistente de instalação uma segunda vez.
* Rastreamento ETW fixo para a sincronização de senha corrigido.

## <a name="104701023"></a>1.0.470.1023
Lançamento: outubro de 2014

**Novos recursos:**

* Sincronização de senha de vários locais do Active Directory para o Azure AD.
* Interface do usuário de instalação localizada para todos os idiomas do Windows Server.

**Atualizando do AADSync 1.0 GA**

Se já tiver instalado o Azure AD Sync, há uma etapa adicional que você precisa realizar caso você já tenha alterado algumas das regras de sincronização de fábrica. Depois de atualizar para a versão 1.0.470.1023, a sincronização de regras que você modificou será duplicada. Para cada regra de sincronização modificada, faça o seguinte:

1.  Localize a regra de sincronização você modificou e anote as alterações.
* Exclua a regra de sincronização.
* Localize a nova regra de sincronização que é criada pelo Azure AD Sync e reaplique as alterações.

**Permissões para a conta do Active Directory**

A conta do Active Directory deve receber permissões adicionais para poder ler os hashes de senha do Active Directory. As permissões a serem concedidas são denominadas "Replicar Alterações de Diretório" e "Replicar Todas as Alterações de Diretório". Ambas as permissões são necessárias para ler os hashes de senha.

## <a name="104190911"></a>1.0.419.0911
Lançamento: setembro de 2014

**Versão inicial do Azure AD Sync.**

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
