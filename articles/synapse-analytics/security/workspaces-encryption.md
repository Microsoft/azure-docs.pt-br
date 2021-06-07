---
title: Criptografia do Azure Synapse Analytics
description: Um artigo que explica a criptografia no Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d4bc59a9bd5299698bff9949aaaa881fbdf385ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526265"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Criptografia para workspaces do Azure Synapse Analytics

Este artigo descreverá:
* Criptografia de dados em repouso em espaços de trabalho do Synapse Analytics.
* Configuração de espaços de trabalho do Synapse para habilitar a criptografia com uma chave gerenciada pelo cliente.
* Gerenciando chaves usadas para criptografar dados em espaços de trabalho.

## <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso

Uma solução completa de criptografia em repouso garante que os dados nunca sejam persistidos na forma não criptografada. A criptografia dupla de dados em repouso reduz as ameaças com duas camadas separadas de criptografia para proteger contra comprometimentos de qualquer camada única. O Azure Synapse Analytics oferece uma segunda camada de criptografia para os dados em seu espaço de trabalho com uma chave gerenciada pelo cliente. Essa chave é protegida em seu [Azure Key Vault](../../key-vault/general/overview.md), o que permite que você assuma a propriedade do gerenciamento e da rotação de chaves.

A primeira camada de criptografia para os serviços do Azure é habilitada com chaves gerenciadas pela plataforma. Por padrão, os discos do Azure e os dados nas contas de armazenamento do Azure são criptografados automaticamente em repouso. Saiba mais sobre como a criptografia é usada em Microsoft Azure na [visão geral da criptografia do Azure](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Criptografia Synapse do Azure

Esta seção ajudará você a entender melhor como a criptografia de chave gerenciada pelo cliente é habilitada e imposta em espaços de trabalho do Synapse. Essa criptografia usa chaves existentes ou novas chaves geradas em Azure Key Vault. Uma única chave é usada para criptografar todos os dados em um espaço de trabalho. Os espaços de trabalho do Synapse dão suporte a chaves RSA com 2048 e 3072 chaves de tamanho de byte.

> [!NOTE]
> Os espaços de trabalho Synapse não dão suporte ao uso de chaves ECC (criptografia de Elliptic-Curve) para criptografia.

Os dados nos seguintes componentes do Synapse são criptografados com a chave gerenciada pelo cliente configurada no nível do espaço de trabalho:
* Pools de SQL
 * Pools de SQL dedicados
 * Pools SQL sem servidor
* Pools do Apache Spark
* Azure Data Factory tempos de execução de integração, pipelines, conjuntos de valores.

## <a name="workspace-encryption-configuration"></a>Configuração de criptografia do espaço de trabalho

Os espaços de trabalho podem ser configurados para habilitar a criptografia dupla com uma chave gerenciada pelo cliente no momento da criação do espaço de trabalho. Selecione a opção "habilitar criptografia dupla usando uma chave gerenciada pelo cliente" na guia "segurança" ao criar seu novo espaço de trabalho. Você pode optar por inserir um URI de identificador de chave ou selecionar em uma lista de cofres de chaves na **mesma região** que o espaço de trabalho. A Key Vault em si precisa ter a **proteção de limpeza habilitada**.

> [!IMPORTANT]
> A definição de configuração para a criptografia dupla não pode ser alterada depois que o espaço de trabalho é criado.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Este diagrama mostra a opção que deve ser selecionada para habilitar um espaço de trabalho para criptografia dupla com uma chave gerenciada pelo cliente.":::

### <a name="key-access-and-workspace-activation"></a>Acesso à chave e ativação do espaço de trabalho

O modelo de criptografia Synapse do Azure com chaves gerenciadas pelo cliente envolve o espaço de trabalho acessando as chaves em Azure Key Vault para criptografar e descriptografar conforme necessário. As chaves tornam-se acessíveis para o espaço de trabalho por meio de uma política de acesso ou [acesso Azure Key Vault RBAC](../../key-vault/general/rbac-guide.md). Ao conceder permissões por meio de uma política de acesso de Azure Key Vault, escolha a opção ["somente aplicativo"](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) durante a criação da política (selecione a identidade gerenciada do espaço de trabalho e não a adicione como um aplicativo autorizado).

 A identidade gerenciada do espaço de trabalho deve receber as permissões necessárias no cofre de chaves antes que o espaço de trabalho possa ser ativado. Essa abordagem em fases para a ativação do espaço de trabalho garante que os dados no espaço de trabalho sejam criptografados com a chave gerenciada pelo cliente. Observe que a criptografia pode ser habilitada ou desabilitada para pools dedicados do SQL – cada pool não está habilitado para criptografia por padrão.

#### <a name="permissions"></a>Permissões

Para criptografar ou descriptografar dados em repouso, a identidade gerenciada do espaço de trabalho deve ter as seguintes permissões:
* WrapKey (para inserir uma chave em Key Vault ao criar uma nova chave).
* UnwrapKey (para obter a chave para descriptografia).
* Get (para ler a parte pública de uma chave)

#### <a name="workspace-activation"></a>Ativação do espaço de trabalho

Depois que o espaço de trabalho (com a criptografia dupla habilitada) for criado, ele permanecerá em um estado "pendente" até que a ativação seja realizada com sucesso. O espaço de trabalho deve ser ativado antes que você possa usar totalmente toda a funcionalidade. Por exemplo, você só pode criar um novo pool dedicado do SQL depois que a ativação for realizada com sucesso. Conceda ao espaço de trabalho acesso de identidade gerenciada ao cofre de chaves e clique no link de ativação na faixa de portal do Azure do espaço de trabalho. Depois que a ativação for concluída com êxito, o espaço de trabalho estará pronto para ser usado com a garantia de que todos os dados nele serão protegidos com a chave gerenciada pelo cliente. Conforme observado anteriormente, o cofre de chaves deve ter a proteção de limpeza habilitada para que a ativação tenha sucesso.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Este diagrama mostra a faixa com o link de ativação para o espaço de trabalho.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Gerenciar a chave gerenciada pelo cliente do espaço de trabalho 

Você pode alterar a chave gerenciada pelo cliente usada para criptografar dados da página **criptografia** no portal do Azure. Aqui também, você pode escolher uma nova chave usando um identificador de chave ou selecionar os cofres de chaves aos quais você tem acesso na mesma região que o espaço de trabalho. Se você escolher uma chave em um cofre de chaves diferente daqueles usados anteriormente, conceda as permissões "Get", "encapsular" e "desencapsular" do espaço de trabalho no novo cofre de chaves. O espaço de trabalho validará seu acesso ao novo cofre de chaves e todos os dados no espaço de trabalho serão criptografados novamente com a nova chave.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Este diagrama mostra a seção criptografia de espaço de trabalho no portal do Azure.":::

>[!IMPORTANT]
>Ao alterar a chave de criptografia de um espaço de trabalho, mantenha a chave até substituí-la no espaço de trabalho por uma nova chave. Isso é para permitir a descriptografia de dados com a chave antiga antes que ela seja criptografada novamente com a nova chave.

As políticas do Azure Key Vaults para rotação de chaves automática, periódica ou ações nas chaves podem resultar na criação de novas versões de chave. Você pode optar por criptografar novamente todos os dados no espaço de trabalho com a versão mais recente da chave ativa. Para criptografar novamente, altere a chave no portal do Azure para uma chave temporária e, em seguida, volte para a chave que você deseja usar para criptografia. Por exemplo, para atualizar a criptografia de dados usando a versão mais recente da chave de discagem ativa, altere a chave gerenciada pelo cliente do espaço de trabalho para a chave temporária, Key2. Aguarde a conclusão da criptografia com Key2. Em seguida, alterne a chave gerenciada pelo cliente do espaço de trabalho de volta para key1-os dados no espaço de trabalho serão criptografados novamente com a versão mais recente da key1.

> [!NOTE]
> O Azure Synapse Analytics está trabalhando ativamente para adicionar funcionalidade para criptografar novamente os dados automaticamente quando novas versões de chave são criadas. Até que essa funcionalidade esteja disponível, para garantir a consistência em seu espaço de trabalho, force a nova criptografia dos dados usando o processo detalhado acima.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>SQL Transparent Data Encryption com chaves gerenciadas pelo serviço

O SQL Transparent Data Encryption (TDE) está disponível para pools SQL dedicados em espaços de trabalho *não* habilitados para criptografia dupla. Nesse tipo de espaço de trabalho, uma chave gerenciada por serviço é usada para fornecer criptografia dupla para os dados nos pools SQL dedicados. O TDE com a chave gerenciada por serviço pode ser habilitado ou desabilitado para pools individuais de SQL dedicados.

## <a name="next-steps"></a>Próximas etapas

[Usar políticas internas do Azure para implementar a proteção de criptografia para espaços de trabalho do Synapse](../policy-reference.md)

