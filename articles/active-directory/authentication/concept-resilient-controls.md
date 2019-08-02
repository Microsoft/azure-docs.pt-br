---
title: Criar uma estratégia de gerenciamento de controle de acesso flexível - Azure Active Directory
description: Este documento fornece diretrizes sobre as estratégias que uma organização deve adotar para fornecer resiliência, visando reduzir o risco de bloqueio durante interrupções imprevistas
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 831ba47ea4e999219a6d8cf34cb5fb0fdcd1ead8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594954"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Criar uma estratégia de gerenciamento de controle de acesso resiliente com o Azure Active Directory

>[!NOTE]
> As informações contidas neste documento representam a visão atual da Microsoft Corporation acerca das questões discutidas até a data da publicação. Como a Microsoft deve reagir às dinâmicas condições do mercado, essas informações não devem ser interpretadas como um compromisso por parte da Microsoft e a Microsoft não garante a precisão de qualquer informação apresentada após a data de publicação.

As organizações que confiam em um controle de acesso único, como MFA (autenticação multifator) ou um único local de rede, para proteger seus sistemas de TI, estão suscetíveis a falhas de acesso aos seus aplicativos e recursos se esse controle de acesso único ficar indisponível ou for configurado incorretamente. Por exemplo, um desastre natural pode resultar em indisponibilidade de grandes segmentos de infraestrutura de telecomunicações ou de redes corporativas. Um interrupção dessa natureza pode impedir que os usuários finais e administradores sejam capazes de entrar.

Este documento fornece diretrizes sobre as estratégias que uma organização deve adotar para fornecer resiliência, visando reduzir o risco de bloqueio durante interrupções imprevistas com os cenários a seguir:

 1. As organizações podem aumentar a resiliência para reduzir o risco de bloqueio **antes de uma interrupção**, implementando estratégias de mitigação ou planos de contingência.
 2. As organizações podem continuar a acessar a aplicativos e recursos que escolherem **durante uma interrupção**, dispondo de estratégias de mitigação e planos de contingência em vigor.
 3. As organizações devem garantir a preservação das informações, como logs, **após uma interrupção** e antes de reverter quaisquer contingências implementadas.
 4. As organizações que ainda não implementaram estratégias de prevenção ou planos alternativos podem ser capazes de implementar **opções de emergência** para lidar com a interrupção.

## <a name="key-guidance"></a>Diretrizes principais

Há quatro observações principais neste documento:

* Evite o bloqueio do administrador usando contas de acesso de emergência.
* Implemente a autenticação Multifator usando o acesso condicional (CA) em vez de MFA por usuário.
* Reduzir o bloqueio do usuário por meio de vários controles de acesso condicional (CA).
* Reduza o bloqueio do usuário provisionando vários métodos de autenticação ou equivalentes para cada usuário.

## <a name="before-a-disruption"></a>Antes de uma interrupção

Mitigar uma interrupção real deve ser o foco primário de uma organização ao lidar com problemas de controle de acesso que podem surgir. A mitigação inclui planejamento para um evento real, além de implementar estratégias para garantir que as operações e os controles de acesso não sejam afetados durante as interrupções.

### <a name="why-do-you-need-resilient-access-control"></a>Por que é necessário um controle de acesso resiliente?

 Identidade é o plano de controle dos usuários que acessam aplicativos e recursos. O sistema de identidade controla quais usuários e em quais condições, como requisitos de autenticação ou controles de acesso, os usuários obtêm acesso aos aplicativos. Quando um ou mais requisitos de controle de acesso ou autenticação não estão disponíveis para autenticação dos usuários devido a circunstâncias imprevistas, as organizações podem enfrentar um dos problemas a seguir, ou ambos:

* **Bloqueio do administrador:** Os administradores não podem gerenciar o locatário ou serviços.
* **Bloqueio do usuário:** Os usuários não podem acessar aplicativos ou recursos.

### <a name="administrator-lockout-contingency"></a>Contingência de bloqueio de administrador

Para desbloquear o acesso de administrador para o locatário, é necessário criar contas de acesso de emergência. Essas contas de acesso de emergência, também conhecidas como contas *break glass*, permitem acesso para gerenciar a configuração do Azure AD quando os procedimentos de acesso de conta com privilégios normais não estão disponíveis. Pelo menos duas contas de acesso de emergência devem ser criadas seguindo as [recomendações de conta de acesso de emergência]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Mitigar bloqueio do usuário

 Para atenuar o risco de bloqueio do usuário, use políticas de acesso condicional com vários controles para dar aos usuários uma escolha de como eles acessarão recursos e aplicativos. Permitir que um usuário escolha entre, por exemplo, entrar com MFA **ou** entrar a partir de um dispositivo gerenciado **ou** entrar a partir da rede corporativa, se um dos controles de acesso não estiver disponível, proporciona ao usuário outras opções para continuar a trabalhar.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Incorpore os seguintes controles de acesso em suas políticas de acesso condicional existentes para a organização:

1. Provisione vários métodos de autenticação para cada usuário que dependa de canais de comunicação diferentes, por exemplo, o aplicativo Microsoft Authenticator (baseado na internet), token OATH (gerado no dispositivo) e SMS (telefônico).
2. Implante o Windows Hello para Empresas em dispositivos Windows 10 para atender aos requisitos de MFA diretamente do logon de dispositivo.
3. Use dispositivos confiáveis por meio do [Azure AD Hybrid Join](https://docs.microsoft.com/azure/active-directory/devices/overview) ou [dispositivos gerenciados pelo Microsoft Intune](https://docs.microsoft.com/intune/planning-guide). Dispositivos confiáveis melhorarão a experiência do usuário porque o próprio dispositivo confiável pode atender aos requisitos de autenticação forte de política sem um desafio MFA para o usuário. Em seguida, a MFA será necessária ao registrar um novo dispositivo e ao acessar a aplicativos ou recursos de dispositivos não confiáveis.
4. Use as políticas de proteção de identidade com base no risco do Azure AD, que impedem o acesso quando o usuário ou logon está em risco, no lugar de políticas de MFA fixas.

>[!NOTE]
> As políticas baseadas em risco requerem licenças do [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).

O exemplo a seguir descreve as políticas que você deve criar para fornecer um controle de acesso resiliente para que o usuário acesse os aplicativos e recursos deles. Neste exemplo, será necessário um grupo de segurança **AppUsers** com os usuários de destino para os quais você quer conceder acesso, um grupo nomeado **CoreAdmins** com os administradores principais e um grupo nomeado  **EmergencyAccess** com as contas de acesso de emergência.
Este conjunto de políticas de exemplo concederá aos usuários selecionados no **AppUsers** acesso aos aplicativos selecionados se eles estiverem conectando a partir de um dispositivo confiável OU fornecerá autenticação forte, por exemplo, MFA. Isso exclui contas de emergência e administradores principais.

**Conjunto de políticas de mitigação de CA:**

* Política 1: Bloquear o acesso a pessoas fora dos grupos de destino
  * Usuários e Grupos: Incluir todos os usuários. Excluir AppUsers, CoreAdmins, e EmergencyAccess
  * Aplicativos de Nuvem: Incluir todos os aplicativos
  * Condições: (Nenhuma)
  * Controles de Concessão: Bloco
* Política 2: Conceder acesso a AppUsers exigindo MFA ou dispositivo confiável.
  * Usuários e Grupos: Incluir AppUsers. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos de Nuvem: Incluir todos os aplicativos
  * Condições: (Nenhuma)
  * Controles de Concessão: Conceder acesso, exigir a autenticação multifator, exigir que o dispositivo esteja em conformidade. Para vários controles: Requer um dos controles selecionados.

### <a name="contingencies-for-user-lockout"></a>Contingências para bloqueio do usuário

Como alternativa, sua organização também pode criar políticas de contingência. Para criar políticas de contingência, é necessário definir critérios de compensação entre continuidade dos negócios, custos operacionais, custo financeiro e riscos de segurança. Por exemplo, você poderá ativar uma política de contingência apenas para um subconjunto de usuários, um subconjunto de aplicativos, um subconjunto de clientes, ou a partir de um subconjunto dos locais. Políticas de contingência concederá aos administradores e usuários finais acesso a aplicativos e recursos durante uma interrupção quando nenhum método de mitigação foi implementado.
Reconhecer sua exposição durante uma interrupção ajuda a reduzir o risco e é uma parte crítica do processo de planejamento. Para criar o plano de contingência, primeiro determine os seguintes requisitos de negócios da sua organização:

1. Determine seus aplicativos críticos com antecedência: Quais são os aplicativos aos quais você deve conceder acesso, mesmo com uma postura de riscos/segurança inferior? Crie uma lista desses aplicativos e certifique-se de que todos os outros stakeholders (lideranças jurídicas, de negócios e segurança) concordam que se todos os controle de acesso forem perdidos, esses aplicativos ainda devem continuar em execução. Isso provavelmente resultará nas categorias a seguir:
   * **Categoria 1 - aplicativos críticos** que não podem ficar indisponíveis por mais de alguns minutos, por exemplo, aplicativos que afetam diretamente a receita da organização.
   * **Categoria 2 - aplicativos importantes** que a empresa precisa que estejam acessíveis em algumas horas.
   * **Categoria 3 - aplicativos de baixa prioridade** que podem resistir a uma interrupção de alguns dias.
2. Para aplicativos na categoria 1 e 2, a Microsoft recomenda que você planeje previamente que tipo de nível de acesso quer permitir:
   * Você quer permitir acesso completo ou sessão restrita, como limitar downloads?
   * Você quer permitir o acesso a parte do aplicativo, mas não ao aplicativo inteiro?
   * Você quer permitir o acesso de trabalhador de informações e bloquear o acesso de administrador até que o controle de acesso seja restaurado?
3. Para esses aplicativos, a Microsoft também recomenda planejar quais caminhos de acesso você deliberadamente abrirá e quais deles serão fechados:
   * Você quer permitir acesso somente para navegador e bloquear clientes avançados que podem salvar os dados offline?
   * Você quer permitir acesso somente para usuários dentro da rede corporativa e manter os usuários de fora bloqueados?
   * Você quer permitir o acesso de certos países ou regiões somente durante a interrupção?
   * Você quer que as políticas para as políticas de contingência, especialmente para aplicativos críticos, falhem ou tenham êxito se um controle de acesso alternativo não estiver disponível?

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Uma política de acesso condicional de contingência é uma **desabilitado política** que omite os controles de Azure MFA, a MFA de terceiros, com base em dispositivo ou risco. Então, quando sua organização decidir ativar o plano de contingência, os administradores poderão habilitar a política e desabilitar as políticas com base em controle regulares.

>[!IMPORTANT]
> Desabilitar políticas que impõem segurança nos usuários, mesmo temporariamente, reduzirá a postura de segurança enquanto o plano de contingência estiver vigente.

* Configure um conjunto de políticas de fallback se uma interrupção em um mecanismo de controle de acesso ou um tipo de credencial afete o acesso aos seus aplicativos. Configure uma política em um estado desabilitado que requer ingresso no domínio como um controle, como um backup para uma política ativa que requer um provedor MFA de terceiros.
* Reduza o risco de atores maliciosos detectarem senhas, quando a MFA não é necessária, seguindo as práticas no white paper [diretrizes de senha](https://aka.ms/passwordguidance).
* Implante [SSPR (Redefinição de Senha de Autoatendimento do Azure AD)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) e [Proteção por Senha do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) para certificar-se de que os usuários não usem uma senha comum e os termos que você quer vetar.
* Use as políticas que restringem o acesso dentro dos aplicativos se um determinado nível de autenticação não for atingido, em vez de simplesmente fazer fallback para acesso completo. Por exemplo:
  * Configure uma política de backup que envia a declaração de sessão restrita para Exchange e SharePoint.
  * Se sua organização usa o Microsoft Cloud App Security, considere fazer fallback para uma política que envolve o MCAS e, então, o MCAS permite acesso somente leitura, mas não uploads.
* Dê um nome às suas políticas para garantir que seja fácil encontrá-las durante uma interrupção. Inclua os seguintes elementos no nome da política:
  * Um *número de rótulo* para a política.
  * Texto a ser exibido, essa política é somente para emergências. Por exemplo:  **HABILITAR EM EMERGÊNCIA**
  * A *interrupção* à qual ela se aplica. Por exemplo: **Durante Interrupção da MFA**
  * Um *número de sequência* para mostrar a ordem em que você deve ativar as políticas.
  * Os *aplicativos* aos quais ela se aplica.
  * Os *controles* aos quais ela se aplica.
  * As *condições* às quais ela se aplica.
  
Esse padrão de nomenclatura para as políticas de contingência será o seguinte: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

O exemplo a seguir: **Exemplo A - política de CA de contingência para restaurar o acesso aos aplicativos de colaboração críticos**, é uma contingência corporativa típica. Nesse cenário, a organização normalmente requer MFA para todos os acessos do Exchange Online e SharePoint Online e a interrupção, nesse caso, é que o provedor de MFA para o cliente tem uma interrupção (quer seja a MFA do Azure, provedor MFA local ou a MFA de terceiros). Essa política mitiga essa interrupção, permitindo que os usuários de destino específicos acessem esses aplicativos em dispositivos confiáveis do Windows somente quando eles estão acessando o aplicativo a partir da rede corporativa confiável deles. Ela também exclui contas de emergência e administradores principais nessas restrições. Assim, os usuários de destino terão acesso ao Exchange Online e ao SharePoint Online, enquanto outros usuários ainda não terão acesso a aplicativos devido à interrupção. Este exemplo exigirá um local de rede nomeado **CorpNetwork** e um grupo de segurança **ContingencyAccess** com os usuários de destino, um grupo nomeado **CoreAdmins** com os administradores principais e um grupo nomeado **EmergencyAccess** com as contas de acesso de emergência. A contingência requer quatro políticas para fornecer o acesso exigido. 

**Exemplo A - políticas de CA de contingência para restaurar o acesso aos aplicativos de colaboração críticos:**

* Política 1: Exigir dispositivos ingressados no domínio para Exchange e SharePoint
  * Nome: EM001 – HABILITAR EM EMERGÊNCIA: Interrupção de MFA [1/4] – Exchange SharePoint – Exigir ingresso no Azure AD Híbrido
  * Usuários e Grupos: Incluir ContingencyAccess. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos de Nuvem: Exchange Online e SharePoint Online
  * Condições: Any
  * Controles de Concessão: Requer um dispositivo ingressado no domínio
  * Estado: Desabilitado
* Política 2: Bloquear as plataformas que não sejam Windows
  * Nome: EM002 – HABILITAR EM EMERGÊNCIA: Interrupção de MFA [2/4] – Exchange SharePoint – Bloquear acesso, exceto do Windows
  * Usuários e Grupos: Incluir todos os usuários. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos de Nuvem: Exchange Online e SharePoint Online
  * Condições: Plataforma de dispositivo inclui todas as plataformas, excluir Windows
  * Controles de Concessão: Bloco
  * Estado: Desabilitado
* Política 3: Bloquear redes que não sejam CorpNetwork
  * Nome: EM003 – HABILITAR EM EMERGÊNCIA: Interrupção de MFA [3/4] – Exchange SharePoint – Bloquear acesso, exceto da rede corporativa
  * Usuários e Grupos: Incluir todos os usuários. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos de Nuvem: Exchange Online e SharePoint Online
  * Condições: Locais incluem qualquer local, excluir CorpNetwork
  * Controles de Concessão: Bloco
  * Estado: Desabilitado
* Política 4: Bloquear EAS explicitamente
  * Nome: EM004 – HABILITAR EM EMERGÊNCIA: Interrupção de MFA [4/4] – Exchange – Bloquear EAS para todos os usuários
  * Usuários e Grupos: Incluir todos os usuários
  * Aplicativos de Nuvem: Incluir Exchange Online
  * Condições: Aplicativos do cliente: Exchange Active Sync
  * Controles de Concessão: Bloco
  * Estado: Desabilitado

Ordem de ativação:

1. Exclua ContingencyAccess, CoreAdmins e EmergencyAccess da política de MFA existente. Verifique se um usuário em ContingencyAccess pode acessar o SharePoint Online e o Exchange Online.
2. Habilitar política 1: Verifique se os usuários em dispositivos ingressados no domínio que não estão nos grupos de exclusão são capazes de acessar o Exchange Online e o SharePoint Online. Verifique se os usuários no grupo de exclusão podem acessar o SharePoint Online e Exchange de qualquer dispositivo.
3. Habilitar política 2: Verifique se os usuários que não estão no grupo de exclusão não podem acessar o SharePoint Online e o Exchange Online de seus dispositivos móveis. Verifique se os usuários no grupo de exclusão podem acessar o SharePoint e o Exchange de qualquer dispositivo (Windows/iOS/Android).
4. Habilitar política 3: Verifique se os usuários que não estão nos grupos de exclusão não podem acessar o SharePoint e o Exchange fora da rede corporativa, mesmo com uma máquina ingressada no domínio. Verifique se os usuários no grupo de exclusão podem acessar o SharePoint e o Exchange de qualquer rede.
5. Habilitar política 4: Verifique se todos os usuários não podem acessar o Exchange Online de aplicativos de email nativo em dispositivos móveis.
6. Desabilite a política MFA existente para o SharePoint Online e o Exchange Online.

Neste exemplo, **Exemplo B - políticas de CA de contingência para permitir o acesso móvel à Salesforce**, o acesso de um aplicativo de negócios é restaurado. Nesse cenário, o cliente normalmente requer o acesso de funcionários de vendas à Salesforce (configurada para logon único com o Azure AD) de dispositivos móveis a serem permitidos apenas de dispositivos em conformidade. A interrupção nesse caso é que há um problema com a avaliação da conformidade do dispositivo e a interrupção está acontecendo durante um tempo sensível em que a equipe de vendas precisa de acesso à Salesforce para fechar negócios. Essas políticas de contingência concederão aos usuários críticos o acesso à Salesforce a partir de um dispositivo móvel para possibilitar que eles continuem a fechar negócios e não interrompam os negócios. Neste exemplo, **SalesforceContingency** contém todos os funcionários de vendas que precisam manter o acesso e **SalesAdmins** contém os administradores necessários da Salesforce.

**Exemplo B - políticas de CA de contingência:**

* Política 1: Bloquear todos que não estejam na equipe SalesContingency
  * Nome: EM001 – HABILITAR EM EMERGÊNCIA: Interrupção de Conformidade do Dispositivo [1/2] – Salesforce – Bloquear todos os usuários, exceto SalesforceContingency
  * Usuários e Grupos: Incluir todos os usuários. Excluir SalesAdmins e SalesforceContingency
  * Aplicativos de Nuvem: Salesforce.
  * Condições: Nenhum
  * Controles de Concessão: Bloco
  * Estado: Desabilitado
* Política 2: Bloquear a equipe de vendas de qualquer plataforma que não seja móvel (para reduzir a área da superfície do ataque)
  * Nome: EM002 – HABILITAR EM EMERGÊNCIA: Interrupção de Conformidade do Dispositivo [2/2] – Salesforce – Bloquear todas as plataformas, exceto iOS e Android
  * Usuários e Grupos: Incluir SalesforceContingency. Excluir SalesAdmins
  * Aplicativos de Nuvem: Salesforce
  * Condições: Plataforma de dispositivo inclui todas as plataformas, excluir iOS e Android
  * Controles de Concessão: Bloco
  * Estado: Desabilitado

Ordem de ativação:

1. Exclua SalesAdmins e SalesforceContingency da política de conformidade de dispositivo existente para a Salesforce. Verifique se um usuário do grupo SalesforceContingency pode acessar a Salesforce.
2. Habilitar política 1: Verifique se os usuários fora de SalesContingency não pode acessar a Salesforce. Verifique se os usuários em SalesAdmins e SalesforceContingency podem acessar a Salesforce.
3. Habilitar política 2: Verifique se os usuários no grupo de SalesContingency não podem acessar a Salesforce dos laptops Windows/Mac deles, mas ainda podem acessar dos dispositivos móveis deles. Verifique se SalesAdmin ainda pode acessar a Salesforce de qualquer dispositivo.
4. Desabilite a política de conformidade do dispositivo existente para a Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Implantar sincronização de hash de senha, mesmo se você for federado ou usar a autenticação de passagem

O bloqueio do usuário também pode ocorrer se as condições a seguir forem verdadeiras:

- Sua organização usa uma solução de identidade híbrida com autenticação de passagem ou federação.
- Seus sistemas de identidade locais (como o Active Directory, AD FS ou um componente dependente) não estão disponíveis. 
 
Para ser mais resiliente, sua organização deve [habilitar a sincronização de hash de senha](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), pois ela permite [mudar para usar a sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) se os sistemas de identidade locais estiverem inoperantes.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft
 Habilite a sincronização de hash de senha usando o assistente do Azure AD Connect, independentemente se sua organização usa a autenticação de passagem ou federação.

>[!IMPORTANT]
> Não é necessário converter os usuários de autenticação federada para gerenciada para usar a sincronização de hash de senha.

## <a name="during-a-disruption"></a>Durante uma interrupção

Se você optou por implementar um plano de mitigação, será possível sobreviver automaticamente a uma interrupção de controle de acesso único. No entanto, se você optou por criar um plano de contingência, será possível ativar suas políticas de contingência durante a interrupção de controle de acesso:

1. Habilite as políticas de contingência que concedam a usuários de destino o acesso a aplicativos específicos, a partir de redes específicas.
2. Desabilite as políticas com base em controle regulares.

### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Dependendo de quais mitigações ou contingências são usadas durante uma interrupção, sua organização pode estar concedendo acesso com apenas senhas. Nenhuma garantia é um risco de segurança considerável que deve ser avaliado com cuidado. Organizações devem:

1. Como parte da sua estratégia de controle de alterações, documente todas as alterações e o estado anterior para poder reverter quaisquer contingências implementadas, tão logo os controles de acesso estejam totalmente operacionais.
2. Suponha que atores mal-intencionados tentarão coletar senhas por meio de ataques de pulverização de senha ou de phishing enquanto você tiver desabilitado a MFA. Além disso, os atores maliciosos podem já ter senhas que anteriormente não concediam acesso a qualquer recurso que podem ser tentadas durante essa janela. Para usuários críticos como executivos, você pode mitigar parcialmente esse risco, redefinindo as senhas deles antes de desabilitar a MFA para eles.
3. Arquive todas as atividades de entrada para identificar quem acessa o que durante o tempo em que MFA estava desabilitada.
4. [Triagem de todos os eventos de risco relatados](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) durante essa janela.

## <a name="after-a-disruption"></a>Após uma interrupção

Desfaça as alterações feitas como parte do plano de contingência ativado quando for restaurado o serviço que causou a interrupção. 

1. Habilitar as políticas regulares
2. Desabilite as políticas de contingência. 
3. Reverta todas as outras alterações feitas e documentadas durante a interrupção.
4. Se você usou uma conta de acesso de emergência, lembre-se de regenerar as credenciais e proteger fisicamente os detalhes das novas credenciais como parte dos procedimentos de conta de acesso de emergência.
5. Continue a [triagem de todos os eventos de risco relatados](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) após a interrupção por atividade suspeita.
6. Revogue todos os tokens de atualização que foram emitidos [usando o PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) como destino de um conjunto de usuários. A revogação de todos os tokens de atualização é importante para contas com privilégios usadas durante a interrupção e fazer isso as forçará a autenticarem-se novamente e cumprirem o controle das políticas restauradas.

## <a name="emergency-options"></a>Opções de emergência

 No caso de emergência e sua organização antes não eram implementar um plano de contingência ou atenuação e siga as recomendações a [contingências para bloqueio do usuário](#contingencies-for-user-lockout) seção se eles já usam o acesso condicional políticas para impor o MFA.
Se sua organização estiver usando políticas herdadas de MFA por usuário, você pode considerar a seguinte alternativa:

1. Se você tiver o endereço IP de saída de rede corporativa, é possível adicioná-las como IPs confiáveis para habilitar a autenticação somente para a rede corporativa.
   1. Se você não tem o inventário de endereços IP de saída ou se precisa habilitar o acesso dentro e fora da rede corporativa, é possível adicionar todo o espaço de endereços IPv4 como IPs confiáveis especificando 0.0.0.0/1 e 128.0.0.0/1.

>[!IMPORTANT]
 > Se você expandir os endereços IP confiáveis para desbloquear o acesso, os eventos de risco associados a endereços IP (por exemplo, viagem impossível ou localizações desconhecidas) não serão gerados.

>[!NOTE]
 > Configurar [IPs confiáveis](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) para a MFA do Azure só está disponível com [licenças do Azure AD Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Saiba mais

* [Documentação de Autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Gerenciar contas administrativas de acesso de emergência no Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Configurar locais nomeados no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Como configurar dispositivos adicionados ao Azure Active Directory híbrido](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Guia de implantação do Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Diretrizes de senha - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Quais são as condições no acesso condicional do Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Quais são os controles de acesso no acesso condicional do Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
