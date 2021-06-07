---
title: Criptografia de dados do Azure em repouso-segurança do Azure
description: Este artigo fornece uma visão geral da criptografia de dados do Azure em repouso, os recursos gerais e as considerações gerais.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 1318aaf50d4f05b8a999e5661318dc83997a2f02
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920791"
---
# <a name="azure-data-encryption-at-rest"></a>Criptografia de dados do Azure em repouso

O Microsoft Azure inclui ferramentas para proteger os dados de acordo com as necessidades de segurança e conformidade de sua empresa. Este documento se concentra em:

- Como os dados são protegidos em repouso no Microsoft Azure
- Descreve os vários componentes que fazem parte da implementação de proteção de dados,
- Revisa os prós e contras das diferentes abordagens de proteção de gerenciamento de chaves.

A criptografia em repouso é um requisito de segurança comum. No Azure, as organizações podem criptografar dados em repouso sem ter o risco nem o custo de uma solução de gerenciamento de chaves personalizada. As organizações têm a opção de permitir que o Azure gerencie completamente a criptografia em repouso. Além disso, as organizações possuem várias opções para gerenciar de maneira rigorosa a criptografia ou as chaves de criptografia.

## <a name="what-is-encryption-at-rest"></a>O que é criptografia em repouso?

A criptografia é a codificação segura dos dados usados para proteger a confidencialidade dos dados. Os designs de Criptografia em Repouso no Azure utilizam criptografia simétrica para criptografar e descriptografar grandes quantidades de dados rapidamente de acordo com um modelo conceitual simples:

- Uma chave de criptografia simétrica é usada para criptografar dados, pois eles são gravados no armazenamento.
- A mesma chave de criptografia é usada para descriptografar esses dados conforme estiverem prontos para serem usados na memória.
- Os dados podem ser particionados, e diferentes chaves podem ser usadas para cada partição.
- As chaves devem ser armazenadas em uma localização segura com controle de acesso baseado em identidade e políticas de auditoria. As chaves de criptografia de dados geralmente são criptografadas com uma chave de criptografia de chave em Azure Key Vault para limitar ainda mais o acesso.

Na prática, os principais cenários de controle e gerenciamento, assim como garantias de disponibilidade e escala, requerem construções adicionais. Os conceitos e componentes de Criptografia em Repouso no Microsoft Azure são descritos abaixo.

## <a name="the-purpose-of-encryption-at-rest"></a>A finalidade da criptografia em repouso

A criptografia em repouso oferece proteção de dados para dados armazenados (em repouso). Os ataques contra dados em repouso incluem tentativas de obter acesso físico ao hardware em que os dados são armazenados e, em seguida, comprometer os dados contidos. Nesse tipo de ataque, o disco rígido de um servidor pode ter sido manipulado intensamente durante a manutenção, permitindo que um invasor remova o disco rígido. Mais tarde, o invasor poderia colocar o disco rígido em um computador sob seu controle para tentar acessar os dados.

A criptografia em repouso é projetada para impedir que o invasor acesse os dados não criptografados, assegurando que os dados sejam criptografados quando em disco. Se um invasor tiver um disco rígido com os dados criptografados, mas não tiver as chaves de criptografia, ele precisará superar a criptografia para ler os dados. Esse ataque é muito mais complexo e consome mais recursos do que o acesso a dados não criptografados em um disco rígido. Por esse motivo, a criptografia em repouso é altamente recomendada e é um requisito de alta prioridade para muitas organizações.

A criptografia em repouso também pode ser exigida pela necessidade de uma organização de governança de dados e esforços de conformidade. As regulamentações governamentais e do setor como HIPAA, PCI e FedRAMP estabelecem proteções específicas sobre a proteção de dados e requisitos de criptografia. A criptografia em repouso é uma medida obrigatória necessária para oferecer conformidade com alguns desses regulamentos. Para obter mais informações sobre a abordagem da Microsoft para a validação do FIPS 140-2, consulte a [publicação 140-2 do padrão FIPS (FIPS)](/microsoft-365/compliance/offering-fips-140-2).

Além de atender a requisitos de regulamentação e conformidade, a criptografia em repouso fornece proteção com defesa em profundidade. O Microsoft Azure fornece uma plataforma em conformidade com serviços, aplicativos e dados. Ele também fornece uma abrangente segurança física e das instalações, controle de acesso a dados e auditoria. No entanto, é importante fornecer medidas de segurança adicionais "sobrepostas" no caso de uma das outras medidas de segurança falhar e a criptografia em repouso fornece tal medida de segurança.

A Microsoft tem o compromisso de fornecer opções de criptografia em repouso nos serviços de nuvem e de proporcionar aos clientes controle das chaves de criptografia e dos logs de uso da chave. Além disso, a Microsoft está trabalhando para criptografar todos os dados do cliente em repouso por padrão.

## <a name="azure-encryption-at-rest-components"></a>Componentes da Criptografia em Repouso no Azure

Conforme descrito anteriormente, a meta da criptografia em repouso é que os dados que são persistentes no disco sejam criptografados com uma chave de criptografia secreta. Para atingir essa meta, é necessário fornecer criação de chave segura, armazenamento, controle de acesso e gerenciamento das chaves de criptografia. Embora os detalhes possam variar, os serviços de implementações de Criptografia em Repouso do Azure podem ser descritos em termos ilustrados no diagrama a seguir.

![Componentes](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Cofre de Chave do Azure

O local de armazenamento das chaves de criptografia e controle de acesso a essas chaves é central para um modelo de criptografia em repouso. As chaves precisam ser altamente seguras, mas gerenciáveis por usuários especificados e disponíveis para serviços específicos. Para os serviços do Azure, o Azure Key Vault é a solução de armazenamento de chave recomendada e fornece uma experiência de gerenciamento comum em todos os serviços. As chaves são armazenadas e gerenciadas em cofres de chaves e o acesso a um cofre de chave pode ser fornecido para usuários ou serviços. O Azure Key Vault fornece suporte a criação de chaves do cliente ou importação de chaves do cliente para uso em cenários de chave de criptografia de cliente gerenciado.

### <a name="azure-active-directory"></a>Azure Active Directory

Permissões para utilizar as chaves armazenadas no Azure Key Vault, seja para gerenciá-las ou acessá-las para criptografia e descriptografia da Criptografia em Repouso, podem ser fornecidas para contas do Azure Active Directory.

### <a name="key-hierarchy"></a>Hierarquia de Chave

Mais de uma chave de criptografia é utilizada em uma implementação de criptografia em repouso. O armazenamento de uma chave de criptografia no Azure Key Vault garante o acesso de chave seguro e o gerenciamento central de chaves. No entanto, o acesso local do serviço a chaves de criptografia é mais eficiente para criptografia e descriptografia em massa do que interagir com Key Vault para cada operação de dados, permitindo uma criptografia mais forte e melhor desempenho. Limitar o uso de uma chave de criptografia única diminui o risco de que a chave fique comprometida e o custo de nova criptografia quando uma chave precisar ser substituída. Os modelos de criptografias em repouso do Azure usam uma hierarquia de chave composta pelos seguintes tipos de chaves para atender a todas essas necessidades:

- **DEK (Chave de Criptografia de Dados)** – Uma chave simétrica AES256 utilizada para criptografar uma partição ou bloco de dados.  Um recurso único pode ter muitas partições e muitas Chaves de Criptografia de Dados. Criptografar cada bloco de dados com uma chave diferente torna os ataques de análise de criptografia mais difíceis. O acesso a DEKs é necessário pelo provedor de recursos ou instância do aplicativo que criptografa e descriptografa um bloco específico. Quando um DEK é substituído por uma nova chave, apenas os dados em seu bloco associado devem ser reciclados com a nova chave.
- Chave de **criptografia de chave (Kek)** – uma chave de criptografia usada para criptografar as chaves de criptografia de dados. O uso de uma chave de criptografia de chave que nunca deixa Key Vault permite que as próprias chaves de criptografia de dados sejam criptografadas e controladas. A entidade que tem acesso ao KEK pode ser diferente da entidade que requer o DEK. Uma entidade pode acessar o DEK como agente para limitar o acesso de cada DEK a uma partição específica. Uma vez que o KEK é necessário para descriptografar os DEKs, o KEK é efetivamente um ponto único pelo qual os DEKs podem ser efetivamente excluídos pela exclusão do KEK.

As chaves de criptografia de dados, criptografadas com as chaves de criptografia de chave, são armazenadas separadamente e apenas uma entidade com acesso à chave de criptografia de chave pode descriptografar essas chaves de criptografia de dados. Há suporte para diferentes modelos de armazenamento de chaves. Consulte [modelos de criptografia de dados](encryption-models.md) para obter mais informações.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Criptografia em repouso em serviços em nuvem da Microsoft

Os serviços do Microsoft Cloud são utilizados em todos os três modelos de nuvem: IaaS, PaaS, SaaS. A seguir, são apresentados exemplos de como ajustam-se em cada modelo:

- Serviços de software, chamados de software como um servidor ou SaaS, que têm aplicativos fornecidos pela nuvem, como o Microsoft 365.
- Serviços de plataforma nos quais os clientes aproveitam a nuvem em seus aplicativos, utilizando-a para armazenamento, análise e funcionalidade de barramento de serviço.
- Serviços de infraestrutura, ou IaaS (Infraestrutura como Serviço), em que o cliente implanta sistemas operacionais e aplicativos hospedados na nuvem e que possivelmente usam outros serviços de nuvem.

### <a name="encryption-at-rest-for-saas-customers"></a>Criptografia em repouso para clientes SaaS

Os clientes de Software como Serviço (SaaS), geralmente possuem criptografia em repouso habilitada ou disponível em cada serviço. Microsoft 365 tem várias opções para que os clientes verifiquem ou habilitem a criptografia em repouso. Para obter informações sobre Microsoft 365 serviços, consulte [criptografia em Microsoft 365](/microsoft-365/compliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Criptografia em repouso para clientes de PaaS

Os dados do cliente de PaaS (plataforma como serviço) normalmente residem em um serviço de armazenamento, como o armazenamento de BLOB, mas também podem ser armazenados em cache ou armazenadas no ambiente de execução do aplicativo, como uma máquina virtual. Para ver as opções de criptografia em repouso disponíveis, examine a tabela abaixo para as plataformas de aplicativos e armazenamento que você usa.

### <a name="encryption-at-rest-for-iaas-customers"></a>Criptografia em repouso para clientes de IaaS

Os clientes de Infraestrutura como Serviço (IaaS) podem ter uma variedade de serviços e aplicativos em uso. Os serviços de IaaS podem habilitar a criptografia em repouso em suas máquinas virtuais hospedadas no Azure e VHDs utilizando o Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Armazenamento criptografado

Como PaaS, as soluções de IaaS podem aproveitar outros serviços do Azure que armazenam dados criptografados em repouso. Nestes casos, é possível habilitar o suporte para criptografia em repouso, conforme fornecido por cada serviço do Azure consumido. A tabela abaixo enumera as principais plataformas de aplicativos, serviços e armazenamento e o modelo de Criptografia em Repouso compatível.

#### <a name="encrypted-compute"></a>Computação criptografada

Todos os Managed Disks, instantâneos e imagens são criptografados usando Criptografia do Serviço de Armazenamento usando uma chave gerenciada por serviço. Uma solução mais completa de criptografia em repouso garante que os dados nunca sejam persistidos em formato não criptografado. Durante o processamento dos dados em uma máquina virtual, os dados podem ser persistidos no arquivo de paginação do Windows ou no arquivo de permuta do Linux, em um despejo de memória ou em um log de aplicativo. Para garantir que esses dados sejam criptografados em repouso, os aplicativos de IaaS podem usar o Azure Disk Encryption em uma máquina virtual de IaaS do Azure (Windows ou Linux) e no disco virtual.

#### <a name="custom-encryption-at-rest"></a>Criptografia em repouso personalizada

É recomendável, sempre que possível, que os aplicativos de IaaS utilizem as opções de Criptografia em Repouso e o Azure Disk Encryption fornecidos por quaisquer serviços do Azure consumidos. Em alguns casos, como requisitos de criptografia irregulares ou armazenamento não baseado em Azure, um desenvolvedor de um aplicativo de IaaS pode precisar implementar criptografia em repouso. Os desenvolvedores das soluções de IaaS podem integra-se melhor com o gerenciamento do Azure e as expectativas dos clientes, aproveitando determinados componentes do Azure. Especificamente, os desenvolvedores devem utilizar o serviço do Azure Key Vault para fornecer armazenamento de chaves seguras, além de fornecer aos seus clientes opções de gerenciamento de chaves consistentes com a maioria dos serviços da plataforma do Azure. Além disso, as soluções personalizadas devem usar as Identidades de Serviço Gerenciado do Azure para permitir que as contas do serviço acessem chaves de criptografia. Para obter informações de desenvolvedor sobre o Azure Key Vault e as Identidades de Serviço Gerenciado, confira seus respectivos SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Suporte ao modelo de criptografia dos provedores de recursos do Azure

Os serviços do Microsoft Azure oferecem suporte para um ou mais dos modelos de criptografia em repouso. Para alguns serviços, no entanto, um ou mais dos modelos de criptografia podem não ser aplicáveis. Para serviços que dão suporte a cenários de chave gerenciada pelo cliente, eles somente podem dar suporte a um subconjunto dos tipos de chave cujo Azure Key Vault dá suporte para chaves de criptografia de chaves. Além disso, os serviços podem liberar suporte a esses cenários e tipos de chaves em diferentes agendamentos. Esta seção descreve o suporte de criptografia em repouso no momento desta escrita para cada um dos principais serviços de armazenamento de dados do Azure.

### <a name="azure-disk-encryption"></a>Criptografia de disco do Azure

Qualquer cliente que utiliza recursos de IaaS (Infraestrutura como Serviço) pode alcançar criptografia em repouso para seus discos e VMs de IaaS através do Azure Disk Encryption. Para obter mais informações sobre o Azure Disk Encryption, confira a [documentação do Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md).

#### <a name="azure-storage"></a>Armazenamento do Azure

Todos os serviços de armazenamento do Azure (armazenamento de BLOBs, armazenamento de filas, armazenamento de tabelas e arquivos do Azure) dão suporte à criptografia no lado do servidor em repouso; alguns serviços adicionalmente dão suporte a chaves gerenciadas pelo cliente e criptografia do lado do cliente.

- No servidor: Todos os serviços de Armazenamento do Azure habilitam a criptografia do servidor por padrão que usa chaves de serviço gerenciado, que é transparente para o aplicativo. Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](../../storage/common/storage-service-encryption.md). O Armazenamento de Blobs do Azure e os Arquivos do Azure também dão suporte a chaves gerenciadas pelo cliente RSA de 2048 bits no Azure Key Vault. Para obter mais informações, consulte [Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).
- No cliente: os Blobs do Azure, tabelas e filas oferecem suporte para criptografia de cliente. Ao usar criptografia do cliente, os clientes criptografam os dados e carregam os dados como um blob criptografado. O gerenciamento de chaves é feito pelo cliente. Para obter mais informações, consulte [Criptografia do lado do cliente e Azure Key Vault para o Armazenamento do Microsoft Azure](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure dá suporte à criptografia em repouso para cenários de criptografia gerenciada pela Microsoft no lado do cliente e no lado do serviço.

O suporte para criptografia do servidor atualmente é fornecido através do recurso SQL chamado Transparent Data Encryption. Quando um cliente do Banco de Dados SQL do Azure habilita a chave de TDE, elas são criadas e gerenciadas automaticamente. A criptografia em repouso pode ser habilitada nos níveis de servidor e banco de dados. A partir de junho de 2017, a [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption) estará habilitada por padrão em bancos de dados criados recentemente. O Banco de Dados SQL do Azure dá suporte a chaves gerenciadas pelo cliente RSA de 2048 bits no Azure Key Vault. Para obter mais informações, consulte [Transparent Data Encryption com suporte para Bring Your Own Key para Banco de Dados SQL do Azure e Data Warehouse](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql).

A criptografia do cliente dos dados do Banco de Dados SQL do Azure tem suporte por meio do recurso [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted utiliza uma chave criada e armazenada pelo cliente. Os clientes podem armazenar a chave mestre em um repositório de certificados do Windows, no Azure Key Vault ou em um Módulo de Segurança de Hardware local. Usando SQL Server Management Studio, os usuários do SQL escolhem qual chave gostaria de usar para criptografar qual coluna.

## <a name="conclusion"></a>Conclusão

A proteção dos dados do cliente armazenados nos Serviços do Azure é de fundamental importância para a Microsoft. Todos os serviços hospedados do Azure estão comprometido em fornecer opções de criptografia em repouso. Os serviços do Azure dão suporte a chaves gerenciadas por serviço, chaves gerenciadas pelo cliente ou criptografia do lado do cliente. Os serviços do Azure estão aprimorando amplamente a criptografia em disponibilidade em repouso e novas opções são planejadas para a versão prévia e a disponibilidade geral nos próximos meses.

## <a name="next-steps"></a>Próximas etapas

- Consulte [modelos de criptografia de dados](encryption-models.md) para saber mais sobre chaves gerenciadas por serviço e chaves gerenciadas pelo cliente.
- Saiba como o Azure usa a [criptografia dupla](double-encryption.md) para atenuar as ameaças que vêm com a criptografia de dados.
- Saiba o que a Microsoft faz para garantir a [integridade da plataforma e a segurança](platform.md) dos hosts que atravessam os pipelines de desenvolvimento, integração, operacionalização e reparo de hardware e firmware.
