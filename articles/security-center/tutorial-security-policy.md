---
title: Trabalhar com políticas de segurança | Microsoft Docs
description: Este artigo descreve como trabalhar com políticas de segurança na central de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 4550532d36753d9b8ed472193bc833855ddd34c9
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314910"
---
# <a name="working-with-security-policies"></a>Trabalhando com políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e como exibi-las na Central de Segurança. A Central de Segurança do Azure atribui automaticamente suas [políticas de segurança internas](security-center-policy-definitions.md) a cada assinatura que é incorporada. Você pode configurá-los em [Azure Policy](../governance/policy/overview.md), que também permite definir políticas em grupos de gerenciamento e em várias assinaturas.

Para obter instruções sobre como definir políticas usando o PowerShell, consulte [Início rápido: crie uma atribuição de política para identificar recursos sem conformidade usando o módulo do PowerShell do Azure](../governance/policy/assign-policy-powershell.md).

>[!NOTE]
> A Central de Segurança iniciou sua integração com o Azure Policy. Os clientes existentes serão migrados automaticamente para a nova iniciativa interna no Azure Policy, ao invés das políticas de segurança anteriores na Central de Segurança. Essa alteração não afetará seus recursos ou o ambiente, exceto a presença da nova iniciativa no Azure Policy.

## <a name="what-are-security-policies"></a>Quais são políticas de segurança?
Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir a conformidade com requisitos de regulamentação de segurança ou da empresa. No Azure Policy, você pode definir políticas para suas assinaturas do Azure e adaptá-las ao seu tipo de carga de trabalho ou à sensibilidade de seus dados. Por exemplo, os aplicativos que usam dados regulamentados, como dados pessoais ou dados do cliente, podem exigir um nível mais alto de segurança do que outras cargas de trabalho. Para definir uma política em assinaturas ou em Grupos de gerenciamento, faça-o no [Azure Policy](../governance/policy/overview.md).

Suas políticas de segurança direcionam as recomendações de segurança que você recebe na Central de Segurança do Azure. Você pode monitorar a conformidade com eles para ajudá-lo a identificar possíveis vulnerabilidades e atenuar ameaças. Para obter mais informações sobre como determinar a opção apropriada para você, consulte a lista de [políticas de segurança internas](security-center-policy-definitions.md).

Quando você habilita a Central de Segurança, a política de segurança interna da Central de Segurança é refletida no Azure Policy como iniciativa interna, na categoria Central de Segurança. A iniciativa interna é atribuída automaticamente a todas as assinaturas registradas da Central de Segurança (camadas Gratuita ou Standard). A iniciativa interna contém somente políticas de Auditoria.


### <a name="management-groups"></a>Grupos de gerenciamento
Se sua organização tiver muitas assinaturas, talvez seja necessária uma maneira de gerenciar com eficiência o acesso, as políticas e a conformidade dessas assinaturas. Os Grupos de Gerenciamento do Azure fornecem um nível de escopo sobre assinaturas. Você organiza assinaturas em contêineres chamados “grupos de gerenciamento” e aplica as políticas de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as políticas aplicadas ao grupo de gerenciamento. Cada diretório recebe um único grupo de gerenciamento de nível superior chamado grupo de gerenciamento “raiz”. Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. Esse grupo de gerenciamento raiz permite que políticas globais e atribuições de RBAC sejam aplicados no nível de diretório. Para configurar grupos de gerenciamento para uso com a Central de Segurança do Azure, siga as instruções em [Obtenha visibilidade de todo o locatário da Central de Segurança do Azure](security-center-management-groups.md).

> [!NOTE]
> É importante compreender a hierarquia dos grupos de gerenciamento e assinaturas. Consulte [Organizar seus recursos com Grupos de Gerenciamento do Azure](../governance/management-groups/index.md#root-management-group-for-each-directory) para saber mais sobre grupos de gerenciamento, gerenciamento raiz e acesso ao grupo de gerenciamento.
>

## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
A Central de Segurança cria automaticamente uma política de segurança padrão para cada uma de suas assinaturas do Azure. Você pode editar as políticas no Azure Policy para fazer o seguinte:
- Criar definições de políticas.
- Atribuir as políticas entre grupos de gerenciamento e assinaturas, que podem representar uma organização inteira ou uma unidade de negócios dentro da organização.
- Monitorar a conformidade da política.

Para obter mais informações sobre o Azure Policy, confira [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

Uma política do Azure consiste nos seguintes componentes:

- Uma **diretiva** é uma regra.
- Uma **iniciativa** é uma coleção de políticas.
- Uma **designação** é a aplicação de uma iniciativa ou política para um escopo específico (grupo de gerenciamento, assinatura ou grupo de recursos).

## <a name="view-security-policies"></a>Exibir políticas de segurança

Para exibir suas políticas de segurança na Central de Segurança:

1. No painel **Security Center**, selecione **Security policy**.

    ![O painel Gerenciamento de Política](./media/security-center-policies/security-center-policy-mgt.png)

   Na tela **Gerenciamento de políticas**, você pode ver o número de grupos de gerenciamento, assinaturas e áreas de trabalho, bem como a estrutura do grupo de gerenciamento.

   > [!NOTE]
   > O painel da Central de Segurança pode mostrar um número maior de inscrições em **Cobertura de assinatura** do que o número de inscrições mostrado em **Gerenciamento de políticas**. Cobertura de assinatura mostra o número de assinaturas Padrão, Gratuitas e “não cobertas”. As assinaturas "não cobertas" não têm o Security Center ativado e não são exibidas em **Gerenciamento de políticas**.
   >

2. Selecione a assinatura ou o grupo de gerenciamento cujas políticas você deseja visualizar.

   - A tela **Política de segurança** reflete a ação tomada pelas políticas atribuídas na assinatura ou no grupo de gerenciamento selecionado.
   - Na parte superior, use os links fornecidos para abrir cada **atribuição** de política aplicável à assinatura ou ao grupo de gerenciamento. Você pode usar os links para acessar a atribuição e editar ou desativar a política. Por exemplo, se você perceber que determinada atribuição de política está efetivamente negando a proteção do ponto de extremidade, poderá usar o link para acessar a política e editá-la ou desativá-la.
   - Na lista de políticas, você pode ver a aplicação efetiva da política em sua assinatura ou grupo de gerenciamento. Isso significa que as configurações de cada política que se aplicam ao escopo são levadas em consideração e você recebe o resultado cumulativo de qual ação é tomada pela política. Por exemplo, se em uma atribuição a política estiver desabilitada, mas em outra ela estiver definida como AuditIfNotExist, o efeito cumulativo será aplicado a AuditIfNotExist. O efeito mais ativo sempre tem precedência.
   - O efeito das políticas pode ser: Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Para obter mais informações sobre como os efeitos são aplicados, consulte [Reconhecer os efeitos da política](../governance/policy/concepts/effects.md).

   ![tela de política](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> Ao visualizar as políticas atribuídas, você pode ver várias atribuições e pode ver como cada atribuição é configurada por conta própria.

## <a name="edit-security-policies"></a>Editar políticas de segurança
Você pode editar a política de segurança padrão para cada uma das suas assinaturas e grupos de gerenciamento do Azure em [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Para modificar uma política de segurança, você deve ser um proprietário ou administrador de segurança da assinatura ou o grupo de gerenciamento que o contém.

Para obter instruções sobre como editar uma política de segurança na Azure Policy, consulte e [Crie e gerencie políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

Você pode editar as políticas de segurança no portal Azure Policy usando a API REST ou o Windows PowerShell. O exemplo a seguir fornece instruções para editar usando a API REST.


## <a name="disable-security-policies"></a>Desabilitar políticas de segurança
Se a política de segurança padrão estiver gerando uma recomendação que não seja relevante para seu ambiente, você poderá interrompê-la desabilitando a definição de política que envia a recomendação.
Para obter mais informações sobre recomendações, consulte [Gerenciando recomendações de segurança](security-center-recommendations.md).

1. Na central de segurança, na seção **conformidade do & de política** , clique em **política de segurança**.

   ![gerenciamento de políticas](./media/tutorial-security-policy/policy-management.png)

2. Clique na assinatura ou grupo de gerenciamento para o qual você deseja desabilitar a recomendação.

   > [!Note]
   > Lembre-se de que um grupo de gerenciamento aplica suas políticas a suas assinaturas. Portanto, se você desabilitar a política de uma assinatura e a assinatura pertencer a um grupo de gerenciamento que ainda usa a mesma política, você continuará a receber as recomendações de política. A política ainda será aplicada a partir do nível de gerenciamento e as recomendações ainda serão geradas.

1. Clique na política atribuída.

   ![desabilitar política](./media/tutorial-security-policy/security-policy.png)

1. Na seção **parâmetros** , procure a política que invoca a recomendação que você deseja desabilitar e, na lista suspensa, selecione **desabilitada**

   ![desabilitar política](./media/tutorial-security-policy/disable-policy.png)
1. Clique em **Salvar**.
   > [!Note]
   > As alterações de política de desabilitação podem levar até 12 horas para entrar em vigor.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Configurar uma política de segurança usando a API REST

Como parte da integração nativa com o Azure Policy, a Central de Segurança do Azure permite que você use a API REST do Azure Policy para criar atribuições de política. As instruções a seguir orientam você a criar as atribuições da política e personalizar atribuições existentes. 

Conceitos importantes no Azure Policy: 

- Uma **definição de política** é uma regra 

- Uma **iniciativa** é uma coleção de definições de política (regras) 

- Uma **atribuição** é um aplicativo de uma iniciativa ou uma política para um escopo específico (grupo de gerenciamento, assinatura, etc.) 

A Central de Segurança tem uma iniciativa interna que inclui todas as suas políticas de segurança. Para avaliar as políticas da Central de Segurança nos recursos do Azure, você deve criar uma atribuição no grupo de gerenciamento ou na assinatura que você deseja avaliar.  

A iniciativa interna tem todas as políticas da Central de Segurança habilitadas por padrão. Você pode optar por desabilitar certas políticas da iniciativa integrada, por exemplo, aplicar todas as políticas da Central de Segurança, exceto o **firewall de aplicativos Web** alterando o valor do parâmetro de efeito da política para **Desativado**. 

### <a name="api-examples"></a>Exemplos de API

Nos exemplos a seguir, substitua essas variáveis:

- **{scope}** insira o nome do grupo de gerenciamento ou a assinatura à qual você está aplicando a política.
- **{policyAssignmentName}** insira o [nome da atribuição de política relevante](#policy-names).
- **{name}** insira seu nome ou o nome do administrador que aprovou a alteração da política.

Este exemplo mostra como atribuir a iniciativa interna da Central de Segurança a uma assinatura ou um grupo de gerenciamento
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Este exemplo mostra como atribuir a iniciativa interna da Central de Segurança a uma assinatura, com as seguintes políticas desabilitadas: 

- Atualizações do sistema (“systemUpdatesMonitoringEffect”) 

- Configurações de segurança ("systemConfigurationsMonitoringEffect") 

- Proteção do ponto de extremidade ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Este exemplo mostra como remover uma atribuição:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

### Referência dos nomes de políticas <a name="policy-names"></a>

|Nome da política na Central de Segurança|Nome da política exibido no Azure Policy |Nome do parâmetro de efeito da política|
|----|----|----|
|Criptografia do SQL |Monitorar o banco de dados SQL não criptografado na Central de Segurança do Azure |sqlEncryptionMonitoringEffect| 
|Auditoria do SQL |Monitorar o banco de dados SQL não auditado na Central de Segurança do Azure |sqlAuditingMonitoringEffect|
|Atualizações do sistema |Monitorar atualizações de sistemas ausentes na Central de Segurança do Azure |systemUpdatesMonitoringEffect|
|Criptografia do armazenamento |Auditar a criptografia de blob ausente para contas de armazenamento |storageEncryptionMonitoringEffect|
|Acesso à rede JIT |Monitorar o possível acesso Just In Time (JIT) à rede na Central de Segurança do Azure |jitNetworkAccessMonitoringEffect |
|Controles de aplicativo adaptáveis |Monitorar a possível lista de permissões de aplicativos na Central de Segurança do Azure |adaptiveApplicationControlsMonitoringEffect|
|Grupos de segurança de rede |Monitorar o acesso de rede permissivo na Central de Segurança do Azure |networkSecurityGroupsMonitoringEffect| 
|Configurações de segurança |Monitorar as vulnerabilidades do SO na Central de Segurança do Azure |systemConfigurationsMonitoringEffect| 
|Proteção do ponto de extremidade |Monitorar o Endpoint Protection ausente na Central de Segurança do Azure |endpointProtectionMonitoringEffect |
|Criptografia do disco |Monitorar discos da VM não criptografados na Central de Segurança do Azure |diskEncryptionMonitoringEffect|
|Avaliação de vulnerabilidade |monitora vulnerabilidades de máquinas virtuais na Central de Segurança do Azure |vulnerabilityAssessmentMonitoringEffect|
|Firewall do aplicativo Web |Monitorar o aplicativo Web desprotegido na Central de Segurança do Azure |webApplicationFirewallMonitoringEffect |
|Firewall da próxima geração |Monitorar pontos de extremidade de rede desprotegidos na Central de Segurança do Azure| |


### <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?
A Central de Segurança usa o RBAC (Controle de Acesso Baseado em Função) que fornece funções internas que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando os usuários abrem a Central de Segurança, eles veem somente informações relacionadas aos recursos aos quais eles têm acesso. Isso significa que os usuários são atribuídos com a função de proprietário, colaborador ou leitor para a assinatura ou grupo de recursos ao qual o recurso pertence. Além dessas funções, há duas funções específicas da Central de Segurança:

- Leitor de Segurança: tem direitos de exibição na Central de Segurança, incluindo recomendações, alertas, política e integridade, mas não pode fazer alterações.
- Admin de Segurança: tem os mesmos direitos de exibição que o leitor de segurança, pode atualizar a política de segurança e ignorar recomendações e alertas.



## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a editar políticas de segurança no Azure Policy. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md): Saiba como planejar e entender as considerações de design sobre a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): Saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerenciar e responder aos alertas de segurança.
* [Monitorar soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Obter visibilidade de todo o locatário na Central de Segurança do Azure](security-center-management-groups.md): saiba como configurar grupos de gerenciamento na Central de Segurança do Azure.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): Obtenha respostas para perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre postagens no blog sobre a conformidade e segurança do Azure.

Para saber mais sobre o Azure Policy, confira [O que é o Azure Policy?](../governance/policy/overview.md)
