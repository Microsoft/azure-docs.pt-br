---
title: Data Encryption-portal do Azure-for Azure Database para PostgreSQL-servidor único
description: Saiba como configurar e gerenciar a criptografia de dados para o servidor único para PostgreSQL usando o portal do Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 98c413f85fe556f5fb413716037163931753e1d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240725"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Criptografia de dados para um servidor único para PostgreSQL para Azure usando o portal do Azure

Saiba como usar o portal do Azure para configurar e gerenciar a criptografia de dados para o servidor único para PostgreSQL do banco de dado do Azure.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para CLI do Azure

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
* No Azure Key Vault, crie um cofre de chaves e a chave para usar para uma chave gerenciada pelo cliente.
* O cofre de chaves deve ter as seguintes propriedades para usar como uma chave gerenciada pelo cliente:
  * [Exclusão reversível](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Limpar protegido](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A chave deve ter os seguintes atributos a serem usados como chave gerenciada pelo cliente:
  * Sem data de validade
  * Não desabilitado
  * Capaz de executar operações Get, wrap Key e Unwrap Key

## <a name="set-the-right-permissions-for-key-operations"></a>Definir as permissões corretas para operações de chave

1. Em Key Vault, selecione **políticas de acesso**  >  **Adicionar política de acesso**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Captura de tela de Key Vault, com políticas de acesso e adicionar política de acesso realçado":::

2. Selecione **permissões de chave** e selecione **obter**, **encapsular**, **desencapsular** e a **entidade de segurança**, que é o nome do servidor PostgreSQL. Se a entidade de segurança do servidor não puder ser encontrada na lista de entidades de segurança existentes, você precisará registrá-la. Você será solicitado a registrar a entidade de segurança do servidor ao tentar configurar a criptografia de dados pela primeira vez e ela falhará.  

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Visão geral da política de acesso":::

3. Clique em **Salvar**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Definir criptografia de dados para um servidor único do banco de dado do Azure para PostgreSQL

1. No banco de dados do Azure para PostgreSQL, selecione **criptografia de dado** para configurar a chave gerenciada pelo cliente.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Captura de tela do banco de dados do Azure para PostgreSQL, com Data Encryption realçado":::

2. Você pode selecionar um cofre de chaves e um par de chaves, ou inserir um identificador de chave.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Captura de tela do banco de dados do Azure para PostgreSQL, com as opções de criptografia realçadas":::

3. Clique em **Salvar**.

4. Para garantir que todos os arquivos (incluindo arquivos temporários) sejam totalmente criptografados, reinicie o servidor.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Usando a criptografia de dados para servidores de restauração ou de réplica

Depois que o servidor único do Banco de Dados do Azure para PostgreSQL é criptografado com uma chave gerenciada pelo cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia por meio de uma operação local ou de restauração geográfica, ou por meio de uma operação de réplica (local/entre regiões). Portanto, para um servidor PostgreSQL criptografado, você pode usar as etapas a seguir para criar um servidor restaurado criptografado.

1. No servidor, selecione **visão geral**  >  **restaurar**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Captura de tela do banco de dados do Azure para PostgreSQL, com visão geral e restauração realçadas":::

   Ou para um servidor habilitado para replicação, no cabeçalho **configurações** , selecione **replicação**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-replica.png" alt-text="Captura de tela do banco de dados do Azure para PostgreSQL, com replicação realçada":::

2. Depois que a operação de restauração for concluída, o novo servidor criado será criptografado com a chave do servidor primário. No entanto, os recursos e as opções no servidor estão desabilitados e o servidor está inacessível. Isso impede qualquer manipulação de dados, porque a identidade do novo servidor ainda não recebeu permissão para acessar o cofre de chaves.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Captura de tela do banco de dados do Azure para PostgreSQL, com status inacessível realçado":::

3. Para tornar o servidor acessível, revalide a chave no servidor restaurado. Selecione a  >  **chave de revalidação** de criptografia de dados.

   > [!NOTE]
   > A primeira tentativa de revalidação falhará, pois a entidade de serviço do novo servidor precisa receber acesso ao cofre de chaves. Para gerar a entidade de serviço, selecione **chave de revalidação**, que mostrará um erro, mas gerará a entidade de serviço. Depois disso, consulte [estas etapas](#set-the-right-permissions-for-key-operations) anteriormente neste artigo.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Captura de tela do banco de dados do Azure para PostgreSQL, com a etapa de revalidação realçada":::

   Você precisará conceder ao cofre de chaves acesso ao novo servidor.

4. Depois de registrar a entidade de serviço, revalide a chave novamente e o servidor retoma sua funcionalidade normal.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Captura de tela do banco de dados do Azure para PostgreSQL, mostrando a funcionalidade restaurada":::

## <a name="next-steps"></a>Próximas etapas

 Para saber mais sobre a criptografia de dados, consulte [banco de dados do Azure para PostgreSQL criptografia de dado de servidor único com chave gerenciada pelo cliente](concepts-data-encryption-postgresql.md).
