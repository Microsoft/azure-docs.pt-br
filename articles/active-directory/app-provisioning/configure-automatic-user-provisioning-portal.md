---
title: Gerenciamento de provisionamento de usuário para aplicativos empresariais no Azure Active Directory
description: Saiba como gerenciar o provisionamento de conta de usuário para aplicativos empresariais usando o Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5dceeb11ed9a4d6af88650a6146f58db412748d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579409"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gerenciamento de provisionamento de conta de usuário para aplicativos empresariais no Portal do Azure

Este artigo descreve as etapas gerais para gerenciar o provisionamento e o desprovisionamento automáticos de conta de usuário para aplicativos com suporte. *Provisionamento de contas de usuário* é o ato de criar, atualizar e/ou desabilitar os registros de conta de usuário no armazenamento de perfil do usuário local do aplicativo. A maioria dos aplicativos de nuvem e SaaS armazenam a função e as permissões de usuário no armazenamento de perfil do usuário local, e a presença de um registro de usuário no armazenamento local do usuário é *necessária* para logon único e acesso ao trabalho. Para saber mais sobre o provisionamento de contas de usuário automático, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS com o Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> O Azure AD (Azure Active Directory) tem uma galeria que contém milhares de aplicativos pré-integrados habilitados para provisionamento automático com o Azure AD. Comece localizando o tutorial de configuração de provisionamento específico de seu aplicativo na [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](../saas-apps/tutorial-list.md). Provavelmente, você encontrará diretrizes passo a passo para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento.

## <a name="finding-your-apps-in-the-portal"></a>Localizar seus aplicativos no portal

Use o portal do Azure Active Directory para exibir e gerenciar todos os aplicativos configurados para logon único em um diretório. Aplicativos empresariais são aplicativos que são implantados e usados dentro da sua organização. Siga estas etapas para exibir e gerenciar seus aplicativos empresariais:

1. Abra o [portal do Azure Active Directory](https://aad.portal.azure.com).
1. Selecione **Aplicativos empresariais** no painel esquerdo. É mostrada uma lista com todos os aplicativos configurados, incluindo aplicativos que foram adicionados da galeria.
1. Selecione qualquer aplicativo para carregar o painel de recursos dele, no qual é possível ver relatórios e gerenciar configurações do aplicativo.
1. Selecione **Provisionamento** para gerenciar as configurações de provisionamento de conta do usuário no aplicativo selecionado.

   ![Tela Provisionamento para gerenciar as configurações de provisionamento de conta do usuário](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modos de provisionamento

O painel **Provisionamento** começa com um menu **Modo**, que mostra os modos de provisionamento com suporte para um aplicativo empresarial e permite configurá-los. As opções disponíveis incluem:

* **Automático** – essa opção será exibida se o Azure AD der suporte ao provisionamento ou desprovisionamento automático baseado em API das contas de usuário desse aplicativo. Selecione este modo para exibir uma interface que ajuda os administradores a:

  * Configurar o Azure AD para conexão à API de gerenciamento de usuários do aplicativo
  * Criar mapeamentos de conta e fluxos de trabalho que definem como os dados de conta de usuário devem fluir entre o Azure AD e o aplicativo
  * Gerenciar o serviço de provisionamento do Azure AD

* **Manual** – essa opção será mostrada se o Azure AD não der suporte ao provisionamento automático de contas de usuário para o aplicativo. Nesse caso, os registros de conta de usuário armazenados no aplicativo devem ser gerenciados usando um processo externo, com base nos recursos de gerenciamento e provisionamento do usuário fornecidos pelo aplicativo (o que pode incluir o provisionamento de SAML Just-In-Time).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurar o provisionamento de contas de usuário automático

Selecione a opção **Automático** para especificar as configurações das credenciais de administrador, mapeamentos, início e parada e sincronização.

### <a name="admin-credentials"></a>Credenciais de administrador

Expanda **Credenciais de Administrador** para inserir as credenciais necessárias para o Azure AD se conectar à API de gerenciamento de usuários do aplicativo. A entrada necessária varia dependendo do aplicativo. Para saber mais sobre os tipos de credencial e os requisitos para aplicativos específicos, confira o [tutorial de configuração para o aplicativo específico](user-provisioning.md).

Selecione **Testar Conexão** para testar as credenciais, fazendo com que o Azure AD tente se conectar ao aplicativo de provisionamento do aplicativo usando as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos

Expanda **Mapeamentos** para exibir e editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino quando as contas de usuário são provisionadas ou atualizadas.

Há um conjunto predefinido de mapeamentos entre os objetos de usuário do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos também gerenciam objetos de grupo. Selecione um mapeamento na tabela para abrir o editor de mapeamento, onde você pode exibi-los e personalizá-los.

As personalizações com suporte incluem:

* Habilitar e desabilitar mapeamentos para objetos específicos, como o objeto de usuário do Azure AD para o objeto de usuário do aplicativo SaaS.
* Editar os atributos que fluem do objeto de usuário do Azure AD para o objeto de usuário do aplicativo. Para obter mais informações sobre mapeamento de atributos, confira [Noções básicas sobre tipos de mapeamento de atributos](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrar as ações de provisionamento que o Azure AD executa no aplicativo de destino. Em vez de fazer com que o Azure AD sincronize totalmente os objetos, você pode limitar as ações executadas.

  Por exemplo, selecione apenas **Atualizar** e o Azure AD apenas atualizará as contas de usuário existentes, sem criar outras. Selecione **Criar** e o Azure apenas criará contas de usuário, sem atualizar as existentes. Esse recurso permite que os administradores criem mapeamentos diferentes para criar contas e atualizar os fluxos de trabalho.

* Adicionar um novo mapeamento de atributos. Selecione **Adicionar Novo Mapeamento** na parte inferior do painel **Mapeamento de Atributos**. Preencha o formulário **Editar Atributo** e selecione **Ok** para adicionar o novo mapeamento à lista.

### <a name="settings"></a>Configurações

Expanda **configurações** para definir um endereço de email para receber notificações e se deseja receber alertas sobre erros. Você também pode selecionar o escopo de usuários a serem sincronizados. Você pode optar por sincronizar todos os usuários e grupos ou somente aqueles atribuídos.

### <a name="provisioning-status"></a>Status de provisionamento 

Se o provisionamento estiver sendo habilitado pela primeira vez para um aplicativo, ative o serviço alterando o **Status de Provisionamento** para **Ativado**. Essa alteração faz com que o serviço de provisionamento do Azure AD execute um ciclo inicial. Ele lê os usuários atribuídos na seção **Usuários e grupos**, consulta o aplicativo de destino para eles e executa as ações de provisionamento definidas na seção **Mapeamentos** do Azure AD. Durante esse processo, o serviço de provisionamento armazena dados em cache sobre quais contas de usuário ele está gerenciando, para que contas não gerenciadas nos aplicativos de destino que nunca estiveram em escopo para atribuição não sejam afetadas por operações de desprovisionamento. Após o ciclo inicial, o serviço de provisionamento sincroniza automaticamente objetos de usuário e grupo em um intervalo de quarenta minutos.

Altere o **Status de Provisionamento** para **Desativado** para pausar o serviço de provisionamento. Nesse estado, o Azure não cria, atualiza nem remove objetos de usuário ou grupo no aplicativo. Altere o estado de volta para **Ativado** e o serviço continuará de onde parou.
