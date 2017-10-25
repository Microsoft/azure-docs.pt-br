title: Dados pessoais da Proteção do Azure em repouso com criptografia | Microsoft Docs description: Esse artigo é parte de uma série que o ajuda a usar o Azure para proteger serviços de dados pessoais: security documentationcenter: na author: gerente do Barclayn: MBaldwin editor: TomSh

ms.assetid: ms.service: security ms.devlang: na ms.topic: article ms.tgt_pltfrm: na ms.workload: na ms.date: 08/22/2017 ms.author: barclayn ms.custom: 

---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Tecnologias de criptografia do Azure: proteger dados pessoais em repouso com criptografia

Este artigo ajuda você a entender e usar as tecnologias de criptografia do Azure para proteger dados em repouso.

A criptografia de dados em repouso é essencial como uma melhor prática para proteger dados confidenciais ou pessoais e para atender aos requisitos de conformidade e privacidade de dados.
A criptografia em repouso é projetada para impedir que o invasor acesse os dados não criptografados, assegurando que os dados sejam criptografados quando em disco.

## <a name="scenario"></a>Cenário 

Uma empresa de cruzeiro de grande porte, com sede nos Estados Unidos, está expandindo suas operações para oferecer roteiros nos mares Mediterrâneo e Báltico, bem como nas Ilhas Britânicas. Para dar suporte a esses esforços, ela adquiriu várias linhas de cruzeiro menores localizadas na Itália, na Alemanha, na Dinamarca e no Reino Unido.

A empresa usa o Microsoft Azure para armazenar dados corporativos na nuvem. Isso pode incluir informações de clientes e/ou de funcionários, como:

- endereços
- números de telefone
- números de identificação fiscal
- informações de cartão de crédito

A empresa deve proteger a privacidade dos dados de clientes e funcionários e, ao mesmo tempo, tornar os dados acessíveis aos departamentos que precisam deles. (como departamentos de folha de pagamento e reservas)

A linha de cruzeiro também mantém um banco de dados grande de membros do programa de recompensa e fidelidade que inclui informações pessoais para acompanhamento das relações com os clientes atuais e anteriores.

### <a name="problem-statement"></a>Problema declarado

A empresa deve proteger a privacidade dos dados pessoais de clientes e funcionários e, ao mesmo tempo, tornar os dados acessíveis aos departamentos que precisam deles (por exemplo, departamentos de folha de pagamento e reservas). Esses dados pessoais são armazenados fora do data center corporativo controlado e não estão sob o controle físico da empresa.

### <a name="company-goal"></a>Meta da empresa

Como parte de uma estratégia de segurança de proteção avançada de várias camadas, uma das metas da empresa é garantir que todas as fontes de dados que contêm dados pessoais sejam criptografadas, incluindo aquelas que residem no armazenamento em nuvem. Caso pessoas não autorizadas obtenham acesso aos dados pessoais, eles devem estar em um formato que os tornará ilegíveis. A aplicação da criptografia deve ser fácil ou transparente – para os usuários e administradores.

## <a name="solutions"></a>Soluções

Os serviços do Azure fornecem várias ferramentas e tecnologias para ajudá-lo a proteger dados pessoais em repouso com sua criptografia.

### <a name="azure-key-vault"></a>Cofre da Chave do Azure

O [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) fornece armazenamento seguro para as chaves usadas para criptografar dados em repouso em serviços do Azure e é a solução recomendada de armazenamento e gerenciamento de chaves. O gerenciamento de chaves de criptografia é essencial para proteger os dados armazenados.

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>Como fazer para usar o Azure Key Vault para proteger chaves que criptografam dados pessoais?

Para usar o Azure Key Vault, você precisa de uma assinatura para uma conta do Azure. Você também precisa do Azure PowerShell instalado. As etapas incluem o uso de cmdlets do PowerShell para fazer o seguinte:

1. Conectar-se às suas assinaturas

2. Criar um cofre de chave

3. Adicionar uma chave ou segredo ao cofre da chave

4. Registrar aplicativos que usarão o cofre de chaves com o Azure Active Directory

5. Autorizar os aplicativos a usar a chave ou o segredo

Para criar um cofre de chaves, use o New-AzureRmKeyVault PowerShell CmDlt. Você atribuirá um nome de cofre, um nome do grupo de recursos e a localização geográfica. Você usará o nome do cofre ao gerenciar chaves por meio de outros Cmdlets. Os aplicativos que usam o cofre por meio da API REST usarão o URI do cofre.

O Azure Key Vault pode fornecer uma chave protegida por software para você ou você pode importar uma chave existente em um arquivo .PFX. Você também pode armazenar segredos (senhas) no cofre.

Você também pode gerar uma chave no HSM local e transferi-la para os HSMs no serviço Key Vault, sem que a chave deixe os limites do HSM.

Para obter instruções detalhadas sobre como usar o Azure Key Vault, siga as etapas em [Introdução ao Azure Key Vault.](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-get-started)

Para obter uma lista de Cmdlets do PowerShell usados com o Azure Key Vault, consulte [AzureRM.KeyVault](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

### <a name="azure-disk-encryption-for-windows"></a>Azure Disk Encryption para o Windows

O [Azure Disk Encryption para VMs IaaS Windows e Linux](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) protege dados pessoais em repouso em máquinas virtuais do Azure e é integrado ao Azure Key Vault. O Azure Disk Encryption usa o [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) no Windows e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para criptografar os discos do sistema operacional e de dados. Há suporte para o Azure Disk Encryption no Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 e nos clientes Windows 8 e Windows 10.

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>Como fazer para usar o Azure Disk Encryption para proteger dados pessoais?

Para usar o Azure Disk Encryption, você precisa de uma assinatura para uma conta do Azure. Para habilitar o Azure Disk Encryption para VMs Windows e Linux, faça o seguinte:

1. Use o modelo do Resource Manager do Azure Disk Encryption, o PowerShell ou a CLI (interface de linha de comando) para habilitar a criptografia de disco e especificar a configuração de criptografia. 

2. Conceda acesso à plataforma Azure para ler o material de criptografia por meio do cofre de chaves.

3. Forneça uma identidade de aplicativo do AAD (Azure Active Directory) para gravar o material de chave de criptografia no cofre de chaves.

O Azure atualizará a configuração da VM e do cofre de chaves e configurará a VM criptografada.

Ao configurar o cofre de chaves para dar suporte ao Azure Disk Encryption, adicione uma KEK (chave de criptografia de chave) para aumentar a segurança e dar suporte ao backup de máquinas virtuais criptografadas.

![](media/protect-personal-data-at-rest/create-key.png)

Instruções detalhadas para cenários de implantação específicos e as experiências do usuário são incluídas em [Azure Disk Encryption para VMs IaaS Windows e Linux.](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)

### <a name="azure-storage-service-encryption"></a>Criptografia do Serviço de Armazenamento do Azure

A [SSE (Criptografia do Serviço de Armazenamento) do Azure para Dados em Repouso](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption) ajuda a proteger seus dados para atender aos compromissos de conformidade e segurança de sua organização. O Armazenamento do Azure criptografa automaticamente os dados usando a criptografia AES de 256 bits antes de persistir no armazenamento e os descriptografa antes da recuperação. Esse serviço está disponível para Blobs e Arquivos do Azure.

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>Como fazer para usar a Criptografia do Serviço de Armazenamento para proteger dados pessoais?

Para habilitar a Criptografia do Serviço de Armazenamento, faça o seguinte:

1. Faça logon no portal do Azure.

2. Selecione uma conta de armazenamento.

3. Em Configurações, na seção Serviço Blob, selecione Criptografia.

4. Na seção Serviço Arquivo, selecione Criptografia.

Depois de clicar na configuração da Criptografia, habilite ou desabilite a Criptografia do Serviço de Armazenamento.

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

Novos dados serão criptografados. Os dados em arquivos existentes nesta conta de armazenamento permanecerão não criptografados.

Depois de habilitar a criptografia, copie os dados para a conta de armazenamento usando um dos seguintes métodos:

1. Copie blobs ou arquivos com o [utilitário de linha de comando AzCopy](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy).

2. [Montar um compartilhamento de arquivos usando o SMB](https://docs.microsoft.com/en-us/azure/storage/storage-file-how-to-use-files-windows), de modo que você possa usar um utilitário, como o Robocopy, para copiar arquivos.

3. Copie os dados de blob ou arquivo bidirecionalmente no armazenamento de blobs ou entre contas de armazenamento usando as [Bibliotecas de Clientes de Armazenamento como o .NET](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-how-to-use-blobs).

4.  Use o [Gerenciador de Armazenamento](https://docs.microsoft.com/en-us/azure/storage/storage-explorers) para carregar blobs em sua conta de armazenamento com a criptografia habilitada.

### <a name="transparent-data-encryption"></a>Transparent Data Encryption

A TDE (Transparent Data Encryption) é um recurso do SQL Azure pelo qual você pode criptografar os dados nos níveis do servidor e do banco de dados. A TDE agora é habilitada por padrão em todos os bancos de dados recém-criados. A TDE realiza a criptografia e a descriptografia de E/S em tempo real dos arquivos de log e de dados.

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>Como fazer para usar a TDE para proteger dados pessoais?

Configure a TDE por meio do portal do Azure, usando a API REST ou o PowerShell. Para habilitar a TDE em um banco de dados existente usando o Portal do Azure, faça o seguinte:

1. Visite o portal do Azure em <https://portal.azure.com> e entre com sua conta de Administrador ou Colaborador do Azure.

2. Na faixa à esquerda, clique em PROCURAR e, em seguida, clique em bancos de dados SQL.

3. Com bancos de dados SQL selecionados no painel esquerdo, clique no banco de dados de usuário.

4. Na folha do banco de dados, clique em Todas as configurações.

5. Na folha Configurações, clique na parte Transparent Data Encryption para abrir a folha Transparent Data Encryption.

6. Na folha Criptografia de dados, mova o botão Criptografia de dados para Ativado e, em seguida, clique em Salvar (na parte superior da página) para aplicar a configuração. O status da Criptografia aproximará o progresso da Transparent Data Encryption.

![Habilitando a criptografia de dados](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

Encontre instruções sobre como habilitar a TDE e informações sobre como descriptografar bancos de dados protegidos por TDE e muito mais no artigo [Transparent Data Encryption com o Banco de Dados SQL do Azure.](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)

## <a name="summary"></a>Resumo

A empresa pode atingir sua meta de criptografar os dados pessoais armazenados na nuvem do Azure. Ela pode fazer isso usando o Azure Disk Encryption para proteger volumes inteiros. Isso pode incluir os arquivos do sistema operacional e os arquivos de dados que contêm informações de identificação pessoal e outros dados confidenciais. A criptografia do Serviço de Armazenamento do Azure pode ser usada para proteger dados pessoais armazenados em blobs e arquivos. Para dados armazenados em bancos de dados SQL do Azure, a Transparent Data Encryption oferece proteção contra a exposição não autorizada de informações pessoais.

Para proteger as chaves usadas para criptografar dados no Azure, a empresa pode usar o Azure Key Vault. Isso simplifica o processo de gerenciamento de chaves e permite que a empresa mantenha o controle das chaves que acessam e criptografam dados pessoais.

## <a name="next-steps"></a>Próximas etapas

- [Guia de solução de problemas do Azure Disk Encryption](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-tsg)

- [Criptografar uma Máquina Virtual do Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Criptografia de dados no Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-encryption)

- [Criptografia de banco de dados em repouso do Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/database-encryption-at-rest)
