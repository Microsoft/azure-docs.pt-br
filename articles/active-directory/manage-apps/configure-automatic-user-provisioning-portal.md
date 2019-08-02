---
title: Gerenciamento de provisionamento de usuário para aplicativos empresariais no Azure Active Directory | Microsoft Docs
description: Saiba como gerenciar o provisionamento de contas de usuário para aplicativos empresariais usando o Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: b994078350aec5657659f8835d228eb907606bb8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807632"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gerenciamento de provisionamento de conta de usuário para aplicativos empresariais no Portal do Azure

Este artigo descreve como usar o [Portal do Microsoft Azure](https://portal.azure.com) para gerenciar o provisionamento e o desprovisionamento de conta de usuário automáticos para aplicativos que o suportam. Para saber mais sobre o provisionamento de contas de usuário automático e como ele funciona, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS com o Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Localizar seus aplicativos no portal

Use o portal do Azure Active Directory para exibir e gerenciar todos os aplicativos que estão configurados para logon único em um diretório. Aplicativos empresariais são aplicativos que são implantados e usados dentro da sua organização. Siga estas etapas para exibir e gerenciar seus aplicativos empresariais:

1. Abra o [portal do Azure Active Directory](https://aad.portal.azure.com).
1. Selecione **aplicativos empresariais** no painel esquerdo. Uma lista de todos os aplicativos configurados, incluindo os aplicativos que foram adicionados da galeria, é mostrada.
1. Selecione qualquer aplicativo para carregar seu painel de recursos, onde você pode exibir relatórios e gerenciar as configurações do aplicativo.
1. Selecione **provisionamento** para gerenciar configurações para o aplicativo selecionado de provisionamento de conta de usuário.

   ![Tela de provisionamento para gerenciar configurações de provisionamento de conta de usuário](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modos de provisionamento

O **provisionamento** painel começa com um **modo** menu, que mostra os modos de provisionamento com suporte para um aplicativo empresarial e permite que você configurá-los. As opções disponíveis incluem:

* **Automático** -essa opção será mostrada se Azure AD dá suporte ao provisionamento automático baseado em API ou desprovisionamento de contas de usuário para este aplicativo. Selecione este modo para exibir uma interface que ajuda os administradores:

  * Configurar o Azure AD para se conectar à API de gerenciamento de usuário do aplicativo
  * Criar mapeamentos de conta e fluxos de trabalho que definem como os dados da conta de usuário devem fluir entre o Azure AD e o aplicativo
  * Gerenciar o serviço de provisionamento do AD do Azure

* **Manual** -essa opção será mostrada se o AD do Azure não dá suporte a provisionamento automático de contas de usuário para este aplicativo. Nesse caso, os registros armazenados no aplicativo de conta de usuário deve ser gerenciada usando um processo externo, com base em que os recursos de gerenciamento e provisionamento de usuário fornecidos pelo aplicativo (o que pode incluir o provisionamento de SAML just-in-).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurar o provisionamento de contas de usuário automático

Selecione o **automática** opção para especificar configurações de credenciais de administrador, mapeamentos, iniciando e parando e sincronização.

### <a name="admin-credentials"></a>Credenciais de administrador

Expandir **credenciais de administrador** para inserir as credenciais necessárias para o Azure AD para se conectar à API de gerenciamento de usuário do aplicativo. A entrada necessária varia dependendo do aplicativo. Para saber mais sobre os tipos de credencial e os requisitos para aplicativos específicos, confira o [tutorial de configuração para o aplicativo específico](user-provisioning.md).

Selecione **Testar Conexão** para testar as credenciais fazendo com que o Azure AD tente se conectar ao aplicativo de provisionamento do aplicativo usando as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos

Expandir **mapeamentos** para exibir e editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino quando as contas de usuário são provisionadas ou atualizadas.

Há um conjunto predefinido de mapeamentos entre os objetos de usuário do Azure AD e objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos ou contatos. Selecione um mapeamento na tabela para abrir o editor de mapeamento para a direita, onde você pode exibir e personalizá-los.

![Mostra a tela de mapeamento de atributos](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

As personalizações com suporte incluem:

* Habilitar e desabilitar mapeamentos para objetos específicos, como o objeto de usuário do Azure AD para o objeto de usuário do aplicativo SaaS.
* Editar os atributos que fluem do objeto de usuário do Azure AD para o objeto de usuário do aplicativo. Para obter mais informações sobre mapeamento de atributos, confira [Noções básicas sobre tipos de mapeamento de atributos](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrando as ações de provisionamento do Azure AD é executado no aplicativo de destino. Em vez de ter o Azure AD sincronize totalmente objetos, você pode limitar as ações a executar.

  Por exemplo, selecione somente **atualização** e Azure AD atualiza apenas contas em um aplicativo de usuário existente, mas não cria novos. Selecione somente **criar** e Azure só cria novas contas de usuário, mas não atualiza as existentes. Esse recurso permite que os administradores criem mapeamentos diferentes para a criação da conta e atualizar os fluxos de trabalho.

* Adicionando um novo mapeamento de atributo. Selecione **adicionar novo mapeamento** na parte inferior a **mapeamento de atributos** painel. Preencha a **Editar atributo** formam e selecione **Okey** para adicionar o novo mapeamento para a lista.

### <a name="settings"></a>Configurações

Você pode iniciar e parar o serviço para o aplicativo selecionado de provisionamento do Azure AD a **as configurações** área da **provisionamento** tela. Você também pode optar por limpar o cache de provisionamento e reinicie o serviço.

Se o provisionamento estiver sendo habilitado pela primeira vez para um aplicativo, ative o serviço alterando o **Status de Provisionamento** para **Ativado**. Essa alteração faz com que o Azure AD serviço de provisionamento executar uma sincronização inicial. Ele lê os usuários atribuídos na **usuários e grupos** seção, consulta o aplicativo de destino para eles e, em seguida, executa as ações de provisionamento definidas no Azure AD **mapeamentos** seção. Durante esse processo, o serviço de provisionamento armazena dados em cache sobre quais contas de usuário que está gerenciando, para que contas não gerenciadas nos aplicativos de destino que nunca estiveram em escopo de atribuição não são afetadas por operações de desprovisionamento. Após a sincronização inicial, o serviço de provisionamento sincroniza automaticamente objetos de usuário e grupo em um intervalo de dez minutos.

Alterar o **Status de provisionamento** à **Off** para pausar o serviço de provisionamento. Nesse estado, o Azure não criar, atualizar ou remover qualquer usuário ou objetos de grupo no aplicativo. Altere o estado de volta para **em** e o serviço escolhe de onde parou.

Selecione o **limpar o estado atual e reiniciar a sincronização** caixa de seleção e selecione **salvar** para:

* Parar o serviço de provisionamento
* Despejar os dados armazenados em cache sobre quais contas do Azure AD está gerenciando
* Reinicie os serviços e execute novamente a sincronização inicial

Essa opção permite aos administradores iniciar o processo de implantação de provisionamento novamente.

### <a name="synchronization-details"></a>Detalhes da sincronização

Esta seção fornece detalhes adicionais sobre a operação do serviço de provisionamento, incluindo os tempos de primeiros e o últimos que serviço de provisionamento foi executado contra o aplicativo e quantos objetos de usuário e grupo que ele gerencia.

É fornecido um link para o **relatório de atividade de provisionamento**, que fornece um log de todos os usuários e grupos criado, atualizado e removido entre o Azure AD e o aplicativo de destino. Também é fornecido um link para o **relatório de erros de provisionamento**, que fornece mais detalhado de mensagens de erro para objetos de usuário e grupo que não puderam ser lidos, criados, atualizados ou removidos.
