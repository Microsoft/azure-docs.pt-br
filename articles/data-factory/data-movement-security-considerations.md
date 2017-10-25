---
title: "Considerações sobre segurança no Azure Data Factory | Microsoft Docs"
description: "Descreve a infraestrutura básica de segurança usada pelos serviços de movimentação de dados no Azure Data Factory para proteger seus dados."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2017
ms.author: abnarain
ms.openlocfilehash: 0514710606b74db7c13f04e961347d215d2d283d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory – Considerações sobre segurança para movimentação de dados
Este artigo descreve a infraestrutura básica de segurança usada pelos serviços de movimentação de dados no Azure Data Factory para proteger seus dados. Os recursos de gerenciamento do Azure Data Factory se baseiam na infraestrutura de segurança do Azure e usam todas as medidas de segurança possíveis oferecidas pelo Azure.

Em uma solução de Data Factory, você cria um ou mais [pipelines](concepts-pipelines-activities.md)de dados. Um pipeline é um agrupamento lógico de atividades que juntas executam uma tarefa. Esses pipelines residem na região em que o data factory foi criado. 

Embora o Data Factory esteja disponível somente nas regiões **Leste dos EUA** e **Leste dos EUA 2** (versão prévia 2), o serviço de movimentação de dados está disponível [globalmente em várias regiões](concepts-integration-runtime.md#azure-ir). Se o serviço de movimentação de dados ainda não estiver implementado nessa região, o serviço Data Factory garante que os dados não saiam de uma área geográfica/região, a menos que você explicitamente instrua o serviço a usar uma região alternativa. 

O Azure Data Factory em si não armazena nenhum dado, exceto as credenciais do serviço vinculado de armazenamentos de dados em nuvem, que são criptografadas com o uso de certificados. Ele permite criar fluxos de trabalho controlados por dados para orquestrar a movimentação de dados entre [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) e o processamento de dados usando [serviços de computação](compute-linked-services.md) em outras regiões ou em um ambiente local. Ele também permite monitorar e gerenciar fluxos de trabalho usando SDKs e Azure Monitor.

A movimentação de dados com o uso do Azure Data Factory foi **certificada** na:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Se você estiver interessado na conformidade do Azure e como ele protege sua própria infraestrutura, visite a [Central de Confiabilidade da Microsoft](https://www.microsoft.com/TrustCenter/default.aspx). 

Neste artigo, examinamos as considerações sobre segurança nestes dois cenários de movimentação de dados: 

- **Cenário de nuvem** – neste cenário, a origem e o destino são publicamente acessíveis pela Internet. Eles incluem serviços de armazenamento em nuvem gerenciados como Armazenamento do Azure, SQL Data Warehouse do Azure, Banco de Dados SQL do Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS como Salesforce e protocolos da Web como FTP e OData. Encontre uma lista completa de fontes de dados com suporte [aqui](copy-activity-overview.md#supported-data-stores-and-formats).
- **Cenário híbrido** – neste cenário, a origem ou o destino está atrás de um firewall ou dentro de uma rede corporativa local, ou o armazenamento de dados está em uma rede privada/virtual (geralmente, a origem) e não é acessível publicamente. Os servidores de banco de dados hospedados em máquinas virtuais também se enquadram nesse cenário.

## <a name="cloud-scenarios"></a>Cenários de nuvem
###<a name="securing-data-store-credentials"></a>Protegendo as credenciais do armazenamento de dados
- Armazene credenciais criptografadas no armazenamento gerenciado do Azure Data Factory.

   O Azure Data Factory protege suas credenciais do armazenamento de dados **criptografando-as** usando **certificados gerenciados pela Microsoft**. Esses certificados são trocados a cada **dois anos** (que inclui a renovação do certificado e a migração de credenciais). Essas credenciais criptografadas são armazenadas com segurança em um **Armazenamento do Azure gerenciado pelos serviços de gerenciamento do Azure Data Factory**. Para obter mais informações sobre a segurança do Armazenamento do Azure, consulte [Visão geral de segurança do Armazenamento do Azure](../security/security-storage-overview.md).
- Armazenar credenciais no Azure Key Vault 

   Agora, você pode optar por armazenar credenciais do armazenamento de dados no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e então deixar o Azure Data Factory recuperá-las durante a execução de uma atividade. Para obter mais informações, consulte [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md).

   > [!NOTE]
   > Atualmente, apenas o [conector do Dynamics](connector-dynamics-crm-office-365.md) oferece suporte a esse recurso. 

### <a name="data-encryption-in-transit"></a>Criptografia de dados em trânsito
Caso o armazenamento de dados em nuvem dê suporte a HTTPS ou TLS, todas as transferências de dados entre serviços de movimentação de dados no Data Factory e um armazenamento de dados em nuvem ocorrerão por meio de um canal seguro HTTPS ou TLS.

> [!NOTE]
> Todas as conexões com o **Banco de Dados SQL do Azure** e o **SQL Data Warehouse do Azure** sempre exigem criptografia (SSL/TLS) quando os dados estão em trânsito, entrando e saindo do banco de dados. Ao criar um pipeline usando JSON, adicione a propriedade **criptografia** e defina-a como **verdadeira** na **cadeia de conexão**. Para **Armazenamento do Azure**, é possível usar **HTTPS** na cadeia de conexão.

### <a name="data-encryption-at-rest"></a>Criptografia de dados em repouso
Alguns armazenamentos de dados dão suporte à criptografia de dados em repouso. Sugerimos que você habilite o mecanismo de criptografia de dados nesses armazenamentos de dados. 

#### <a name="azure-sql-data-warehouse"></a>SQL Data Warehouse do Azure
A TDE (Transparent Data Encryption) do SQL Data Warehouse do Azure ajuda a proteger contra ameaças de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real dos dados em repouso. Esse comportamento é transparente para o cliente. Para obter mais informações, consulte [Proteger um banco de dados no SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
O Banco de Dados SQL do Azure também dá suporte à TDE (Transparent Data Encryption), que ajuda a proteger contra ameaças de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real dos dados, sem a necessidade de alterações no aplicativo. Esse comportamento é transparente para o cliente. Para obter mais informações, consulte [Transparent Data Encryption com o Banco de Dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Repositório Azure Data Lake
O Azure Data Lake Store também fornece criptografia para os dados armazenados na conta. Quando está habilitado, o Data Lake Store criptografa os dados automaticamente antes de persisti-los e descriptografá-los antes da recuperação, tornando-os transparentes para o cliente que acessa os dados. Para obter mais informações, consulte [Segurança no Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de Blobs do Azure e Armazenamento de Tabelas do Azure
O Armazenamento de Blobs do Azure e o Armazenamento de Tabelas do Azure dão suporte à SSE (Storage Service Encryption), que criptografa os dados automaticamente antes de persisti-los no armazenamento e descriptografa-os antes da recuperação. Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
O Amazon S3 dá suporte à criptografia de cliente e de servidor de dados em repouso. Para obter mais informações, consulte [Proteger dados usando a criptografia](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Atualmente, o Data Factory não dá suporte ao Amazon S3 em uma VPC (nuvem privada virtual).

#### <a name="amazon-redshift"></a>Amazon Redshift
O Amazon Redshift dá suporte à criptografia de cluster de dados em repouso. Para obter mais informações, consulte [Criptografia de banco de dados do Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Atualmente, o Data Factory não dá suporte ao Amazon Redshift em uma VPC. 

#### <a name="salesforce"></a>Salesforce
O Salesforce dá suporte à Shield Platform Encryption, que permite a criptografia de todos os arquivos, anexos e campos personalizados. Para obter mais informações, consulte [Noções básicas sobre o fluxo de autenticação OAuth do Servidor Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-self-hosted-integration-runtime"></a>Cenários híbridos (usando o tempo de execução de integração auto-hospedado)
Os cenários híbridos exigem que o tempo de execução de integração auto-hospedado seja instalado em uma rede local ou dentro de uma rede virtual (Azure) ou uma nuvem privada virtual (Amazon). O tempo de execução de integração auto-hospedado deve ser capaz de acessar os armazenamentos de dados locais. Para obter mais informações sobre o tempo de execução de integração auto-hospedado, consulte [tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md). 

![Canais do tempo de execução de integração auto-hospedado](media/data-movement-security-considerations/data-management-gateway-channels.png)

O **canal de comando** permite a comunicação entre os serviços de movimentação de dados no Data Factory e no tempo de execução de integração auto-hospedado. A comunicação contém informações relacionadas à atividade. O canal de dados é usado para transferir dados entre armazenamentos de dados locais e armazenamentos de dados em nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais do armazenamento de dados local
As credenciais dos armazenamentos de dados locais são sempre criptografadas e armazenadas. Elas podem ser armazenadas localmente na máquina do tempo de execução de integração auto-hospedado ou no armazenamento gerenciado do Azure Data Factory (assim como as credenciais de armazenamento em nuvem). 

1. Você pode optar por **armazenar credenciais localmente**. Se você quiser criptografar e armazenar credenciais localmente no tempo de execução de integração auto-hospedado, siga as etapas em [criptografar credenciais no tempo de execução de integração auto-hospedado](encrypt-credentials-self-hosted-integration-runtime.md). Todos os conectores oferecem suporte a essa opção. O tempo de execução de integração auto-hospedado usa Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) para criptografar dados confidenciais e informações de credenciais. 

   Use o cmdlet **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** para criptografar as credenciais do Serviço vinculado/criptografar detalhes confidenciais no Serviço vinculado. Você pode então usar o JSON retornado (com o elemento **EncryptedCredential** em **connectionString**) para criar um serviço vinculado pelo cmdlet **Set-AzureRmDataFactoryV2LinkedSevrice**.  

2. Se você não usar o cmdlet **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** como descrito na etapa anterior e, em vez disso, usar diretamente o cmdlet **Set-AzureRmDataFactoryV2LinkedSevrice** com as cadeias de conexão/credenciais embutidas no JSON, o Serviço vinculado será **criptografado e armazenado no armazenamento gerenciado do Azure Data Factory**. As informações confidenciais ainda são criptografadas por certificado e esses certificados são gerenciados pela Microsoft.



#### <a name="ports-used-during-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portas usadas durante a criptografia do serviço vinculado no tempo de execução de integração auto-hospedado
Por padrão, o PowerShell usa a porta **8050** na máquina com tempo de execução de integração auto-hospedado para oferecer comunicação segura. Se necessário, essa porta pode ser alterada.  

![Porta HTTPS do gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Criptografia em trânsito
Todas as transferências de dados são feitas por meio do canal seguro **HTTPS** e **TLS via TCP** para impedir ataques man-in-the-middle durante a comunicação com os serviços do Azure.

Você também pode usar a [VPN IPsec](../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou o [ExpressRoute](../expressroute/expressroute-introduction.md) para fornecer proteção adicional ao canal de comunicação entre a rede local e o Azure.

Uma rede virtual é uma representação lógica de sua rede na nuvem. Você pode conectar uma rede local à VNet (rede virtual) do Azure ao configurar a VPN IPsec (site a site) ou o ExpressRoute (Emparelhamento Privado)     

A tabela a seguir resume as recomendações de configuração de rede e tempo de execução de integração auto-hospedado de acordo com diferentes combinações dos locais de origem e de destino para a movimentação de dados híbridos.

| Fonte      | Destino                              | Configuração de rede                    | Configuração do tempo de execução de integração                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Configuração local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | VPN IPsec (ponto a site ou site a site) | O tempo de execução de integração auto-hospedado pode ser instalado localmente ou em uma VM (máquina virtual) do Azure na VNet |
| Configuração local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | ExpressRoute (Emparelhamento Privado)           | O tempo de execução de integração auto-hospedado pode ser instalado localmente ou em uma VM do Azure na VNet |
| Configuração local | Serviços baseados no Azure que têm um ponto de extremidade público | ExpressRoute (Emparelhamento Público)            | O tempo de execução de integração auto-hospedado deve ser instalado no local |

As imagens a seguir mostram o uso do tempo de execução de integração auto-hospedado para mover dados entre um banco de dados local e os serviços do Azure usando o ExpressRoute e a VPN IPsec (com a Rede Virtual):

**ExpressRoute:**

![Usar o ExpressRoute com o gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**VPN IPsec:**

![VPN IPsec com gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-self-hosted-integration-runtime"></a>Configurações de firewall e lista de permissões do endereço IP (tempo de execução de integração auto-hospedado)

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para a rede local/privada  
Em uma empresa, um **firewall corporativo** é executado no roteador central da organização. E o **firewall do Windows** é executado como um daemon no computador local em que o tempo de execução de integração auto-hospedado está instalado. 

A tabela a seguir fornece os requisitos de **porta de saída** e de domínio do **firewall corporativo**.

| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443, 80        | Necessárias para que o tempo de execução de integração auto-hospedado se conecte aos serviços de movimentação de dados no Data Factory |
| `*.core.windows.net`          | 443            | Usada pelo tempo de execução de integração auto-hospedado para se conectar à Conta de Armazenamento do Azure ao usar o recurso [cópia em etapas](copy-activity-performance.md#staged-copy). |
| `*.frontend.clouddatahub.net` | 443            | Necessárias para que o tempo de execução de integração auto-hospedado se conecte ao serviço do Azure Data Factory. |
| `*.database.windows.net`      | 1433           | (OPCIONAL) Necessária quando o destino é o Banco de Dados SQL do Azure/SQL Data Warehouse do Azure. Use o recurso de cópia em etapas para copiar dados para o Banco de Dados SQL do Azure/SQL Data Warehouse do Azure sem abrir a porta 1433. |
| `*.azuredatalakestore.net`    | 443            | (OPCIONAL) Necessária quando o destino é o Azure Data Lake Store |

> [!NOTE] 
> Talvez você precise gerenciar as portas e os domínios na lista de permissões no nível do firewall corporativo, conforme exigido pelas respectivas fontes de dados. Esta tabela usa apenas o Banco de Dados SQL do Azure, o SQL Data Warehouse do Azure e o Azure Data Lake Store como exemplos.   

A tabela a seguir fornece os requisitos de **porta de entrada** do **firewall do Windows**.

| Portas de entrada | Descrição                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Exigido pelo cmdlet de criptografia do PowerShell conforme descrito em [criptografando credenciais em tempo de execução de integração auto-hospedado](encrypt-credentials-self-hosted-integration-runtime.md)/aplicativo gerenciador de credenciais para definir credenciais com segurança para armazenamentos de dados locais no tempo de execução de integração auto-hospedado. |

![Requisitos de porta do gateway](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurationswhitelisting-in-data-store"></a>Configurações de IP/lista de permissões no armazenamento de dados
Alguns armazenamentos de dados na nuvem também exigem a lista de permissões do endereço IP do computador que os acessa. Verifique se o endereço IP do computador do tempo de execução de integração auto-hospedado está na lista de permissões/configurado no firewall corretamente.

Os armazenamentos de dados na nuvem exige a lista de permissões do endereço IP do computador do tempo de execução de integração auto-hospedado. Por padrão, alguns desses armazenamentos de dados poderão não exigir a lista de permissões do endereço IP. 

- [Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md) 
- [SQL Data Warehouse do Azure](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal)
- [Repositório Azure Data Lake](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Pergunta:** O tempo de execução de integração auto-hospedado pode ser compartilhado entre diferentes data factories?
**Resposta:** Ainda não damos suporte a esse recurso. No entanto, estamos trabalhando de forma ativa para implementá-lo.

**Pergunta:** Quais são os requisitos de porta para o tempo de execução de integração auto-hospedado funcionar?
**Resposta:** O tempo de execução de integração auto-hospedado faz conexões com base em HTTP com a internet aberta. As **portas de saída 443 e 80** devem estar abertas para que o tempo de execução de integração auto-hospedado estabeleça essa conexão. Abra a **Porta de Entrada 8050** somente no nível do computador (não no nível de firewall corporativo) para o aplicativo Gerenciador de Credenciais. Se o Banco de Dados SQL do Azure ou o SQL Data Warehouse do Azure for usado como a origem ou o destino, você precisará abrir a porta **1433** também. Para obter mais informações, consulte a seção [Configurações de firewall e endereços IP na lista de permissões](#firewall-configurations-and-whitelisting-ip-address-of gateway). 


## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre o desempenho da atividade de cópia, consulte [Guia desempenho e ajuste da atividade de cópia](copy-activity-performance.md).

 
