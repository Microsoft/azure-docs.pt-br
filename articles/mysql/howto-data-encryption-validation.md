---
title: Como garantir a validação do banco de dados do Azure para MySQL – criptografia de data
description: Saiba como validar a criptografia do banco de dados do Azure para o MySQL-Data Encryption usando a chave gerenciada dos clientes.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 1f47b3d5679b70461eaba64b5815770162cb6fda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241065"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Validando a criptografia de dados para o Azure Database para MySQL

Este artigo ajuda você a validar que a criptografia de dados usando a chave gerenciada pelo cliente para o banco de dados do Azure para MySQL está funcionando conforme o esperado.

## <a name="check-the-encryption-status"></a>Verificar o status de criptografia

### <a name="from-portal"></a>No portal

1. Se você quiser verificar se a chave do cliente é usada para criptografia, siga estas etapas:

    * Na portal do Azure, navegue até as   ->  **chaves** de Azure Key Vault
    * Selecione a chave usada para criptografia do servidor.
    * Defina o status da chave **habilitada** como **não**.
  
       Após algum tempo (**~ 15 min**), o **status** do servidor de banco de dados do Azure para MySQL deve estar **inacessível**. Qualquer operação de e/s feita no servidor falhará, o que validará que o servidor está realmente criptografado com a chave de clientes e que a chave não é válida no momento.
    
       Para **disponibilizar o servidor para** o, você pode revalidar a chave. 
    
    * Defina o status da chave no Key Vault como **Sim**.
    * Na criptografia de **dados** do servidor, selecione **chave de revalidação**.
    * Depois que a revalidação da chave for bem-sucedida, o **status** do servidor será alterado para **disponível**.

2. Na portal do Azure, se você puder garantir que a chave de criptografia esteja definida, os dados serão criptografados usando a chave de clientes usada no portal do Azure.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Visão geral da política de acesso":::

### <a name="from-cli"></a>Da CLI

1. Podemos usar o comando *AZ CLI* para validar os principais recursos que estão sendo usados para o servidor do banco de dados do Azure para MySQL.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Para um servidor sem criptografia de dados definida, esse comando resulta em um conjunto vazio [].

### <a name="azure-audit-reports"></a>Relatórios de auditoria do Azure

Os [relatórios de auditoria](https://servicetrust.microsoft.com) também podem ser analisados, fornecendo informações sobre a conformidade com os padrões de proteção de dados e requisitos regulatórios.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a criptografia de dados, consulte [banco de dados do Azure para MySQL Data Encryption com chave gerenciada pelo cliente](concepts-data-encryption-mysql.md).