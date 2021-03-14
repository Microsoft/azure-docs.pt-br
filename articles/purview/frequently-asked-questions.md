---
title: Perguntas frequentes
description: Este artigo responde às perguntas frequentes sobre o Azure alcance.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667807"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Perguntas frequentes sobre o Azure alcance

## <a name="overview"></a>Visão geral

Muitas organizações não têm uma compreensão holística de seus dados. É desafiador entender quais dados existem, onde os dados estão localizados e como localizar e acessar dados relevantes. Os dados não têm contexto como linhagem, classificação e metadados abrangentes, o que dificulta para os usuários empresariais Pesquisar os dados certos e usar esses dados adequadamente. Como resultado, apenas uma pequena fração dos dados coletados é usada para informar decisões de negócios. Por fim, a identificação de problemas de segurança de dados e a proteção de dados confidenciais são inconsistentes. Ele requer tempo e esforço contínuos, especialmente enquanto mantém a agilidade dos dados.

O Azure alcance é uma solução de governança de dados. Ele ajuda os clientes a obter conhecimento profundo de todos os seus dados, mantendo o controle sobre o seu uso. Com o Azure alcance, as organizações detectam e organizam os dados. Eles se aprofundam em seus dados e controlam o acesso aos dados de forma centralizada.

## <a name="purpose-of-this-faq"></a>Finalidade destas perguntas frequentes

Essas perguntas frequentes respondem a perguntas comuns que os clientes e as equipes de campo costumam fazer. Ele se destina a esclarecer perguntas sobre o Azure alcance e soluções relacionadas, como o ADC (catálogo de dados do Azure) Gen 2 (preterido) e a proteção de informações do Azure.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Quais são os tipos de origem disponíveis para verificação de metadados e classificação?

|Azure|Não Azure|
|---------|---------|
|Armazenamento de Blobs do Azure|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (disponível até o final de 2020)|
|Instância Gerenciada do Azure SQL|SAP ECC (disponível por fim de 2020)|
|Azure Data Explorer|SAP S/4 HANA (disponível por fim de 2020)|
|Azure Data Lake Storage Gen1|Metastore do hive (disponível até o final de 2020)|
|Azure Data Lake Storage Gen2|Amazon S3|
|Arquivos do Azure|--|
|Banco de Dados SQL do Azure|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Quais sistemas/processadores de dados podemos nos conectar e obter a linhagem?

|Sistema/processador de dados 
|---------
|Azure Data Factory: atividade de cópia, atividade de fluxo de dados 
|Linhagem personalizada   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>Como o ADC Gen 2, a proteção de informações do Azure e o Azure alcance estão relacionados?

O Azure alcance começou originalmente como ADC Gen 2, mas desde que tenha sido ampliado no escopo. Agora, ele adota os recursos de catálogo avançado do ADC Gen 2 combinados com os recursos de classificação de dados, rotulamento e imposição de política de conformidade da proteção de informações do Azure. Hoje, ela se alinha mais próxima à definição mais ampla do setor de governança de dados.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>O que acontece com os clientes que usam ADC Gen 1?

O Azure alcance é o foco de toda a inovação de produtos no espaço de solução de catálogo para a Microsoft. O ADC Gen 1 continuará a ter suporte.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>Os clientes podem ter várias contas do Azure alcance na mesma assinatura?

Sim, há suporte para muitas contas do Azure alcance por assinatura e por locatário.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>Posso executar ADC Gen 1 e Azure alcance em paralelo?

Sim. Ambos são serviços independentes.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Como fazer migrar ativos de dados ADC Gen 1 existentes para o Azure alcance?

Use as APIs alcance do Azure para extrair do ADC Gen 1 e ingerir o Azure alcance. Para o Glossário, damos suporte a ferramentas em massa baseadas em CSV.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Como fazer criptografar dados confidenciais para tabelas SQL usando o Azure alcance?

A criptografia de dados é feita no nível de fonte de dados. O Azure alcance armazena apenas os metadados. Ele não visualiza dados.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Todos os recursos do ADC Gen 2 existem no Azure alcance?

Sim.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Qual é a diferença entre um glossário e uma classificação?

Um glossário usa uma Convenção de nomenclatura seguida por usuários não técnicos/comerciais dos dados, também conhecidos como consumidores de dados. Esses tipos de pessoas são analistas de negócios ou cientistas de dados que usam o Azure alcance para pesquisar determinados tipos de dados, com base no uso dos negócios. Por exemplo, analistas de cadeia de suprimentos talvez precisem Pesquisar os termos *tipos de SKU* e *detalhes de remessa*. Eles pesquisam o Glossário em busca desses termos para localizar dados relevantes.
Classificação é uma marca aplicada a um ativo de dados na tabela, coluna ou nível de arquivo, que identifica quais dados existem no ativo. A classificação pode ser aplicada automaticamente ou manualmente, com base no tipo de dados encontrado. Normalmente, você usa marcas de classificação para identificar se um ativo contém dados confidenciais e quais tipos de dados confidenciais podem ser.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>O Azure alcance examina e classifica emails, PDFs etc. no SharePoint e no OneDrive?

A verificação de sites e bibliotecas locais do SharePoint é fornecida por meio do verificador da proteção de informações do Azure. O scanner está disponível para uso por meio de uma assinatura de Microsoft 365 do cliente com as seguintes SKUs: AIP P1, EMS E3 e M365 E3. Se você tiver qualquer uma dessas SKUs, deverá ter os direitos certos para começar a usar o verificador da proteção de informações do Azure.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Qual é a computação usada para a verificação?
Há uma infraestrutura de verificação gerenciada pela Microsoft. Para a maioria dos recursos do Azure/AWS aos quais damos suporte, você não precisa implantar uma infraestrutura de verificação.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Há uma maneira de provisionar alcance do Azure por meio do modelo/CLI/PowerShell do Azure Resource Manager (ARM)?

Sim, o modelo ARM está disponível

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Já estou usando o Atlas, posso migrar facilmente para o Azure alcance?

O Azure alcance é compatível com a API do Atlas. Se você estiver migrando do Atlas, é recomendável verificar suas fontes de dados primeiro usando o Azure alcance. Depois que os ativos estiverem disponíveis em sua conta, você poderá usar APIs do Atlas semelhantes para integrar como atualizar ativos ou adicionar a linhagem personalizada. O alcance do Azure modifica a API de pesquisa para usar Azure Search para que você possa usar a pesquisa avançada.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>Posso criar vários catálogos em meu locatário?

Sim, você pode criar várias contas do Azure alcance por assinatura e por locatário. Você pode examinar a página limites [gerenciar e aumentar as cotas de recursos com o Azure alcance](how-to-manage-quotas.md).

A recomendação adicional sobre quando você deve ou não deve ter várias contas está documentada em nossas [práticas recomendadas de implantação do Azure alcance](deployment-best-practices.md).

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>Posso registrar vários locatários em uma única conta do Azure alcance?

Não, atualmente para verificar a fonte de dados de outro locatário, você precisa criar uma conta do Azure alcance separada nesse locatário.

### <a name="does-azure-purview-support-column-level-lineage"></a>O Azure alcance dá suporte à linhagem de nível de coluna?

Sim, o Azure alcance dá suporte à linhagem de nível de coluna.