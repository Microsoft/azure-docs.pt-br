---
title: "Gerenciamento de provisionamento de usuário para aplicativos empresariais no Azure Active Directory | Microsoft Docs"
description: "Saiba como gerenciar o provisionamento de contas de usuário para aplicativos empresariais usando o Azure Active Directory"
services: active-directory
documentationcenter: 
author: asmalser
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 44476bbf0e878e347d176077e08706d1afd44479
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gerenciamento de provisionamento de conta de usuário para aplicativos empresariais no Portal do Azure
Este artigo descreve como usar o [Portal do Azure](https://portal.azure.com) para gerenciar o provisionamento de conta de usuário automático e o desprovisionamento para aplicativos que oferecem suporte a ele, especialmente aqueles que foram adicionados da categoria "em destaque" da [Galeria de aplicativos do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Para saber mais sobre o provisionamento de contas de usuário automático e como ele funciona, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Localizar seus aplicativos no portal
Todos os aplicativos configurados para logon único em um diretório por um administrador de diretório usando a [galeria de aplicativos do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) podem ser exibidos e gerenciados no [Portal do Azure](https://portal.azure.com). Os aplicativos podem ser encontrados na seção **Mais Serviços** &gt; **Aplicativos Empresariais** do Portal. Aplicativos empresariais são aplicativos que são implantados e usados dentro da sua organização.

![Folha de Aplicativos Empresariais][0]

Selecionar o link **Todos os aplicativos** à esquerda mostra uma lista de todos os aplicativos que foram configurados, incluindo aplicativos adicionados da galeria. A seleção de um aplicativo carrega a folha de recursos do aplicativo, na qual os relatórios podem ser exibidos para que o aplicativo e várias configurações possam ser gerenciados.

Configurações de provisionamento de conta de usuário podem ser gerenciadas selecionando **Provisionamento** à esquerda.

![Folha de recursos do aplicativo][1]

## <a name="provisioning-modes"></a>Modos de provisionamento
A folha **Provisionamento** começa com um menu **Modo**, que mostra quais modos de provisionamento têm suporte para um aplicativo empresarial e permite que eles sejam configurados. As opções disponíveis incluem:

* **Automático** - essa opção aparecerá se o Azure AD der suporte ao provisionamento automático baseado em API e/ou ao desprovisionamento de contas de usuário para esse aplicativo. Selecionar esse modo exibe uma interface que orienta os administradores a configurar o Azure AD para conectar-se à API de gerenciamento de usuário do aplicativo, criando mapeamentos de conta e fluxos de trabalho que definem como os dados da conta de usuário devem fluir entre o Azure AD e o aplicativo e gerenciar o serviço de provisionamento do Azure AD.
* **Manual** - essa opção será mostrada se o Azure AD não der suporte ao provisionamento automático de contas de usuário para esse aplicativo. Essa opção significa que registros de conta de usuário armazenados no aplicativo devem ser gerenciados usando um processo externo, com base nos recursos de gerenciamento e provisionamento do usuário fornecidos pelo aplicativo (o que pode incluir o provisionamento de SAML Just-In-Time).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurar o provisionamento de contas de usuário automático
Selecionar a opção **Automático** exibe uma tela dividida em quatro seções:

### <a name="admin-credentials"></a>Credenciais de administrador
É onde são inseridas as credenciais necessárias para o Azure AD conectar-se à API de gerenciamento de usuários do aplicativo. A entrada necessária varia dependendo do aplicativo. Para saber mais sobre os tipos de credencial e os requisitos para aplicativos específicos, confira o [tutorial de configuração para o aplicativo específico](active-directory-saas-app-provisioning.md).

Selecionar o botão **Testar Conexão** permite que você teste as credenciais fazendo com que o Azure AD tente se conectar ao aplicativo de provisionamento do aplicativo usando as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos
É onde os administradores podem exibir e editar o fluxo de atributos de usuário entre o Azure AD e o aplicativo de destino, quando as contas de usuário são provisionadas ou atualizadas.

Há um conjunto predefinido de mapeamentos entre objetos de usuário do Azure AD e objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos ou contatos. Selecionar um desses mapeamentos na tabela mostra o editor de mapeamento à direita, no qual eles podem ser visualizados e personalizados.

![Folha de recursos do aplicativo][2]

As personalizações com suporte incluem:

* Habilitar e desabilitar mapeamentos para objetos específicos, como o objeto de usuário do Azure AD para o objeto de usuário do aplicativo SaaS.
* Editar os atributos de fluxo do objeto de usuário do Azure AD para o objeto de usuário do aplicativo. Para obter mais informações sobre mapeamento de atributos, confira [Noções básicas sobre tipos de mapeamento de atributos](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Filtre as ações de provisionamento que o Azure AD executa no aplicativo de destino. Em vez de fazer com que o Azure AD sincronize totalmente objetos, você pode limitar as ações executadas. Por exemplo, selecionando apenas **Atualizar**, o Azure AD atualiza apenas contas de usuário existentes em um aplicativo e não cria novas. Selecionando apenas **Criar**, o Azure só cria novas contas de usuário, mas não atualiza as existentes. Esse recurso permite que os administradores criem mapeamentos diferentes para criar contas e atualizar os fluxos de trabalho.

### <a name="settings"></a>Configurações
Esta seção permite que os administradores iniciem e parem o serviço de provisionamento do Azure AD para o aplicativo selecionado, bem como, opcionalmente, limpem o cache de provisionamento e reiniciem o serviço.

Se o provisionamento estiver sendo habilitado pela primeira vez para um aplicativo, ative o serviço alterando o **Status de Provisionamento** para **Ativado**. Isso faz com que o serviço de provisionamento do Azure AD execute uma sincronização inicial, em que ele lê os usuários atribuídos na seção **Usuários e grupos**, consulta o aplicativo de destino para eles e, em seguida, executa as ações de provisionamento definidas na seção **Mapeamentos** do Azure AD. Durante esse processo, o serviço de provisionamento armazena dados em cache sobre quais contas de usuário ele está gerenciando, para que contas não gerenciadas nos aplicativos de destino que nunca estiveram em escopo para atribuição não sejam afetadas por operações de desprovisionamento. Após a sincronização inicial, o serviço de provisionamento sincroniza automaticamente objetos de usuário e grupo em um intervalo de dez minutos.

Alterar o **Status de Provisionamento** para **Desativado** simplesmente pausa o serviço de provisionamento. Nesse estado, o Azure não cria, atualiza nem remove objetos de usuário ou grupo no aplicativo. Alterar o estado para ativado faz com que o serviço continue de onde parou.

Marcar a caixa de seleção **Limpar o estado atual e reiniciar a sincronização** e salvar interrompe o serviço de provisionamento, despeja os dados de cache sobre quais contas o Azure AD está gerenciando, reinicia os serviços e executa novamente a sincronização inicial. Essa opção permite que os administradores iniciem novamente o processo de implantação de provisionamento.

### <a name="synchronization-details"></a>Detalhes da sincronização
Esta seção fornece detalhes de adição sobre a operação de serviço do provisionamento, incluindo a primeira e a última vez que o serviço de provisionamento foi executado no aplicativo e quantos objetos de usuário e grupo estão sendo gerenciados.

São fornecidos links para o **Relatório de atividade de provisionamento**, que fornece um log de todos os usuários e grupos criados, atualizados e removidos entre o Azure AD e o aplicativo de destino e o **Relatório de erros de provisionamento**, que fornece mensagens de erro mais detalhadas para objetos de usuário e grupo que não puderam ser lidos, criados, atualizados ou removidos. 

##<a name="feedback"></a>Comentários

Esperamos que você goste de sua experiência com o Azure AD. Continue a fazer seus comentários! Poste seus comentários e suas ideias para aprimoramento na seção **Portal de Administração** do nosso [fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Estamos empolgados para criar algo novo e interessante diariamente e usar suas diretrizes para formar e definir o que devemos criar a seguir.


[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
