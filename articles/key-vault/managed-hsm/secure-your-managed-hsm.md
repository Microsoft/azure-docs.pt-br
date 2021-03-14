---
title: Acesso seguro a um HSM gerenciado – HSM gerenciado do Azure Key Vault
description: Saiba como proteger o acesso ao HSM Gerenciado usando o RBAC do Azure e o RBAC local do HSM Gerenciado
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 5a3fcc10f318f2a8065550a48eb2bfb4bbdd4915
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218393"
---
# <a name="secure-access-to-your-managed-hsms"></a>Proteger o acesso aos HSMs gerenciados

O HSM gerenciado do Azure Key Vault é um serviço de nuvem que protege as chaves de criptografia. Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus HSMs gerenciados permitindo que apenas aplicativos e usuários autorizados os acessem. Este artigo apresenta uma visão geral do modelo de controle de acesso do HSM gerenciado. Explica a autenticação e a autorização e descreve como proteger o acesso ao seus HSMs gerenciados.

Este tutorial apresentará um exemplo simples que mostra como obter a diferenciação de direitos e o controle de acesso usando o RBAC do Azure e o RBAC local do HSM Gerenciado. Confira [Controle de acesso do HSM gerenciado](access-control.md) para aprender sobre o modelo de controle de acesso do HSM gerenciado.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você precisará ter os seguintes itens:

* Uma assinatura do Microsoft Azure. Se você não tiver uma, pode se inscrever e fazer uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A CLI do Azure versão 2.12.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM Gerenciado na sua assinatura. Confira [Início Rápido: Provisione e ative um HSM Gerenciado usando a CLI do Azure](quick-create-cli.md) para provisionar e ativar um HSM Gerenciado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

Para obter mais informações sobre as opções de logon por meio da CLI, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli)

## <a name="example"></a>Exemplo

Neste exemplo, estamos desenvolvendo um aplicativo que usa uma chave RSA de 2.048 bits para operações de assinatura. Nosso aplicativo é executado em uma VM (máquina virtual) do Azure com uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md). A chave RSA usada para assinatura é armazenada no HSM gerenciado.

Identificamos as funções a seguir que gerenciam, implantam e auditam nosso aplicativo:

- **Equipe de segurança**: a equipe de TI do escritório do Diretor de segurança ou colaboradores equivalentes. A equipe de segurança é responsável por guardar adequadamente as chaves. As chaves RSA ou chaves EC para assinatura e chaves RSA ou AES para criptografia de dados.
- **Desenvolvedores e operadores**: a equipe que desenvolve o aplicativo e o implanta no Azure. Os membros desta equipe não fazem parte da equipe de segurança. Eles não devem ter acesso a dados confidenciais, como chaves RSA. Somente o aplicativo que eles implantam deve ter acesso a esses dados confidenciais.
- **Auditores**: essa função é para colaboradores que não são membros da equipe de desenvolvimento ou de TI geral. Eles analisam o uso e a manutenção de certificados, chaves e segredos para garantir a conformidade com padrões de segurança.

Há outra função que está fora do escopo do nosso aplicativo: o administrador da assinatura (ou do grupo de recursos). O administrador da assinatura define permissões de acesso inicial para a equipe de segurança. Ele concede acesso à equipe de segurança usando um grupo de recursos que tem os recursos exigidos pelo aplicativo.

É necessário autorizar as seguintes operações para as funções:

**Equipe de segurança**
- Crie o HSM gerenciado.
- Baixar o domínio de segurança do HSM gerenciado (para recuperação de desastre)
- Ative o registro em log.
- Gerar ou importar chaves
- Crie os backups do HSM gerenciados para recuperação de desastres.
- Defina o RBAC local do HSM gerenciado para conceder permissões a usuários e aplicativos para operações específicas.
- Role as chaves periodicamente.

**Desenvolvedores e operadores**
- Obtenha a referência (URI de chave) da equipe de segurança da chave RSA usada para assinatura.
- Desenvolva e implante o aplicativo que acessa a chave programaticamente.

**Auditores**
- Examinar as datas de término das chaves para garantir que as chaves estejam atualizadas
- Monitorar as atribuições de função para garantir que as chaves só possam ser acessadas por usuários/aplicativos autorizados
- Examine os logs do HSM gerenciado para confirmar o uso adequado das chaves em conformidade com os padrões de segurança de dados.

A tabela a seguir resume as atribuições de função para equipes e recursos para acessar o HSM gerenciado.

| Função | Função do plano de gerenciamento | Função do plano de dados |
| --- | --- | --- |
| Equipe de segurança | Colaborador do HSM gerenciado | Administrador de HSM Gerenciado |
| Desenvolvedores e operadores de TI | Nenhum | Nenhum |
| Auditores | Nenhum | Auditor de criptografia do HSM gerenciado |
| Identidade gerenciada da VM usada pelo aplicativo| Nenhum | Usuário de criptografia do HSM gerenciado |
| Identidade gerenciada da conta de armazenamento usada pelo aplicativo| Nenhum| Criptografia do serviço do HSM gerenciado |

As três funções da equipe precisam ter acesso a outros recursos e a permissões do HSM gerenciado. Para implantar VMs (ou o recurso de Aplicativos Web do Serviço de Aplicativo do Azure), os desenvolvedores e operadores precisam de acesso `Contributor` a esses tipos de recursos. Os auditores precisam de acesso de leitura para a Conta de armazenamento em que os logs do HSM gerenciado estão armazenados.

Para atribuir funções de plano de gerenciamento (RBAC do Azure), você pode usar o portal do Azure ou qualquer uma das outras interfaces de gerenciamento, como a CLI do Azure ou o Azure PowerShell. Para atribuir funções de plano de dados do HSM gerenciado, use a CLI do Azure.

Os snippets da CLI do Azure nesta seção são criados com as seguintes suposições:

- O administrador do Azure Active Directory criou grupos de segurança para representar as três funções: Equipe de segurança, DevOps de aplicativo e Auditores de aplicativo da Contoso. O administrador adicionou os usuários aos respectivos grupos.
- Todos os recursos estão localizados no grupo de recursos **ContosoAppRG**.
- Os logs do HSM gerenciado são armazenados na conta de armazenamento **contosologstorage**.
- O HSM gerenciado **ContosoMHSM** e a conta de armazenamento **contosologstorage** estão no mesmo local do Azure.

O administrador de assinatura atribui as funções `Managed HSM Contributor` à equipe de segurança. Essa função permite à equipe de segurança gerenciar HSMs gerenciados atuais e criar outros. Se houver HSMs gerenciados, será necessário atribuir a função "Administrador do HSM gerenciado" para gerenciá-los.

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

A equipe de segurança configura o registro em log e atribui funções a auditores e ao aplicativo da VM.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

Este tutorial mostra apenas as ações relevantes para o controle de acesso geral. Outras ações e operações relacionadas à implantação de aplicativo em sua VM, à ativação da criptografia com a chave gerenciada pelo cliente para uma conta de armazenamento e à criação de HSM gerenciado não são mostrados aqui para manter o exemplo focado no controle de acesso e no gerenciamento de função.

Este exemplo descreve um cenário simples. Os cenários da vida real podem ser mais complexos. Você pode ajustar as permissões do cofre de chaves com base em suas necessidades. Vamos presumir que a equipe de segurança forneceu as referências de chave e segredo (URIs e impressões digitais), usadas pela equipe de DevOps nos aplicativos. Desenvolvedores e operadores não precisam de nenhum acesso ao plano de dados. Vimos como proteger seu cofre de chaves. Pense da mesma maneira ao proteger [suas VMs](https://azure.microsoft.com/services/virtual-machines/security/), [contas de armazenamentos](../../storage/blobs/security-recommendations.md) e outros recursos do Azure.

## <a name="resources"></a>Recursos

- [Documentação do Azure RBAC](../../role-based-access-control/overview.md)
- [RBAC do Azure: funções internas](../../role-based-access-control/built-in-roles.md)
- [gerenciar o RBAC do Azure com a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>Próximas etapas

Para ver um tutorial de introdução para um administrador, consulte [O que é o HSM gerenciado?](overview.md).

Para obter mais informações sobre o registro em log do uso para o registro em log do HSM gerenciado, confira [Registro em log do HSM gerenciado](logging.md).
