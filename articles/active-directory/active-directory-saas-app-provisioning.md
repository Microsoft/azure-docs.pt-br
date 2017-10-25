---
title: "Provisionamento de usuário automatizado em aplicativo SaaS no Azure AD | Microsoft Docs"
description: "Uma introdução a como você pode usar o Azure AD para provisionar, desprovisionar e atualizar contínua e automaticamente as contas de usuário em vários aplicativos SaaS de terceiros."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 3fe57e9c22d04a3557978093ce3fe86613c5c1d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatize o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>O que é o provisionamento automatizado de usuários para aplicativos SaaS?
O Azure AD (Azure Active Directory) permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos em nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) como Dropbox, Salesforce, ServiceNow e muito mais.

**Abaixo estão alguns exemplos do que esse recurso permite que você faça:**

* Crie automaticamente novas contas nos sistemas certos quando novas pessoas entrarem em sua equipe ou organização.
* Desative automaticamente as contas nos sistemas certos quando as pessoas deixarem a equipe ou organização.
* Mantenha as identidades em seus aplicativos e sistemas atualizadas com base nas alterações no diretório ou em seu sistema de recursos humanos.
* Provisione objetos que não são de usuário, como grupos, para aplicativos que dão suporte a eles.

**O provisionamento automatizado de usuário também inclui a seguinte funcionalidade:**

* A capacidade de corresponder identidades existentes entre os aplicativos de origem e destino.
* Opções de personalização para ajudar o Azure AD a se ajustar às configurações atuais dos aplicativos e sistemas que sua organização está usando atualmente.
* Alertas de email opcionais para erros de provisionamento.
* Logs de relatórios e de registros para ajudá-lo a monitorar e a solucionar problemas.

## <a name="why-use-automated-provisioning"></a>Por que usar o provisionamento automatizado?
Alguns motivos comuns para usar esse recurso incluem:

* Evitar custos, ineficiências e erro humano associados a processos manuais de provisionamento.
* Evitar os custos associados à hospedagem e manutenção de scripts e soluções de provisionamento personalizadas
* Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles saírem da organização.
* Importar facilmente um número em massa de usuários para determinado aplicativo SaaS ou sistema.
* Aproveitar a conveniência de ter a solução de provisionamento ser executada fora das mesmas políticas de acesso do aplicativo que você definiu para o Logon Único do Azure AD.


## <a name="how-does-automatic-provisioning-work"></a>Como funciona o provisionamento automático?
    
O **Serviço de provisionamento do Azure AD** provisiona usuários para aplicativos SaaS e outros sistemas conectando-se aos pontos de extremidade da API de gerenciamento fornecidos por cada fornecedor de aplicativo. Esses pontos de extremidade de API de gerenciamento permitem que o Azure AD crie, atualize e remova usuários por meio de programação. Para os aplicativos selecionados, o serviço de provisionamento também pode criar, atualizar e remover objetos adicionais relacionados à identidade, como grupos e funções. 

![Provisionamento](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*Figura 1: o Serviço de provisionamento do Azure AD*

![Provisionamento externo](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*Figura 2: fluxo de trabalho de provisionamento do usuário "Externo" do Azure AD para aplicativos SaaS populares*

![Provisionamento interno](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*Figura 3: fluxo de trabalho de provisionamento do usuário "Interno" de aplicativos de HCM (gerenciamento de capital humano) para o Azure Active Directory e o Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso usar com o provisionamento de usuário automático do Azure AD?

O Azure AD apresenta suporte pré-integrado para vários sistemas de recursos humanos e aplicativos SaaS populares, bem como suporte genérico para aplicativos que implementam partes específicas do [padrão SCIM 2.0](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-scim-provisioning).

Todos os aplicativos "Em destaque" na galeria de aplicativos do Azure AD dão suporte a provisionamento de usuários automatizado. [A lista de aplicativos em destaque pode ser exibida aqui.](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

Para que um aplicativo dê suporte a provisionamento automatizado de usuários, primeiro ele deve fornecer os pontos de extremidade de gerenciamento necessários que permitam que programas externos automatizem a criação, a manutenção e a remoção de usuários. Portanto, nem todos os aplicativos SaaS são compatíveis com esse recurso. Para aplicativos que dão suporte às APIs de gerenciamento de usuário, a equipe de engenharia do Azure AD será capaz de criar um conector de provisionamento para eles e esse trabalho é priorizado de acordo com as necessidades de clientes atuais e potenciais. 

Para entrar em contato com a equipe de engenharia do Azure AD e solicitar o suporte de provisionamento para mais aplicativos, envie uma mensagem no [Fórum de comentários do Active Directory do Azure](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning). 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como configurar o provisionamento automático para um aplicativo?

A configuração do serviço de provisionamento do Azure AD em um aplicativo selecionado começa no **[Portal do Azure](https://potal.azure.com)**. Na seção **Azure Active Directory > Aplicativos Corporativos**, selecione **Adicionar**, **Tudo** e, depois, adicione o seguinte, dependendo do cenário:

* Todos os aplicativos na seção **Aplicativos em destaque** oferecem suporte ao provisionamento automático

* Use a opção "aplicativo que não é da galeria" para integrações de SCIM personalizados

![Galeria](./media/active-directory-saas-app-provisioning/gallery.png)

Na tela de gerenciamento de aplicativo, o provisionamento é configurado na guia **Provisionamento**.

![Configurações](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* É necessário fornecer **credenciais de administrador** para o serviço de provisionamento do Azure AD, assim ele poderá se conectar à API de gerenciamento de usuário fornecida pelo aplicativo.

* **Mapeamentos de atributo** podem ser configurados para especificar quais campos no sistema de origem (exemplo: Azure AD) passarão por sincronização de seus conteúdos, e com quais campos no sistema de destino (exemplo: ServiceNow). Se o aplicativo de destino oferecer suporte a isso, esta seção permitirá que você configure opcionalmente o provisionamento de grupos, além de contas de usuário. "Propriedades de correspondência" permitem que você selecione quais campos são usados para corresponder as contas entre os sistemas. "[Expressões](active-directory-saas-writing-expressions-for-attribute-mappings.md)" permitem que você modifique e transforme os valores recuperados do sistema de origem, antes de serem gravados no sistema de destino. Para saber mais, confira [Personalizar mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md).

![Configurações](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **Filtros de escopo** informam ao serviço de provisionamento quais usuários e grupos no sistema de origem devem ser provisionados e/ou desprovisionados no sistema de destino. Há dois aspectos para a definição de escopo dos filtros que são avaliados em conjunto e determinam quem está no escopo para provisionamento:

* **Filtro em valores de atributo** - O menu "Escopo do Objeto de Origem" nos mapeamentos de atributo permitem a filtragem de valores de atributo específicos. Por exemplo, você pode especificar que apenas os usuários com um atributo "Departamento" de "Vendas" devem estar no escopo para provisionamento.

* **Filtro nas atribuições de** - O menu "Escopo" na seção Provisionamento > Configurações do portal permite que você especifique se apenas usuários e grupos "atribuídos" devem estar no escopo para provisionamento, ou se todos os usuários no diretório do Azure AD devem ser provisionados. Para saber mais sobre "como atribuir" usuários e grupos, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* **Configurações** controlam a operação do serviço de provisionamento de um aplicativo, incluindo se ele está em execução no momento ou não.

* **Logs de auditoria** fornecem registros de todas as operações executadas pelo serviço de provisionamento do Azure AD. Para obter mais detalhes, consulte o [guia de relatório de provisionamento](active-directory-saas-provisioning-reporting.md).

![Configurações](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>O que acontece durante o provisionamento?

1. Quando você habilita o provisionamento de um aplicativo pela primeira vez, as seguintes ações são executadas:
   * O Azure AD tentará corresponder usuários existentes no aplicativo SaaS com suas identidades correspondentes no diretório. Quando um usuário for correspondido, ele *não* será automaticamente habilitado para logon único. Para que um usuário tenha acesso ao aplicativo, ele deve ser explicitamente atribuído ao aplicativo no AD do Azure, seja diretamente ou seja por meio da associação de grupo.
   * Se você já especificou quais usuários devem ser atribuídos ao aplicativo e o Azure AD não conseguir localizar contas existentes para esses usuários, o Azure AD provisionará novas contas para eles no aplicativo.
2. Depois que a sincronização inicial foi concluída conforme descrito acima, o Azure AD verificará as alterações abaixo a cada 20 minutos:
   * Se novos usuários tiverem sido atribuídos ao aplicativo (diretamente ou por meio de associação de grupo), eles serão provisionados com uma nova conta no aplicativo SaaS.
   * Se um acesso de usuário tiver sido removido, suas contas no aplicativo SaaS serão marcadas como desabilitadas (os usuários nunca são totalmente excluídos, o que protege contra perda de dados no caso de uma configuração incorreta).
   * Se um usuário tiver sido recentemente atribuído ao aplicativo e já tiver uma conta no aplicativo SaaS, essa conta será marcada como habilitada e determinadas propriedades de usuário poderão ser atualizadas se estiverem desatualizadas em relação ao diretório.
   * Se as informações de um usuário (como número de telefone, localização do escritório) forem alteradas no diretório, essas informações também serão atualizadas no aplicativo SaaS.


## <a name="frequently-asked-questions"></a>Perguntas frequentes
**Com que frequência o Azure AD grava as alterações de diretório para o aplicativo SaaS?**

Após uma sincronização completa inicial, o serviço de provisionamento do Azure AD normalmente verifica se houve alterações a cada 20 minutos. 

Se o aplicativo SaaS estiver retornando vários erros (por exemplo, como no caso de credenciais de administrador inválidas), o Azure AD diminuirá gradualmente sua frequência para até uma vez por dia até que os erros sejam corrigidos. Quando isso acontece, o trabalho de provisionamento do Azure AD entra em um estado de "quarentena", e indicará isso no [relatório resumido de provisionamento](active-directory-saas-provisioning-reporting.md).

**Quanto tempo levará para provisionar meus usuários?**

Após uma sincronização completa inicial, as alterações incrementais normalmente acontecem dentro de 20 a 40 minutos. Se você estiver tentando provisionar a maior parte do seu diretório, dependerá do número de usuários e de grupos que você tem. O desempenho depende do desempenho das APIs de gerenciamento de usuário usadas pelos serviços de provisionamento para ler dados do sistema de origem e gravar os dados no sistema de destino. 

O desempenho também é mais lento se houver muitos erros (registrados nos [logs de auditoria](active-directory-saas-provisioning-reporting.md)) e o serviço de provisionamento tiver entrado em um estado de "quarentena".

**O que é uma sincronização completa inicial, e por que ela demora mais do que as sincronizações subsequentes?**

Na primeira execução do serviço de provisionamento do Azure AD para um determinado aplicativo, ele tira um "instantâneo" dos usuários (e, opcionalmente, grupos) no diretório de origem. Esse instantâneo permite que o serviço de provisionamento reduza o número de viagens de ida e volta para as APIs de origem e de destino, e permite que as sincronizações "delta" subsequentes se comportem de forma mais eficiente. 

Normalmente, a sincronização completa inicial de usuários pode ser concluída em minutos no caso de diretórios muito pequenos, mas pode demorar várias horas para diretórios maiores. A conclusão inicial de diretórios corporativos com centenas de milhares de usuários pode demorar várias horas. No entanto, após a sincronização inicial, as sincronizações "delta" subsequentes ocorrem muito mais rapidamente.

> [!NOTE]
> Para aplicativos que dão suporte ao provisionamento de grupos e associações de grupo, habilitar essa opção aumenta muito o tempo necessário para concluir uma sincronização completa. Se você não quiser provisionar nomes de grupo e associações de grupo para o seu aplicativo, você pode desabilitar essa opção nos [mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md) de sua configuração de provisionamento.

**Como posso acompanhar o progresso do trabalho de provisionamento atual?**

Confira o [guia de relatório de provisionamento](active-directory-saas-provisioning-reporting.md).

**Como saber se os usuários não estão sendo provisionados corretamente?**

Todas as falhas são registradas nos logs de auditoria do Azure AD. Para saber mais, confira o [guia de relatório de provisionamento](active-directory-saas-provisioning-reporting.md).

**Como posso enviar comentários à equipe de engenharia?**

Entre em contato conosco através do [Fórum de comentários do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](active-directory-saas-customizing-attribute-mappings.md)
* [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de escopo para provisionamento de usuários](active-directory-saas-scoping-filters.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
* [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

