---
title: Perguntas frequentes e problemas conhecidos de identidades gerenciadas – Azure AD
description: Problemas conhecidos com identidades gerenciadas para recursos do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/04/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f1be2e64435cb0bcdb369a398a9a65fc3714fb2
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008529"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Como você pode encontrar recursos que têm uma identidade gerenciada?

Você pode encontrar a lista de recursos que têm uma identidade gerenciada atribuída pelo sistema usando o seguinte comando de CLI do Azure: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>Identidades gerenciadas têm um objeto de aplicativo de backup?

Não. As identidades gerenciadas e os registros de Aplicativo Azure AD não são a mesma coisa no diretório. 

Registros de aplicativo tem dois componentes: um objeto de aplicativo + um objeto de entidade de serviço. Identidades gerenciadas para recursos do Azure têm apenas um desses componentes: um objeto de entidade de serviço. 

Identidades gerenciadas não têm um objeto de aplicativo no diretório, que é comumente usado para conceder permissões de aplicativo para o MS Graph. Em vez disso, as permissões do MS Graph para identidades gerenciadas precisam ser concedidas diretamente à entidade de serviço.  

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>A mesma identidade gerenciada pode ser usada em várias regiões?

Em suma, sim, você pode usar identidades gerenciadas atribuídas pelo usuário em mais de uma região do Azure. A resposta mais longa é que, enquanto as identidades gerenciadas atribuídas pelo usuário são criadas como recursos regionais, a [entidade de serviço](../develop/app-objects-and-service-principals.md#service-principal-object) associada (SPN) criada no Azure ad está disponível globalmente. A entidade de serviço pode ser usada em qualquer região do Azure e sua disponibilidade depende da disponibilidade do Azure AD. Por exemplo, se você criou uma identidade gerenciada atribuída pelo usuário na região South-Central e essa região ficar indisponível, esse problema afetará apenas as atividades do [plano de controle](../../azure-resource-manager/management/control-plane-and-data-plane.md) na própria identidade gerenciada.  As atividades executadas por todos os recursos já configurados para usar as identidades gerenciadas não seriam afetadas.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>As identidades gerenciadas dos recursos do Azure funcionam com os serviços de nuvem do Azure?

Não, não há planos para suportar identidades gerenciadas para recursos do Azure no Azure Cloud Services.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Qual é a credencial associada a uma identidade gerenciada? Por quanto tempo ele é válido e com que frequência ele é girado?

> [!NOTE]
> A forma como as identidades gerenciadas são autenticadas é um detalhe de implementação interna que está sujeito a alterações sem aviso prévio.

Identidades gerenciadas usam autenticação baseada em certificado. Cada credencial de identidade gerenciada tem uma expiração de 90 dias e é revertida após 45 dias.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>O que é o limite de segurança de identidades gerenciadas para recursos do Azure?

O limite de segurança da identidade é o recurso ao qual ele está anexado. Por exemplo, o limite de segurança de uma Máquina Virtual com identidades gerenciadas para recursos do Azure ativados é a Máquina Virtual. Qualquer código em execução nessa VM pode chamar as identidades gerenciadas do ponto de extremidade de recursos do Azure e solicitar tokens. É a experiência semelhante com outros recursos que suportam identidades gerenciadas para recursos do Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Qual identidade será o padrão IMDS se não for especificada a identidade na solicitação?

- Se a identidade gerenciada atribuída do sistema estiver habilitada e nenhuma identidade for especificada na solicitação, o IMDS será o padrão para a identidade gerenciada atribuída ao sistema.
- Se a identidade gerenciada atribuída do sistema não e nenhum existir identidade gerenciada atribuída ao usuário, o IMDS padrão será a identidade gerenciada atribuída ao usuário único. 
- Se a identidade gerenciada atribuída ao sistema não estiver habilitada e vários de usuário atribuídos a identidades gerenciadas existirem, é necessário, em seguida, especificar uma identidade gerenciada na solicitação.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Identidades gerenciadas serão recriadas automaticamente se eu mover uma assinatura para outro diretório?

Não. Se você mover uma assinatura para outro diretório, precisará recriá-las manualmente e conceder as atribuições de função do Azure novamente.
- Para sistema atribuído a identidades gerenciadas: desabilite e habilite novamente. 
- Para identidades gerenciadas atribuídas pelo usuário: excluir, recriar e anexá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Posso usar uma identidade gerenciada para acessar um recurso em um diretório/locatário diferente?

Não. Identidades gerenciadas não têm suporte a cenários entre diretórios. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quais permissões do RBAC do Azure são necessárias para a identidade gerenciada em um recurso? 

- Identidade gerenciada atribuída ao sistema: Você precisa de permissões de gravação sobre o recurso. Por exemplo, para máquinas virtuais, você precisa do Microsoft.Compute/virtualMachines/write. Essa ação é incluída em funções internas específicas do recurso, como [Colaborador da máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Identidade gerenciada atribuída ao usuário: Você precisa de permissões de gravação sobre o recurso. Por exemplo, para máquinas virtuais, você precisa do Microsoft.Compute/virtualMachines/write. Também é necessário atribuir a função de [Operador da identidade gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator) sobre essa identidade.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Como fazer impedir a criação de identidades gerenciadas atribuídas pelo usuário?

Você pode impedir que os usuários criem identidades gerenciadas atribuídas pelo usuário usando [Azure Policy](../../governance/policy/overview.md)

- Navegue até a [portal do Azure](https://portal.azure.com) e vá para **política**.
- Escolher **definições**
- Selecione **+ definição de política** e insira as informações necessárias.
- Na seção regra de política, Cole

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {}
}

```

Depois de criar a política, atribua-a ao grupo de recursos que você deseja usar.

- Navegue até grupos de recursos.
- Localize o grupo de recursos que você está usando para teste.
- Escolha **políticas** no menu à esquerda.
- Selecionar **atribuir política**
- Na seção **noções básicas** , forneça:
    - **Escopo** O grupo de recursos que estamos usando para teste
    - **Definição de política**: a política que criamos anteriormente.
- Deixe todas as outras configurações em seus padrões e escolha **revisar + criar**

Neste ponto, qualquer tentativa de criar uma identidade gerenciada atribuída pelo usuário no grupo de recursos falhará.

  ![Violação de política](./media/known-issues/policy-violation.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação" falha ao tentar exportar esquemas para identidades gerenciadas para a extensão de recursos do Azure

Quando as identidades gerenciadas dos recursos do Azure são habilitadas em uma VM, o seguinte erro é exibido ao tentar usar o recurso "Script de automação" para a VM ou seu grupo de recursos:

![Identidades gerenciadas para erro de exportação de script de automação de recursos do Azure](./media/msi-known-issues/automation-script-export-error.png)

As identidades gerenciadas da extensão de VM dos recursos do Azure (que deverão ser preteridas em janeiro de 2019) não dão suporte atualmente à capacidade de exportar seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para habilitar identidades gerenciadas para recursos do Azure no recurso. Essas seções podem ser adicionadas manualmente seguindo os exemplos em [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando modelos](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema se torna disponível para as identidades gerenciadas da extensão de VM dos recursos do Azure (que deverão ser preteridas em janeiro de 2019), ela será listada em [Exportando grupos de recursos que contêm extensões de VM](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>A VM falha ao iniciar depois de ser movida do grupo de recursos ou assinatura

Se você mover uma máquina virtual no estado de execução, ela continua a ser executada durante a movimentação. No entanto, após a movimentação, se a VM for interrompida e reiniciada, ela falhará ao iniciar. Esse problema ocorre porque a VM não está atualizando a referência às identidades gerenciadas para a identidade de recursos do Azure e continua apontando para ela no grupo de recursos antigo.

**Solução alternativa** 
 
Acione uma atualização na VM para obter valores corretos para as identidades gerenciadas dos recursos do Azure. Você pode fazer uma alteração de propriedade de VM para atualizar a referência às identidades gerenciadas para a identidade de recursos do Azure. Por exemplo, você pode definir um novo valor de marca na VM com o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Este comando define uma nova marca "fixVM" com um valor 1 na VM. 
 
Ao definir essa propriedade, a VM atualiza com as identidades gerenciadas corretas para o URI de recurso de recursos do Azure e, em seguida, você deve poder iniciar a VM. 
 
Depois que a VM for iniciada, a marca poderá ser removida usando o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Transferindo uma assinatura entre diretórios do Azure Active Directory

Identidades gerenciadas não são atualizadas quando uma assinatura for movida/transferida para outro diretório. Como resultado, quaisquer identidades gerenciadas atribuídas pelo sistema ou designadas pelo usuário serão quebradas. 

Para identidades gerenciadas em uma assinatura movidas para outro diretório, confira as seguintes soluções alternativas:

 - Para sistema atribuído a identidades gerenciadas: desabilite e habilite novamente. 
 - Para identidades gerenciadas atribuídas pelo usuário: excluir, recriar e anexá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

Para obter mais informações, confira [Transferir uma assinatura do Azure para um diretório diferente do Azure AD](../../role-based-access-control/transfer-subscription.md).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Mover a identidade gerenciada atribuída pelo usuário para uma assinatura/um grupo de recursos diferente

Não há suporte à ação de mover a identidade gerenciada atribuída pelo usuário para uma assinatura/um grupo de recursos diferente.
