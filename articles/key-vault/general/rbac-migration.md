---
title: Migrar para o controle de acesso baseado em função do Azure | Microsoft Docs
description: Saiba como migrar de políticas de acesso do cofre para funções do Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 23a36bfc048a6214ccb79b793a23c21d5f8e305e
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288258"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-preview-permission-model"></a>Migrar da política de acesso do cofre para um modelo de permissão de controle de acesso baseado em função (versão prévia) do Azure

O modelo de política de acesso do cofre é um sistema de autorização existente interno Key Vault para fornecer acesso a chaves, segredos e certificados. Você pode controlar o acesso atribuindo permissões individuais à entidade de segurança (usuário, grupo, entidade de serviço, identidade gerenciada) em Key Vault escopo. 

O Azure RBAC (controle de acesso baseado em função) do Azure é um sistema de autorização criado em [Azure Resource Manager](../../azure-resource-manager/management/overview.md) que fornece gerenciamento de acesso refinado de recursos do Azure. O Azure RBAC para Key Vault chaves, segredos e gerenciamento de acesso de certificados está atualmente em visualização pública. Com o RBAC do Azure, você controla o acesso aos recursos criando atribuições de funções, que consistem em três elementos: entidade de segurança, definição de função (conjunto predefinido de permissões) e escopo (grupo de recursos ou recurso individual). Para obter mais informações, consulte [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md).

Antes de migrar para o RBAC do Azure, é importante entender seus benefícios e limitações.

Benefícios da chave RBAC do Azure em relação às políticas de acesso do cofre:
- Fornece o modelo de controle de acesso unificado para recursos do Azure-mesma API nos serviços do Azure
- Gerenciamento de acesso centralizado para administradores – gerencie todos os recursos do Azure em uma única exibição
- Integrado com [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) para controle de acesso baseado em tempo
- Negar atribuições – capacidade de excluir a entidade de segurança em um escopo específico. Para obter informações, consulte [entender as atribuições de negação do Azure](../../role-based-access-control/deny-assignments.md)

Desvantagens do RBAC do Azure:
- Latência para atribuições de função – pode levar vários minutos para que a atribuição de função seja aplicada. As políticas de acesso ao cofre são atribuídas instantaneamente.
- Número limitado de atribuições de função-2000 atribuições de funções por assinatura versus 1024 políticas de acesso por Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Políticas de acesso para mapeamento de funções do Azure

O RBAC do Azure tem várias funções internas do Azure que você pode atribuir a usuários, grupos, entidades de serviço e identidades gerenciadas. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) próprias.

Key Vault funções internas para chaves, certificados e segredos de gerenciamento de acesso:
- Key Vault administrador (versão prévia)
- Leitor de Key Vault (versão prévia)
- Key Vault responsável pelo certificado (versão prévia)
- Key Vault de criptografia (versão prévia)
- Key Vault usuário de criptografia (visualização)
- Diretor de segredos Key Vault (versão prévia)
- Usuário Key Vault segredos (visualização)

Para obter mais informações sobre funções internas existentes, consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md)

As políticas de acesso do cofre podem ser atribuídas com permissões selecionadas individualmente ou com modelos de permissão predefinidos.

Modelos de permissão predefinidos de políticas de acesso:
- Chave, segredo, gerenciamento de certificados
- Gerenciamento de segredos de & chave
- Segredo & gerenciamento de certificado
- Gerenciamento de chaves
- Gerenciamento de segredos
- Gerenciamento de certificado
- Conector do SQL Server
- Azure Data Lake Storage ou armazenamento do Azure
- Serviço de Backup do Azure
- Chave do cliente do Exchange Online
- Chave do cliente do SharePoint Online
- BYOK de informações do Azure

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Modelos de políticas de acesso para mapeamento de funções do Azure
| Modelo de política de acesso | Operations | Função do Azure |
| --- | --- | --- |
| Chave, segredo, gerenciamento de certificados | Chaves: todas as operações <br>Certificados: todas as operações<br>Segredos: todas as operações | Key Vault administrador (versão prévia) |
| Gerenciamento de segredos de & chave | Chaves: todas as operações <br>Segredos: todas as operações| Key Vault de criptografia (versão prévia)<br> Diretor de segredos Key Vault (versão prévia)|
| Segredo & gerenciamento de certificado | Certificados: todas as operações <br>Segredos: todas as operações| Key Vault o responsável por certificados (versão prévia)<br> Diretor de segredos Key Vault (versão prévia)|
| Gerenciamento de chaves | Chaves: todas as operações| Key Vault de criptografia (versão prévia)|
| Gerenciamento de segredos | Segredos: todas as operações| Diretor de segredos Key Vault (versão prévia)|
| Gerenciamento de certificado | Certificados: todas as operações | Key Vault o responsável por certificados (versão prévia)|
| Conector do SQL Server | Chaves: obter, listar, encapsular chave, desencapsular chave | Criptografia do serviço de criptografia Key Vault (visualização)|
| Azure Data Lake Storage ou armazenamento do Azure | Chaves: obter, listar, desencapsular chave | N/D<br> Função personalizada necessária|
| Serviço de Backup do Azure | Chaves: obter, listar, backup<br> Certificado: obter, listar, backup | N/D<br> Função personalizada necessária|
| Chave do cliente do Exchange Online | Chaves: obter, listar, encapsular chave, desencapsular chave | Criptografia do serviço de criptografia Key Vault (visualização)|
| Chave do cliente do Exchange Online | Chaves: obter, listar, encapsular chave, desencapsular chave | Criptografia do serviço de criptografia Key Vault (visualização)|
| BYOK de informações do Azure | Chaves: obter, descriptografar, assinar | N/D<br>Função personalizada necessária|


## <a name="assignment-scopes-mapping"></a>Mapeamento de escopos de atribuição  

O RBAC do Azure para Key Vault permite a atribuição de funções nos seguintes escopos:
- Grupo de gerenciamento
- Subscription
- Resource group
- Key Vault recurso
- Chave, segredo e certificado individuais

O modelo de permissão de política de acesso do cofre é limitado para atribuir a política somente em Key Vault nível de recurso, que 

Em geral, é recomendável ter um Key Vault por aplicativo e gerenciar o acesso no nível do Key Vault. Há cenários em que o gerenciamento de acesso em outros escopos pode simplificar o gerenciamento de acesso.

- * * Infraestrutura, administradores de segurança e operadores: o gerenciamento de grupos de cofres de chaves em grupo de gerenciamento, assinatura ou nível de grupo de recursos com políticas de acesso do cofre requer a manutenção de políticas para cada cofre de chaves. O RBAC do Azure permite criar uma atribuição de função no grupo de gerenciamento, na assinatura ou no grupo de recursos. Essa atribuição será aplicada a qualquer novo cofre de chaves criado no mesmo escopo. Nesse cenário, é recomendável usar Privileged Identity Management com acesso just-in-time ao fornecer acesso permanente.
 
- * * Aplicativos: há cenários em que o aplicativo precisaria compartilhar segredo com outro aplicativo. O uso de políticas de acesso do cofre separam o Key Vault tinha que ser criado para evitar o acesso a todos os segredos. O RBAC do Azure permite atribuir função com escopo para segredo individual usando um único cofre de chaves.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Política de acesso do cofre para as etapas de migração do RBAC do Azure
Há muitas diferenças entre o modelo de permissão de política de acesso de cofre e RBAC do Azure. Para evitar interrupções durante a migração, as etapas a seguir são recomendadas.
 
1. **Identificar e atribuir funções** : identificar funções internas com base na tabela de mapeamento acima e criar funções personalizadas quando necessário. Atribua funções em escopos, com base em diretrizes de mapeamento de escopos. Para obter mais informações sobre como atribuir funções ao key Vault, consulte [fornecer acesso ao key Vault com um controle de acesso baseado em função do Azure (versão prévia)](rbac-guide.md)
1. **Validar atribuição de funções** : as atribuições de função no RBAC do Azure podem levar vários minutos para serem propagadas. Para obter instruções sobre como verificar atribuições de função, consulte [listar atribuições de funções no escopo](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Configurar o monitoramento e os alertas no Key Vault** : é importante habilitar o log e a instalação de alertas para exceções de acesso negado. Para obter mais informações, consulte [monitoramento e alertas para Azure Key Vault](./alert.md)
1. **Definir o modelo de permissão de controle de acesso baseado em função do Azure no Key Vault** : habilitar o modelo de permissão do RBAC do Azure invalidará todas as políticas de acesso existentes. Se um erro, o modelo de permissão pode ser revertido com todas as políticas de acesso existentes restantes inalteradas.

> [!NOTE]
> Quando o modelo de permissão do RBAC do Azure estiver habilitado, todos os scripts que tentarem atualizar as políticas de acesso falharão. É importante atualizar esses scripts para usar o RBAC do Azure.

## <a name="troubleshooting"></a>Solução de problemas
-  A atribuição de função não está funcionando após vários minutos – há situações em que as atribuições de função podem levar mais tempo. É importante escrever lógica de repetição no código para cobrir esses casos.
- As atribuições de função desapareceram quando Key Vault foi excluída (exclusão reversível) e recuperadas – atualmente, há uma limitação do recurso de exclusão reversível em todos os serviços do Azure. É necessário recriar todas as atribuições de função após a recuperação.    

## <a name="learn-more"></a>Saiba mais

- [Visão geral do RBAC do Azure](../../role-based-access-control/overview.md)
- [Tutorial de funções personalizadas](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)