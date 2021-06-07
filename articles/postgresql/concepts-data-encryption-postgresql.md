---
title: Criptografia de dados com chave gerenciada pelo cliente – Banco de Dados do Azure para PostgreSQL – Servidor único
description: A criptografia de dados de servidor único do Banco de Dados do Azure para PostgreSQL com chave gerenciada pelo cliente permite que você BYOK (Bring Your Own Key) para proteção de dados em repouso. Ela também permite que as organizações implementem a separação de tarefas no gerenciamento de chaves e dados.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 66faa2b3e6d24c264e2fe26ab42eeaffd48384f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732829"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Criptografia de dados de servidor único do Banco de Dados do Azure para PostgreSQL com uma chave gerenciada pelo cliente

O PostgreSQL do Azure aproveita a [criptografia de armazenamento do Azure](../storage/common/storage-service-encryption.md) para criptografar dados em repouso por padrão usando chaves gerenciadas pela Microsoft. Para usuários do PostgreSQL do Azure, é muito semelhante a Transparent Data Encryption (TDE) em outros bancos de dados, como SQL Server. Muitas organizações exigem controle total sobre o acesso aos dados usando uma chave gerenciada pelo cliente. A criptografia de dados com chaves gerenciadas pelo cliente para o servidor único do Banco de Dados do Azure para PostgreSQL permite que você BYOK (Bring Your Own Key) para proteção de dados em repouso. Ela também permite que as organizações implementem a separação de tarefas no gerenciamento de chaves e dados. Com a criptografia gerenciada pelo cliente, você é responsável por (com controle total): ciclo de vida de uma chave, permissões de uso de chave e auditoria de operações em chaves.

A criptografia de dados com chaves gerenciadas pelo cliente para o servidor único do Banco de Dados do Azure para PostgreSQL é definida no nível do servidor. Para determinado servidor, uma chave gerenciada pelo cliente, chamada KEK (chave de criptografia de chave), é usada para criptografar a DEK (chave de criptografia de dados) usada pelo serviço. A KEK é uma chave assimétrica armazenada em uma instância do [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) gerenciada pelo cliente e de propriedade dele. A KEK (chave de criptografia de chave) e a DEK (chave de criptografia de dados) são descritas em mais detalhes posteriormente neste artigo.

O Key Vault é um sistema de gerenciamento de chaves externas baseado em nuvem. Ele é altamente disponível e fornece armazenamento seguro escalonável para chaves de criptografia RSA, opcionalmente apoiado por HSMs (módulos de segurança de hardware) validados pelo FIPS 140-2 Nível 2. Ele não permite acesso direto a uma chave armazenada, mas fornece serviços de criptografia e descriptografia para as entidades autorizadas. O Key Vault pode gerar a chave, importá-la ou [transferi-la de um dispositivo HSM local](../key-vault/keys/hsm-protected-keys.md).

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure nas quais o servidor único do Banco de Dados do Azure para PostgreSQL dá suporte aos tipos de preço "Uso Geral" e "Otimizado para Memória". Para obter outras limitações, consulte a seção [limitação](concepts-data-encryption-postgresql.md#limitations) .

## <a name="benefits"></a>Benefícios

A criptografia de dados com chaves gerenciadas pelo cliente para um servidor único para PostgreSQL fornece os seguintes benefícios:

* O acesso a dados é totalmente controlado por você pela capacidade de remover a chave e tornar o banco de dados inacessível 
*    Controle total sobre o ciclo de vida da chave, incluindo a rotação da chave para se alinhar com as políticas corporativas
*    Gerenciamento central e organização de chaves no Azure Key Vault
*    Capacidade de implementar a separação de tarefas entre os gerentes de segurança, DBA e administradores do sistema

## <a name="terminology-and-description"></a>Descrição e terminologia

**DEK (chave de criptografia de dados)** : uma chave simétrica AES256 usada para criptografar uma partição ou bloco de dados. Criptografar cada bloco de dados com uma chave diferente torna os ataques de análise de criptografia mais difíceis. O acesso a DEKs é necessário pelo provedor de recursos ou instância do aplicativo que criptografa e descriptografa um bloco específico. Quando você substitui uma DEK por uma nova chave, apenas os dados no respectivo bloco associado precisam ser criptografados novamente com a nova chave.

**KEK (chave de criptografia de chave)** : uma chave de criptografia usada para criptografar as DEKs. Uma KEK que nunca deixa o Key Vault permite que as DEKs sejam criptografadas e controladas. A entidade que tem acesso à KEK pode ser diferente da entidade que requer a DEK. Uma vez que o KEK é necessário para descriptografar os DEKs, o KEK é efetivamente um ponto único pelo qual os DEKs podem ser efetivamente excluídos pela exclusão do KEK.

As DEKs, criptografadas com as KEKs, são armazenadas separadamente. Somente uma entidade com acesso à KEK pode descriptografar essas DEKs. Para obter mais informações, confira [Segurança na criptografia em repouso](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Como funciona a criptografia de dados com uma chave gerenciada pelo cliente

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png" alt-text="Diagrama que mostra uma visão geral de Bring Your Own Key":::

Para que um servidor PostgreSQL use chaves gerenciadas pelo cliente armazenadas no Key Vault para criptografia da DEK, um administrador do Key Vault fornece os seguintes direitos de acesso ao servidor:

* **obter**: para recuperar a parte pública e as propriedades da chave no cofre de chaves.
* **wrapKey**: para poder criptografar a DEK. O DEK criptografado é armazenado no banco de dados do Azure para PostgreSQL.
* **unwrapKey**: para poder descriptografar a DEK. O banco de dados do Azure para PostgreSQL precisa do DEK descriptografado para criptografar/descriptografar os dados

O administrador do cofre de chaves também pode [habilitar o registro em log de eventos de auditoria do Key Vault](../azure-monitor/insights/key-vault-insights-overview.md), para que eles possam ser auditados posteriormente.

Quando o servidor é configurado para usar a chave gerenciada pelo cliente armazenada no cofre de chaves, o servidor envia a DEK para o cofre de chaves para criptografias. O Key Vault retorna a DEK criptografada, que é armazenada no banco de dados do usuário. De modo semelhante, quando necessário, o servidor envia a DEK protegida para o cofre de chaves para descriptografia. Os auditores poderão usar o Azure Monitor para examinar os logs de eventos do Key Vault se o registro em log estiver habilitado.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Requisitos para configurar a criptografia de dados para o servidor único do Banco de Dados do Azure para PostgreSQL

Veja a seguir os requisitos para configurar o Key Vault:

* O Key Vault e o servidor único do Banco de Dados do Azure para PostgreSQL precisam pertencer ao mesmo locatário do Azure AD (Azure Active Directory). Não há suporte para interações do servidor e do Key Vault entre locatários. A movimentação do recurso de Key Vault depois requer que você reconfigure a criptografia de dados.
* O cofre de chaves deve ser definido com 90 dias para ' dias para manter os cofres excluídos '. Se o cofre de chaves existente tiver sido configurado com um número mais baixo, você precisará criar um novo cofre de chaves, pois ele não pode ser modificado após a criação.
* Habilite o recurso de exclusão reversível no cofre de chaves para proteger contra perda de dados em caso de exclusão acidental da chave (ou do cofre de chaves). Os recursos excluídos com a exclusão reversível são retidos por 90 dias, a menos que o usuário os recupere ou limpe pelo. As ações de recuperação e limpeza têm suas próprias permissões associadas em uma política de acesso do Key Vault. O recurso de exclusão reversível está desativado por padrão, mas você pode habilitá-lo por meio do PowerShell ou da CLI do Azure (observe que não é possível habilitá-lo por meio do portal do Azure). 
* Habilitar a proteção de limpeza para impor um período de retenção obrigatório para cofres e objetos de cofre excluídos
* Conceda o acesso de servidor único do Banco de Dados do Azure para PostgreSQL ao cofre de chaves com as permissões get, wrapKey e unwrapKey usando a identidade gerenciada exclusiva. No portal do Azure, a identidade exclusiva do ' serviço ' é criada automaticamente quando a criptografia de dados é habilitada no servidor único PostgreSQL. Confira [Data encryption for Azure Database for PostgreSQL Single server by using the Azure portal](howto-data-encryption-portal.md) (Criptografia de dados para servidor único do Banco de Dados do Azure para PostgreSQL usando o portal do Azure) para obter instruções detalhadas passo a passo quando você estiver usando o portal do Azure.

Veja a seguir os requisitos para configurar a chave gerenciada pelo cliente:

* A chave gerenciada pelo cliente a ser usada para criptografar a DEK só pode ser assimétrica, RSA 2048.
* A data de ativação da chave (se definida) precisa ser uma data e uma hora no passado. A data de validade (se definida) precisa ser uma data e hora no futuro.
* A chave precisa estar no estado *Habilitado*.
* Se você estiver [importando uma chave existente](/rest/api/keyvault/ImportKey/ImportKey) para o cofre de chaves, certifique-se de fornecê-la nos formatos de arquivo com suporte ( `.pfx` , `.byok` , `.backup` ).

## <a name="recommendations"></a>Recomendações

Quando você estiver usando a criptografia de dados usando uma chave gerenciada pelo cliente, veja as recomendações para configurar o Key Vault:

* Defina um bloqueio de recurso no Key Vault para controlar quem pode excluir esse recurso crítico e evitar a exclusão acidental ou não autorizada.
* Habilite a auditoria e relatórios em todas as chaves de criptografia. O Key Vault fornece logs que são fáceis de serem injetados em outras ferramentas de gerenciamento de eventos e informações de segurança. O Log Analytics do Azure Monitor é um exemplo de um serviço que já está integrado.
* Verifique se o Key Vault e o servidor único do Banco de Dados do Azure para PostgreSQL residem na mesma região, para garantir um acesso mais rápido para as operações de encapsulamento e desencapsulamento da DEK.
* Bloqueie o Azure Key Vault para apenas **ponto de extremidade privado e redes selecionadas** e permita somente serviços *confiáveis da Microsoft* para proteger os recursos.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

Veja recomendações para configurar uma chave gerenciada pelo cliente:

* Mantenha uma cópia da chave gerenciada pelo cliente em um local seguro ou coloque-a no serviço de caução.

* Se o Key Vault gerar uma chave, crie um backup da chave antes de usá-la pela primeira vez. Você só pode restaurar o backup para o Key Vault. Para obter mais informações sobre o comando backup, confira [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condição de chave gerenciada pelo cliente inacessível

Quando você configura a criptografia de dados com uma chave gerenciada pelo cliente no Key Vault, o acesso contínuo a essa chave é necessário para que o servidor permaneça online. Se o servidor perder o acesso à chave gerenciada pelo cliente no Key Vault, o servidor começará a negar todas as conexões em dez minutos. O servidor emite uma mensagem de erro correspondente e altera o estado do servidor para *inacessível*. Algumas das razões pelas quais o servidor pode alcançar esse estado são:

* Se criarmos um servidor de restauração pontual para o servidor único do Banco de Dados do Azure para PostgreSQL, que tem criptografia de dados habilitada, o servidor recém-criado estará no estado *Inacessível*. Você pode corrigir o estado do servidor por meio do [portal do Azure](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) ou da [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Se criarmos uma réplica de leitura para o servidor único do Banco de Dados do Azure para PostgreSQL, que tem criptografia de dados habilitada, o servidor de réplica estará no estado *Inacessível*. Você pode corrigir o estado do servidor por meio do [portal do Azure](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) ou da [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Se você excluir o Key Vault, o servidor único do Banco de Dados do Azure para PostgreSQL não poderá acessar a chave e será movido para o estado *Inacessível*. Recupere o [Key Vault](../key-vault/general/key-vault-recovery.md) e revalide a criptografia de dados para tornar o servidor *Disponível*.
* Se excluirmos a chave do Key Vault, o servidor único do Banco de Dados do Azure para PostgreSQL não poderá acessar a chave e será movido para o estado *Inacessível*. Recupere a [Chave](../key-vault/general/key-vault-recovery.md) e revalide a criptografia de dados para tornar o servidor *Disponível*.
* Se a chave armazenada no Azure Key Vault expirar, ela se tornará inválida e o servidor único do Banco de Dados do Azure para PostgreSQL passará para o estado *Inacessível*. Estenda a data de expiração da chave usando a [CLI](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) e revalide a criptografia de dados para tornar o servidor *Disponível*.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Revogação acidental de acesso à chave do Key Vault

Pode acontecer de alguém com direitos de acesso suficientes ao Key Vault desabilitar acidentalmente o acesso do servidor à chave ao:

* Revogar as permissões get, wrapKey, and unwrapKey do cofre de chaves do servidor.
* Excluir a chave.
* Excluir o cofre de chaves.
* Alterar as regras de firewall do cofre de chaves.

* Excluir a identidade gerenciada do servidor no Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorar a chave gerenciada pelo cliente no Key Vault

Para monitorar o estado do banco de dados e habilitar o alerta para perda de acesso ao protetor do Transparent Data Encryption, configure os seguintes recursos do Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md): um banco de dados inacessível que perdeu o acesso à chave do cliente aparecerá como "Inacessível" após a negação da primeira conexão com o banco de dados.
* [Log de atividades](../service-health/alerts-activity-log-service-notifications-portal.md): quando o acesso à chave do cliente falha no Key Vault gerenciado pelo cliente, as entradas são adicionadas ao log de atividades. Você poderá restabelecer o acesso assim que possível se criar alertas para esses eventos.

* [Grupos de ação](../azure-monitor/alerts/action-groups.md): defina esses grupos para enviar notificações e alertas com base em suas preferências.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Restaurar e replicar com uma chave gerenciada do cliente no Key Vault

Depois que o servidor único do Banco de Dados do Azure para PostgreSQL é criptografado com uma chave gerenciada pelo cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia por meio de uma operação de restauração local ou geográfica, ou por meio de réplicas de leitura. No entanto, a cópia pode ser alterada para refletir a nova chave gerenciada pelo cliente para criptografia. Quando a chave gerenciada pelo cliente é alterada, os backups antigos do servidor começam a usar a chave mais recente.

Para evitar problemas durante a configuração da criptografia de dados gerenciada pelo cliente durante a restauração ou a criação da réplica de leitura, é importante seguir estas etapas nos servidores primário e restaurado/de réplica:

* Inicie o processo de restauração ou leitura de réplica do servidor único do banco de dados do Azure primário para PostgreSQL.
* Mantenha o servidor recém-criado (restaurado/de réplica) em um estado inacessível, porque sua identidade exclusiva ainda não recebeu permissões para o Key Vault.
* No servidor restaurado/de réplica, revalide a chave gerenciada pelo cliente nas configurações de criptografia de dados. Isso garante que o servidor recém-criado receba permissões de encapsulamento e desencapsulamento para a chave armazenada no Key Vault.

## <a name="limitations"></a>Limitações

Para o banco de dados do Azure para PostgreSQL, o suporte para criptografia de data em repouso usando a chave gerenciada do cliente (CMK) tem poucas limitações-

* O suporte para essa funcionalidade é limitado a tipos de preço **uso geral** e com **otimização de memória** .
* Esse recurso só tem suporte em regiões e servidores que dão suporte ao armazenamento de até 16TB. Para obter a lista de regiões do Azure que dão suporte ao armazenamento de até 16TB, consulte a seção armazenamento na documentação [aqui](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Todos os novos servidores PostgreSQL criados nas regiões listadas acima, o suporte para criptografia com as chaves do Gerenciador de clientes está **disponível**. O servidor PITR (ponto no tempo restaurado) ou a réplica de leitura não serão qualificados, embora, na teoria, sejam ' New '.
    > - Para validar se o servidor provisionado dá suporte a até 16TB, você pode ir para a folha tipo de preço no portal e ver o tamanho máximo de armazenamento suportado pelo servidor provisionado. Se você puder mover o controle deslizante até 4 TB, seu servidor poderá não dar suporte à criptografia com chaves gerenciadas pelo cliente. No entanto, os dados são criptografados usando chaves de serviço gerenciadas em todos os momentos. Entre em contato com AskAzureDBforPostgreSQL@service.microsoft.com se você tiver alguma dúvida.

* A criptografia só tem suporte com a chave de criptografia RSA 2048.

## <a name="next-steps"></a>Próximas etapas

Saiba como [configurar a criptografia de dados com uma chave gerenciada pelo cliente para o servidor único do Banco de Dados do Azure para PostgreSQL usando o portal do Azure](howto-data-encryption-portal.md).
