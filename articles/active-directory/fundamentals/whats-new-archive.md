---
title: Arquivos de Novidades - Active Directory do Azure | Microsoft Docs
description: As notas da versão Novidades na seção Visão geral deste conjunto de conteúdo contêm seis meses de atividade. Após 6 meses, os itens são removidos do artigo principal e colocados neste artigo.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6935d2a89968b55dec1626d23b3f290261cf66f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655437"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Arquivar para Novidades no Active Directory do Azure

O artigo principal [ O que há de novo nas notas de versão ](whats-new.md) contém os últimos 6 meses de informações, enquanto este artigo inclui todas as informações mais antigas.

As notas de lançamento do Novidades fornecem informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

---

## <a name="novemberdecember-2018"></a>Novembro/dezembro de 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Os usuários removidos do escopo de sincronização não alternam mais para contas somente nuvem

**Tipo:** Correção  
**Categoria de serviço:** Gerenciamento de Usuários  
**Funcionalidade do produto:** Diretório

>[!Important]
>Nós ouvimos e entendemos sua frustração devido a essa correção. Portanto, revertemos essa alteração até o momento em que possamos facilitar a correção, para que você possa implementar em sua organização.

Corrigimos um bug no qual o sinalizador DirSyncEnabled de um usuário seria alternado erroneamente para **Falso** quando o objeto de AD DS (Active Directory Domain Services) fosse excluído do escopo de sincronização e, em seguida, movido para a Lixeira no Azure AD no ciclo de sincronização a seguir. Como resultado dessa correção, se o usuário for excluído do escopo de sincronização e, em seguida, restaurado da Lixeira do Azure AD, a conta do usuário permanecerá como sincronizada do AD local, conforme o esperado, e não poderá ser gerenciada na nuvem, já que a SoA (origem da autoridade) permanecerá como AD local.

Antes dessa correção, havia um problema quando o sinalizador DirSyncEnabled era alternado para Falso. Isso resultava na impressão errada de que essas contas eram convertidas em objetos somente nuvem e que as contas poderiam ser gerenciadas na nuvem. No entanto, as contas ainda retinham a SoA como propriedades locais e todas as propriedades sincronizadas (atributos de sombra) provenientes do AD local. Essa condição causou vários problemas no Azure AD e em outras cargas de trabalho de nuvem (como o Exchange Online) que esperavam tratar essas contas como sincronizadas do AD, mas agora se comportavam como contas somente nuvem.

No momento, a única maneira de converter verdadeiramente uma conta do AD de sincronizada para uma conta somente nuvem é desabilitando o DirSync no nível do locatário, que disparará uma operação de back-end para transferir a SoA. Esse tipo de alteração da SoA exige (mas não limita-se a) limpar todos os atributos relacionados locais (como LastDirSyncTime e atributos de sombra) e enviar um sinal para outras cargas de trabalho de nuvem para que o respectivo objeto também seja convertido em uma conta somente nuvem.

Essa correção, consequentemente, impede atualizações diretas no atributo ImmutableID de um usuário sincronizado do AD, que em alguns cenários no passado foram necessários. Por padrão, o ImmutableID de um objeto no Azure AD, como o nome indica, é imutável. Os novos recursos implementados no Azure AD Connect Health e no cliente de Sincronização do Azure AD Connect estão disponíveis para tratar esses cenários:

- **Atualização do ImmutableID em grande escala para muitos usuários em uma abordagem em etapas**
  
  Por exemplo, você precisa fazer uma migração longa entre florestas do AD DS. Solução: Use o Azure AD Connect para **Configurar Âncora de Origem** e, enquanto o usuário migra, copie os valores ImmutableID existentes do Azure AD para o atributo ms-DS-Consistency-Guid do usuário local do AD DS da nova floresta. Para obter mais informações, consulte [Usar ms-DS-ConsistencyGuid como sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Atualizações do ImmutableID em grande escala para muitos usuários em um único disparo**

  Por exemplo, ao implementar o Azure AD Connect, você comete um erro e agora precisa alterar o atributo SourceAnchor. Solução: Desabilite o DirSync no nível do locatário e limpe todos os valores ImmutableID inválidos. Para obter mais informações, consulte [Desativar a sincronização de diretório do Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Corresponder novamente o usuário local com um usuário existente no Azure AD** Por exemplo, um usuário que foi recriado no AD DS gera uma duplicata na conta do Azure AD, em vez de correspondê-la novamente com uma conta do Azure AD (objeto órfão) existente. Solução: Use o Azure AD Connect Health no portal do Azure para remapeamento da Âncora de Origem/ImmutableID. Para obter mais informações, consulte [Cenário de objeto órfão](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Alterações da falha: Atualizações no esquema de auditoria e de logs de entrada por meio do Azure Monitor

**Tipo:** Recurso alterado  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

No momento, estamos publicando os fluxos de log de Auditoria e Entrada por meio do Azure Monitor, para que você possa integrar os arquivos de log às suas ferramentas SIEM ou ao Log Analytics. Com base em seus comentários e em preparação para o comunicado de disponibilidade geral desse recurso, estamos fazendo as alterações a seguir em nosso esquema. Essas alterações de esquema e suas atualizações de documentação relacionadas ocorrerão até a primeira semana de janeiro.

#### <a name="new-fields-in-the-audit-schema"></a>Novos campos no esquema de Auditoria
Estamos adicionando um novo campo **Tipo de Operação**, para fornecer o tipo de operação executada no recurso. Por exemplo, **Adicionar**, **Atualizar** ou **Excluir**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campos alterados no esquema de Auditoria
Os seguintes campos estão sendo alterados no esquema de Auditoria:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|Categoria|Esse era o campo **Nome do Serviço**. Agora ele passou a ser o campo **Categorias de Auditoria**. **Nome do Serviço** foi renomeado para o campo **loggedByService**.|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|<ul><li>Gerenciamento de Usuários</li><li>Gerenciamento de grupos</li><li>Gerenciamento de aplicativos</li></ul>|
|targetResources|Inclui **TargetResourceType** no nível superior.|&nbsp;|<ul><li>Política</li><li>Aplicativo</li><li>User</li><li>Grupo</li></ul>|
|loggedByService|Fornece o nome do serviço que gerou o log de auditoria.|Nulo|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|
|Resultado|Fornece o resultado dos logs de auditoria. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li></ul>|<ul><li>Êxito</li><li>Failure</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campos alterados no esquema de Entrada
Os seguintes campos estão sendo alterados no esquema de Entrada:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Esse era o campo **conditionalaccessPolicies**. Agora ele passou a ser o campo **appliedConditionalAccessPolicies**.|Sem alteração|Sem alteração|
|conditionalAccessStatus|Fornece o resultado do Status da Política de Acesso Condicional na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Êxito</li><li>Failure</li><li>Não aplicado</li><li>Desabilitado</li></ul>|
|appliedConditionalAccessPolicies: resultado|Fornece o resultado do Status da Política de Acesso Condicional individual na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Êxito</li><li>Failure</li><li>Não aplicado</li><li>Desabilitado</li></ul>|

Para obter mais informações sobre o esquema, confira [Interpretar o esquema de logs de auditoria do Azure AD no Azure Monitor (versão prévia)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Melhorias de Proteção de Identidade no modelo de machine learning supervisionado e no mecanismo de pontuação de risco

**Tipo:** Recurso alterado  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Pontuações de risco

As melhorias no mecanismo e avaliação de risco de entrada e de usuário relacionadas à Proteção de Identidade podem ajudar a melhorar a cobertura e a precisão de risco de usuário. Os administradores talvez observem que o nível de risco de usuário deixou de estar vinculado ao nível de risco de detecções específicas e que há um aumento no número e no nível de eventos de entrada de risco.

As detecções de risco agora são avaliadas pelo modelo de machine learning supervisionado, que calcula o risco de usuário usando recursos adicionais de entradas do usuário e um padrão de detecções. Com base nesse modelo, o administrador poderá encontrar usuários com pontuações de alto risco, mesmo se as detecções associadas a esse usuário forem de risco baixo ou médio. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Os administradores podem redefinir suas próprias senhas usando o aplicativo Microsoft Authenticator (versão prévia pública)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha por autoatendimento  
**Funcionalidade do produto:** Autenticação de usuário

Os administradores do Azure AD agora podem redefinir suas próprias senhas usando as notificações do aplicativo Microsoft Authenticator ou um código em qualquer aplicativo de autenticador móvel ou token de hardware. Para redefinir suas próprias senhas, os administradores agora poderão usar dois dos seguintes métodos:

- Notificação do aplicativo Microsoft Authenticator

- Outro aplicativo de autenticador móvel/código de token de hardware

- Email

- chamada telefônica

- mensagem de texto

Para obter mais informações sobre como usar o aplicativo Microsoft Authenticator para redefinir senhas, confira [Redefinição de senha de autoatendimento do Azure AD – aplicativo móvel e SSPR (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nova função Administrador de Dispositivo de Nuvem do Azure AD (versão prévia pública)

**Tipo:** New recurso  
**Categoria de serviço:** Registro e Gerenciamento de Dispositivos  
**Funcionalidade do produto:** Controle de acesso

Os administradores podem atribuir usuários à nova função Administrador de Dispositivo de Nuvem para executar tarefas de administrador de dispositivo de nuvem. Os usuários que recebem a função Administradores de Dispositivo de Nuvem podem habilitar, desabilitar e excluir dispositivos no Azure AD, além de poder ler as chaves do BitLocker do Windows 10 (se houver) no portal do Azure.

Para obter mais informações sobre funções e permissões, confira [Atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gerenciar seus dispositivos usando o novo carimbo de data/hora de atividade no Azure AD (versão prévia pública)

**Tipo:** New recurso  
**Categoria de serviço:** Registro e Gerenciamento de Dispositivos  
**Funcionalidade do produto:** Gerenciamento do Ciclo de Vida do Dispositivo

Sabemos que, ao longo do tempo, você deve atualizar e desativar dispositivos de sua organização no Azure AD, para evitar que dispositivos obsoletos em seu ambiente. Para ajudar com esse processo, o Azure AD agora atualiza seus dispositivos com um novo carimbo de data/hora de atividade, ajudando você a gerenciar o ciclo de vida do dispositivo.

Para obter mais informações sobre como obter e usar esse carimbo de data/hora, confira [Como: Gerenciar os dispositivos obsoletos no Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Os administradores podem exigir que os usuários aceitem os termos de uso em cada dispositivo

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Governança
 
Os administradores agora podem ativar o **exigem o consentimento em todos os dispositivos dos usuários** opção para exigir que os usuários aceitem os termos de uso em todos os dispositivos estão usando em seu locatário.

Para obter mais informações, consulte o [termos de acordo com o dispositivo da seção de uso dos termos do uso de recurso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Os administradores podem configurar os termos de uso para expirar com base em um agendamento recorrente

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Governança
 

Os administradores agora podem ativar o **expirar consentimentos** expirar de opção para tornar os termos de uso para todos os seus usuários com base em seu agendamento recorrente especificado. O agendamento pode ser anual, semestral, trimestral ou mensalmente. Depois que os termos de uso expiram, os usuários devem aceitem novamente.

Para obter mais informações, consulte o [adicionar termos de uso seção dos termos do Azure Active Directory do uso de recurso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Os administradores podem configurar os termos de uso para expirar com base no agendamento de cada usuário

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Governança

Os administradores agora podem especificar uma duração que o usuário deve aceitem novamente termos de uso. Por exemplo, os administradores podem especificar que os usuários devem aceitem novamente termos de uso a cada 90 dias.

Para obter mais informações, consulte o [adicionar termos de uso seção dos termos do Azure Active Directory do uso de recurso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Novos emails do Azure AD PIM (Privileged Identity Management) para funções do Azure Active Directory

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Os clientes que usam o Azure AD PIM (Privileged Identity Management) agora podem receber um email de resumo semanal, incluindo as seguintes informações para os últimos sete dias:

- Visão geral sobre as principais atribuições de função qualificadas e permanentes

- Número de usuários que ativaram funções

- Número de usuários atribuídos a funções no PIM

- Número de usuários atribuídos a funções fora do PIM

- Número de usuários que passaram a ser "permanentes" no PIM

Para obter mais informações sobre o PIM e as notificações por email disponíveis, confira [Notificações por email no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>O licenciamento baseado em grupo agora está disponível

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Diretório

O licenciamento baseado em grupo está fora da versão prévia pública e agora está disponível. Como parte dessa versão geral, tornamos esse recurso mais escalonável e adicionamos a capacidade de reprocessar as atribuições de licenciamento baseado em grupo para um único usuário e a capacidade de usar o licenciamento baseado em grupo com licenças do Office 365 E3/A3.

Para obter mais informações sobre o licenciamento baseado em grupo, confira [O que é o licenciamento baseado em grupo no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Novos Aplicativos Federados disponíveis na galeria de aplicativos do Azure AD – novembro de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em novembro de 2018, adicionamos estes 26 novos aplicativos com suporte de Federação à galeria de aplicativos:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps – Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps – UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/) e [CRAFTS – Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Outubro de 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Os Logs do Azure AD agora funcionam com o Azure Log Analytics (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Temos o prazer de anunciar que agora você pode encaminhar seus logs do Azure AD ao Azure Log Analytics! Esse recurso mais solicitados ajuda dão acesso ainda melhor a análise para seu negócio, operações e segurança, bem como uma maneira de ajudar a monitorar sua infra-estrutura. Para obter mais informações, consulte o [logs de atividades de diretório do Azure Active Directory no Azure Log Analytics agora disponível](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - outubro de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros

Em outubro de 2018, adicionamos esses 14 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Meus Pontos de Prêmio](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Teclado de discagem, [ON24 Ambiente Virtual](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Controle do Workspot](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notificações por Email de serviços de domínio do AD do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services

Os Serviços de Domínio do Azure AD fornecem alertas no portal do Azure sobre configurações incorretas ou problemas com o domínio gerenciado. Esses alertas incluem guias passo a passo para que você possa tentar corrigir os problemas sem precisar entrar em contato com o suporte.

A partir de outubro, você poderá personalizar as configurações de notificação do seu domínio gerenciado para que, quando novos alertas ocorrerem, um e-mail seja enviado a um grupo designado de pessoas, eliminando a necessidade de verificar constantemente o portal em busca de atualizações.

Para obter mais informações, consulte [Configurações de notificação nos Serviços de Domínio do AD do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD portal dá suporte ao uso do domínio ForceDelete API para excluir os domínios personalizados 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de diretórios  
**Funcionalidade do produto:** Diretório

Temos o prazer de anunciar que agora você pode usar o domínio ForceDelete API para excluir nomes de domínios personalizados renomeando referências, como usuários, grupos e aplicativos de seu nome de domínio personalizado (contoso.com) volta para o (de nome de domínio padrão inicial de forma assíncrona Contoso.onmicrosoft.com).

Essa alteração ajuda você a excluir mais rapidamente seus nomes de domínio personalizados se sua organização não usar mais o nome ou se você precisar usar o nome de domínio com outro AD do Azure.

Para obter mais informações, consulte [Excluir um nome de domínio personalizado](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Setembro de 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Permissões de função de administrador atualizadas para grupos dinâmicos

**Tipo:** Correção  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Corrigimos um problema para que funções de administrador específicas agora possam criar e atualizar regras de associação dinâmica, sem a necessidade de ser o proprietário do grupo.

As funções são:

- Administrador global

- Administrador do Intune

- Administrador de usuários

Para obter mais informações, consulte [Criar um grupo dinâmico e verificar o status](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Definições de configuração simplificada de SSO (logon único) para alguns aplicativos de terceiros

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Sabemos que configurar SSO (logon único) para aplicativos SaaS (software como serviço) pode ser um desafio devido à natureza específica de cada configuração de aplicativo. Criamos uma experiência de configuração simplificada para preencher automaticamente as definições de configuração de SSO para os seguintes aplicativos SaaS de terceiros:

- Zendesk

- ArcGIS Online

- Jamf Pro

Para começar a usar essa experiência de um único clique, acesse a página do **portal do Azure** > **configuração de SSO** do aplicativo. Para obter mais informações, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Página: Azure Active Directory – Onde seus dados estão localizados?

**Tipo:** New recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** GoLocal

Selecione a região da sua empresa na página **Azure Active Directory – Onde seus dados estão localizados** para exibir qual datacenter do Azure armazena os dados em repouso do Azure AD de todos os seus serviços do Azure AD. Você pode filtrar as informações por serviços específicos do Azure AD da região da sua empresa.

Para acessar esse recurso e obter mais informações, consulte [Azure Active Directory – Onde seus dados estão localizados](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Novo plano de implantação disponível para o painel de acesso Meus Aplicativos

**Tipo:** New recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO

Confira o novo plano de implantação que está disponível para o painel de acesso Meus Aplicativos (https://aka.ms/deploymentplans).
O painel de acesso Meus Aplicativos fornece aos usuários um único lugar para localizar e acessar os aplicativos. Esse portal também fornece aos usuários oportunidades de autoatendimento, como solicitar acesso a aplicativos e grupos ou gerenciamento de acesso a esses recursos em nome de outros.

Para obter mais informações, consulte [O que é o portal Meus Aplicativos?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nova guia de Solução de problemas e Suporte na página de entradas de Logs do portal do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

A nova guia **Solução de problemas e Suporte** na página **Entradas** do portal do Azure serve para ajudar administradores e engenheiros de suporte a solucionar problemas relacionados a entradas do Azure AD. Esta nova guia oferece o código de erro, a mensagem de erro e as recomendações de correção (caso haja alguma) para ajudar a resolver o problema. Se você não conseguir resolver o problema, também oferecemos uma nova maneira de criar um tíquete de suporte usando a experiência de **Copiar para a área de transferência**, que preenche os campos **ID da Solicitação** e **Data (UTC)** do arquivo de log em seu tíquete de suporte.  

![Logs de entrada que mostra a nova guia](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Suporte aprimorado para propriedades de extensão personalizadas usadas para criar regras de associação dinâmica

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Com essa atualização, agora você pode clicar no link **Obter propriedades de extensão personalizadas** no construtor de regra dinâmico de grupo dos usuários, inserir sua ID do aplicativo exclusiva e receber a lista completa das propriedades de extensão personalizadas para usar ao criar uma regra de associação dinâmica para usuários. Essa lista também pode ser atualizada para obter as novas propriedades de extensão personalizadas do aplicativo.

Para obter mais informações sobre como usar propriedades de extensão personalizadas para regras de associação dinâmica, consulte [Propriedades de extensão e propriedades de extensão personalizadas](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos cliente aprovados para o Azure AD baseado em aplicativo o acesso condicional

**Tipo:** Plano para alteração  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Os seguintes aplicativos estão na lista de [aplicativos do cliente aprovados](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Para obter mais informações, consulte:

- [Acesso condicional baseado no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Novo suporte para redefinição de senha de autoatendimento da tela de bloqueio do Windows 7/8/8.1

**Tipo:** New recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação de usuário

Depois de configurar esse novo recurso, os usuários verão um link para redefinir a senha da tela de **bloqueio** de um dispositivo que executa o Windows 7, Windows 8 ou Windows 8.1. Ao clicar nesse link, o usuário é guiado pelo mesmo fluxo de redefinição de senha que no navegador da Web.

Para obter mais informações, consulte [Como habilitar a redefinição de senha do Windows 7, 8 e 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização não ficarão mais disponíveis para reutilização 

**Tipo:** Plano para alteração  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Um aplicativo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para isso e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades em autenticação](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Novos Aplicativos Federados disponíveis na galeria de Aplicativos do Azure AD – setembro de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em setembro de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Software de Recrutamento Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Suporte para métodos de transformações de declarações adicionais

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Apresentamos os novos métodos de transformação de declaração, ToLower () e ToUpper (), que podem ser aplicados aos tokens SAML por meio da página baseada em SAML **Configuração de logon único**.

Para obter mais informações, consulte [Como personalizar declarações emitidas no token SAML para aplicativos empresariais no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interface do usuário atualizada da configuração de aplicativo baseado em SAML (versão prévia)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Como parte da nossa interface do usuário atualizada de configuração de aplicativo baseado em SAML, você obterá:

- Uma experiência atualizada de explicação passo a passo para configurar seus aplicativos baseados em SAML.

- Maior visibilidade sobre o que está ausente ou incorreto em sua configuração.

- A capacidade de adicionar vários endereços de email para notificação de expiração de certificado.

- Novos métodos de transformação de declaração, ToLower () e ToUpper () e muito mais.

- Uma forma de carregar seu próprio token de assinatura de certificado para aplicativos empresariais.

- Uma maneira de definir o formato NameID para aplicativos SAML e uma maneira de definir o valor NameID como Extensões de diretório.

Para ativar este modo de exibição atualizado, clique no link **Experimentar nossa nova experiência** na parte superior da página **Logon único**. Para obter mais informações, confira [Tutorial: Configurar o logon único baseado em SAML para um aplicativo com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Agosto de 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Alterações em intervalos de endereços IP do Azure Active Directory

**Tipo:** Plano para alteração  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Plataforma

Estamos introduzindo intervalos de IP maiores no Azure AD, o que significa que, se você tiver configurado intervalos de endereços IP do Azure AD para firewalls, roteadores ou grupos de segurança de rede, será necessário atualizá-los. Estamos fazendo essa atualização para que você não tenha que alterar as configurações do intervalo IP, do roteador ou do grupo de segurança de rede novamente quando o Azure AD adicionar novos pontos de extremidade. 

O tráfego de rede está se movendo para esses novos intervalos nos próximos dois meses. Para continuar com o serviço ininterrupto, você deve adicionar esses valores atualizados aos seus endereços IP antes de 10 de setembro de 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

É altamente recomendável não remover os intervalos de endereços IP antigos até que todo o tráfego da rede seja movido para os novos intervalos. Para obter atualizações sobre a movimentação e saber quando você pode remover os intervalos antigos, consulte [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização não ficarão mais disponíveis para reutilização 

**Tipo:** Plano para alteração  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Um aplicativo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para isso e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades em autenticação](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gerenciamento de informações de segurança convergida para SSPR (senha de autoatendimento) e MFA (Autenticação Multifator do Microsoft Azure)

**Tipo:** New recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação de usuário
 
Esse novo recurso ajuda as pessoas a gerenciar suas informações de segurança (por exemplo, número de telefone, aplicativo móvel e assim por diante) para SSPR e MFA em um único local e a experiência; anteriormente, isso era feito em dois locais diferentes.

Essa experiência convergida também funciona para pessoas que usam SSPR ou MFA. Além disso, se sua organização não impõe registro de MFA ou SSPR, as pessoas ainda podem registrar métodos de informações de segurança de MFA ou SSPR permitidos pela sua organização no portal Meus aplicativos.

Esta é uma visualização pública opt-in. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado ou para todos os usuários em um locatário. Para obter mais informações sobre a experiência convergida, consulte o [Blog da experiência convergida](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nova configuração de cookies somente HTTP em aplicativos do proxy de aplicativo do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Há uma nova configuração chamada **Cookies somente HTTP** em seus aplicativos de proxy de aplicativo. Essa configuração ajuda a fornecer segurança adicional, pois inclui o sinalizador HTTPOnly no cabeçalho de resposta HTTP para os cookies de sessão e acesso de proxy de aplicativo, impede o acesso ao cookie de um script do lado do cliente e também impede ações como copiar ou modificar o cookie. Embora esse sinalizador não tenha sido usado anteriormente, os cookies sempre foram criptografados e transmitidos usando uma conexão SSL para ajudar a proteger contra modificações inadequadas.

Essa configuração não é compatível com aplicativos que usam os controles ActiveX, como Área de Trabalho Remota. Caso esteja nessa situação, é recomendável desativar essa configuração.

Para obter mais informações sobre a configuração Cookies somente HTTP, consulte [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>O PIM (Privileged Identity Management) para recursos do Azure dá suporte aos tipos de recurso do grupo de gerenciamento

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Agora, as configurações de ativação e atribuição Just-In-Time podem ser aplicadas aos tipos de recursos de Grupo de Gerenciamento, como você já faz para Assinaturas, Grupos de Recursos e Recursos (tais como VMs, Serviços de Aplicativos e muito mais). Além disso, qualquer pessoa com uma função que conceda acesso de administrador para um Grupo de Gerenciamento pode descobrir e gerenciar tal recurso no PIM.

Para obter mais informações sobre o PIM e os recursos do Azure, consulte [Descobrir e gerenciar recursos do Azure usando o Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>O acesso do aplicativo (versão prévia) fornece um acesso mais rápido ao portal do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Hoje, ao ativar uma função usando o PIM, as permissões podem levar mais de 10 minutos para entrar em vigor. Se você optar por usar o acesso do aplicativo, que está em visualização pública no momento, os administradores poderão acessar o portal do Azure AD assim que a solicitação de ativação for concluída.

Atualmente, o acesso do aplicativo dá suporte apenas à experiência do portal do Azure AD e aos recursos do Azure. Para obter mais informações sobre o PIM e o acesso do Aplicativo, confira [O que é o Privileged Identity Management do Azure AD?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Novos Aplicativos Federados disponíveis na galeria de Aplicativos do Azure AD – agosto de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em agosto de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (por Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>O suporte nativo ao Tableau já está disponível no Proxy de Aplicativo do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Com nossa atualização do OpenID Connect para o protocolo OAuth 2.0 Code Grant para nosso protocolo de pré-autenticação, você não precisa mais fazer nenhuma configuração adicional para usar o Tableau com Proxy de Aplicativo. Essa alteração de protocolo também ajuda o Proxy de Aplicativo a oferecer um suporte melhor a aplicativos mais modernos usando apenas redirecionamentos de HTTP, que normalmente recebem suporte em marcações HTML e JavaScript.

Para obter mais informações sobre o suporte nativo para Tableau, consulte [Proxy de Aplicativo do Azure AD agora com suporte nativo para Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Novo suporte para adicionar o Google como provedor de identidade para usuários convidados B2B no Azure Active Directory (versão prévia)

**Tipo:** New recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Ao configurar a federação com o Google em sua organização, você pode permitir que os usuários convidados do Gmail entrem em seus aplicativos e recursos compartilhados usando as respectivas contas do Google existentes, sem precisar criar uma MSA (Conta Microsoft) pessoal ou uma conta do Azure AD.

Esta é uma visualização pública opt-in. Para obter mais informações sobre a federação do Google, consulte [Adicionar o Google como provedor de identidade para usuários convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Julho de 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Melhorias nas notificações por email do Azure Active Directory

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidades
 
Os emails do Active Directory do Azure (Azure AD) agora apresentam um design atualizado, bem como alterações no endereço de email do remetente e no nome de exibição do remetente, quando enviados dos seguintes serviços:
 
- Revisões de acesso do Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Gerenciamento de identidades com privilégios do AD do Azure
- Notificação de certificado expirando do Aplicativo Enterprise
- Notificações do serviço de provisionamento do Aplicativo Enterprise
 
As notificações por email serão enviadas a partir do seguinte endereço de email e nome de exibição:

- Endereço de email: azure-noreply@microsoft.com
- Nome de exibição: Microsoft Azure
 
Para obter um exemplo de alguns dos novos designs de email e mais informações, consulte [Notificações por email no PIM do Azure AD](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Os Logs de atividade do Azure AD agora estão disponíveis por meio do Azure Monitor

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Os Logs de atividade do Azure AD agora estão disponíveis na visualização pública do Monitor do Azure (serviço de monitoramento da plataforma do Azure). O Monitor do Azure oferece retenção a longo prazo e integração perfeita, além dessas melhorias:

- Retenção de longo prazo roteando seus arquivos de log para sua própria conta de armazenamento do Azure.

- Integração perfeita do SIEM, sem exigir que você escreva ou mantenha scripts personalizados.

- Integração perfeita com suas próprias soluções personalizadas, ferramentas de análise ou soluções de gerenciamento de incidentes.

Para obter mais informações sobre esses novos recursos, consulte o [log de atividades do Azure AD no diagnóstico do Monitor do Azure agora na visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e nossa documentação, [logs de atividades do Azure Active Directory no Azure Monitor (visualização)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informações de acesso condicionais adicionadas ao relatório de entradas do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Segurança de identidade e proteção
 
Esta atualização permite ver quais políticas são avaliadas quando um usuário faz login junto com o resultado da política. Além disso, o relatório agora inclui o tipo de aplicativo cliente usado pelo usuário, para que você possa identificar o tráfego de protocolo herdado. Agora, as entradas de relatório também podem ser pesquisadas em busca de um ID de correlação, que pode ser encontrado na mensagem de erro do usuário, e pode ser usado para identificar e solucionar problemas da solicitação de entrada correspondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Veja autenticações herdadas por meio dos logs de atividades de Entradas

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório
 
Com a introdução do campo **Client App** nos logs de atividades de login, os clientes agora podem ver usuários que estão usando autenticações herdadas. Os clientes poderão acessar essas informações usando a API do MS Graph de entradas ou por meio dos logs de atividades de entrada no portal do Azure AD, onde você pode usar o controle **Aplicativo Cliente** para filtrar as autenticações herdadas. Confira a documentação para obter mais detalhes.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - julho de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em julho de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certos SSO de administrador](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Suporte Remoto Bomgar](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [ImagineVisualizar Web](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Base de Habilidades](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Novas integrações de aplicativos SaaS para provisionamento de usuários - julho de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Integração de terceiros
 
O Azure AD permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS, como Dropbox, Salesforce, ServiceNow e muito mais. Para julho de 2018, adicionamos o suporte ao aprovisionamento de usuários aos seguintes aplicativos na galeria de aplicativos do Azure AD:

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Para obter uma lista de todos os aplicativos que dão suporte ao provisionamento de usuário na Galeria do Azure AD, consulte [integração de aplicativos SaaS ao Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Conecte o Health for Sync - Uma maneira mais fácil de corrigir erros de sincronização de atributos órfãos e duplicados

**Tipo:** New recurso  
**Categoria de serviço:** AD Connect  
**Funcionalidade do produto:** Monitoramento e relatório
 
O Azure AD Connect Health apresenta a correção de autoatendimento para ajudá-lo a destacar e corrigir erros de sincronização. Esse recurso soluciona erros de sincronização de atributos duplicados e corrige objetos que são órfãos do AD do Azure. Este diagnóstico tem os seguintes benefícios:

- Diminui os erros de sincronização de atributos duplicados, fornecendo correções específicas

- Aplica uma correção para cenários dedicados do AD do Azure, resolvendo erros em uma única etapa

- Nenhuma atualização ou configuração é necessária para ativar e usar esse recurso

Para obter mais informações, consulte [Diagnosticar e corrigir erros de sincronização de atributos duplicados](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Atualizações visuais nas experiências de entrada do Azure AD e MSA

**Tipo:** Recurso alterado  
**Categoria de serviço:** AD do Azure  
**Funcionalidade do produto:** Autenticação de usuário

Atualizamos a interface do usuário para a experiência de login de serviços on-line da Microsoft, como no Office 365 e no Azure. Essa alteração torna as telas menos confusas e mais simples. Para obter mais informações sobre essa alteração, consulte as [próximas melhorias no blog de experiência de logon do Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nova versão do Azure AD Connect - julho de 2018

**Tipo:** Recurso alterado  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidades

A versão mais recente do Azure AD Connect inclui: 

- Correções de bugs e atualizações de capacidade de suporte 

- Disponibilidade geral da integração federada de Ping

- Atualizações para o cliente mais recente do SQL 2012 

Para obter mais informações sobre essa atualização, confira [Azure AD Connect: Histórico de lançamento de versões](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Atualizações para os termos de usam a interface do usuário final

**Tipo:** Recurso alterado  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Governança

Estamos atualizando a sequência de aceitação na interface do usuário final do TOU.

**Texto atual.** Para acessar os recursos do [tenantName], você deve aceitar os termos de uso.<br>**Novo texto.** Para acessar o recurso [tenantName], você deve ler os termos de uso.

**Texto atual:** A escolha de aceitação significa que você concorda com todos os termos de uso acima.<br>**Novo texto:** Clique em Aceitar para confirmar que leu e compreendeu os termos de uso.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>A autenticação de passagem dá suporte a aplicativos e protocolos herdados

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário
 
A autenticação de passagem agora suporta protocolos e aplicativos legados. As limitações a seguir agora são totalmente suportadas:

- Logins de usuário para aplicativos cliente legados do Office, Office 2010 e Office 2013, sem exigir autenticação moderna.

- Acesso ao compartilhamento de calendário e informações livres/ocupadas nos ambientes híbridos do Exchange somente no Office 2010.

- Entradas de usuário para aplicativos cliente do Skype for Business sem a necessidade de autenticação moderna.

- Entradas de usuário no PowerShell versão 1.0.

- O Programa de Inscrição de Dispositivos da Apple (Apple DEP), usando o Assistente de Configuração do iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gerenciamento de informações de segurança convergida para redefinição de senha de autoatendimento e a autenticação multifator

**Tipo:** New recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação de usuário

Esse novo recurso permite que os usuários gerenciem suas informações de segurança (por exemplo, número de telefone, endereço de email, aplicativo móvel etc.) para redefinição de senha de autoatendimento (SSPR) e autenticação de vários fatores (MFA) em uma única experiência. Os usuários não precisarão mais registrar as mesmas informações de segurança para SSPR e MFA em duas experiências diferentes. Essa nova experiência também se aplica a usuários que possuem SSPR ou MFA.

Se uma organização não estiver aplicando o registro de MFA ou SSPR, os usuários poderão registrar suas informações de segurança no portal **Meus aplicativos**. A partir daí, os usuários podem registrar qualquer método habilitado para MFA ou SSPR. 

Esta é uma visualização pública opt-in. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado de usuários ou todos os usuários em um locatário.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Use o aplicativo Microsoft Authenticator para verificar sua identidade quando você redefinir sua senha

**Tipo:** Recurso alterado  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação de usuário

Esse recurso permite que não administradores verifiquem sua identidade ao redefinir uma senha usando uma notificação ou código do Microsoft Authenticator (ou qualquer outro aplicativo autenticador). Depois que os administradores ativarem esse método de redefinição de senha de autoatendimento, os usuários que tiverem registrado um aplicativo móvel por meio de aka.ms/mfasetup ou aka.ms/setupsecurityinfo poderão usar o aplicativo móvel como um método de verificação ao redefinir a senha.

A notificação de aplicativos para dispositivos móveis só pode ser ativada como parte de uma política que exige dois métodos para redefinir sua senha.

---

## <a name="june-2018"></a>Junho de 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Aviso de alteração: Correção de segurança para o fluxo de autorização delegada para aplicativos que usam a API de Logs de Atividades do Azure AD

**Tipo:** Plano para alteração  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Devido a nossa aplicação de segurança mais forte, foi necessário fazer uma alteração nas permissões para aplicativos que usam um fluxo de autorização delegado para acessar [APIs dos Logs de Atividades do Azure AD](https://aka.ms/aadreportsapi). Essa alteração ocorrerá até **26 de junho de 2018**.

Se algum dos aplicativos usar APIs do Log de Atividades do Azure AD, siga estas etapas para garantir que o aplicativo não seja interrompido após a alteração.

**Para atualizar as permissões do aplicativo**

1. Entre no portal do Azure, selecione **Active Directory do Azure**, e em seguida, selecione **Registros de Aplicativos**.
2. Selecione seu aplicativo que usa a API de Logs de Atividade do Azure AD, selecione **Configurações**, selecione **Permissões necessárias**, e em seguida, selecione a **API do Windows Azure Active Directory**.
3. Na área **Permissões delegadas** da folha **Habilitar acesso**, selecione a caixa ao lado de **Ler dados de diretório** e, em seguida, selecione **Salvar**.
4. Selecione **Conceder permissões** e, em seguida, selecione **Sim**.
    
    >[!Note]
    >Você deve ser um administrador Global para conceder permissões ao aplicativo.

Para obter mais informações, consulte a área [ Conceder permissões ](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) dos Pré-requisitos para acessar o artigo da API de relatórios do Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Definir configurações de TLS para se conectar aos serviços do Azure AD para conformidade com o PCI DSS

**Tipo:** New recurso  
**Categoria de serviço:** N/D  
**Funcionalidade do produto:** Plataforma

O TLS (Transport Layer Security) é um protocolo que fornece privacidade e integridade de dados entre dois aplicativos de comunicação e é o protocolo de segurança mais utilizado atualmente.

O [PCI Security Standards Council](https://www.pcisecuritystandards.org/) determinou que as primeiras versões de TLS e Secure Sockets Layer (SSL) devem ser desativadas em favor de permitir protocolos de aplicativos novos e mais seguros, com conformidade a partir de **30 de junho de 2018**. Essa alteração significa que, se você se conectar aos serviços do Azure AD e exigir conformidade com o PCI DSS, deverá desabilitar o TLS 1.0. Várias versões do TLS estão disponíveis, mas o TLS 1.2 é a versão mais recente disponível para os Serviços do Active Directory do Azure. É altamente recomendável mover diretamente para o TLS 1.2 para as combinações cliente / servidor e navegador / servidor.

Os navegadores desatualizados podem não suportar versões mais recentes do TLS, como o TLS 1.2. Para ver quais versões do TLS são suportadas pelo seu navegador, acesse o site [Qualys SSL Labs](https://www.ssllabs.com/) e clique em **Test your browser**. Recomendamos que você atualize para a versão mais recente do seu navegador da Web e, de preferência, ative somente o TLS 1.2.

**Para ativar o TLS 1.2, pelo navegador**

- **Microsoft Edge e Internet Explorer (ambos são definidos usando o Internet Explorer)**

    1. Abra o Internet Explorer, selecione **Ferramentas** > **Opções da Internet**  >  **Avançadas**.
    2. Na área **Segurança**, selecione **use TLS 1.2** e, em seguida, selecione **OK**.
    3. Feche todas as janelas do navegador e reinicie o Internet Explorer. 

- **Google Chrome**

    1. Abra o Google Chrome, digite *chrome: // settings /* na barra de endereço e pressione **Enter**.
    2. Expanda as opções **Avançadas**, vá para a área **System** e selecione **Open proxy settings**.
    3. Na caixa **Propriedades da Internet**, selecione a guia **Avançado**, vá para a área **Segurança**, selecione **use TLS 1.2** e selecione **OK**.
    4. Feche todas as janelas do navegador e reinicie o Google Chrome.

- **Mozilla Firefox**

    1. Abra o Firefox, digite *about: config* na barra de endereços e, em seguida, pressione **Enter**.
    2. Pesquise pelo termo *TLS* e selecione a entrada **security.tls.version.max**.
    3. Defina o valor para **3** para forçar o navegador a usar a versão TLS 1.2 e, em seguida, selecione **OK**.

        >[!NOTE]
        >A versão 60.0 do Firefox suporta o TLS 1.3, portanto você também pode definir o valor de security.tls.version.max como **4**.

    4. Feche todas as janelas do navegador e reinicie o Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - junho de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em junho de 2018, adicionamos esses 15 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Liquidação de música](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Aplicativo de LOB habilitado para tokens](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint local](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>A proteção de senha do Azure AD está disponível na visualização pública

**Tipo:** New recurso  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Autenticação de usuário

Use a proteção de senha do Azure AD para ajudar a eliminar senhas fáceis de adivinhar do seu ambiente. A eliminação dessas senhas ajuda a diminuir o risco de comprometimento de um tipo de ataque de spray de senha.

Especificamente, o Azure AD Password Protection ajuda você a:

- Proteja as contas da sua organização no AD do Azure e no Active Directory (AD) do Windows Server. 
- Impede que os usuários usem senhas em uma lista com mais de 500 das senhas mais usadas e mais de 1 milhão de variações de substituição de caracteres dessas senhas.
- Administre a Proteção de Senha do Azure AD a partir de um único local no portal do Azure AD, tanto para o Azure AD quanto para o Windows Server AD local.

Para obter mais informações sobre o Azure AD Password Protection, consulte [Eliminar senhas incorretas em sua organização](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo "todos os convidados" acesso condicional modelo de política criado durante a termos de uso de criação

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Governança

Durante a criação de seus termos de uso, também é criado um novo modelo de política de acesso condicional para "todos os convidados" e "todos os aplicativos". Esse novo modelo de política aplica os ToU recém-criados, simplificando o processo de criação e execução para os convidados.

Para obter mais informações, consulte [Recurso de Termos de Uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "custom" criado durante a termos de uso de criação

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Governança

Durante a criação de seus termos de uso, também é criado um novo modelo de política de acesso condicional "personalizado". Esse novo modelo de política permite criar ToU e imediatamente vá para a folha de criação de política de acesso condicional, sem precisar navegar manualmente por meio do portal.

Para obter mais informações, consulte [Recurso de Termos de Uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Orientação nova e abrangente sobre a implantação da autenticação de vários fatores do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Segurança de identidade e proteção
 
Lançamos uma nova orientação passo a passo sobre como implantar a Autenticação Multifator do Azure (MFA) em sua organização.

Para visualizar o guia de implantação do MFA, vá para o repositório [Guias de Implantação de Identidade](https://aka.ms/DeploymentPlans) no GitHub. Para fornecer feedback sobre os guias de implantação, use o [formulário de Feedback do Plano de Implantação](https://aka.ms/deploymentplanfeedback). Se você tiver alguma dúvida sobre os guias de implantação, entre em contato conosco em [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>As funções de gerenciamento de aplicativos delegados do Azure AD estão em pré-visualização pública

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Controle de Acesso

Os administradores agora podem delegar tarefas de gerenciamento de aplicativos sem atribuir a função de administrador global. As novas funções e capacidades são:

- **Novas funções administrativas padrão do AD do Azure:**

    - **Administrador do Aplicativo.** Concede a capacidade de gerenciar todos os aspectos de todos os aplicativos, incluindo registro, configurações de SSO, atribuições de aplicativos e licenciamento, configurações de proxy de aplicativo e consentimento (exceto para recursos do Azure AD).

    - **Administrador do aplicativo na nuvem.** Concede todas as habilidades de Administrador do Aplicativo, exceto para o proxy de Aplicativo, porque ele não fornece acesso local.

    - **Desenvolvedor de aplicativos.** Concede a capacidade de criar registros de aplicativos, mesmo se a opção **permitir que os usuários registrem aplicativos** seja desativada.

- **Propriedade (configure o registro por aplicativo e o aplicativo por empresa, semelhante ao processo de propriedade do grupo:**
 
    - **Proprietário do registro do aplicativo.** Concede a capacidade de gerenciar todos os aspectos do registro de aplicativos de propriedade, incluindo o manifesto do aplicativo e a adição de outros proprietários.

    - **Proprietário do aplicativo corporativo.** Concede a capacidade de gerenciar muitos aspectos de aplicativos corporativos próprios, incluindo configurações de SSO, designações de aplicativos e consentimento (exceto para recursos do Azure AD).

Para obter mais informações sobre a visualização pública, consulte as [funções de gerenciamento de aplicativos delegados do Azure AD em pré-visualização pública!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. Para obter mais informações sobre funções e permissões, consulte [Atribuindo funções de administrador no Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maio de 2018

### <a name="expressroute-support-changes"></a>Alterações de suporte do ExpressRoute

**Tipo:** Plano para alteração  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Plataforma  

A oferta de Software como Serviço, como Azure AD (Azure Active Directory), foi projetada para funcionar melhor passando diretamente pela Internet, sem a necessidade do ExpressRoute ou de qualquer outro túnel VPN privado. Por isso, em **1º de agosto de 2018**, deixaremos de dar suporte ao ExpressRoute para serviços do Azure AD usando emparelhamento público do Azure e comunidades do Azure no emparelhamento da Microsoft. Quaisquer serviços afetados por essa alteração podem observar o tráfego do Azure AD alternando gradualmente do ExpressRoute para a Internet.

Enquanto estamos alterando nosso suporte, também sabemos que ainda há situações em que você poderá precisar usar um conjunto dedicado de circuitos para o tráfego de autenticação. Por esse motivo, o Azure AD continuará dando suporte a restrições de intervalo de IP por locatário usando o ExpressRoute e serviços já em emparelhamento da Microsoft com a comunidade "Outros serviços online do Office 365". Se seus serviços forem afetados, mas você precisar do ExpressRoute, deverá fazer o seguinte:

- **Se estiver no emparelhamento público do Azure.** Vá para a Microsoft peering e inscreva-se na comunidade **Outros serviços do Office 365 Online (12076: 5100)** . Para obter mais informações sobre como passar do emparelhamento público do Azure para emparelhamento da Microsoft, consulte o artigo [Mover um emparelhamento público para o emparelhamento da Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).

- **Se estiver no emparelhamento da Microsoft.** Inscreva-se na comunidade **Other Office 365 Online service (12076: 5100)** . Para obter mais informações sobre requisitos de roteamento, consulte a seção [Suporte comunidades BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) do artigo de requisitos de roteamento ExpressRoute.

Se você precisar continuar usando circuitos dedicados, fale com a equipe da sua conta da Microsoft sobre como obter autorização para usar a comunidade **Outros serviços online do Office 365 (12076:5100)** . O quadro de revisão gerenciado do MS Office verificará se você precisará desses circuitos e se reconhece as implicações técnicas de mantê-los. Assinaturas não autorizadas tentando criar filtros de rota para o Office 365 receberão uma mensagem de erro. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>APIs do Microsoft Graph para cenários administrativos para TOU

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Experiência do desenvolvedor
 
Adicionamos APIs do Microsoft Graph para a operação de administração de termos de uso do Azure AD. É possível criar, atualizar, excluir os termos de uso de objeto.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Adicionar ponto de extremidade multilocatário do Azure AD como um provedor de identidade no Azure AD B2C

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Agora, usando políticas personalizadas, você pode adicionar o ponto de extremidade comum do Azure AD como um provedor de identidade no Azure AD B2C. Isso permite que você tenha um único ponto de entrada para todos os usuários do Azure AD que estão entrando em seus aplicativos. Para obter mais informações, confira [Azure Active Directory B2C: Permitir que os usuários entrem em um provedor de identidade multilocatário do Azure AD usando políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Use URLs internas para acessar os aplicativos de qualquer lugar com nossa Extensão de Entrada Meus Aplicativos e o Proxy de Aplicativo do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO
 
Os usuários agora podem acessar aplicativos por meio de URLs internas, mesmo de fora da rede corporativa, usando a Extensão Logon Protegido dos Meus aplicativos para Azure AD. Isso funcionará com qualquer aplicativo que você tenha publicado usando o Proxy de Aplicativo do Azure AD, em qualquer navegador que também tenha a extensão de navegador do Painel de Acesso instalada. A funcionalidade de redirecionamento de URL é habilitada automaticamente quando um usuário faz logon na extensão. A extensão está disponível para fazer o download no [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) e [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – Dados na Europa para clientes europeus

**Tipo:** New recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** GoLocal

Os clientes na Europa exigem que seus dados permaneçam na Europa e não sejam replicados fora de datacenters europeus para o cumprimento da legislação europeia sobre privacidade. Este [artigo](https://go.microsoft.com/fwlink/?linkid=872328) fornece detalhes específicos sobre quais informações de identidade serão armazenadas na Europa e também sobre as informações que serão armazenadas fora dos datacenters europeus. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Novo usuário provisionando integrações de aplicativo SaaS – maio de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Integração de terceiros
 
O Azure AD permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS, como Dropbox, Salesforce, ServiceNow e muito mais. Em maio de 2018, adicionamos suporte ao provisionamento de usuários para os seguintes aplicativos na Galeria de aplicativo do Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Para obter uma lista de todos os aplicativos que dão suporte ao provisionamento de usuário na Galeria do Azure AD, confira [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>O Azure AD acessa revisões de grupos e o acesso do aplicativo agora fornece revisões recorrentes

**Tipo:** New recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança
 
A revisão de acesso de grupos e aplicativos agora está geralmente disponível como parte do Azure AD Premium P2.  Os administradores poderão configurar revisões de acesso de membros de grupos e atribuições de aplicativo para que ocorram automaticamente em intervalos regulares, como mensal ou trimestralmente.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Os logs de atividades do Azure AD (entradas e auditoria) agora estão disponíveis por meio do MS Graph

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório
 
Os logs de atividade do Azure AD, que incluem Logs de entradas e de auditoria, agora estão disponíveis por meio do MS Graph. Nós expusemos dois pontos finais através do MS Graph para acessar esses logs. Confira nossos [documentos](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) para acesso programático às APIs de relatórios do Azure AD para começar. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Aprimoramentos à experiência de resgate B2B e deixar uma organização

**Tipo:** New recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

**Resgate Just-In-Time:** Depois que você compartilha um recurso com um usuário convidado usando a API de B2B, não precisa enviar um email de convite especial. Na maioria dos casos, o usuário convidado pode acessar o recurso e será levado até a experiência de resgate a tempo. Chega de consequências devido a emails não recebidos. Chega de perguntar aos convidados: "você clicou no link de resgate que o sistema enviou?". Isso significa que quando o SPO usa o gerenciador de convites, os anexos de nuvem podem ter a mesma URL canônica para todos os usuários, internos e externos, em qualquer estado do resgate.

**Experiência de resgate moderna:** Diga adeus à página de aterrissagem de resgate em tela dividida. Os usuários verão uma experiência moderna de consentimento com a declaração de privacidade da organização convidativa, assim como fazem para aplicativos de terceiros.

**Usuários convidados podem deixar a organização:** Depois que uma relação de usuário com uma organização terminar, eles poderão deixar a organização usando autoatendimento. Não é mais necessário chamar o administrador da organização para “ser removido”; chega de criar tíquetes de suporte.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - maio de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em maio de 2018, adicionamos esses 18 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Novos guias de implantação passo a passo para o Active Directory do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Diretório
 
Diretrizes de novo, passo a passo sobre como implantar o Azure Active Directory (Azure AD), incluindo a senha de autoatendimento (SSPR) de redefinição, logon único (SSO), acesso condicional (CA), o proxy de aplicativo, provisionamento de usuário, Active Directory Federation Services (ADFS) para A autenticação de passagem (PTA) e o ADFS para sincronização de hash de senha (PHS).

Para visualizar os guias de implantação, acesse o repositório [Guias de Implantação de Identidade](https://aka.ms/DeploymentPlans) no GitHub. Para fornecer feedback sobre os guias de implantação, use o [formulário de Feedback do Plano de Implantação](https://aka.ms/deploymentplanfeedback). Se você tiver alguma dúvida sobre os guias de implantação, entre em contato conosco em [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Pesquisa de Aplicativos Empresariais – Carregar Mais Aplicativos

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
Problemas para localizar seus aplicativos/entidades de serviço? Adicionamos a capacidade de carregar mais aplicativos em sua lista Aplicativos corporativos – todos os aplicativos. Por padrão, mostramos 20 aplicativos. Agora você pode clicar em **Carregar mais** para visualizar aplicativos adicionais. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>O lançamento de maio do AADConnect contém uma prévia pública da integração com PingFederate, atualizações de segurança importantes, muitas correções de bugs e novas e ótimas novas ferramentas de solução de problemas. 

**Tipo:** Recurso alterado  
**Categoria de serviço:** AD Connect  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidades
 
O lançamento de maio do AADConnect contém uma prévia pública da integração com PingFederate, atualizações de segurança importantes, muitas correções de bugs e novas e ótimas novas ferramentas de solução de problemas. Você pode encontrar as notas de versão [aqui](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Revisões de acesso do Azure AD: aplicação automática

**Tipo:** Recurso alterado  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança

As revisões de acesso de grupos e aplicativos agora estão disponíveis como parte do Azure AD Premium P2. Um administrador pode configurar para aplicar as alterações do revisor automaticamente a esse grupo ou aplicativo quando a revisão de acesso for concluída. O administrador também pode especificar o que acontece com o acesso continuado do usuário se os revisores não responderem, removerem o acesso, manterem o acesso ou receberem recomendações do sistema. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>Os tokens de ID não podem ser mais retornados usando a consulta response_mode para novos aplicativos. 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário
 
Os aplicativos criados em ou após 25 de abril de 2018 não poderão mais solicitar um **id_token** usando a **consulta** response_mode.  Isso coloca o Azure AD de acordo com as especificações de OIDC e ajuda a reduzir a superfície de ataque dos aplicativos.  Aplicativos criados antes de 25 de abril de 2018 não são impedidos de usar a **consulta** response_mode com um response_type de **id_token**.  O erro retornado, ao solicitar um id_token do AAD, é **AADSTS70007: 'query' não é um valor de 'response_mode' para solicitar um token**.

O **fragmento** e **form_post** response_modes continuam funcionando - ao criar novos objetos de aplicativo (por exemplo, para uso do App Proxy), garanta o uso de um desses response_modes antes que eles criem um novo aplicativo.  

---
 
## <a name="april-2018"></a>Abril de 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Os Tokens de Acesso do Azure AD B2C estão disponíveis ao público

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C 

Agora você pode acessar APIs da Web protegidas pelo Azure AD B2C usando tokens de acesso. O recurso está mudando da versão prévia pública para GA. A experiência da interface do usuário para configurar aplicativos do Azure AD B2C e APIs da Web foi aprimorada e outras pequenas melhorias foram feitas.
 
Para obter mais informações, confira [Azure AD B2C: Solicitando tokens de acesso](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testar configuração de logon único para aplicativos baseados em SAML

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Ao configurar aplicativos SSO baseados em SAML, você pode testar a integração na página de configuração. Se você encontrar um erro durante o login, poderá fornecer o erro na experiência de teste e o Azure AD fornecerá etapas de resolução para resolver o problema específico.

Para obter mais informações, consulte:

- [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Como depurar o logon único baseado em SAML em aplicativos no Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Termos do Azure AD de uso agora têm relatórios por usuário

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Conformidade
 
Agora os administradores podem selecionar um Termo de Uso determinado e ver todos os usuários que consentiram com ele e em que data/hora isso aconteceu.

Para obter mais informações, consulte o [Azure AD terms of use feature](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use) (Recurso de termos de uso do Azure AD).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: IP de risco para proteção de bloqueio de extranet do AD FS 

**Tipo:** New recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Monitoramento e relatório

Agora o Connect Health é compatível com a capacidade de detectar endereços IP que excedem um limite de logons U/P com falha por hora ou diariamente. As funcionalidades fornecidas por esse recurso são:

- Relatório completo que mostra o endereço IP e o número de logons com falha gerados por hora/diariamente com limite personalizável.
- Alertas de email que mostram quando um endereço IP específico excedeu o limite de logons U/P com falha por hora/diariamente.
- Uma opção de download para fazer uma análise detalhada dos dados

Para obter mais informações, consulte [Risky IP Report](https://aka.ms/aadchriskyip) (Relatório IP arriscado).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Configuração fácil do aplicativo com arquivos ou URL de metadados

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Na página Aplicativos empresariais, os administradores podem carregar um arquivo de metadados SAML para configurar o logon baseado em SAML para um aplicativo da Galeria e não da galeria do AAD.

Além disso, é possível usar a URL de metadados de federação do aplicativo Azure AD para configurar o SSO com o aplicativo direcionado.

Para obter mais informações, consulte [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Agora os Termos de uso do Azure AD já estão disponíveis

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Conformidade
 

Os termos de uso do AD do Azure foram movidos da visualização pública para disponibilidade geral.

Para obter mais informações, consulte o [Azure AD terms of use feature](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use) (Recurso de termos de uso do Azure AD).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Permitir ou bloquear convites para usuários B2B de organizações específicas

**Tipo:** New recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 

Agora é possível especificar com quais organizações parceiras você deseja compartilhar e colaborar na Colaboração B2B do Azure AD. Para fazer isso, é possível optar por criar a lista de permissão ou negação de domínios específica. Quando um domínio é bloqueado usando essas funcionalidades, os funcionários não podem mais enviar convites para pessoas nesse domínio.

Isso ajuda a controlar o acesso aos recursos e permite uma experiência perfeita para usuários aprovados.

Esse recurso de colaboração B2B está disponível para todos os clientes do Azure Active Directory e pode ser usado em conjunto com recursos do Azure AD Premium, como a proteção de identidade e acesso condicional para um controle mais granular de quando e como os usuários de negócios externos entrar no e obter acesso.

Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros

Em abril de 2018, adicionamos esses 13 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

Criterion HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (local)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial), [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Permitir acesso a seus aplicativos locais a usuários B2B no Azure AD (versão prévia pública)

**Tipo:** New recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Como uma organização que usa recursos de colaboração B2B do Microsoft Azure AD (Azure Active Directory) para convidar usuários convidados de organizações parceiras para o Microsoft Azure AD, agora é possível fornecer a esses usuários B2B acesso a aplicativos locais. Esses aplicativos locais podem usar a autenticação baseada em SAML ou a IWA (Autenticação Integrada do Windows) com KCD (delegação restrita de Kerberos).

Para obter mais informações, consulte [Conceda aos usuários B2B no acesso do Azure AD aos seus aplicativos locais](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Obter tutoriais de integração de SSO do Azure Marketplace

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Integração de terceiros

Se um aplicativo listado no [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) for compatível com o logon único baseado em SAML, clicar em **Obter agora** fornecerá o tutorial de integração associado a esse aplicativo. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Desempenho mais rápido do provisionamento automático de usuários do Azure AD para aplicativos SaaS

**Tipo:** Recurso alterado  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Integração de terceiros
 
Anteriormente, os clientes que usavam os conectores de provisionamento de usuário do Azure Active Directory para aplicativos SaaS (por exemplo, Salesforce, ServiceNow e Box) poderiam ter um desempenho lento se os inquilinos do Azure AD contivessem mais de 100.000 usuários e grupos combinados e estivessem usando atribuições de usuário e grupo determinar quais usuários devem ser provisionados.

Em 2 de abril de 2018, aprimoramentos de desempenho significativos foram implantados no serviço de provisionamento do Azure AD que reduzem muito o tempo necessário para executar as sincronizações iniciais entre o Active Directory do Azure e os aplicativos de SaaS de destino.

Como resultado, muitos clientes que tinham sincronizações iniciais para aplicativos que demoravam dias ou nunca eram concluídas, agora estão sendo concluídas em questão de minutos ou horas.

Para obter mais informações, consulte [What happens during provisioning?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning) (O que acontece durante o provisionamento?)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Redefinição de senha de autoatendimento da tela de bloqueio do Windows 10 para computadores associados ao Azure AD híbrido

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha por autoatendimento  
**Funcionalidade do produto:** Autenticação de usuário
 
Atualizamos o recurso SSPR do Windows 10 para incluir suporte para computadores ingressados no Azure AD híbridos. Esse recurso está disponível no Windows 10 RS4 e permite que os usuários redefinam sua senha da tela de bloqueio de um computador Windows 10. Os usuários habilitados e registrados para redefinição de senha de autoatendimento podem usar esse recurso.

Para obter mais informações, consulte [Redefinição de senha do Azure AD na tela de logon](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Março de 2018
 
### <a name="certificate-expire-notification"></a>Notificação de expiração de certificado

**Tipo:** Correção  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
O Azure AD envia uma notificação quando um certificado para um aplicativo que está na galeria ou não está prestes a expirar. 

Alguns usuários não recebiam as notificações para aplicativos corporativos configurados para SSO baseado em SAML. Esse problema foi resolvido. O Azure AD envia uma notificação sobre certificados que expiram em 7, 30 e 60 dias. Você pode ver esse evento nos logs de auditoria. 

Para obter mais informações, consulte:

- [Gerenciar certificados para logon único federado no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Audit activity reports in the Azure Active Directory portal (Relatórios de atividades de auditoria no portal do Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Provedores de identidade do Twitter e do GitHub no Azure AD B2C

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Agora você pode adicionar o Twitter ou o GitHub como um provedor de identidade no Azure AD B2C. O Twitter está mudando da visualização pública para GA. O GitHub está sendo lançado em visualização pública.

Para saber mais, confira [O que é a colaboração B2B do Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Restringir o acesso do navegador usando Intune Managed Browser com o Azure AD baseada em aplicativo acesso condicional para iOS e Android

**Tipo:** New recurso  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção
 
**Agora, em visualização pública!**

**SSO do Intune Managed Browser:** Seus funcionários podem usar o logon único em clientes nativos (como o Microsoft Outlook) e o Intune Managed Browser para todos os aplicativos conectados ao Azure AD.

**Suporte ao Acesso Condicional do Intune Managed Browser:** Você agora pode exigir que os funcionários usar o Intune Managed browser usando políticas de acesso condicional com base no aplicativo.

Leia mais sobre isso em nossa [postagem no blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Para obter mais informações, consulte:

- [Configurar o acesso condicional com base no aplicativo](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Configurar políticas do navegador gerenciado](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlets do Proxy de aplicativo no módulo do Powershell GA

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso
 
O suporte para cmdlets de Proxy de Aplicativo agora estão no Módulo Powershell GA! Isso exige que você permaneça atualizado nos módulos do Powershell - se você ficar mais de um ano atrasado, alguns cmdlets poderão parar de funcionar. 

Para saber mais, veja [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Os clientes nativos do Office 365 têm suporte do SSO Contínuo usando um protocolo não interativo

**Tipo:** New recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário
 
O usuário que utiliza clientes nativos do Office 365 (versão 16.0.8730.xxxx e acima) obtém uma experiência de logon silencioso usando o SSO Contínuo. Esse suporte é fornecido com a adição de um protocolo não interativo (WS-Trust) ao Azure AD.

Para saber mais, confira [Como entrar em um cliente nativo com o trabalho de SSO contínuo?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Os usuários obtêm uma experiência de logon silenciosa, com o SSO contínuo, se um aplicativo envia solicitações de entrada para os terminais de inquilino do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário
 
Os usuários terão uma experiência de logon silenciosa, com SSO contínuo, se um aplicativo (por exemplo, `https://contoso.sharepoint.com`) enviar solicitações de entrada para pontos de extremidade com locatários do Microsoft Azure AD, ou seja, `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>`, em vez de enviar para o ponto de extremidade comum do Azure AD (`https://login.microsoftonline.com/common/<...>`).

Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>É preciso adicionar apenas uma URL do Azure AD, em vez de duas URLs como anteriormente, às configurações de zona de Intranet dos usuários para implementar o SSO Contínuo

**Tipo:** New recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário
 
Para implementar o SSO contínuo para seus usuários, você deve adicionar apenas uma URL do Azure Active Directory às configurações de zona da Intranet dos usuários usando a política de grupo no Active Directory: `https://autologon.microsoftazuread-sso.com`. Anteriormente, era necessário que os clientes adicionassem duas URLs.

Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros

Em março de 2018, adicionamos esses 15 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>O PIM para Recursos do Azure está disponível

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Se você estiver usando o Azure AD Privileged Identity Management para funções de diretório, agora poderá usar o acesso com limite de tempo do PIM e os recursos de atribuição para funções de recurso do Azure como Assinaturas, Grupos de Recursos, Máquinas Virtuais e qualquer outro recurso com suporte pelo Azure Resource Manager. Imponha a Autenticação Multifator ao ativar funções Just-In-Time e agende as ativações em coordenação com os períodos de alterações aprovadas. Além disso, esta versão adiciona aprimoramentos não disponíveis durante a visualização pública, incluindo uma interface do usuário atualizada, fluxos de trabalho de aprovação e a capacidade de estender funções que expirarão em breve e renovar funções expiradas.

Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure (Versão Prévia)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Adicionar declarações opcionais aos tokens de aplicativos (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário
 
O aplicativo Azure AD agora pode solicitar declarações personalizadas ou opcionais em tokens SAML ou JWTs.  Essas são as declarações sobre o usuário ou locatário que não são incluídas por padrão no token, devido a restrições de tamanho ou aplicabilidade.  Isso está atualmente em visualização pública para aplicativos Azure AD nos pontos de extremidade v1.0 e v2.0.  Confira a documentação para saber mais sobre quais declarações podem ser adicionadas e como editar o manifesto do aplicativo para solicitá-las.  

Para saber mais, confira [Declarações opcionais no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>O Azure AD dá suporte a PKCE para fluxos de OAuth mais seguros

**Tipo:** New recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário
 
Os documentos do Azure AD foram atualizados para indicar o suporte para PKCE, que permite a comunicação mais segura durante o fluxo de concessão de Código de Autorização do OAuth 2.0.  Code_challenges S256 e o texto sem formatação têm suporte nos pontos de extremidade v1.0 e v2.0. 

Para saber mais, confira [Solicitar um código de autorização](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>O suporte para o provisionamento de todos os valores de atributo de usuário está disponível na API Workday Get_Workers

**Tipo:** New recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Integração de terceiros
 
A visualização pública do provisionamento de entrada do Workday para Active Directory e Azure AD agora dá suporte à capacidade de extrair e provisionar todos os valores de atributo disponíveis na API Workday Get_Workers. Isso inclui suporte a centenas de atributos padrão e personalizados adicionais, além daqueles fornecidos na versão inicial do conector de provisionamento de entrada Workday.

Para obter mais informações, consulte: [Personalizando a lista de atributos de usuário do Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Alterar a associação de grupo de dinâmica para estática e vice-versa

**Tipo:** New recurso  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração
 
É possível alterar como a associação é gerida em um grupo. Isso é útil quando você quer manter o mesmo ID e nome de grupo no sistema, então, todas as referências existentes no grupo ainda serão válidas; criar um novo grupo exigiria atualizar essas referências.
Atualizamos o centro de administração do Azure AD para oferecer suporte a essa funcionalidade. Agora, os clientes podem converter grupos existentes de associação dinâmica para associação atribuída e vice-versa. Os cmdlets do PowerShell existentes também ainda estão disponíveis.

Para obter mais informações, consulte [regras de associação dinâmica para grupos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Comportamento aprimorado de saída com o SSO contínuo

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário
 
Anteriormente, mesmo que os usuários saíssem explicitamente de um aplicativo protegido pelo Azure AD, seriam conectados automaticamente de novo usando o SSO Contínuo se tentassem acessar um aplicativo Azure AD novamente na rede corporativa por meio de seus dispositivos associados ao domínio. Com essa alteração, há suporte para sair.  Isso permite que os usuários escolham a mesma conta do Azure AD ou outra para entrar novamente, em vez de serem conectados automaticamente usando o SSO contínuo.

Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Versão de conector de proxy de aplicativo 1.5.402.0 lançado

**Tipo:** Recurso alterado  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Segurança de identidade e proteção
 
Esta versão do conector está sendo distribuída gradualmente até novembro. Essa nova versão do conector inclui as seguintes alterações:

- O conector agora define cookies no nível do domínio em vez do nível do subdomínio. Isso garante uma experiência de SSO sem problemas e evita prompts de autenticação redundantes.
- Suporte para solicitações de codificação em partes
- Monitoramento de integridade de conector aprimorado 
- Várias correções de bug e melhorias de estabilidade

Para saber mais, veja [Noções básicas sobre conectores de proxy de aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Fevereiro de 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Navegação aprimorada para gerenciar usuários e grupos

**Tipo:** Plano para alteração  
**Categoria de serviço:** Gerenciamento de diretórios  
**Funcionalidade do produto:** Diretório

A experiência de navegação para gerenciar usuários e grupos foi simplificada. Agora, é possível navegar da visão geral do diretório diretamente para a lista de todos os usuários, com acesso mais fácil à lista dos usuários excluídos. Adicionalmente, você também pode navegar da visão geral do diretório diretamente para a lista de todos os grupos, com acesso mais fácil às configurações de gerenciamento de grupo. Além disso, a partir da página de visão geral do diretório, é possível procurar um usuário, grupo, aplicativo empresarial ou registro do aplicativo. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilidade de inscrições e relatórios de auditoria no Microsoft Azure operado pela 21Vianet (parceiro 21Vianet do Azure na China)

**Tipo:** New recurso  
**Categoria de serviço:** Azure Stack  
**Funcionalidade do produto:** Monitoramento e relatório

Relatórios de log de atividades do Azure Active Directory já estão disponíveis no Microsoft Azure operado pelas instâncias 21Vianet (parceiro 21Vianet do Azure na China). Os logs a seguir estão incluídos:

- **Logs de atividades de entradas** - Inclui todos os logs de entradas associados ao locatário.

- **Logs de auditoria de senha de autoatendimento** - Inclui todos os logs de auditoria SSPR.

- **Logs de auditoria de gerenciamento de diretório** - Inclui todos os registros de auditoria relacionados ao gerenciamento de diretório, como gerenciamento de usuários, gerenciamento de aplicativos e outros.

Com esses logs, é possível obter informações sobre como o ambiente está funcionando. Os dados fornecidos permitem a você:

- Determinar como os aplicativos e serviços serão utilizados pelos usuários.

- Solucionar problemas que impedem a conclusão dos trabalhos pelos usuários.

Para obter mais informações sobre como usar esses relatórios, consulte [Relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Usar a função "Leitor do Relatório" (função não administrador) para exibir relatórios de atividades do Azure Active Directory

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Como parte do feedback dos clientes para permitir que funções não administrativas tenham acesso aos logs de atividades do Azure AD, habilitamos a capacidade de usuários que estão na função "Report Reader" acessarem as atividades de Login e Auditoria no portal do Azure, bem como usando nossas APIs de gráficos. 

Para obter mais informações sobre como usar esses relatórios, consulte [Relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Declaração EmployeeID disponível como atributo de usuário e identificador de usuário

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
É possível configurar **EmployeeID** como o identificador de usuário e atributo de usuário para usuários membros e usuários B2B em aplicativos de logon com base em SAML da interface do usuário do aplicativo Enterprise.

Para obter mais informações, consulte [Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gerenciamento de aplicativo simplificado usando caracteres curingas no Proxy de Aplicativo do AD

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Autenticação de usuário
 
Para tornar a implantação de aplicativo mais fácil e reduzir a sobrecarga administrativa, agora podemos dar suporte à capacidade de publicar aplicativos usando caracteres curingas. Para publicar um aplicativo curinga, você pode seguir o fluxo de publicação de aplicativo padrão, mas usar um caractere curinga nas URLs internas e externas.

Para obter mais informações, consulte [Aplicativos curinga no proxy de aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Novos cmdlets para dar suporte à configuração do Proxy de Aplicativo

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Plataforma

A versão mais recente do módulo de visualização do AzureAD PowerShell contém novos cmdlets que permitem que os clientes configurem aplicativos de proxy de aplicativo usando o PowerShell.

Os novos cmdlets são: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Novos cmdlets para dar suporte à configuração de grupos

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Plataforma

A versão mais recente do módulo AzureAD PowerShell contém cmdlets para gerenciar grupos no Azure Active Directory. Esses cmdlets já estavam disponíveis no módulo AzureADPreview e agora adicionados ao módulo AzureAD

Os cmdlets de Grupo agora liberados para disponibilidade geral são: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Uma nova versão do Azure AD Connect está disponível

**Tipo:** New recurso  
**Categoria de serviço:** AD Sync  
**Funcionalidade do produto:** Plataforma
 
O Azure AD Connect é a ferramenta preferencial para sincronizar dados entre o Azure Active Directory e nas fontes de dados locais, incluindo Windows Server Active Directory e LDAP.

>[!Important]
>Essa compilação apresenta alterações de regras de sincronização e esquema. O Serviço de Sincronização do Azure AD Connect dispara as etapas de Importação Completa e Sincronização Completa após uma atualização. Para obter informações sobre como alterar esse comportamento, consulte [Como adiar a sincronização completa após a atualização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Essa versão contém as alterações e atualizações a seguir:

**Problemas corrigidos**

- Correção da janela de tempo nas tarefas em segundo plano da página de Filtragem de Partições ao comutar para a próxima página.

- Corrigido um bug que causou violação de Acesso durante a ação personalizada do ConfigDB.

- Corrigido um bug para recuperar do tempo limite de conexão do SQL.

- Corrigido um bug onde os certificados com curingas SAN falharam no teste de pré-requisito.

- Corrigido um erro que causa o crash miiserver.exe durante a exportação do conector AAD.

- Corrigido um erro no qual uma tentativa de senha incorreta conectada ao DC durante a execução fazia com que o assistente de conexão do AAD alterasse a configuração

**Novos recursos e aprimoramentos**
 
- Telemetria de aplicativos - Os administradores podem ativar/desativar essa classe de dados.

- Dados de Integridade do Azure Active Directory - Os administradores devem visitar o portal de integridade para controlar as configurações de integridade. Quando a política de atendimento for alterada, os agentes farão a leitura e irão impor a política.

- Adicionadas ações de configuração de write-back de dispositivo e uma barra de progresso para inicialização de página.

- Diagnósticos gerais aprimorados com relatório HTML e coleta de dados completa em um relatório HTML/ZIP-Text.

- Aperfeiçoada confiabilidade da atualização automática e adicionada a telemetria adicional para garantir que a integridade do servidor possa ser determinada.

- Restringir permissões disponíveis para contas privilegiadas na conta do AD Connector. Para novas instalações, o assistente restringe as permissões que as contas privilegiadas tenham na conta MSOL, após criar a conta MSOL. As alterações afetam instalações expressas e instalações personalizadas com a conta de Criação Automática.

- Alterado o instalador para não exigir privilégio de SA na instalação limpa do AADConnect.

- Novo utilitário para solucionar problemas de sincronização para um objeto específico. Atualmente, o utilitário verifica os seguintes itens:

    - UserPrincipalName incompatível entre o objeto de usuário sincronizado e a conta de usuário no Locatário do Azure Active Directory.
  
    - Se o objeto for filtrado da sincronização devido à filtragem de domínio
  
    - Se o objeto for filtrado da sincronização devido à filtragem da OU (unidade organizacional)

- Novo utilitário para sincronizar o hash de senha atual armazenado no Active Directory local para uma conta de usuário específica. O utilitário não exige uma alteração de senha. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplicativos que dão suporte a políticas do Intune App Protection adicionadas para uso com o Azure AD baseada em aplicativo o acesso condicional

**Tipo:** Recurso alterado  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Adicionamos mais aplicativos que dão suporte ao acesso condicional baseado no aplicativo. Agora, é possível acessar o Office 365 e outros aplicativos de nuvem conectados ao Azure Active Directory usando esses aplicativos clientes aprovados.

As seguintes aplicações serão adicionadas até o final de fevereiro:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acesso condicional baseado no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Termos de uso de atualização para experiência móvel 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Conformidade

Quando os termos de uso são exibidos, você pode clicar em **Tendo problemas com o modo de exibição? Clique aqui**. Clicar nesse link, abre os termos de uso nativamente no seu dispositivo. Independentemente do tamanho da fonte no documento ou do tamanho da tela do dispositivo, é possível ampliar e ler o documento, conforme necessário. 

---
 
## <a name="january-2018"></a>Janeiro de 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD 

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros

Em janeiro de 2018, os seguintes novos aplicativos com suporte à federação foram adicionados à galeria de aplicativos:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [Software de Gerenciamento de Privacidade OneTrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [Diretório Federado IriusRisk e [NetBenefits de Fidelidade](https://go.microsoft.com/fwlink/?linkid=864701).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Entre com risco adicional detectado

**Tipo:** New recurso  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de identidade e proteção

O insight obtido para um evento de risco detectado está vinculado à sua assinatura do Microsoft Azure AD. Com a edição Azure AD Premium P2, você obtém as informações mais detalhadas sobre todas as detecções subjacentes.

Com a edição Azure AD Premium P1, detecções não cobertas por sua licença aparecem como o evento de risco Entrada com risco adicional detectado.

Para saber mais, veja [Eventos de risco do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ocultar aplicativos do Office 365 dos painéis de acesso do usuário final

**Tipo:** New recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO

Agora você pode gerenciar melhor como os aplicativos do Office 365 são exibidos nos painéis de acesso do usuário por meio de uma nova configuração de usuário. Essa opção é útil para reduzir o número de aplicativos nos painéis de acesso de um usuário, se você preferir mostrar apenas os aplicativos do Office no portal do Office. A configuração está localizada nas **Configurações do usuário** e está marcada como **Os usuários só podem ver os aplicativos do Office 365 no portal do Office 365**.

Para obter mais informações, consulte [Ocultar um aplicativo da experiência de um usuário no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Entrada direta em aplicativos habilitados para SSO de Senha diretamente na URL do aplicativo 

**Tipo:** New recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO

A extensão do navegador Meus Aplicativos agora está disponível por meio de uma ferramenta conveniente que fornece a funcionalidade de logon único de Meus Aplicativos como um atalho no navegador. Após a instalação, o usuário verá um ícone de waffle no navegador que fornece acesso rápido aos aplicativos. Os usuários agora podem usufruir dos seguintes benefícios:

- A capacidade de fazer login diretamente em aplicativos baseados em senha com SSO a partir da página de login do aplicativo
- Iniciar um aplicativo usando o recurso de pesquisa rápida
- Atalhos para os aplicativos usados recentemente da extensão
- A extensão está disponível para Microsoft Edge, Chrome e Firefox.
 
Para obter mais informações, consulte [Extensão de Entrada Segura dos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>A experiência de administração do Azure AD no Portal Clássico do Azure foi retirada

**Tipo:** Preteridos   
**Categoria de serviço:** AD do Azure  
**Funcionalidade do produto:** Diretório

A partir de 8 de janeiro de 2018, a experiência de administração do Azure AD no portal clássico do Azure foi desativada. Isso ocorreu em conjunto com a desativação do próprio portal clássico do Azure. No futuro, você deve usar o [centro de administração do Azure AD](https://aad.portal.azure.com) para toda a administração baseada em portal do AD do Azure.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>O portal da Web do PhoneFactor foi desativado

**Tipo:** Preteridos  
**Categoria de serviço:** AD do Azure  
**Funcionalidade do produto:** Diretório
 
A partir de 8 de janeiro de 2018, o portal da Web do PhoneFactor foi desativado. Este portal foi usado para a administração do servidor MFA, mas essas funções foram movidas para o portal do Azure em portal.azure.com. 

A configuração do MFA está localizada em: **Azure Active Directory \> Servidor do MFA**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD

**Tipo:** Preteridos  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidades  


Com a disponibilidade geral do novo Azure Active Directory, um console de Administração e novas APIs agora estão disponíveis para relatórios de atividade e de segurança, as APIs de relatório no ponto de extremidade “/reports” foram desativadas em 31 de dezembro de 2017.

**O que está disponível?**

Como parte da transição para o novo console de administrador, disponibilizamos duas novas APIs para recuperar os Logs de Atividades do Azure AD. O novo conjunto de APIs fornece funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de conexão. Os dados disponíveis anteriormente por meio de relatórios de segurança agora podem ser acessados por meio da API de eventos de risco do Identity Protection do Microsoft Graph.

Para obter mais informações, consulte:

- [Introdução à API de relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Dezembro de 2017

### <a name="terms-of-use-in-the-access-panel"></a>Termos de uso no Painel de Acesso

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Conformidade
 
Agora você pode acessar o Painel de Acesso e exibir os termos de uso que aceitou anteriormente.

Siga estas etapas:

1. Acesse o [portal do MyApps](https://myapps.microsoft.com) e entre.

2. No canto superior direito, selecione seu nome e, em seguida, selecione **Perfil** na lista. 

3. Em seu **Perfil**, selecione **Examinar termos de uso**. 

4. Agora, você pode ler os termos de uso que você aceitou. 

Para obter mais informações, consulte o [Recurso de termos de uso do Azure AD (versão prévia)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nova experiência de entrada do Azure AD.

**Tipo:** New recurso  
**Categoria de serviço:** AD do Azure  
**Funcionalidade do produto:** Autenticação de usuário
 
As interfaces do usuário do Azure AD e do sistema de identidade da conta da Microsoft foram reformuladas para que tenham uma aparência consistente. Além disso, a página de entrada do Azure AD coleta o nome de usuário primeiro, seguido da credencial em uma segunda tela.

Para obter mais informações, consulte [A nova experiência de entrada do Azure AD agora está em visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menos prompts de entrada: Uma nova experiência "Manter-me conectado" para a entrada do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** AD do Azure  
**Funcionalidade do produto:** Autenticação de usuário
 
A caixa de seleção **Manter-me conectado** na página de entrada do Azure AD foi substituída por um novo prompt exibido depois que você é autenticado com êxito. 

Se você responder **Sim** a esse prompt, o serviço fornecerá um token de atualização persistente. Esse comportamento é o mesmo exibido quando você selecionava a caixa de seleção **Manter-me conectado** na experiência antiga. Para locatários federados, esse prompt é mostrado após a autenticação bem-sucedida no serviço federado.

Para obter mais informações, confira [Menos prompts de entrada: A nova experiência "Manter-me conectado" do Azure AD está em versão prévia](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Adicionar uma configuração para exigir que os termos de uso sejam expandidos antes da aceitação

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Conformidade
 
Uma opção para os administradores exige que seus usuários expandam os termos de uso antes de aceitar os termos.

Selecione **Ativado** ou **Desativado** para solicitar que os usuários expandam os termos de uso. A configuração **Ativado** exige que os usuários visualizem os termos de uso antes de aceitá-los.

Para obter mais informações, consulte o [Recurso de termos de uso do Azure AD (versão prévia)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Ativação com escopo para atribuições de função qualificadas

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Use a ativação com escopo para ativar atribuições de função qualificadas em recursos do Azure com menos autonomia do que nos padrões de atribuição originais. Um exemplo é quando você está atribuído como o proprietário de uma assinatura em seu locatário. Com a ativação com escopo, é possível ativar a função de proprietário para até cinco recursos contidos na assinatura (como grupos de recursos e máquinas virtuais). A ativação com escopo pode reduzir a possibilidade de executar alterações indesejadas em recursos críticos do Azure.

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Novos aplicativos federados na galeria de aplicativos Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** Integração de terceiros

Em dezembro de 2017, adicionamos esses novos aplicativos com suporte de federação à nossa galeria de aplicativos:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Fluxos de trabalho de aprovação para as funções de diretório do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
O fluxo de trabalho de aprovação para as funções de diretório do Azure AD está geralmente disponível.

Com o fluxo de trabalho de aprovação, os administradores de função com privilégios podem exigir que membros de função qualificada solicitem a ativação de função antes que possam usar a função com privilégios. Vários usuários e grupos podem receber responsabilidades de aprovação. Os membros de função qualificados recebem notificações quando a aprovação é concluída e suas funções estão ativas.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticação de passagem: Suporte ao Skype for Business

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário

A autenticação de passagem agora dá suporte a entradas do usuário em aplicativos cliente do Skype for Business que dão suporte à autenticação moderna, que incluem topologias online e híbridas. 

Para obter mais informações, consulte [Topologias do Skype for Business compatíveis com a autenticação moderna](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Atualizações no Azure AD Privileged Identity Management para o RBAC do Azure (versão prévia)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Com a atualização da visualização pública do Azure AD PIM (Privileged Identity Management) para o RBAC (Controle de Acesso Baseado em Função), agora você pode:

* Usar a Administração Just Enough.
* Exigir aprovação para ativar as funções de recurso.
* Agendar uma ativação futura de uma função que exige aprovação para funções do Azure AD e de RBAC do Azure.
 
Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure (versão prévia)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Novembro de 2017
 
### <a name="access-control-service-retirement"></a>Desativação do serviço de Controle de Acesso

**Tipo:** Plano para alteração  
**Categoria de serviço:** Serviço de Controle de Acesso  
**Funcionalidade do produto:** Serviço de Controle de Acesso 

O Controle de Acesso do Azure Active Directory (também conhecido como serviço de Controle de Acesso) será desativado no final de 2018. Mais informações que incluem um cronograma detalhado e diretrizes de migração de alto nível serão fornecidas nas próximas semanas. Você pode deixar comentários nesta página com dúvidas sobre o serviço de Controle de Acesso e um membro da equipe responderá a suas dúvidas.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir o acesso do navegador ao Intune Managed Browser 

**Tipo:** Plano para alteração  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Você pode restringir o acesso do navegador ao Office 365 e a outros aplicativos de nuvem conectados ao Azure AD usando o Intune Managed Browser como um aplicativo aprovado. 

Agora você pode configurar a seguinte condição para acesso condicional baseado em aplicativo:

**Aplicativos cliente:** Navegador

**Qual é o efeito da alteração?**

Atualmente, o acesso está bloqueado quando essa condição é usada. Quando a versão prévia estiver disponível, todo o acesso exigirá o uso do aplicativo de navegador gerenciado. 

Procure essa funcionalidade e mais informações em notas de versão e blogs futuros. 

Para obter mais informações, consulte [acesso condicional no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos cliente aprovados para o Azure AD baseado em aplicativo o acesso condicional

**Tipo:** Plano para alteração  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Os seguintes aplicativos estão na lista de [aplicativos do cliente aprovados](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acesso condicional baseado no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Suporte dos termos de uso para diversos idiomas

**Tipo:** New recurso    
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Conformidade

Os administradores agora podem criar novos termos de uso que contêm vários documentos PDF. Você pode marcar esses documentos em PDF com um idioma correspondente. Os usuários recebem o PDF com o idioma correspondente de acordo com suas preferências. Se não houver nenhuma correspondência, o idioma padrão será mostrado.

---
 
### <a name="real-time-password-writeback-client-status"></a>Status do cliente de write-back de senha em tempo real

**Tipo:** New recurso  
**Categoria de serviço:** Redefinição de senha de autoatendimento  
**Funcionalidade do produto:** Autenticação de usuário

Agora você pode examinar o status do cliente de write-back de senha local. Essa opção está disponível na seção **Integração local** da página [Redefinição de senha](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Se houver problemas com sua conexão com o cliente de write-back local, você verá uma mensagem de erro que exibirá:

- Informações sobre o motivo pelo qual não é possível se conectar ao cliente de write-back local.
- Um link para a documentação que auxilia na resolução do problema. 

Para obter mais informações, consulte [Integração local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Acesso condicional baseado no aplicativo do Azure AD 
 
**Tipo:** New recurso  
**Categoria de serviço:** AD do Azure  
**Funcionalidade do produto:** Segurança de identidade e proteção

Você agora pode restringir o acesso ao Office 365 e outros aplicativos de nuvem conectados ao AD do Azure para [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) que dão suporte a políticas de proteção de aplicativo do Intune usando [Azure acesso condicional baseado no aplicativo do AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Políticas da Proteção de Aplicativo do Intune são usadas para configurar e proteger os dados da empresa nesses aplicativos cliente.

Combinando [baseado em aplicativo](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) com [baseado em dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) políticas de acesso condicional, você tem a flexibilidade para proteger dados pessoais e dispositivos da empresa.

As seguintes condições e controles agora estão disponíveis para uso com o acesso condicional baseado em aplicativo:

**Condição de plataforma com suporte**

- iOS
- Android

**Condição de aplicativos cliente**

- Aplicativos móveis e clientes de desktop

**Controle de acesso**

- Exigir um aplicativo cliente aprovado

Para obter mais informações, consulte [Azure acesso condicional baseado no aplicativo do AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gerenciar dispositivos do Azure AD no portal do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Registro e gerenciamento de dispositivos  
**Funcionalidade do produto:** Segurança de identidade e proteção

Agora você pode encontrar todos os dispositivos conectados ao Azure AD e as atividades relacionadas ao dispositivo em um único local. Há uma nova experiência de administração para gerenciar todas as suas identidades e configurações de dispositivos no portal do Azure. Nesta versão, você pode:

- Exiba todos os dispositivos que estão disponíveis para acesso condicional no Azure AD.
- Exibir propriedades, incluindo dispositivos vinculados do Azure AD híbrido.
- Encontrar chaves do BitLocker para os dispositivos ingressados no Azure AD, gerencie seu dispositivo com o Intune e muito mais.
- Gerenciar configurações relacionadas ao dispositivo do Azure AD.

Para obter mais informações, consulte [Gerenciar dispositivos usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Suporte para macOS como plataforma de dispositivo para acesso condicional do Azure AD 

**Tipo:** New recurso    
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção 

Você agora pode incluir (ou excluir) macOS como uma condição de plataforma de dispositivo em sua política de acesso condicional do Azure AD. Com a adição do macOS às plataformas de dispositivos com suporte, você pode:

- **Registrar e gerenciar dispositivos macOS usando o Intune.** Semelhante a outras plataformas como iOS e Android, um aplicativo do portal da empresa está disponível para o macOS para realizar registros unificados. Use o novo aplicativo do portal da empresa para o macOS para registrar um dispositivo no Intune e registrá-lo no Azure AD.
- **Garantir que os dispositivos macOS seguem as políticas de conformidade de sua organização definidas no Intune.** No Intune no portal do Azure, agora você pode configurar políticas de conformidade para dispositivos macOS. 
- **Restringir o acesso a aplicativos no Azure AD apenas a dispositivos macOS em conformidade.** Criação de política de acesso condicional tem macOS como uma opção de plataforma de dispositivo separada. Agora você pode criar políticas de acesso condicional específicas ao macOS para o conjunto de destino do aplicativo no Azure.

Para obter mais informações, consulte:

- [Criar uma política de conformidade do dispositivo para dispositivos macOS com o Intune](https://aka.ms/macoscompliancepolicy)
- [Acesso condicional no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Extensão Servidor de Políticas de Rede para a Autenticação Multifator do Azure 

**Tipo:** New recurso    
**Categoria de serviço:**  Autenticação multifator  
**Funcionalidade do produto:** Autenticação de usuário

A extensão do Servidor de Políticas de Rede para a Autenticação de Vários Fator do Azure adiciona recursos de Autenticação de Vários Fatores com base na nuvem à sua infraestrutura de autenticação usando seus servidores existentes. Com a extensão Servidor de Políticas de Rede, você pode adicionar uma chamada telefônica, mensagem de texto ou verificação de aplicativo de telefone a seu fluxo de autenticação existente. Você não precisa instalar, configurar nem manter novos servidores. 

Essa extensão foi criada para organizações que desejam proteger as conexões de rede virtual privada sem implantar o Servidor de Autenticação Multifator do Azure. A extensão Servidor de Políticas de Rede atua como um adaptador entre o RADIUS e a Autenticação Multifator do Azure baseada em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.

Para obter mais informações, consulte [Integrar sua infraestrutura existente do Servidor de Políticas de Rede à Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaurar ou remover permanentemente os usuários excluídos

**Tipo:** New recurso    
**Categoria de serviço:** Gerenciamento de Usuários  
**Funcionalidade do produto:** Diretório 

No centro de administração do Azure AD, agora você pode:

- Restaurar um usuário excluído. 
- Excluir permanentemente um usuário.

**Para experimentar:**

1. No centro de administração do Azure AD, selecione [Todos os usuários](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) na seção **Gerenciar**. 

2. Na lista **Mostrar**, selecione **Usuários excluídos recentemente**. 

3. Selecione um ou mais usuários excluídos recentemente e, em seguida, restaure-os ou exclua-os permanentemente.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos cliente aprovados para o Azure AD baseado em aplicativo o acesso condicional
 
**Tipo:** Recurso alterado  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Os seguintes aplicativos foram adicionados à lista de [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Proteção de Informações do Azure 

Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acesso condicional baseado no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Use "OR" entre os controles em uma política de acesso condicional 

**Tipo:** Recurso alterado    
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção
 
Você agora pode usar "ou" (exigir um dos controles selecionados) para controles de acesso condicional. Use esse recurso para criar políticas com “OR” entre os controles de acesso. Por exemplo, você pode usar esse recurso para criar uma política que exige que um usuário faça login usando a Autenticação Multifator "OU" para estar em um dispositivo compatível.

Para obter mais informações, consulte [controles de acesso condicional do Azure AD no](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-events"></a>Agregação de eventos de risco em tempo real

**Tipo:** Recurso alterado    
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de identidade e proteção

No Azure AD Identity Protection, todos os eventos de risco em tempo real que foram originados no mesmo endereço IP em determinado dia agora são agregados por tipo de evento de risco. Essa alteração limita o volume de eventos de risco mostrados sem nenhuma alteração na segurança do usuário.

A detecção em tempo real subjacente funciona sempre que o usuário se conecta. Se você tiver uma política de segurança de risco de login configurada para Autenticação de vários fatores ou bloqueio de acesso, ela ainda será acionada durante cada entrada arriscada.
 
---
 
## <a name="october-2017"></a>Outubro de 2017

### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD

**Tipo:** Plano para alteração  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidades  

O Portal do Azure oferece:

- Um novo console de administração do Azure AD.
- Novas APIs para relatórios de atividade e segurança.
 
Devido a essas novas funcionalidades, as APIs de relatórios no ponto de extremidade /reports foram desativadas em 10 de dezembro de 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Detecção automática de campo de conexão

**Tipo:** Correção   
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** Logon único  

O Azure AD é compatível com a detecção automática de campo de entrada para os aplicativos que renderizam um campo de nome de usuário e senha HTML. Essas etapas são documentadas em [Como capturar automaticamente os campos de entrada para um aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Você pode encontrar essa funcionalidade adicionando um aplicativo *Inexistente na galeria* na página **Aplicativos empresariais** do [Portal do Azure](https://aad.portal.azure.com). Além disso, você pode configurar o modo **Logon Único** neste novo aplicativo como **Logon Único Baseado em Senha**, inserir uma URL da Web e, em seguida, salvar a página.
 
Devido a um problema do serviço, essa funcionalidade ficou temporariamente desabilitada. O problema foi resolvido e a detecção automática do campo de entrada está disponível novamente.

---

### <a name="new-multi-factor-authentication-features"></a>Novos recursos de autenticação multifator

**Tipo:** New recurso  
**Categoria de serviço:** Autenticação multifator  
**Funcionalidade do produto:** Segurança de identidade e proteção  

O MFA (Autenticação multifator) é uma parte essencial da proteção de sua organização. Para tornar as credenciais mais adaptáveis e a experiência mais direta, os seguintes recursos foram adicionados: 

- Os resultados do desafio multifator são diretamente integrados ao relatório de entrada do Azure AD, que inclui o acesso programático aos resultados do MFA.
- A configuração do MFA está mais profundamente integrada à experiência de configuração do Azure AD no portal do Azure.

Com essa versão prévia pública, o gerenciamento e os relatórios de MFA são parte integrante da experiência de configuração central do Azure AD. Agora, você pode gerenciar a funcionalidade do portal de gerenciamento do MFA na experiência do Azure AD.

Para obter mais informações, consulte [Referência de relatórios do MFA no portal do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Termos de uso

**Tipo:** New recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Conformidade  

Use os termos de uso do Azure AD para apresentar informações como avisos de isenção de responsabilidade relevantes para requisitos legais ou de conformidade para os usuários.

Use os termos de uso do Azure AD nos seguintes cenários:

- Termos de uso gerais para todos os usuários em sua organização
- Termos de uso específicos com base nos atributos de um usuário (por exemplo, médicos versus enfermeiras ou funcionários nacionais vs. internacionais, feito por grupos dinâmicos)
- Termos de uso específicos para acessar aplicativos de negócios de alto impacto, como o Salesforce

Para obter mais informações, consulte [Termos de uso do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Melhorias no Privileged Identity Management

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management  

Com o Azure AD Privileged Identity Management, você pode gerenciar, controlar e monitorar o acesso aos recursos do Azure (versão prévia) em sua organização para:

- Assinaturas
- Grupos de recursos
- Máquinas virtuais 

Todos os recursos do portal do Azure que usam a funcionalidade de RBAC do Azure podem usufruir todas as funcionalidades de segurança e gerenciamento do ciclo de vida que o Azure AD Privileged Identity Management tem a oferecer.

Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Análises de acesso

**Tipo:** New recurso  
**Categoria de serviço:** Análises de acesso  
**Funcionalidade do produto:** Conformidade  

As organizações podem usar revisões de acesso (versão prévia) para gerenciar com eficiência as associações a um grupo e o acesso a aplicativos corporativos: 

- Você pode reconfirmar o acesso do usuário convidado usando as revisões do acesso deles a aplicativos e a associações de grupos. Os revisores podem decidir com eficiência se permitirão o acesso contínuo aos convidados de acordo com as informações fornecidas pelas revisões de acesso.
- Você pode reconfirmar o acesso de funcionários a aplicativos e associações de grupo com as revisões de acesso.

Você pode coletar os controles de revisão de acesso em programas relevantes para que sua organização rastreie as análises de conformidade ou aplicativos suscetíveis a riscos.

Para obter mais informações, consulte [Revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ocultar aplicativos de terceiros de Meus Aplicativos e do inicializador de aplicativos do Office 365

**Tipo:** New recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** Logon único  

Agora é possível gerenciar melhor os aplicativos que são mostrados nos portais dos usuários por meio de uma nova propriedade **ocultar aplicativo**. Você pode ocultar aplicativos para ajudar em casos em que os blocos de aplicativos são mostrados para serviços de back-end ou blocos duplicados e emails secundários de iniciadores de aplicativos dos usuários. A alternância está localizada na seção **Propriedades** do aplicativo de terceiros e é rotulada **Visível para o usuário?** Também é possível ocultar um aplicativo de forma programática por meio do PowerShell. 

Para obter mais informações, consulte [Ocultar um aplicativo de terceiros da experiência de um usuário no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**O que está disponível?**

 Como parte da transição para o novo console de administrador, duas novas APIs para recuperar os logs de atividades do Azure AD estão disponíveis. O novo conjunto de APIs fornece funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de conexão. Os dados disponíveis anteriormente por meio de relatórios de segurança agora podem ser acessados por meio da API de Eventos de Risco do Identity Protection no Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix para o Identity Manager

**Tipo:** Recurso alterado  
**Categoria de serviço:** Identity Manager  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidades  

Um pacote cumulativo de atualizações do hotfix (build 4.4.1642.0) está disponível a partir de 25 de setembro de 2017 para o Identity Manager 2016 Service Pack 1. Esse pacote cumulativo:

- Resolve problemas e adiciona melhorias.
- É uma atualização cumulativa que substitui todas as atualizações do Identity Manager 2016 Service Pack 1 até o build 4.4.1459.0 para o Identity Manager 2016. 
- Exige o Identity Manager 2016 build 4.4.1302.0. 

Para obter mais informações, consulte [Pacote cumulativo de atualizações do hotfix (build 4.4.1642.0) disponível para o Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
