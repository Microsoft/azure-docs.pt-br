---
title: Controle o acesso externo aos recursos no Azure Active Directory com rótulos de sensibilidade.
description: Use rótulos de sensibilidade como parte de seu plano de segurança geral para acesso externo.
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
ms.openlocfilehash: 0dcb6d24eacbe594a907f084874e76fea963c561
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725121"
---
# <a name="control-access-with-sensitivity-labels"></a>Controlar o acesso com rótulos de sensibilidade 

Os [Rótulos de sensibilidade](/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide) ajudam a controlar o acesso ao seu conteúdo em aplicativos do Office 365 e em contêineres como Microsoft Teams, grupos de Microsoft 365 e sites do SharePoint. Eles podem proteger seu conteúdo sem atrapalhar os recursos de colaboração e produção dos usuários. Os rótulos de sensibilidade permitem que você envie o conteúdo da sua organização entre dispositivos, aplicativos e serviços, ao mesmo tempo em que protege seus dados e atende às suas políticas de conformidade e segurança. 

Com rótulos de sensibilidade, você pode:

* **Classifique o conteúdo sem adicionar nenhuma configuração de proteção**. Você pode atribuir uma classificação ao conteúdo (como um adesivo) que persiste e roames com o conteúdo conforme ele é usado e compartilhado. Você pode usar essa classificação para gerar relatórios de uso e ver dados de atividade relacionados ao seu conteúdo confidencial.

* **Impor as configurações de proteção, como criptografia, marcas-d ' água e restrições de acesso**. Por exemplo, os usuários podem aplicar um rótulo confidencial a um documento ou email, e esse rótulo pode [criptografar o conteúdo](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) e adicionar uma marca d' água "confidencial". Além disso, você pode [aplicar um rótulo de sensibilidade a um contêiner](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide) como um site do SharePoint e impor se usuários externos podem acessar o conteúdo que ele contém.

Rótulos de sensibilidade de email e outros conteúdos viajam com o conteúdo. Os rótulos de sensibilidade nos contêineres podem restringir o acesso ao contêiner, mas o conteúdo no contêiner não herda o rótulo. Por exemplo, um usuário poderia pegar conteúdo de um site protegido, baixá-lo e, em seguida, compartilhá-lo sem restrições, a menos que o conteúdo também tenha um rótulo de sensibilidade.

 >[!NOTE]
>Para aplicar rótulos de sensibilidade, os usuários devem estar conectados à sua conta corporativa ou de estudante da Microsoft. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Permissões necessárias para criar e gerenciar níveis de sensibilidade

Os membros da sua equipe de conformidade que criarão rótulos de sensibilidade precisarão de permissões para o centro de conformidade Microsoft 365, a central de segurança do Microsoft 365 ou o centro de conformidade do & de segurança.

Por padrão, os administradores globais do seu locatário têm acesso a esses centros de administração e podem dar aos gerentes de conformidade e a outras pessoas acesso, sem conceder a eles todas as permissões de um administrador de locatários. Para esse acesso de administrador limitado delegado, adicione usuários ao grupo de funções administrador de dados de conformidade, administrador de conformidade ou administrador de segurança.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Determinar sua estratégia de rótulo de confidencialidade

Como você imagina ao controlar o acesso externo ao seu conteúdo, determine o seguinte:

**Para todo o conteúdo e contêineres**

* Como você definirá o que é um impacto comercial alto, médio ou baixo (ICA, MBI, bin)? Considere o impacto para a sua organização se tipos específicos de conteúdo são compartilhados inadequadamente.

   * Conteúdo com tipos específicos de conteúdo inerentemente [confidencial](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide), como cartões de crédito ou números de passaporte

   * Conteúdo criado por grupos ou pessoas específicas (por exemplo, gerentes de conformidade, gerentes financeiros ou executivos)

   * Conteúdo em bibliotecas ou sites específicos. Por exemplo, contêineres que hospedam estratégia organizacional ou dados financeiros privados

   * Outros critérios

* Quais categorias de conteúdo (por exemplo, conteúdo Ain) devem ser restringidas do acesso por usuários externos?

   * As restrições podem incluir ações como restringir o acesso a contêineres e criptografar o conteúdo.

* Quais padrões devem estar em vigor para dados ICA, sites ou grupos de Microsoft 365?

* Onde você usará rótulos de sensibilidade para [rotular e monitorar](/microsoft-365/compliance/label-analytics?view=o365-worldwide), em vez de [impor a criptografia](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) ou [impor restrições de acesso de contêiner](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)?

**Para email e conteúdo**

* Deseja [aplicar automaticamente rótulos de sensibilidade](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) ao conteúdo ou fazê-lo manualmente?

   * Se você optar por fazer isso manualmente, você deseja [recomendar que os usuários apliquem um rótulo](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)?

**Para contêineres**

* Quais critérios determinarão se os grupos de M365, as equipes ou os sites do SharePoint exigem acesso restrito usando rótulos de sensibilidade?

* Você deseja rotular apenas o conteúdo nesses contêineres se mover para frente ou deseja [rotular automaticamente](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) os arquivos existentes no SharePoint e no onedrive?

Veja esses [cenários comuns de rótulos de sensibilidade](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide) para outras ideias sobre como você pode usar rótulos de sensibilidade.

### <a name="sensitivity-labels-on-email-and-content"></a>Rótulos de sensibilidade de email e conteúdo

Quando você atribui um rótulo de sensibilidade a um documento ou email, é como um carimbo que é aplicado ao conteúdo que é personalizável, de texto não criptografado e persistente. 

* **Personalizável** significa que você pode criar rótulos apropriados para sua organização e determinar o que acontece quando eles são aplicados.

* **Texto não criptografado** significa que ele faz parte dos metadados do item e é legível por aplicativos e serviços para que eles possam aplicar suas próprias ações de proteção.

* **Persistent** significa que o rótulo e as proteções associadas são móveis com o conteúdo e tornam-se a base para aplicar e impor políticas.

 

> [!NOTE]
> Cada item de conteúdo pode ter um único rótulo de sensibilidade aplicado a ele.


### <a name="sensitivity-labels-on-containers"></a>Rótulos de sensibilidade em contêineres

Você pode aplicar rótulos de sensibilidade em contêineres, como [grupos de Microsoft 365](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft Teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)e [sites do SharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide). Quando você aplica esse rótulo de sensibilidade a um contêiner com suporte, o rótulo aplica automaticamente as configurações de classificação e proteção ao site ou grupo conectado. Os rótulos de sensibilidade nesses contêineres podem controlar os seguintes aspectos dos contêineres:

* **Privacidade**. Você pode escolher quem pode ver o site: usuários específicos, todos os usuários internos ou qualquer pessoa.

* **Acesso de usuário externo**. Controla se o proprietário do grupo pode adicionar convidados ao grupo.

* **Acesso de dispositivos não gerenciados**. Determina se e como os dispositivos não gerenciados podem acessar o conteúdo.

 

![Uma captura de tela da edição de rótulos de sensibilidade](media/secure-external-access/8-edit-label.png)

 

Quando você aplica um rótulo de sensibilidade a um contêiner, como um site do SharePoint, ele não é aplicado ao conteúdo ali: os rótulos de sensibilidade nos contêineres controlam o acesso ao conteúdo dentro do contêiner. 

* Se você quiser aplicar rótulos automaticamente ao conteúdo dentro do contêiner, consulte [aplicar uma sensibilidade ao conteúdo automaticamente](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide).

* Se você quiser que os usuários possam aplicar rótulos manualmente a esse conteúdo, certifique-se de ter [habilitado rótulos de sensibilidade para arquivos do Office no SharePoint e no onedrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide).

### <a name="plan-to-implement-sensitivity-labels"></a>Planejar a implementação de rótulos de sensibilidade

Depois de determinar como deseja usar rótulos de sensibilidade e para que conteúdo e sites você deseja aplicá-los, consulte a documentação a seguir para ajudá-lo a executar sua implementação.

1. [Introdução aos rótulos de sensibilidade](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

2. [Criar uma estratégia de implantação](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

3. [Criar e publicar rótulos de sensibilidade](/microsoft-365/compliance/create-sensitivity-labels?view=o365-worldwide)

4. [Restringir o acesso ao conteúdo usando rótulos de sensibilidade para aplicar criptografia](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)

5. [Usar rótulos de sensibilidade com equipes, grupos e sites](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)

6. [Habilitar rótulos de sensibilidade para arquivos do Office no SharePoint e no OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)

### <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir sobre como proteger o acesso externo aos recursos. Recomendamos que você execute as ações na ordem listada.

1. [Determinar a postura de segurança desejada para acesso externo](1-secure-access-posture.md)

2. [Descobrir seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governança](3-secure-access-plan.md)

4. [Usar grupos de segurança](4-secure-access-groups.md)

5. [Transição para B2B do Azure AD](5-secure-access-b2b.md)

6. [Acesso seguro com gerenciamento de direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Proteja o acesso com rótulos de sensibilidade](8-secure-access-sensitivity-labels.md) (você está aqui.)

9. [Proteger o acesso ao Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)