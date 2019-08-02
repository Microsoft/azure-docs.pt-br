---
title: Integração do TDE com o Azure Key Vault ou BYOK (Bring Your Own Key) – Banco de Dados SQL do Azure| Microsoft Docs
description: Suporte BYOK (Bring Your Own Key) para TDE (Transparent Data Encryption) com Azure Key Vault para Banco de Dados SQL e Data Warehouse. Visão geral de TDE com BYOK, benefícios, como funciona, considerações e recomendações.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 07/18/2019
ms.openlocfilehash: cdd5e29fcc01639c03da70614f53ac648ee6620c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318575"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure SQL Transparent Data Encryption com chaves gerenciadas pelo cliente no Azure Key Vault: Suporte Bring Your Own Key

A integração do [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) com o Azure Key Vault permite criptografar a DEK (Chave de Criptografia do Banco de Dados) com uma chave assimétrica gerenciada pelo cliente chamada Protetor de TDE. Isso também é geralmente chamado de suporte a BYOK (Bring Your Own Key) para Transparent Data Encryption.  No cenário de BYOK, o Protetor de TDE é armazenado em um [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) gerenciado e de propriedade do cliente, o sistema de gerenciamento de chave externa baseado em nuvem do Azure. O Protetor de TDE pode ser [gerado](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) pelo cofre de chaves ou [transferido](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) para o cofre de chaves de um dispositivo HSM local. A DEK de TDE, que é armazenada na página de inicialização de um banco de dados, é criptografada e descriptografada pelo Protetor de TDE armazenado no Azure Key Vault, que nunca sai.  O Banco de Dados SQL precisa ter permissões concedidas para o cofre de chaves de propriedade do cliente para descriptografar e criptografar a DEK. Se as permissões do SQL Server lógico para o cofre de chaves forem revogadas, um banco de dados ficará inacessível, as conexões serão negadas e todos os dados serão criptografados. Para o Banco de Dados SQL do Azure, o protetor de TDE é definido no nível do SQL Server lógico e é herdado por todos os bancos de dados associados a esse servidor. Para a [Instância Gerenciada do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), o protetor de TDE é definido no nível de instância e é herdado por todos os bancos de dados *criptografados* nessa instância. O termo *servidor* refere-se ao servidor e instância ao longo deste documento, a menos que indicado de forma diferente.

> [!NOTE]
> Transparent Data Encryption com integração de Azure Key Vault (Bring Your Own Key) para Instância Gerenciada do Banco de Dados SQL do Azure está em versão prévia.


Com a integração do TDE ao Azure Key Vault, os usuários podem controlar as principais tarefas de gerenciamento, incluindo rotações de chave, permissões de cofre de chaves, backups de chaves e habilitar auditoria/relatório em todos os protetores de TDE usando a funcionalidade do Azure Key Vault. O Key Vault fornece gerenciamento central de chaves, utiliza HSMs (Módulos de Segurança de Hardware) rigidamente monitorados e permite a separação de funções entre o gerenciamento de chaves e dados para ajudar a atender a conformidade com políticas de segurança.  

A integração do TDE com o Azure Key Vault fornece os seguintes benefícios:

- Maior transparência e controle granular com a capacidade de autogerenciar o protetor de TDE
- Capacidade de revogar permissões a qualquer momento para renderizar o banco de dados inacessível
- Gerenciamento central de protetores de TDE (em conjunto com outras chaves e segredos usados em outros serviços do Azure) hospedando-os no Key Vault
- Separação das principais responsabilidades de gerenciamento de dados dentro da organização para dar suporte à separação de funções
- Maior confiança dos próprios clientes, pois o Key Vault é projetado para que a Microsoft não veja nem extraia nenhuma chave de criptografia.
- Suporte para rotação de chave

> [!IMPORTANT]
> Para aqueles que usam TDE de serviço gerenciado e que gostariam de começar a usar o Key Vault, a TDE permanecerá habilitada durante o processo de mudança para um protetor de TDE no Key Vault. Não há tempo de inatividade nem nova criptografia dos arquivos do banco de dados. A mudança de uma chave de serviço gerenciado para uma chave do Key Vault requer apenas a nova criptografia da DEK (Chave de Criptografia) do banco de dados, que é uma operação rápida e online.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Como a integração do TDE com o Azure Key Vault dá suporte ao trabalho

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

![Autenticação do Servidor para o Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Quando o TDE é configurado pela primeira vez para usar um protetor de TDE do Key Vault, o servidor envia a DEK de cada banco de dados habilitado por TDE para Key Vault para uma solicitação de chave de encapsulamento. O Key Vault retorna a chave de criptografia do banco de dados criptografada, que é armazenada no banco de dados do usuário.  

> [!IMPORTANT]
> É importante observar que **depois que um Protetor de TDE é armazenado no Azure Key Vault, o protetor nunca sai do Azure Key Vault**. O servidor somente pode enviar solicitações de operação de chave para o material da chave de proteção de TDE dentro do Key Vault e **nunca acessa ou armazena em cache o protetor de TDE** . O administrador de Key Vault tem o direito de revogar Key Vault permissões do servidor a qualquer momento; nesse caso, todas as conexões com o banco de dados são negadas.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Diretrizes para configurar o TDE com o Azure Key Vault

### <a name="general-guidelines"></a>Diretrizes gerais

- Certifique-se de que o Azure Key Vault e o Banco de Dados SQL do Azure/Instância Gerenciada estejam no mesmo locatário.  Interações do servidor e do cofre de chaves entre locatários **não têm suporte**.
- Se você estiver planejando uma movimentação de locatário, TDE com AKV precisará ser reconfigurado, saiba mais sobre como [mover recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Ao configurar TDE com Azure Key Vault, é importante considerar a carga colocada no cofre de chaves por meio de repetidas operações de encapsulamento/desencapsulamento. Por exemplo, como todos os bancos de dados associados a um servidor do Banco de Dados SQL usam o mesmo protetor de TDE, um failover desse servidor disparará o máximo de operações de chave no cofre, pois há bancos de dados no servidor. Com base em nossa experiência e nos [limites de serviço do cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits) documentados, é recomendável associar no máximo 200 bancos de dados Comercialmente Crítico/Premium ou 500 Standard/Uso Geral com um Azure Key Vault em uma única assinatura para garantir alta disponibilidade consistente ao acessar o protetor de TDE no cofre.
- Recomendável: Mantenha uma cópia do protetor de TDE nas instalações.  Isso requer um dispositivo de HSM para criar um Protetor de TDE localmente e um sistema de caução de chave para armazenar uma cópia local do Protetor de TDE.  Saiba [como transferir uma chave de um HSM local para o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Diretrizes para configurar o Azure Key Vault

- Crie um cofre de chaves com a proteção de [exclusão](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) e limpeza reversível habilitada para proteger contra perda de dados em caso de chave acidental – ou cofre de chaves – exclusão.  É necessário usar o [PowerShell para habilitar a propriedade "exclusão reversível"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) no cofre de chaves (essa opção ainda não está disponível no Portal do AKV - mas exigida pelo Azure SQL):  
  - Os recursos excluídos por software são retidos por um período de tempo definido, 90 dias, a menos que sejam recuperados ou limpos.
  - As ações de **recuperação** e **limpeza** têm suas próprias permissões associadas em uma política de acesso de cofre de chaves.
- Defina um bloqueio de recurso no cofre de chaves para controlar quem pode excluir esse recurso crítico e ajudar a evitar a exclusão acidental ou não autorizada.  [Saiba mais sobre bloqueios de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Conceda ao servidor do Banco de Dados SQL acesso ao cofre de chaves usando a identidade do Azure AD (Azure Active Directory).  Ao usar a interface do usuário do portal, a identidade do Azure AD é criada automaticamente e as permissões de acesso ao cofre de chaves são concedidas ao servidor.  Usando o PowerShell para configurar TDE com BYOK, a identidade do Azure AD deve ser criada e a conclusão verificada. Consulte [Configurar a TDE com BYOK](transparent-data-encryption-byok-azure-sql-configure.md) e [Configurar a TDE com BYOK para a Instância Gerenciada](https://aka.ms/sqlmibyoktdepowershell) para obter instruções passo a passo detalhadas ao usar o PowerShell.

   > [!NOTE]
   > Se a identidade do Azure AD **for excluída acidentalmente ou se as permissões do servidor forem revogadas** usando a política de acesso do cofre de chaves ou inadvertidamente movendo o servidor para um locatário diferente, o servidor perderá o acesso ao cofre de chaves e TDEá os bancos de dados criptografados estarão inacessíveis e logons serão negados até que a identidade e as permissões do servidor lógico do Azure AD tenham sido restauradas.  

- Ao usar firewalls e redes virtuais com Azure Key Vault, você deve permitir que serviços confiáveis da Microsoft ignorem esse firewall. Escolha Sim.

   > [!NOTE]
   > Se os bancos de dados SQL criptografados do TDE perderem o acesso ao cofre de chaves porque não podem ignorar o firewall, os bancos de dados estarão inacessíveis e logons serão negados até que as permissões de bypass do firewall tenham sido restauradas.

- Habilite a auditoria e relatórios em todas as chaves de criptografia: O Key Vault fornece logs que são fáceis de serem injetados em outras ferramentas de SIEM (gerenciamento de eventos e informações de segurança). O OMS (Operations Management Suite) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) é um exemplo de um serviço que já está integrado.
- Para garantir a alta disponibilidade de bancos de dados criptografados, configure cada servidor de banco de dados SQL com dois cofres de chaves do Azure que residem em regiões diferentes.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Diretrizes para configurar o protetor de TDE (chave assimétrica)

- Crie a chave de criptografia localmente em um dispositivo de HSM local. Certifique-se de que esta seja uma chave RSA 2048 assimétrica para que possa ser armazenada no Azure Key Vault.
- Garanta a chave em um sistema de caução de chave.  
- Importe o arquivo de chave de criptografia (.pfx, .byok ou .backup) para o Azure Key Vault.

   > [!NOTE]
   > Para fins de teste, é possível criar uma chave com o Azure Key Vault, no entanto, essa chave não pode ser garantida porque a chave privada nunca pode sair do cofre de chaves.  Sempre faça backup e armazene as chaves usadas para criptografar os dados de produção, pois a perda da chave (exclusão acidental no cofre de chaves, expiração, etc.) resulta em perda permanente de dados.

- Se você usar uma chave com uma data de expiração – implemente um sistema de aviso de expiração para girar a chave antes de ela expirar: **depois que a chave expirar, os bancos de dados criptografados perderão o acesso ao seu protetor TDE e** ficarão inacessíveis e todos os logons serão negados até a chave foi girada para uma nova chave.
- Certifique-se de que a chave esteja habilitada e tenha permissões para executar as operações *get*, *codificar chave* e *decodificar chave*.
- Crie um backup de chave do Azure Key Vault antes de usar a chave no Azure Key Vault pela primeira vez. Saiba mais sobre o comando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) .
- Crie um novo backup sempre que forem feitas alterações na chave (por exemplo, adicionar ACLs, adicionar marcas, adicionar atributos de chave).
- **Mantenha as versões anteriores** da chave no cofre de chaves ao fazer rotação de chaves para que os backups mais antigos do banco de dados possam ser restaurados. Quando o Protetor de TDE é alterado para um banco de dados, os backups antigos do banco de dados **não são atualizados** para usar o Protetor de TDE mais recente.  Cada backup precisa do Protetor de TDE em que foi criado na hora da restauração. As rotações de chave podem ser executadas seguindo as instruções em [Girar o Protetor de Transparent Data Encryption usando PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Mantenha todas as chaves usadas anteriormente no Azure Key Vault depois de voltar às chaves de serviço gerenciado.  Isso garante que os backups do banco de dados possam ser restaurados com os protetores de TDE armazenados no Azure Key Vault.  Os protetores de TDE criados com o Azure Key Vault devem ser mantidos até que todos os backups armazenados sejam criados com chaves de serviço gerenciado.  
- Faça cópias de backup recuperáveis dessas chaves usando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).
- Para remover uma chave potencialmente comprometida durante um incidente de segurança sem o risco de perda de dados, siga as etapas em [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

### <a name="guidelines-for-monitoring-the-tde-with-azure-key-vault-configuration"></a>Diretrizes para monitorar o TDE com a configuração Azure Key Vault

Se o SQL Server lógico perder o acesso ao protetor de TDE gerenciado pelo cliente no Azure Key Vault, o banco de dados negará todas as conexões e aparecerá inacessível na portal do Azure.  As causas mais comuns para isso são:
- Key Vault excluído acidentalmente ou atrás de um firewall
- Chave do Key Vault excluída acidentalmente ou expirada
- A AppId da instância de SQL Server lógica excluída acidentalmente
- Permissões de chave específicas para a instância de SQL Server lógica AppId revogada

 > [!NOTE]
 > O banco de dados será reparado e se tornará online automaticamente se o acesso ao protetor de TDE gerenciado pelo cliente for restaurado dentro de 48 horas.  Se o banco de dados estiver inacessível devido a uma interrupção intermitente da rede, não haverá nenhuma ação necessária e os bancos de dados voltarão a ficar online automaticamente.
  
- Para obter mais informações sobre como solucionar problemas de configurações existentes, consulte [solucionar problemas do TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde)

- Para monitorar o estado do banco de dados e habilitar o alerta para perda de acesso ao protetor do TDE, configure os seguintes recursos do Azure:
    - [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Um banco de dados inacessível que perdeu o acesso ao protetor de TDE aparecerá como "indisponível" após a negação da primeira conexão com o banco de dados.
    - [Log de atividades](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) quando o acesso ao protetor de TDE no cofre de chaves gerenciado pelo cliente falha, as entradas são adicionadas ao log de atividades.  A criação de alertas para esses eventos permitirá que você reinstale o acesso assim que possível.
    - Os [grupos de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) podem ser definidos para enviar notificações e alertas com base em suas preferências, por exemplo, email/SMS/Push/voz, aplicativo lógico, webhook, ITSM ou runbook de automação.
    

## <a name="high-availability-geo-replication-and-backup--restore"></a>Alta disponibilidade, replicação geográfica e backup/restauração

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastres

Como configurar alta disponibilidade com Azure Key Vault depende da configuração do banco de dados e do servidor do Banco de Dados SQL, e aqui estão as configurações recomendadas para dois casos distintos.  O primeiro caso é um banco de dados independente ou servidor do Banco de Dados SQL sem redundância geográfica configurada.  O segundo caso é um banco de dados ou servidor do Banco de Dados SQL configurado com grupos de failover ou redundância geográfica, que exige que cada cópia com redundância geográfica tenha um Azure Key Vault local dentro do grupo de failover para garantir que os failovers geográficos funcionem.

No primeiro caso, se você precisar de alta disponibilidade de um banco de dados e servidor do Banco de Dados SQL sem nenhuma redundância geográfica configurada, é altamente recomendável configurar o servidor para usar dois cofres de chaves diferentes em duas regiões diferentes com o mesmo material da chave. Isso pode ser feito, criando um protetor de TDE usando o Key Vault primário colocalizado na mesma região do servidor do Banco de Dados SQL e clonando a chave em um cofre de chaves em uma região do Azure diferente, de modo que o servidor tenha acesso a um segundo cofre de chaves, caso o Key Vault primário sofra uma interrupção enquanto o banco de dados estiver em execução. Use o cmdlet backup-AzKeyVaultKey para recuperar a chave no formato criptografado do cofre de chaves primárias e, em seguida, use o cmdlet Restore-AzKeyVaultKey e especifique um cofre de chaves na segunda região.

![HA de servidor único e sem Geo-DR](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Como configurar Geo-DR com Azure Key Vault

Para manter a alta disponibilidade de Protetores de TDE para bancos de dados criptografados, é necessário configurar Azure Key Vaults redundantes com base nos grupos de failover existentes ou desejados do Banco de Dados SQL ou em instâncias de replicação geográfica ativas.  Cada servidor com replicação geográfica requer um cofre de chaves separado, que deve estar colocalizado com o servidor na mesma região do Azure. Se um banco de dados primário ficar inacessível devido a uma indisponibilidade em uma região e um failover for disparado, o banco de dados secundário poderá assumir o controle usando o cofre de chaves secundário.

Para bancos de dados SQL do Azure com Replicação Geográfica, a seguinte configuração do Azure Key Vault é necessária:

- Um banco de dados primário com um cofre de chaves na região e um banco de dados secundário com um cofre de chaves na região.
- Pelo menos um secundário é necessário e há suporte para até quatro secundários.
- Não há suporte para secundários de secundários (encadeamento).

A seção a seguir descreverá as etapas de configuração e configuração em mais detalhes.

### <a name="azure-key-vault-configuration-steps"></a>Etapas de configuração do Azure Key Vault

- Instalar [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Crie dois Azure Key Vaults em duas regiões diferentes usando o [PowerShell para habilitar a propriedade "exclusão reversível"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) nos cofres de chaves (essa opção ainda não está disponível no Portal do AKV, mas é exigida pelo SQL).
- Ambos os Azure Key Vaults devem estar localizados nas duas regiões disponíveis na mesma área geográfica do Azure para que o backup e a restauração das chaves funcionem.  Se você precisar que os dois cofre de chaves estejam localizados em diferentes áreas geográficas para atender aos requisitos de Geo-DR de SQL, siga o [Processo de BYOK](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) que permite que as chaves sejam importadas de um HSM local.
- Crie uma nova chave no primeiro cofre de chaves:  
  - Chave RSA/RSA-HSA 2048
  - Sem datas de validade
  - A chave está habilitada e tem permissões para executar operações get, codificar chave e decodificar chave
- Faça backup da chave primária e restaure a chave para o segundo cofre de chaves.  Consulte [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) e [Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey).

### <a name="azure-sql-database-configuration-steps"></a>Etapas de configuração do Banco de Dados SQL do Azure

As etapas de configuração a seguir diferem se iniciar com uma nova implantação de SQL ou se estiver trabalhando com uma implantação de Geo-DR de SQL já existente.  Primeiro, serão descritas as etapas de configuração de uma nova implantação e, em seguida, explicado como atribuir Protetores de TDE armazenados no Azure Key Vault a uma implantação existente que já tenha um vínculo de Geo-DR estabelecido.

**Etapas para uma nova implantação**:

- Crie os dois servidores do Banco de Dados SQL nas mesmas duas regiões que os cofres de chaves criados anteriormente.
- Selecione o painel TDE do servidor do Banco de Dados SQL e, para cada servidor do banco de dados SQL:  
  - Selecione o AKV na mesma região
  - Selecione a chave para usar como Protetor de TDE – cada servidor usará a cópia local do Protetor de TDE.
  - Fazer isso no portal criará uma [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) para o servidor do Banco de Dados SQL, que é usada para atribuir as permissões do servidor do Banco de Dados SQL para acessar o cofre de chaves – não exclua essa identidade. O acesso pode ser revogado, removendo as permissões no Azure Key Vault para o servidor do Banco de Dados SQL, que é usado para atribuir as permissões lógicas do servidor do Banco de Dados SQL para acessar o cofre de chaves.
- Crie o banco de dados primário.
- Siga as [diretrizes de replicação geográfica ativa](sql-database-geo-replication-overview.md) para concluir o cenário, e essa etapa criará o banco de dados secundário.

![Grupos de failover e Geo-DR](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> É importante garantir que os mesmos Protetores de TDE estejam presentes em ambos os cofres de chaves, antes de proceder ao estabelecimento do vínculo geográfico entre os bancos de dados.

**Etapas para um banco de dados SQL existente com a implantação de Geo-DR**:

Como os servidores do Banco de Dados SQL já existem e os bancos de dados primário e secundário já estão atribuídos, as etapas para configurar o Azure Key Vault devem ser executadas na seguinte ordem:

- Comece pelo servidor do Banco de Dados SQL que hospeda o banco de dados secundário:
  - Atribuir o cofre de chaves localizado na mesma região
  - Atribuir o Protetor de TDE
- Agora vá para o servidor do Banco de Dados SQL que hospeda o banco de dados primário:
  - Selecione o mesmo Protetor de TDE usado para o banco de dados secundário

![Grupos de failover e Geo-DR](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

> [!NOTE]
> Ao atribuir o cofre de chaves ao servidor, é importante iniciar com o servidor secundário.  Na segunda etapa, atribua o cofre de chaves ao servidor primário e atualize o Protetor de TDE, o vínculo de Geo-DR continuará funcionando porque nesse momento o Protetor de TDE usado pelo banco de dados replicado está disponível para ambos os servidores.

Antes de habilitar a TDE com chaves gerenciadas pelo cliente no Azure Key Vault para um cenário de Geo-DR do Banco de Dados SQL, é importante criar e manter dois Azure Key Vaults com conteúdo idêntico nas mesmas regiões que serão usadas para a replicação geográfica do Banco de Dados SQL.  "Conteúdos idênticos" significa, especificamente, que ambos os cofre de chaves devem conter cópias do(s) mesmo(s) Protetor(es) de TDE para que ambos os servidores tenham acesso aos Protetores de TDE usados por todos os bancos de dados.  Subsequentemente, é necessário manter os dois cofre de chaves em sincronização, significando que devem conter as mesmas cópias dos Protetores de TDE após a rotação de chaves, manter versões antigas de chaves usadas para arquivos de log ou backups, os Protetores de TDE devem manter as mesmas propriedades de chave e os cofres de chaves devem manter as mesmas permissões de acesso para SQL.  

Siga as etapas em [Visão geral da replicação geográfica ativa](sql-database-geo-replication-overview.md) para testar e disparar um failover, que deve ser feito regularmente para confirmar que as permissões de acesso para SQL para ambos os cofres de chaves foram mantidas.

### <a name="backup-and-restore"></a>Backup e restauração

Depois que um banco de dados for criptografado com TDE usando uma chave do Key Vault, todos os backups gerados também serão criptografados com o mesmo Protetor de TDE.

Para restaurar um backup criptografado com um Protetor de TDE do Key Vault, verifique se o material da chave ainda está no cofre original sob o nome da chave original. Quando o Protetor de TDE é alterado para um banco de dados, os backups antigos do banco de dados **não são** atualizados para usar o Protetor de TDE mais recente. Portanto, é recomendável manter todas as versões antigas do Protetor de TDE no Key Vault para que os backups do banco de dados possam ser restaurados.

Se uma chave que pode ser necessária para restaurar um backup não está mais em seu cofre de chaves original, a seguinte mensagem de erro será retornada: "O servidor `<Servername>` de destino não tem acesso a todos os URIs akv \<criados entre carimbo de \<data/hora #1 > e carimbo de data/hora #2 >. Tente realizar a operação novamente depois de restaurar todos os URIs do AKV. "

Para atenuar isso, execute o cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) para retornar a lista de chaves de Key Vault que foram adicionadas ao servidor (a menos que elas tenham sido excluídas por um usuário). Para garantir que todos os backups possam ser restaurados, verifique se o servidor de destino para o backup tem acesso a todas essas chaves.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Para saber mais sobre a recuperação de backup do Banco de Dados SQL, consulte [Recuperar um banco de dados SQL do Azure](sql-database-recovery-using-backups.md). Para saber mais sobre a recuperação de backup do SQL Data Warehouse, consulte [Recuperar um Data Warehouse SQL do Azure](../sql-data-warehouse/backup-and-restore.md).

Considerações adicionais para backup de arquivos de log: Os arquivos de log de backup permanecem criptografados com o Criptografador de TDE original, mesmo que o Protetor de TDE tenha sido girado e o banco de dados agora esteja usando um novo Protetor de TDE.  No momento da restauração, ambas as chaves serão necessárias para restaurar o banco de dados.  Se o arquivo de log estiver usando um Protetor de TDE armazenado no Azure Key Vault, essa chave será necessária na hora da restauração, mesmo que o banco de dados tenha sido alterado para usar TDE de serviço gerenciado.
