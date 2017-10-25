---
title: "Considerações sobre segurança para movimentação de dados no Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre como proteger a movimentação de dados no Azure Data Factory."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 7535a21b80a753f7334bd99d4ac9dc8ca8d43f6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory – Considerações sobre segurança para movimentação de dados
## <a name="introduction"></a>Introdução
Este artigo descreve a infraestrutura básica de segurança usada pelos serviços de movimentação de dados no Azure Data Factory para proteger seus dados. Os recursos de gerenciamento do Azure Data Factory se baseiam na infraestrutura de segurança do Azure e usam todas as medidas de segurança possíveis oferecidas pelo Azure.

Em uma solução de Data Factory, você cria um ou mais [pipelines](data-factory-create-pipelines.md)de dados. Um pipeline é um agrupamento lógico de atividades que juntas executam uma tarefa. Esses pipelines residem na região em que o data factory foi criado. 

Embora o Data Factory esteja disponível somente nas regiões **Oeste dos EUA**, **Leste dos EUA** e **Europa Setentrional**, o serviço de movimentação de dados está disponível [globalmente em várias regiões](data-factory-data-movement-activities.md#global). O serviço Data Factory garante que os dados não saem de uma área geográfica/região, a menos que você explicitamente instrua o serviço a usar uma região alternativa caso o serviço de movimentação de dados ainda não tenha sido implantado nessa região. 

O Azure Data Factory em si não armazena nenhum dado, exceto as credenciais do serviço vinculado de armazenamentos de dados em nuvem, que são criptografadas com o uso de certificados. Ele permite criar fluxos de trabalho controlados por dados para orquestrar a movimentação de dados entre [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e o processamento de dados usando [serviços de computação](data-factory-compute-linked-services.md) em outras regiões ou em um ambiente local. Ele também permite [monitorar e gerenciar fluxos de trabalho](data-factory-monitor-manage-pipelines.md) usando mecanismos programáticos e de interface do usuário.

A movimentação de dados com o uso do Azure Data Factory foi **certificada** na:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Se você estiver interessado na conformidade do Azure e como ele protege sua própria infraestrutura, visite a [Central de Confiabilidade da Microsoft](https://www.microsoft.com/TrustCenter/default.aspx). 

Neste artigo, examinamos as considerações sobre segurança nestes dois cenários de movimentação de dados: 

- **Cenário de nuvem** – neste cenário, a origem e o destino são publicamente acessíveis pela Internet. Eles incluem serviços de armazenamento em nuvem gerenciados como Armazenamento do Azure, SQL Data Warehouse do Azure, Banco de Dados SQL do Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS como Salesforce e protocolos da Web como FTP e OData. Encontre uma lista completa de fontes de dados com suporte [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Cenário híbrido** – neste cenário, a origem ou o destino está atrás de um firewall ou dentro de uma rede corporativa local, ou o armazenamento de dados está em uma rede privada/virtual (geralmente, a origem) e não é acessível publicamente. Os servidores de banco de dados hospedados em máquinas virtuais também se enquadram nesse cenário.

## <a name="cloud-scenarios"></a>Cenários de nuvem
###<a name="securing-data-store-credentials"></a>Protegendo as credenciais do armazenamento de dados
O Azure Data Factory protege suas credenciais do armazenamento de dados **criptografando-as** usando **certificados gerenciados pela Microsoft**. Esses certificados são trocados a cada **dois anos** (que inclui a renovação do certificado e a migração de credenciais). Essas credenciais criptografadas são armazenadas com segurança em um **Armazenamento do Azure gerenciado pelos serviços de gerenciamento do Azure Data Factory**. Para obter mais informações sobre a segurança do Armazenamento do Azure, consulte [Visão geral de segurança do Armazenamento do Azure](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Criptografia de dados em trânsito
Caso o armazenamento de dados em nuvem dê suporte a HTTPS ou TLS, todas as transferências de dados entre serviços de movimentação de dados no Data Factory e um armazenamento de dados em nuvem ocorrerão por meio de um canal seguro HTTPS ou TLS.

> [!NOTE]
> Todas as conexões com o **Banco de Dados SQL do Azure** e o **SQL Data Warehouse do Azure** sempre exigem criptografia (SSL/TLS) quando os dados estão em trânsito, entrando e saindo do banco de dados. Ao criar um pipeline usando um editor de JSON, adicione a propriedade **criptografia** e defina-a como **true** na **cadeia de conexão**. Quando você usa o [Assistente de Cópia](data-factory-azure-copy-wizard.md), o assistente define essa propriedade por padrão. Para **Armazenamento do Azure**, é possível usar **HTTPS** na cadeia de conexão.

### <a name="data-encryption-at-rest"></a>Criptografia de dados em repouso
Alguns armazenamentos de dados dão suporte à criptografia de dados em repouso. Sugerimos que você habilite o mecanismo de criptografia de dados nesses armazenamentos de dados. 

#### <a name="azure-sql-data-warehouse"></a>SQL Data Warehouse do Azure
A TDE (Transparent Data Encryption) do SQL Data Warehouse do Azure ajuda a proteger contra ameaças de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real dos dados em repouso. Esse comportamento é transparente para o cliente. Para obter mais informações, consulte [Proteger um banco de dados no SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
O Banco de Dados SQL do Azure também dá suporte à TDE (Transparent Data Encryption), que ajuda a proteger contra ameaças de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real dos dados, sem a necessidade de alterações no aplicativo. Esse comportamento é transparente para o cliente. Para obter mais informações, consulte [Transparent Data Encryption com o Banco de Dados SQL do Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Repositório Azure Data Lake
O Azure Data Lake Store também fornece criptografia para os dados armazenados na conta. Quando está habilitado, o Data Lake Store criptografa os dados automaticamente antes de persisti-los e descriptografá-los antes da recuperação, tornando-os transparentes para o cliente que acessa os dados. Para obter mais informações, consulte [Segurança no Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de Blobs do Azure e Armazenamento de Tabelas do Azure
O Armazenamento de Blobs do Azure e o Armazenamento de Tabelas do Azure dão suporte à SSE (Storage Service Encryption), que criptografa os dados automaticamente antes de persisti-los no armazenamento e descriptografa-os antes da recuperação. Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
O Amazon S3 dá suporte à criptografia de cliente e de servidor de dados em repouso. Para obter mais informações, consulte [Proteger dados usando a criptografia](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Atualmente, o Data Factory não dá suporte ao Amazon S3 em uma VPC (nuvem privada virtual).

#### <a name="amazon-redshift"></a>Amazon Redshift
O Amazon Redshift dá suporte à criptografia de cluster de dados em repouso. Para obter mais informações, consulte [Criptografia de banco de dados do Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Atualmente, o Data Factory não dá suporte ao Amazon Redshift em uma VPC. 

#### <a name="salesforce"></a>Salesforce
O Salesforce dá suporte à Shield Platform Encryption, que permite a criptografia de todos os arquivos, anexos e campos personalizados. Para obter mais informações, consulte [Noções básicas sobre o fluxo de autenticação OAuth do Servidor Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Cenários híbridos (usando o Gateway de Gerenciamento de Dados)
Os cenários híbridos exigem a instalação de um Gateway de Gerenciamento de Dados em uma rede local ou dentro de uma rede virtual (Azure) ou uma nuvem privada virtual (Amazon). O gateway deve conseguir acessar os armazenamentos de dados locais. Para obter mais informações sobre o gateway, consulte [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md). 

![Canais do Gateway de Gerenciamento de Dados](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

O **canal de comando** permite a comunicação entre os serviços de movimentação de dados no Data Factory e no Gateway de Gerenciamento de Dados. A comunicação contém informações relacionadas à atividade. O canal de dados é usado para transferir dados entre armazenamentos de dados locais e armazenamentos de dados em nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais do armazenamento de dados local
As credenciais dos armazenamentos de dados locais são armazenadas localmente (não na nuvem). Elas podem ser definidas de três maneiras diferentes. 

- Usando **texto sem formatação** (menos seguro) por HTTPS no Portal do Azure/Assistente de Cópia. As credenciais são passadas em texto sem formatação para o gateway local.
- Usando a **biblioteca de Criptografia do JavaScript no Assistente de Cópia**.
- Usando o **aplicativo gerenciador de credenciais baseado em clique único**. O aplicativo de clique único é executado no computador local que tem acesso ao gateway e define as credenciais para o armazenamento de dados. Essa opção e a próxima são as opções mais seguras. Por padrão, o aplicativo gerenciador de credenciais usa a porta 8050 no computador com o gateway para uma comunicação segura.  
- Use o cmdlet [New-AzureRmDataFactoryEncryptValue](/powershell/module/azurerm.datafactories/New-AzureRmDataFactoryEncryptValue) do PowerShell para criptografar as credenciais. O cmdlet usa o certificado que esse gateway está configurado para usar para criptografar as credenciais. É possível usar as credenciais criptografadas retornadas por esse cmdlet e adicioná-las ao elemento **EncryptedCredential** da **connectionString** no arquivo JSON usado com o cmdlet [New-AzureRmDataFactoryLinkedService](/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) ou no trecho de JSON no Editor do Data Factory no portal. Essa opção e o aplicativo de clique único são as opções mais seguras. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Criptografia baseada na biblioteca de criptografia do JavaScript
Você pode criptografar as credenciais do armazenamento de dados usando a [biblioteca de Criptografia do JavaScript](https://www.microsoft.com/download/details.aspx?id=52439) no [Assistente de Cópia](data-factory-copy-wizard.md). Quando você seleciona essa opção, o Assistente de Cópia recupera a chave pública do gateway e a utiliza para criptografar as credenciais do armazenamento de dados. As credenciais são descriptografadas pelo computador do gateway e protegidas pela [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) do Windows.

**Navegadores com suporte:** IE8, IE9, IE10, IE11, Microsoft Edge e últimos navegadores Firefox, Chrome, Opera e Safari. 

#### <a name="click-once-credentials-manager-app"></a>Aplicativo gerenciador de credenciais de clique único
Inicie o aplicativo gerenciador de credenciais baseado em clique único no portal do Azure/Assistente de Cópia ao criar pipelines. Esse aplicativo garante que as credenciais não são transferidas em texto sem formatação durante a transmissão. Por padrão, ele usa a porta **8050** do computador com o gateway para uma comunicação segura. Se necessário, essa porta pode ser alterada.  
  
![Porta HTTPS do gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Atualmente, o Gateway de Gerenciamento de Dados usa um único **certificado**. Esse certificado é criado durante a instalação do gateway (aplica-se ao Gateway de Gerenciamento de Dados criado após novembro de 2016 e à versão 2.4.xxxx.x ou posterior). Você pode substituir esse certificado por seu próprio certificado SSL/TLS. Esse certificado é usado pelo aplicativo gerenciador de credenciais de clique único para se conectar com segurança ao computador do gateway a fim de configurar as credenciais do armazenamento de dados. Ele armazena as credenciais do armazenamento de dados com segurança local usando a [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) do Windows no computador com o gateway. 

> [!NOTE]
> Gateways mais antigos que foram instalados antes de novembro de 2016 ou da versão 2.3.xxxx.x continuam usando as credenciais criptografadas e armazenadas na nuvem. Mesmo se você atualizar o gateway para a última versão, as credenciais não serão migradas para um computador local    
  
| Versão do gateway (durante a criação) | Credenciais armazenadas | Criptografia/segurança de credenciais | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | Na nuvem | Criptografadas por meio de certificado (diferente daquele usado pelo aplicativo Gerenciador de credenciais) | 
| > = 2.4.xxxx.x | No local | Protegidas pela DPAPI | 
  

### <a name="encryption-in-transit"></a>Criptografia em trânsito
Todas as transferências de dados são feitas por meio do canal seguro **HTTPS** e **TLS via TCP** para impedir ataques man-in-the-middle durante a comunicação com os serviços do Azure.
 
Você também pode usar a [VPN IPsec](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou o [ExpressRoute](../../expressroute/expressroute-introduction.md) para fornecer proteção adicional ao canal de comunicação entre a rede local e o Azure.

Uma rede virtual é uma representação lógica de sua rede na nuvem. Você pode conectar uma rede local à VNet (rede virtual) do Azure ao configurar a VPN IPsec (site a site) ou o ExpressRoute (Emparelhamento Privado)     

A tabela a seguir resume as recomendações de configuração de rede e de gateway de acordo com diferentes combinações dos locais de origem e de destino para a movimentação de dados híbridos.

| Fonte | Destino | Configuração de rede | Instalação do gateway |
| ------ | ----------- | --------------------- | ------------- | 
| Configuração local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | VPN IPsec (ponto a site ou site a site) | O gateway pode ser instalado localmente ou em uma VM (máquina virtual) do Azure na VNet | 
| Configuração local | Máquinas virtuais e serviços de nuvem implantados em redes virtuais | ExpressRoute (Emparelhamento Privado) | O gateway pode ser instalado localmente ou em uma VM do Azure na VNet | 
| Configuração local | Serviços baseados no Azure que têm um ponto de extremidade público | ExpressRoute (Emparelhamento Público) | O gateway deve ser instalado localmente | 

As imagens a seguir mostram o uso do Gateway de Gerenciamento de Dados para mover dados entre um banco de dados local e os serviços do Azure usando o ExpressRoute e a VPN IPsec (com a Rede Virtual):

**ExpressRoute:**
 
![Usar o ExpressRoute com o gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**VPN IPsec:**

![VPN IPsec com gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Configurações de firewall e lista de permissões do endereço IP do gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para a rede local/privada  
Em uma empresa, um **firewall corporativo** é executado no roteador central da organização. E o **firewall do Windows** é executado como um daemon no computador local em que o gateway está instalado. 

A tabela a seguir fornece os requisitos de **porta de saída** e de domínio do **firewall corporativo**.

| Nomes de domínio | Portas de saída | Descrição |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Necessárias para que o gateway se conecte aos serviços de movimentação de dados no Data Factory |
| `*.core.windows.net` | 443 | Usada pelo gateway para se conectar à Conta de Armazenamento do Azure ao usar o recurso [cópia em etapas](data-factory-copy-activity-performance.md#staged-copy). | 
| `*.frontend.clouddatahub.net` | 443 | Necessária para que o gateway se conecte ao serviço Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (OPCIONAL) Necessária quando o destino é o Banco de Dados SQL do Azure/SQL Data Warehouse do Azure. Use o recurso de cópia em etapas para copiar dados para o Banco de Dados SQL do Azure/SQL Data Warehouse do Azure sem abrir a porta 1433. | 
| `*.azuredatalakestore.net` | 443 | (OPCIONAL) Necessária quando o destino é o Azure Data Lake Store | 

> [!NOTE] 
> Talvez você precise gerenciar as portas e os domínios na lista de permissões no nível do firewall corporativo, conforme exigido pelas respectivas fontes de dados. Esta tabela usa apenas o Banco de Dados SQL do Azure, o SQL Data Warehouse do Azure e o Azure Data Lake Store como exemplos.   

A tabela a seguir fornece os requisitos de **porta de entrada** do **firewall do Windows**.

| Portas de entrada | Descrição | 
| ------------- | ----------- | 
| 8050 (TCP) | Necessária para que o aplicativo gerenciador de credenciais defina com segurança as credenciais de armazenamentos de dados locais no gateway. | 

![Requisitos de porta do gateway](media\data-factory-data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Configurações de IP/lista de permissões no armazenamento de dados
Alguns armazenamentos de dados na nuvem também exigem a lista de permissões do endereço IP do computador que os acessa. Verifique se o endereço IP do computador do gateway está na lista de permissões e configurado no firewall corretamente.

Os armazenamentos de dados na nuvem exige a lista de permissões do endereço IP do computador do gateway. Por padrão, alguns desses armazenamentos de dados poderão não exigir a lista de permissões do endereço IP. 

- [Banco de Dados SQL do Azure](../../sql-database/sql-database-firewall-configure.md) 
- [SQL Data Warehouse do Azure](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal)
- [Repositório Azure Data Lake](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Pergunta:** O Gateway pode ser compartilhado entre diferentes data factories?
**Resposta:** Ainda não damos suporte a esse recurso. No entanto, estamos trabalhando de forma ativa para implementá-lo.

**Pergunta:** Quais são os requisitos de porta para o funcionamento do gateway?
**Resposta:** O Gateway faz conexões baseadas em HTTP com a Internet aberta. As **portas de saída 443 e 80** devem estar abertas para que o gateway estabeleça essa conexão. Abra a **Porta de Entrada 8050** somente no nível do computador (não no nível de firewall corporativo) para o aplicativo Gerenciador de Credenciais. Se o Banco de Dados SQL do Azure ou o SQL Data Warehouse do Azure for usado como a origem ou o destino, você precisará abrir a porta **1433** também. Para obter mais informações, consulte a seção [Configurações de firewall e endereços IP na lista de permissões](#firewall-configurations-and-whitelisting-ip-address-of gateway). 

**Pergunta:** Quais são os requisitos de certificado do Gateway?
**Resposta:** O gateway atual exige um certificado que é usado pelo aplicativo gerenciador de credenciais para a configuração segura das credenciais do armazenamento de dados. Esse certificado é um certificado autoassinado criado e configurado pela instalação do gateway. Em vez disso, você pode usar seu próprio certificado TLS/SSL. Para obter mais informações, consulte a seção [Aplicativo gerenciador de credenciais de clique único](#click-once-credentials-manager-app). 

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre o desempenho da atividade de cópia, consulte [Guia desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).

 
