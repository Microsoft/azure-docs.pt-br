---
title: Considerações de segurança
description: Descreve a infraestrutura básica de segurança usada pelos serviços de movimentação de dados no Azure Data Factory para ajudar a proteger seus dados.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 1a99fbd3d3163808a364e8b26e770563a901dc18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371321"
---
# <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Considerações sobre segurança para movimentação de dados no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
>
> * [Versão 1](v1/data-factory-data-movement-security-considerations.md)
> * [Versão atual](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve a infraestrutura básica de segurança usada pelos serviços de movimentação de dados no Azure Data Factory para ajudar a proteger seus dados. Os recursos de gerenciamento do Data Factory se baseiam na infraestrutura de segurança do Azure e usam todas as medidas de segurança possíveis oferecidas pelo Azure.

Em uma solução de Data Factory, você cria um ou mais [pipelines](concepts-pipelines-activities.md)de dados. Um pipeline é um agrupamento lógico de atividades que juntas executam uma tarefa. Esses pipelines residem na região em que o data factory foi criado.

Mesmo que a fábrica de dados está disponível apenas em algumas regiões, o serviço de movimentação de dados é [disponível globalmente](concepts-integration-runtime.md#integration-runtime-location) para garantir a conformidade de dados, eficiência e rede reduzida os custos de saída.

O Azure Data Factory, incluindo o Azure Integration Runtime e o Runtime de Integração Auto-Hospedada, não armazena dados temporários, dados em cache ou logs, exceto as credenciais de serviço vinculado para armazenamentos de dados de nuvem, que são criptografados usando certificados. Com o Data Factory, você cria fluxos de trabalho controlados por dados para orquestrar a movimentação de dados entre os [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) e o processamento de dados usando [serviços de computação](compute-linked-services.md) em outras regiões ou em um ambiente local. Você também pode monitorar e gerenciar fluxos de trabalho usando SDKs e Azure Monitor.

O Data Factory foi certificado para:

| **[Certificação CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](/compliance/regulatory/offering-iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](/compliance/regulatory/offering-hipaa-hitech)** |
| **[HITRUST](/compliance/regulatory/offering-hitrust)** |

Se você estiver interessado na conformidade do Azure e como ele protege sua própria infraestrutura, visite a [Central de Confiabilidade da Microsoft](https://microsoft.com/trustcenter/default.aspx). Para obter a lista mais recente de todas as ofertas de conformidade do Azure, confira: https://aka.ms/AzureCompliance.

Neste artigo, examinamos as considerações sobre segurança nestes dois cenários de movimentação de dados:

- **Cenário de nuvem**: neste cenário, sua origem e destino são publicamente acessíveis por meio da Internet. Isso inclui serviços de armazenamento em nuvem gerenciados como o Armazenamento do Azure, o Azure Synapse Analytics, o Banco de Dados SQL do Azure, o Azure Data Lake Storage, o Amazon S3, o Amazon Redshift, os serviços SaaS como Salesforce e os protocolos da Web como FTP e OData. Localizar uma lista completa de fontes de dados com suporte em [Armazenamentos de dados e formatos com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
- **Cenário híbrido**: nesse cenário, sua origem ou destino está atrás de um firewall ou dentro de uma rede corporativa local. Ou, o armazenamento de dados está em uma particular ou rede virtual (geralmente a origem) e não está acessível publicamente. Os servidores de banco de dados hospedados em máquinas virtuais também se enquadram nesse cenário.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cenários de nuvem

### <a name="securing-data-store-credentials"></a>Protegendo as credenciais do armazenamento de dados

- **Armazene credenciais criptografadas em um armazenamento gerenciado do Azure Data Factory**. O Data Factory ajuda a proteger suas credenciais de armazenamento de dados criptografando-as com certificados gerenciados pela Microsoft. Esses certificados são trocados a cada dois anos (que inclui a renovação do certificado e a migração de credenciais). Para obter mais informações sobre a segurança do Armazenamento do Azure, consulte [Visão geral de segurança do Armazenamento do Azure](../storage/blobs/security-recommendations.md).
- **Armazenar credenciais no Azure Key Vault**. Você também pode armazenar credenciais do repositório de dados em [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). O Data Factory recupera as credenciais durante a execução de uma atividade. Para obter mais informações, consulte [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Criptografia de dados em trânsito
Caso o armazenamento de dados em nuvem dê suporte a HTTPS ou TLS, todas as transferências de dados entre serviços de movimentação de dados no Data Factory e um armazenamento de dados em nuvem ocorrerão por meio de um canal seguro HTTPS ou TLS.

> [!NOTE]
> Todas as conexões com o Banco de Dados SQL do Azure e o Azure Synapse Analytics exigem criptografia (SSL/TLS) quando os dados estão em trânsito de e para o banco de dados. Ao criar um pipeline usando JSON, adicione a propriedade criptografia e defina-a como **verdadeira** na cadeia de conexão. Para Armazenamento do Azure, é possível usar **HTTPS** na cadeia de conexão.

> [!NOTE]
> Para habilitar a criptografia em trânsito, simultaneamente movendo dados do Oracle, siga uma das opções abaixo:
>
> 1. No servidor Oracle, acesse OAS (Segurança Avançada da Oracle) e defina as configurações de criptografia, que dão suporte a 3DES (criptografia DES tripla) e AES (criptografia AES). Consulte os detalhes [aqui](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). O ADF negocia automaticamente o método de criptografia para usar aquele que você configura no OAS ao estabelecer a conexão com o Oracle.
> 2. Em ADF, você pode adicionar EncryptionMethod=1 na cadeia de conexão (no serviço vinculado). Essa opção usará SSL/TLS como o método de criptografia. Para usar essa, é necessário desabilitar as configurações de criptografia não SSL no OAS no lado do servidor Oracle para evitar conflitos de criptografia.

> [!NOTE]
> A versão do TLS usada é a 1.2.

### <a name="data-encryption-at-rest"></a>Criptografia de dados em repouso

Alguns armazenamentos de dados dão suporte à criptografia de dados em repouso. Recomendamos que você habilite o mecanismo de criptografia de dados nesses armazenamentos de dados. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

A TDE (Transparent Data Encryption) no Azure Synapse Analytics ajuda a proteger contra a ameaça de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real dos seus dados inativos. Esse comportamento é transparente para o cliente. Para obter mais informações, confira [Proteger um banco de dados no Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure também dá suporte à TDE (Transparent Data Encryption), que ajuda a proteger contra ameaças de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real dos dados, sem a necessidade de alterações no aplicativo. Esse comportamento é transparente para o cliente. Para obter mais informações, consulte [Transparent Data Encryption para o Banco de Dados SQL e SQL Warehouse](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Repositório Azure Data Lake

O Azure Data Lake Store também fornece criptografia para os dados armazenados na conta. Quando está habilitado, o Data Lake Store criptografa os dados automaticamente antes de persisti-los e descriptografá-los antes da recuperação, tornando-os transparentes para o cliente que acessa os dados. Para obter mais informações, consulte [Segurança no Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de Blobs do Azure e armazenamento de Tabelas do Azure

O armazenamento de Blobs do Azure e o armazenamento de Tabelas do Azure dão suporte à SSE (Storage Service Encryption), que criptografa os dados automaticamente antes de persisti-los no armazenamento e descriptografa-os antes da recuperação. Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3

O Amazon S3 dá suporte à criptografia de cliente e de servidor de dados em repouso. Para obter mais informações, consulte [Proteger dados usando a criptografia](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift

O Amazon Redshift dá suporte à criptografia de cluster de dados em repouso. Para obter mais informações, consulte [Criptografia de banco de dados do Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce

O Salesforce dá suporte à Shield Platform Encryption, que permite a criptografia de todos os arquivos, anexos e campos personalizados. Para obter mais informações, consulte [Noções básicas sobre o fluxo de autenticação OAuth do Servidor Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Cenários híbridos

Os cenários híbridos exigem que o runtime de integração auto-hospedada seja instalado em uma rede local, dentro de uma rede virtual (Azure) ou dentro de uma nuvem privada virtual (Amazon). O runtime de integração auto-hospedada deve ser capaz de acessar os armazenamentos de dados locais. Para obter mais informações sobre o runtime de integração auto-hospedada, consulte [Como criar e configurar o runtime de integração auto-hospedada](./create-self-hosted-integration-runtime.md). 

![Canais do runtime de integração auto-hospedada](media/data-movement-security-considerations/data-management-gateway-channels.png)

O canal de comando permite a comunicação entre os serviços de movimentação de dados no Data Factory e no runtime de integração auto-hospedada. A comunicação contém informações relacionadas à atividade. O canal de dados é usado para transferir dados entre armazenamentos de dados locais e armazenamentos de dados em nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais do armazenamento de dados local

As credenciais podem ser armazenadas no data factory ou ser [referenciadas pelo data factory](store-credentials-in-key-vault.md) durante o runtime do Azure Key Vault. Se estiver armazenando credenciais no data factory, elas sempre serão armazenadas criptografadas no runtime de integração auto-hospedada. 

   - **Armazenar credenciais localmente**. Se você usar diretamente o cmdlet **Set-AzDataFactoryV2LinkedService** com as cadeias de conexão e credenciais embutidas no JSON, o serviço vinculado será criptografado e armazenado no runtime de integração auto-hospedada.  Nesse caso, o fluxo de credenciais por meio do serviço de back-end do Azure, que é extremamente seguro, para o computador de integração auto-hospedada no qual ele é finalmente criptografado e armazenado. O runtime de integração auto-hospedada usa Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) para criptografar dados confidenciais e informações de credenciais.

   - **Armazenar credenciais no Azure Key Vault**. Você também pode armazenar credenciais do repositório de dados em [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). O Data Factory recupera as credenciais durante a execução de uma atividade. Para obter mais informações, consulte [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md).

   - **Armazene credenciais localmente sem transmitir as credenciais por meio do back-end do Azure para o runtime de integração auto-hospedada**. Se você quiser criptografar e armazenar credenciais localmente no runtime de integração auto-hospedada sem precisar transmitir as credenciais por meio do back-end do data factory, siga as etapas em [Criptografar credenciais para armazenamentos de dados locais no Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Todos os conectores oferecem suporte a essa opção. O runtime de integração auto-hospedada usa Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) para criptografar dados confidenciais e informações de credenciais. 

   - Use o cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** para criptografar as credenciais do serviço vinculado e detalhes confidenciais no serviço vinculado. Você pode então usar o JSON retornado (com o elemento **EncryptedCredential** na cadeia de conexão) para criar um serviço vinculado usando o cmdlet **Set-AzDataFactoryV2LinkedService**.  

#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portas usadas para criptografar o serviço vinculado no runtime de integração auto-hospedada

Por padrão, o PowerShell usa a porta 8060 no computador com o runtime de integração auto-hospedada para oferecer uma comunicação segura. Se necessário, essa porta pode ser alterada.  

![Porta HTTPS do gateway](media/data-movement-security-considerations/https-port-for-gateway.png)


### <a name="encryption-in-transit"></a>Criptografia em trânsito

Todas as transferências de dados são feitas por meio do canal seguro HTTPS e TLS via TCP para impedir ataques man-in-the-middle durante a comunicação com os serviços do Azure.

Você também pode usar a [VPN IPsec](../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou o [Azure ExpressRoute](../expressroute/expressroute-introduction.md) para fornecer proteção adicional ao canal de comunicação entre a rede local e o Azure.

A Rede Virtual do Azure é uma representação lógica de sua rede na nuvem. Você pode conectar uma rede local à rede virtual ao configurar a VPN IPsec (site a site) ou o ExpressRoute (emparelhamento privado).

A tabela a seguir resume as recomendações de configuração de rede e runtime de integração auto-hospedada de acordo com diferentes combinações dos locais de origem e de destino para a movimentação de dados híbridos.

| Fonte      | Destino                              | Configuração de rede                    | Configuração do runtime de integração                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | VPN IPsec (ponto a site ou site a site) | O runtime de integração auto-hospedada deve ser instalado localmente ou em uma máquina virtual do Azure na rede virtual.  |
| Local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | ExpressRoute (emparelhamento privado)           | O runtime de integração auto-hospedada deve ser instalado localmente ou em uma máquina virtual do Azure na rede virtual.  |
| Local | Serviços baseados no Azure que têm um ponto de extremidade público | ExpressRoute (emparelhamento da Microsoft)            | O runtime de integração auto-hospedada pode ser instalado localmente ou em uma máquina virtual do Azure. |

As imagens a seguir mostram o uso do runtime de integração auto-hospedada para mover dados entre um banco de dados local e os serviços do Azure usando o ExpressRoute e a VPN IPsec (com a Rede Virtual do Azure):

#### <a name="express-route"></a>ExpressRoute

![Usar o ExpressRoute com o gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

#### <a name="ipsec-vpn"></a>VPN do IPSec

![VPN IPsec com gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a>Configurações de firewall e de lista de permitidos para os endereços IP

> [!NOTE]
> Talvez você precise gerenciar as portas ou configurar a lista de permitidos para os domínios no nível do firewall corporativo, conforme exigido pelas respectivas fontes de dados. Esta tabela usa apenas o Banco de Dados SQL do Azure, o Azure Synapse Analytics e o Azure Data Lake Storage como exemplos.

> [!NOTE]
> Para obter detalhes sobre estratégias de acesso a dados por meio de Azure Data Factory, confira [este artigo](./data-access-strategies.md#data-access-strategies-through-azure-data-factory).

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para a rede local/privada

Em uma empresa, um firewall corporativo é executado no roteador central da organização. O Firewall do Windows é executado como um daemon no computador local em que o runtime de integração auto-hospedada está instalado. 

A tabela a seguir fornece os requisitos de porta de saída e de domínio dos firewalls corporativos:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE]
> Talvez você precise gerenciar as portas ou configurar a lista de permitidos para os domínios no nível do firewall corporativo, conforme exigido pelas respectivas fontes de dados. Esta tabela usa apenas o Banco de Dados SQL do Azure, o Azure Synapse Analytics e o Azure Data Lake Storage como exemplos.   

A tabela a seguir fornece os requisitos de porta de entrada do Firewall do Windows:

| Portas de entrada | Descrição                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Exigido pelo cmdlet de criptografia do PowerShell conforme descrito em [Criptografar credenciais para armazenamentos de dados locais no Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md), e pelo aplicativo gerenciador de credenciais para definir credenciais com segurança para armazenamentos de dados locais no runtime de integração auto-hospedada. |

![Requisitos de porta do gateway](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Configurações de IP e de lista de permitidos em armazenamentos de dados

Alguns armazenamentos de dados na nuvem também exigem que você permita o endereço IP do computador que os acessa. Verifique se o endereço IP do computador do runtime de integração auto-hospedada está permitido ou configurado no firewall corretamente.

Os armazenamentos de dados na nuvem a seguir exigem que você permita o endereço IP do computador do runtime de integração auto-hospedada. Por padrão, alguns desses armazenamentos de dados poderão não exigir a lista de permitidos.

* [Banco de Dados SQL do Azure](../azure-sql/database/firewall-configure.md)
* [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
* [Repositório Azure Data Lake](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
* [Azure Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
* [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**O runtime de integração auto-hospedada pode ser compartilhado entre diferentes data factories?**

Sim. Mais detalhes [aqui](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Quais são os requisitos de porta para o runtime de integração auto-hospedada funcionar?**

O runtime de integração auto-hospedada faz conexões com base em HTTP para acessar a internet. As portas de saída 443 devem ser abertas para o runtime de integração auto-hospedada para fazer essa conexão. Abra a porta de entrada 8060 somente no nível do computador (não no nível de firewall corporativo) para o aplicativo gerenciador de credenciais. Se o Banco de Dados SQL do Azure ou o Azure Synapse Analytics for usado como a origem ou o destino, você precisará abrir a porta 1433 também. Para obter mais informações, confira a seção [Configurações de firewall e de lista de permitidos de endereços IP](#firewall-configurations-and-allow-list-setting-up-for-ip-addresses).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre o desempenho da atividade de cópia do Azure Data Factory, consulte [Guia desempenho e ajuste da atividade de cópia](copy-activity-performance.md).
