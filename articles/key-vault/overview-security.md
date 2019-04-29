---
title: Segurança do Cofre de chaves do Azure | Microsoft Docs
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Abrange o modelo de autenticação e autorização do Key Vault e descreve como proteger o cofre de chaves.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 43847b53fbf84fe42be3efdbd647767904a05fb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777654"
---
# <a name="azure-key-vault-security"></a>Segurança do Cofre de chaves do Azure

Você precisa proteger as chaves de criptografia e segredos, como certificados, cadeias de caracteres de conexão e senhas na nuvem para que você estiver usando o Azure Key Vault. Como você está armazenando confidenciais e dados críticos de negócios, você precisa tomar medidas para maximizar a segurança de seus cofres e os dados armazenados nelas. Este artigo abordará alguns dos conceitos que você deve considerar ao projetar a segurança do Azure Key Vault.

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

Ao criar um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure Active Directory da assinatura. Qualquer pessoa que tentar gerenciar ou recuperar o conteúdo de um cofre deve ser autenticada pelo AD do Azure.

- A autenticação estabelece a identidade do chamador.
- Autorização determina quais operações o chamador pode executar. Autorização no cofre de chaves usa uma combinação de [controle de acesso baseado em função](../role-based-access-control/overview.md) (RBAC) e políticas de acesso do Azure Key Vault.

### <a name="access-model-overview"></a>Visão geral do modelo de acesso

Acesso aos cofres ocorre por meio de duas interfaces ou planos. Esses planos são o plano de gerenciamento e o plano de dados.

- O *plano de gerenciamento* é onde você gerenciar o Cofre de chaves em si e é a interface usada para criar e excluir os cofres. Você também pode ler as propriedades de Cofre de chaves e gerenciar políticas de acesso.
- O *plano de dados* permite que você trabalhe com os dados armazenados em um cofre de chaves. Você pode adicionar, excluir e alterar chaves, segredos e certificados.

Para acessar um cofre de chaves em um plano, todos os chamadores (usuários ou aplicativos) devem ser autenticados e autorizados. Os dois planos usam o Azure AD (Azure Active Directory) para autenticação. Para receber autorização, o plano de gerenciamento usa o RBAC (controle de acesso baseado em função) e o plano de dados usa uma política de acesso ao Key Vault.

O modelo de mecanismo único para autenticação em ambos os planos tem vários benefícios:

- As organizações podem controlar centralmente o acesso a todos os cofres de chaves em sua organização.
- Se um usuário parte, ele perde instantaneamente o acesso a todos os cofres de chaves na organização.
- As organizações podem personalizar a autenticação usando as opções no Azure AD, como habilitar a autenticação multifator para aumentar a segurança

### <a name="managing-administrative-access-to-key-vault"></a>Gerenciar o acesso administrativo ao Cofre de chaves

Quando você cria um cofre de chaves em um grupo de recursos, gerenciar o acesso por meio do Azure AD. Conceda a usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Você pode conceder acesso em um nível de escopo específico atribuindo funções RBAC apropriadas. Para conceder acesso a um usuário para gerenciar os cofres de chaves, atribua uma função `key vault Contributor` predefinida ao usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função RBAC:

- **Assinatura**: uma função RBAC atribuída no nível da assinatura que se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos**: uma função RBAC atribuída no nível do grupo de recursos que se aplica a todos os recursos nesse grupo de recursos.
- **Recursos específicos**: uma função atribuída a um recurso específico que se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para saber mais, confira [RBAC: funções internas](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um usuário tem permissões `Contributor` para um plano de gerenciamento de cofre de chaves, pode conceder a si mesmo o acesso ao plano de dados ao definir a política de acesso do Key Vault. Você deve controlar rigorosamente quem tem função de acesso `Contributor` aos cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controlando o acesso a dados do Key Vault

Políticas de acesso do Key Vault concedem permissões separadamente para chaves, segredos ou certificados. Você pode conceder a um usuário o acesso apenas às chaves e não aos segredos. Permissões de acesso para chaves, segredos e certificados são gerenciadas no nível de cofre.

> [!IMPORTANT]
> As políticas de acesso do Key Vault não aceitam permissões granulares no nível do objeto, como uma chave, um segredo ou um certificado específicos. Quando um usuário tem permissão para criar e excluir chaves, ele pode executar essas operações em todas as chaves no cofre de chaves.

Para definir as políticas de acesso para um cofre de chaves, use o [Portal do Microsoft Azure](https://portal.azure.com/), a [CLI do Azure](../cli-install-nodejs.md), o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST de gerenciamento do Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Você pode restringir o acesso ao plano de dados usando [pontos de extremidade de serviço de rede virtual no Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). É possível configurar [regras de firewalls e da rede virtual](key-vault-network-security.md) para ter uma camada adicional de segurança.

## <a name="network-access"></a>Acesso à rede

Você pode reduzir a exposição de seus cofres especificando quais endereços IP têm acesso a eles. Os pontos de extremidade de serviço de rede virtual para o Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo de Internet versão 4). Qualquer usuário que conecte o cofre de chaves de fora dessas fontes terá acesso negado.

Depois de firewall regras estão em vigor, os usuários podem apenas ler dados do Cofre de chaves quando suas solicitações originam em redes virtuais permitidas ou intervalos de endereços IPv4. Isso também aplica-se ao acessar o Key Vault a partir do portal do Azure. Embora os usuários possam navegar em um cofre de chaves a partir do portal do Azure, eles talvez não consigam listar chaves, segredos ou certificados se o computador cliente deles não estiver na lista permitida. Isso também afeta o Seletor do Cofre de Chaves de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.

Revisão de endereço de rede para obter mais informações sobre o Azure Key Vault [pontos de extremidade do serviço de rede de Virtual do Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitoramento

Logs do cofre da chave salva informações sobre as atividades executadas em seu cofre. Logs do Cofre de chaves:

- Todos os autenticados solicitações da API REST, incluindo solicitações com falha
  - Operações na chave do cofre em si. Essas operações incluem a criação, exclusão, configuração de políticas de acesso e atualizar os atributos de Cofre de chaves, como marcas.
  - Operações em chaves e segredos no cofre de chaves, incluindo:
    - Criar, modificar ou excluir essas chaves ou segredos.
    - Assinatura, verificando, criptografar, descriptografar, encapsular e descodificar chaves, obter segredos e listagem de chaves e segredos (e suas versões).
- Solicitações não autenticadas que resultam em uma resposta 401. Os exemplos são as solicitações que não têm um token de portador, que estão malformadas ou expiradas ou que têm um token inválido.

Informações de registro em log podem ser acessadas dentro de 10 minutos após a operação do Cofre de chaves. Cabe a você gerenciar seus logs na conta de armazenamento. 

- Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
- Exclua os logs que você não deseja manter em sua conta de armazenamento.

Para obter recomendações sobre como gerenciar o armazenamento com segurança contas revisar o [guia de segurança do armazenamento do Azure](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>Próximas etapas

- [Pontos de extremidade de serviço de rede virtual para o Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: funções internas](../role-based-access-control/built-in-roles.md)
- [Pontos de extremidade de serviço de rede virtual para o Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
