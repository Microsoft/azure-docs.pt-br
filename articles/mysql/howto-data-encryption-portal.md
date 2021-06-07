---
title: Data Encryption-portal do Azure-banco de dados do Azure para MySQL
description: Saiba como configurar e gerenciar a criptografia de dados para o banco de dado do Azure para MySQL usando o portal do Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00670746c1686bca354adc989ddce6c9dd336491
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96519052"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Criptografia de dados para o Azure Database para MySQL usando o portal do Azure

Saiba como usar o portal do Azure para configurar e gerenciar a criptografia de dados para o banco de dado do Azure para MySQL.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para CLI do Azure

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
* No Azure Key Vault, crie um cofre de chaves e uma chave para usar para uma chave gerenciada pelo cliente.
* O cofre de chaves deve ter as seguintes propriedades para usar como uma chave gerenciada pelo cliente:
  * [Exclusão reversível](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Limpar protegido](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * Dias de retenção definidos como 90 dias
  
    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* A chave deve ter os seguintes atributos a serem usados como chave gerenciada pelo cliente:
  * Sem data de validade
  * Não desabilitado
  * Executar operações **Get**, **Wrap** e **Unwrap**
  * atributo recoverylevel definido como **recuperável** (isso requer a exclusão reversível habilitada com o período de retenção definido como 90 dias)
  * Proteção de limpeza habilitada

Você pode verificar os atributos acima da chave usando o seguinte comando:

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>Definir as permissões corretas para operações de chave

1. Em Key Vault, selecione **políticas de acesso**  >  **Adicionar política de acesso**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Captura de tela de Key Vault, com políticas de acesso e adicionar política de acesso realçado":::

2. Selecione **permissões de chave** e selecione **obter**, **encapsular**, **desencapsular** e a **entidade de segurança**, que é o nome do servidor MySQL. Se a entidade de segurança do servidor não puder ser encontrada na lista de entidades de segurança existentes, você precisará registrá-la. Você será solicitado a registrar a entidade de segurança do servidor ao tentar configurar a criptografia de dados pela primeira vez e ela falhará.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Visão geral da política de acesso":::

3. Clique em **Salvar**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Definir a criptografia de dados para o Azure Database para MySQL

1. No banco de dados do Azure para MySQL, selecione **criptografia de dado** para configurar a chave gerenciada pelo cliente.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Captura de tela do banco de dados do Azure para MySQL, com Data Encryption realçado":::

2. Você pode selecionar um cofre de chaves e um par de chaves, ou inserir um identificador de chave.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Captura de tela do banco de dados do Azure para MySQL, com opções de criptografia de dados destacadas":::

3. Clique em **Salvar**.

4. Para garantir que todos os arquivos (incluindo arquivos temporários) sejam totalmente criptografados, reinicie o servidor.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Usando a criptografia de dados para servidores de restauração ou de réplica

Depois que o Banco de Dados do Azure para MySQL é criptografado com uma chave gerenciada pelo cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia por meio de uma operação local ou de restauração geográfica, ou por meio de uma operação de réplica (local/entre regiões). Portanto, para um servidor MySQL criptografado, você pode usar as etapas a seguir para criar um servidor restaurado criptografado.

1. No servidor, selecione **visão geral**  >  **restaurar**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Captura de tela do banco de dados do Azure para MySQL, com visão geral e restauração realçadas":::

   Ou para um servidor habilitado para replicação, no cabeçalho **configurações** , selecione **replicação**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="Captura de tela do banco de dados do Azure para MySQL, com replicação realçada":::

2. Depois que a operação de restauração for concluída, o novo servidor criado será criptografado com a chave do servidor primário. No entanto, os recursos e as opções no servidor estão desabilitados e o servidor está inacessível. Isso impede qualquer manipulação de dados, porque a identidade do novo servidor ainda não recebeu permissão para acessar o cofre de chaves.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Captura de tela do banco de dados do Azure para MySQL, com status inacessível realçado":::

3. Para tornar o servidor acessível, revalide a chave no servidor restaurado. Selecione a  >  **chave de revalidação** de criptografia de dados.

   > [!NOTE]
   > A primeira tentativa de revalidação falhará, pois a entidade de serviço do novo servidor precisa receber acesso ao cofre de chaves. Para gerar a entidade de serviço, selecione **chave de revalidação**, que mostrará um erro, mas gerará a entidade de serviço. Depois disso, consulte [estas etapas](#set-the-right-permissions-for-key-operations) anteriormente neste artigo.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Captura de tela do banco de dados do Azure para MySQL, com a etapa de revalidação realçada":::

   Você precisará conceder ao cofre de chaves acesso ao novo servidor.

4. Depois de registrar a entidade de serviço, revalide a chave novamente e o servidor retoma sua funcionalidade normal.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Captura de tela do banco de dados do Azure para MySQL, mostrando a funcionalidade restaurada":::

## <a name="next-steps"></a>Próximas etapas

 Para saber mais sobre a criptografia de dados, consulte [banco de dados do Azure para MySQL Data Encryption com chave gerenciada pelo cliente](concepts-data-encryption-mysql.md).
