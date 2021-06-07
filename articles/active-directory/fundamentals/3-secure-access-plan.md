---
title: Criar um plano de segurança para acesso externo ao Azure Active Directory
description: Planeje a segurança para acesso externo aos recursos da sua organização.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266142240ba9e892c905ac8aa6521da5a14c4c3d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554013"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. criar um plano de segurança para acesso externo 

Agora que você [determinou sua postura de segurança de postura de segurança desejada para acesso externo](1-secure-access-posture.md) e [descobriu seu estado de colaboração atual](2-secure-access-current-state.md), você pode criar um plano de segurança de usuário externo e de governança. 

Este plano deve documentar o seguinte:

* Os aplicativos e outros recursos que devem ser agrupados para acesso.

* As condições de entrada apropriadas para usuários externos. Elas podem incluir estado do dispositivo, localização de entrada, requisitos de aplicativo cliente e risco do usuário.

* Políticas de negócios sobre quando examinar e remover o acesso. 

* As populações do usuário a serem agrupadas e tratadas de forma semelhante.

Depois que essas áreas forem documentadas, você poderá usar as políticas de gerenciamento de acesso e identidade da Microsoft ou de qualquer outro provedor de identidade (IdP) para implementar esse plano.

## <a name="document-resources-to-be-grouped-for-access"></a>Documentar recursos a serem agrupados para acesso

Há várias maneiras de agrupar recursos para acesso. 

* O Microsoft Teams agrupa arquivos, threads de conversa e outros recursos em um único lugar. Você deve formular uma estratégia de acesso externo para o Microsoft Teams. Consulte [proteger o acesso a equipes, onedrive e SharePoint](9-secure-access-teams-sharepoint.md).

* Os pacotes de acesso de gerenciamento de direitos permitem que você crie um único pacote de aplicativos e outros recursos aos quais você pode conceder acesso. 

* As políticas de acesso condicional podem ser aplicadas a até 250 aplicativos com os mesmos requisitos de acesso.

No entanto, você vai gerenciar o acesso, deve documentar quais aplicativos devem ser agrupados. As considerações devem incluir:

* **Perfil de risco**. Qual é o risco para sua empresa se um ator inadequado obtiver acesso a um aplicativo? Considere codificar cada aplicativo como alto, médio ou baixo risco. Tenha cuidado ao agrupar aplicativos de alto risco com itens de baixo risco. 

   * Documentar aplicativos que nunca devem ser compartilhados com usuários externos também.

* **Estruturas de conformidade**. E se alguma estrutura de conformidade precisar de um aplicativo ser atendida? Quais são os requisitos de acesso e de revisão?

* **Aplicativos para funções ou departamentos de trabalho específicos**. Há aplicativos que devem ser agrupados, pois todos os usuários em uma função ou departamento de trabalho específico precisarão de acesso?

* **Aplicativos focados em colaboração**. Quais aplicativos focados em colaboração os usuários externos podem acessar? O Microsoft Teams e o SharePoint podem precisar estar acessíveis. Para aplicativos de produtividade no Office 365, como o Word e o Excel, os usuários externos trazem suas próprias licenças ou você precisará licencia-los e fornecer acesso?

Para cada Agrupamento de aplicativos e recursos que você deseja tornar acessíveis a usuários externos, documente o seguinte:

* Um nome descritivo para o grupo, por exemplo *High_Risk_External_Access_Finance*. 

* Lista completa de todos os aplicativos e recursos no grupo.

* Proprietários de aplicativos e recursos e informações de contato.

* Se o acesso é controlado por ele ou delegado ao proprietário da empresa.

* Quaisquer pré-requisitos, por exemplo, concluindo uma verificação em segundo plano ou um treinamento, para acesso.

* Quaisquer requisitos de conformidade para acessar os recursos.

* Quaisquer desafios adicionais, por exemplo, exigindo a autenticação multifator para recursos específicos.

* Com que frequência o acesso será revisado, por quem e onde ele será documentado.

Esse tipo de plano de governança também pode ser concluído para acesso interno.

## <a name="document-sign-in-conditions-for-external-users"></a>Documentar condições de entrada para usuários externos.

Como parte do seu plano, você deve determinar os requisitos de entrada para seus usuários externos à medida que acessam recursos. Os requisitos de entrada geralmente são baseados no perfil de risco dos recursos e na avaliação de risco da entrada dos usuários.

As condições de entrada são configuradas no [acesso condicional do Azure ad](../conditional-access/overview.md) e são constituídas de uma condição e um resultado. Por exemplo, quando exigir autenticação multifator

**Condições de entrada baseadas em risco de recurso.**

| Perfil de risco do aplicativo| Considere estas políticas para disparar a autenticação multifator |
| - |-|
| Baixo risco| Exigir MFA para conjuntos de aplicativos específicos |
| Risco de Med| Exigir MFA quando outros riscos estiverem presentes |
| Alto risco| Exigir o MFA sempre para usuários externos |


Hoje, você pode [impor a autenticação multifator para usuários B2B em seu locatário](../external-identities/b2b-tutorial-require-mfa.md). 

**Condições de entrada baseadas no usuário e no dispositivo**.

| Usuário ou risco de entrada| Considere estas políticas |
| - | - |
| Dispositivo| Exigir dispositivos em conformidade |
| Aplicativos móveis| Exigir aplicativos aprovados |
| A proteção de identidade mostra alto risco| Exigir que o usuário altere a senha |
| Local da rede| Exigir entrada de um intervalo de endereços IP específico para projetos altamente confidenciais |

Hoje, para usar o estado do dispositivo como uma entrada para uma política, o dispositivo deve ser registrado ou ingressado em seu locatário. 

[As políticas baseadas em risco da proteção de identidade](../conditional-access/howto-conditional-access-policy-risk.md) podem ser usadas. No entanto, os problemas devem ser atenuados no locatário inicial do usuário.

Para [locais de rede](../conditional-access/howto-conditional-access-policy-location.md), você pode restringir o acesso a qualquer intervalo de endereços IP que você possui. Você pode usar isso se quiser que os parceiros externos acessem um aplicativo enquanto eles estiverem no site da sua organização.

[Saiba mais sobre as políticas de acesso condicional](../conditional-access/overview.md).

## <a name="document-access-review-policies"></a>Documentar políticas de revisão de acesso

Documente suas políticas de negócios para quando você precisar examinar o acesso aos recursos e quando precisar remover o acesso à conta para usuários externos. As entradas para essas decisões podem incluir:

* Requisitos detalhados em qualquer estrutura de conformidade.

* Processos e políticas de negócios internos

* Comportamento do usuário

Embora suas políticas sejam altamente personalizadas para suas necessidades, considere o seguinte:

* **Revisões de acesso de gerenciamento de direitos**. Use a funcionalidade no gerenciamento de direitos para

   * [Expirar automaticamente os pacotes de acesso](../governance/entitlement-management-access-package-lifecycle-policy.md)e, portanto, o acesso de usuário externo aos recursos incluídos.

   * Defina uma [frequência de revisão necessária](../governance/entitlement-management-access-reviews-create.md) para as revisões de acesso.

   * Se você estiver usando [organizações conectadas](../governance/entitlement-management-organization.md) para agrupar todos os usuários de um único parceiro, agende análises regulares com o proprietário da empresa e o representante do parceiro.

* **Grupos de Microsoft 365**. Defina uma [política de expiração de grupo](/microsoft-365/solutions/microsoft-365-groups-expiration-policy) para Microsoft 365 grupos aos quais os usuários externos são convidados. 

* **Outras opções**. Se usuários externos tiverem acesso fora dos pacotes de acesso de gerenciamento de direitos ou grupos de Microsoft 365, configure o processo comercial para examinar quando as contas devem ser tornadas inativas ou excluídas. Por exemplo:

   * Remova a capacidade de entrada de qualquer conta que não esteja conectada por 90 dias.

   * Avaliar as necessidades de acesso e tomar medidas no final de cada projeto com usuários externos.

 

## <a name="determine-your-access-control-methods"></a>Determinar seus métodos de controle de acesso

Agora que você sabe o que deseja controlar o acesso, como esses ativos devem ser agrupados para acesso comum e políticas necessárias de entrada e de revisão de acesso, você pode decidir como realizar seu plano. 

Algumas funcionalidades, por exemplo, [Gerenciamento de direitos](../governance/entitlement-management-overview.md), só estão disponíveis com licenças de Azure ad Premium 2 (P2). As licenças Microsoft 365 E5 e Office 365 E5 incluem licenças do Azure AD P2. 

Outras combinações de Microsoft 365, Office 365 e Azure AD também habilitam algumas funcionalidades para gerenciar usuários externos. Consulte [proteção de informações](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) para obter mais informações.

> [!NOTE]
> As licenças são por usuário. Portanto, você pode ter usuários específicos, incluindo administradores e proprietários de negócios delegados controle de acesso, no nível do Azure AD P2 ou Microsoft 365 E5 sem habilitar essas licenças para todos os usuários. Seus primeiros 50.000 usuários externos são gratuitos. Se você não habilitar licenças P2 para seus outros usuários internos, eles não poderão usar a funcionalidade de gerenciamento de direitos, como pacotes de acesso. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Controle o acesso com o Azure AD P2 e o Microsoft/Office 365 e5
O Azure AD P2 e o Microsoft 365 E5 têm o conjunto completo de ferramentas de segurança e governança.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>Provisionamento, entrada, revisão de acesso e desprovisionamento. As entradas em negrito são métodos preferenciais

| Recurso| Provisionar usuários externos| Impor requisitos de entrada.| Examinar acesso| Desprovisionar o acesso |
| - | - | - | - | - |
| Colaboração B2B do Azure AD| Convidar por email, OTP, autoatendimento| | **Análise periódica por parceiro**| Remover conta<br>Restringir entrada |
| Gerenciamento de direitos| **Adicionar usuário por atribuição ou acesso de autoatendimento**| | Análises de acesso|**Expiração ou remoção do pacote de acesso**|
| Grupos do Office 365| | | Examinar associações de grupo| Expiração ou exclusão do grupo<br> Grupo de formulários de remoção |
| Grupos de segurança do Azure AD| | **Políticas de acesso condicional** (adicionar usuários externos a grupos de segurança, conforme necessário)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Acesso aos recursos. As entradas em negrito são métodos preferenciais

|Recurso | Acesso a recursos de & de aplicativo| Acesso do SharePoint & OneDrive| Acesso às equipes| Segurança de documentos de & de email |
| - |-|-|-|-|
| Gerenciamento de direitos| **Adicionar usuário por atribuição ou acesso de autoatendimento**| **Pacotes de acesso**| **Pacotes de acesso**|  |
| Grupo do Office 365| | Acesso a site (s) (e conteúdo associado) incluído no grupo| Acesso a equipes (e conteúdo associado) incluído com o grupo|  |
| Rótulos de confidencialidade| | **Classificar e restringir o acesso manualmente e automaticamente**| **Classificar e restringir o acesso manualmente e automaticamente**| **Classificar e restringir o acesso manualmente e automaticamente** |
| Grupos de segurança do Azure AD| **Políticas de acesso condicional para acesso não incluídas em pacotes do Access**| | |  |


### <a name="entitlement-management"></a>Gerenciamento de direitos 

Os [pacotes de acesso de gerenciamento de direitos](../governance/entitlement-management-access-package-create.md) permitem o provisionamento e o desprovisionamento de acesso a grupos e equipes, aplicativos e sites do SharePoint. Você pode definir quais organizações conectadas têm permissão de acesso, se as solicitações de autoatendimento são permitidas e quais fluxos de trabalho de aprovação são necessários (se houver) para conceder acesso. Para garantir que o acesso não fique mais longo do que o necessário, você pode definir políticas de expiração e revisões de acesso para cada pacote de acesso. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Controle o acesso com o Azure AD P1 e o Microsoft/Office 365 E3
Você pode obter um controle robusto com o Azure AD P1 e o Microsoft 365 E3

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Provisionamento, entrada, revisão de acesso e desprovisionamento


|Recurso | Provisionar usuários externos| Impor requisitos de entrada| Examinar acesso| Desprovisionar o acesso |
| - |-|-|-|-|
| Colaboração B2B do Azure AD| **Convidar por email, OTP, autoatendimento**| Federação B2B direta| **Análise periódica por parceiro**| Remover conta<br>Restringir entrada |
| Grupos do Microsoft ou Office 365| | | | Expiração ou exclusão do grupo.<br>Remoção do grupo. |
| Grupos de segurança| | **Adicionar usuários externos a grupos de segurança (org, Team, Project, etc.)**| |  |
| Políticas de acesso condicional| | **Políticas de acesso condicional de entrada para usuários externos**| |  |


 ### <a name="access-to-resources"></a>Acesso aos recursos.

|Recurso | Acesso a recursos de & de aplicativo| Acesso do SharePoint & OneDrive| Acesso às equipes| Segurança de documentos de & de email |
| - |-|-|-|-|
| Grupos do Microsoft ou Office 365| | **Acesso a sites incluídos com grupo (e conteúdo associado)**|**Acesso às equipes incluídas no grupo de Microsoft 365 (e conteúdo associado)**|  |
| Rótulos de confidencialidade| | Classificar e restringir o acesso manualmente| Classifique e restrinja o acesso manualmente.| Classificar manualmente para restringir e criptografar |
| Políticas de Acesso Condicional| Políticas de acesso condicional para controle de acesso| | |  |
| Métodos adicionais| | Restrinja o acesso ao site do SharePoint de maneira granular com grupos de segurança.<br>Não permitir compartilhamento direto.| **Restringir convites externos de dentro de equipes**|  |


### <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir sobre como proteger o acesso externo aos recursos. Recomendamos que você execute as ações na ordem listada.

1. [Determinar sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descobrir seu estado atual](2-secure-access-current-state.md)

3. [Crie um plano de governança](3-secure-access-plan.md) (você está aqui.)

4. [Usar grupos de segurança](4-secure-access-groups.md)

5. [Transição para B2B do Azure AD](5-secure-access-b2b.md)

6. [Acesso seguro com gerenciamento de direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com rótulos de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Proteger o acesso ao Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)