---
title: Criptografia de dados em repouso no Microsoft Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da criptografia de dados em repouso no Microsoft Azure, os recursos gerais e as considerações gerais.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: 4ced712b1b2716d85f0366ea892460053db598b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613038"
---
# <a name="azure-data-encryption-at-rest"></a>Criptografia de dados em repouso no Azure

O Microsoft Azure inclui ferramentas para proteger os dados de acordo com as necessidades de segurança e de conformidade da sua empresa. Este documento se concentra em:

- Como os dados são protegidos em repouso no Microsoft Azure
- Descreve os vários componentes que fazem parte da implementação de proteção de dados,
- Revisa os prós e contras das diferentes abordagens de proteção de gerenciamento de chaves. 

A criptografia em repouso é um requisito de segurança comum. No Azure, as organizações podem criptografar dados em repouso sem ter o risco nem o custo de uma solução de gerenciamento de chaves personalizada. As organizações têm a opção de permitir que o Azure gerencie completamente a criptografia em repouso. Além disso, as organizações possuem várias opções para gerenciar de maneira rigorosa a criptografia ou as chaves de criptografia.

## <a name="what-is-encryption-at-rest"></a>O que é criptografia em repouso?

A criptografia em repouso é a codificação (criptografia) de dados quando eles são persistentes. Os designs de Criptografia em Repouso no Azure utilizam criptografia simétrica para criptografar e descriptografar grandes quantidades de dados rapidamente de acordo com um modelo conceitual simples:

- Uma chave de criptografia simétrica é usada para criptografar dados, pois eles são gravados no armazenamento. 
- A mesma chave de criptografia é usada para descriptografar esses dados conforme estiverem prontos para serem usados na memória.
- Os dados podem ser particionados, e diferentes chaves podem ser usadas para cada partição.
- As chaves devem ser armazenadas em uma localização segura com controle de acesso baseado em identidade e políticas de auditoria. As chaves de criptografia de dados são frequentemente criptografadas com criptografia assimétrica para limitar ainda mais o acesso.

Na prática, os principais cenários de controle e gerenciamento, assim como garantias de disponibilidade e escala, requerem construções adicionais. Os conceitos e componentes de Criptografia em Repouso no Microsoft Azure são descritos abaixo.

## <a name="the-purpose-of-encryption-at-rest"></a>A finalidade da criptografia em repouso

A criptografia em repouso oferece proteção de dados para dados armazenados (em repouso). Os ataques contra dados em repouso incluem tentativas de obter acesso físico ao hardware em que os dados são armazenados e, em seguida, comprometer os dados contidos. Nesse ataque, o disco rígido de um servidor pode ter sido manipulado incorretamente durante a manutenção, permitindo que um invasor remova o disco rígido. Mais tarde, o invasor poderia colocar o disco rígido em um computador sob seu controle para tentar acessar os dados. 

A criptografia em repouso é projetada para impedir que o invasor acesse os dados não criptografados, assegurando que os dados sejam criptografados quando em disco. Se um invasor tiver um disco rígido com os dados criptografados, mas não tiver as chaves de criptografia, ele precisará superar a criptografia para ler os dados. Esse ataque é muito mais complexo e consome mais recursos do que o acesso a dados não criptografados em um disco rígido. Por esse motivo, a criptografia em repouso é altamente recomendada e é um requisito de alta prioridade para muitas organizações. 

A criptografia em repouso também pode ser exigida pela necessidade de uma organização para governança de dados e esforços de conformidade. As regulamentações governamentais e do setor como HIPAA, PCI e FedRAMP estabelecem proteções específicas sobre a proteção de dados e requisitos de criptografia. A criptografia em repouso é uma medida obrigatória necessária para oferecer conformidade com alguns desses regulamentos.

Além de atender a requisitos de regulamentação e conformidade, a criptografia em repouso fornece proteção com defesa em profundidade. O Microsoft Azure fornece uma plataforma em conformidade com serviços, aplicativos e dados. Ele também fornece uma abrangente segurança física e das instalações, controle de acesso a dados e auditoria. No entanto, é importante fornecer medidas de segurança "sobrepostas" adicionais para o caso de uma das outras medidas de segurança falhar e a criptografia em repouso fornece essa medida de segurança

A Microsoft tem o compromisso de fornecer opções de criptografia em repouso nos serviços de nuvem e de proporcionar aos clientes controle das chaves de criptografia e dos logs de uso da chave. Além disso, a Microsoft está trabalhando para criptografar todos os dados do cliente em repouso por padrão.

## <a name="azure-encryption-at-rest-components"></a>Componentes da Criptografia em Repouso no Azure

Conforme descrito anteriormente, a meta da criptografia em repouso é que os dados que são persistentes no disco sejam criptografados com uma chave de criptografia secreta. Para atingir essa meta, é necessário fornecer criação de chave segura, armazenamento, controle de acesso e gerenciamento das chaves de criptografia. Embora os detalhes possam variar, os serviços de implementações de Criptografia em Repouso do Azure podem ser descritos em termos ilustrados no diagrama a seguir.

![Componentes](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Cofre da Chave do Azure

O local de armazenamento das chaves de criptografia e controle de acesso a essas chaves é central para um modelo de criptografia em repouso. As chaves precisam ser altamente seguras, mas gerenciáveis por usuários especificados e disponíveis para serviços específicos. Para os serviços do Azure, o Azure Key Vault é a solução de armazenamento de chave recomendada e fornece uma experiência de gerenciamento comum em todos os serviços. As chaves são armazenadas e gerenciadas em cofres de chaves e o acesso a um cofre de chave pode ser fornecido para usuários ou serviços. O Azure Key Vault fornece suporte a criação de chaves do cliente ou importação de chaves do cliente para uso em cenários de chave de criptografia de cliente gerenciado.

### <a name="azure-active-directory"></a>Azure Active Directory

Permissões para utilizar as chaves armazenadas no Azure Key Vault, seja para gerenciá-las ou acessá-las para criptografia e descriptografia da Criptografia em Repouso, podem ser fornecidas para contas do Azure Active Directory. 

### <a name="key-hierarchy"></a>Hierarquia de Chave

Mais de uma chave de criptografia é utilizada em uma implementação de criptografia em repouso. A criptografia assimétrica é útil para estabelecer a confiança e a autenticação necessárias para acesso e gerenciamento de chaves. A criptografia simétrica é mais eficiente para criptografia e descriptografia em massa, permitindo uma criptografia mais forte e melhor desempenho. Limitar o uso de uma chave de criptografia única diminui o risco de que a chave fique comprometida e o custo de nova criptografia quando uma chave precisar ser substituída. Os modelos de criptografias em repouso do Azure usam uma hierarquia de chave composta pelos seguintes tipos de chaves:

- **DEK (Chave de Criptografia de Dados)** – Uma chave simétrica AES256 utilizada para criptografar uma partição ou bloco de dados.  Um recurso único pode ter muitas partições e muitas Chaves de Criptografia de Dados. Criptografar cada bloco de dados com uma chave diferente torna os ataques de análise de criptografia mais difíceis. O acesso a DEKs é necessário pelo provedor de recursos ou instância do aplicativo que criptografa e descriptografa um bloco específico. Quando um DEK é substituído por uma nova chave, apenas os dados em seu bloco associado devem ser reciclados com a nova chave.
- **KEK (Chave de Criptografia de Chave)** – Uma chave de criptografia assimétrica utilizada para criptografar as Chaves de Criptografia de Dados. O uso de uma Chave de Criptografia de Chave permite que as chaves de criptografia de dados sejam criptografadas e controladas. A entidade que tem acesso ao KEK pode ser diferente da entidade que requer o DEK. Uma entidade pode acessar o DEK como agente para limitar o acesso de cada DEK a uma partição específica. Uma vez que o KEK é necessário para descriptografar os DEKs, o KEK é efetivamente um ponto único pelo qual os DEKs podem ser efetivamente excluídos pela exclusão do KEK.

As Chaves de Criptografia de Dados, criptografadas com as Chaves de Criptografia de Chave são armazenadas separadamente e somente uma entidade com acesso à Chave de Criptografia de Chave pode obter quaisquer Chaves de Criptografia de Dados com essa chave. Há suporte para diferentes modelos de armazenamento de chaves. Cada modelo será discutido com mais detalhes na próxima seção.

## <a name="data-encryption-models"></a>Modelos de Criptografia de Dados

Compreender os vários modelos de criptografia e seus prós e contras é fundamental para entender como os vários provedores de recursos no Azure implementam a criptografia em repouso. Essas definições são compartilhadas em todos os provedores de recursos no Azure para garantir linguagem e taxonomia comuns. 

Há três cenários para criptografia do lado do servidor:

- Criptografia do lado do servidor utilizando chaves gerenciadas pelo serviço
    - Provedores de recursos do Azure executam as operações de criptografia e descriptografia
    - A Microsoft gerencia as chaves
    - Funcionalidade completa na nuvem

- Criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Azure Key Vault
    - Provedores de recursos do Azure executam as operações de criptografia e descriptografia
    - O cliente controla as chaves por meio do Azure Key Vault
    - Funcionalidade completa na nuvem

- Criptografia do lado do servidor utilizando chaves gerenciadas pelo cliente em hardware controlado pelo cliente
    - Provedores de recursos do Azure executam as operações de criptografia e descriptografia
    - O cliente controla as chaves no hardware controlado pelo cliente
    - Funcionalidade completa na nuvem

Para criptografia do lado do cliente, considere o seguinte:

- Os serviços do Azure não podem ver dados descriptografados
- Os clientes gerenciam e armazenam chaves no local (ou em outros repositórios seguros). As chaves não estão disponíveis para os serviços do Azure
- Funcionalidade reduzida na nuvem

Os modelos de criptografia com suporte no Azure dividem-se em dois grupos principais: "Criptografia do cliente" e "criptografia de servidor" como mencionado anteriormente. Independentemente do modelo de criptografia em repouso utilizado, os serviços do Azure recomendam sempre o uso de um transporte seguro, como TLS ou HTTPS. Portanto, a criptografia em transporte deve ser abordada pelo protocolo de transporte e não deve ser um fator importante na determinação do modelo de criptografia em repouso a ser utilizado.

### <a name="client-encryption-model"></a>Modelo de criptografia de cliente

O modelo de Criptografia de Cliente refere-se à criptografia que é realizada fora do Provedor de Recursos ou Azure pelo aplicativo de chamada ou serviço. A criptografia pode ser realizada pelo aplicativo de serviço no Azure ou por um aplicativo em execução no data center do cliente. Em ambos os casos, ao aproveitar esse modelo de criptografia, o Provedor de Recursos do Azure recebe um blob de dados criptografado sem a capacidade de descriptografar os dados de maneira nenhuma ou ter acesso às chaves de criptografia. Nesse modelo, o gerenciamento de chaves é feito pelo aplicativo de chamada/serviço e é opaco para o serviço do Azure.

![Cliente](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modelo de criptografia do lado do servidor

Os modelos de criptografia do lado do servidor referem-se à criptografia que é executada pelo serviço do Azure. Nesse modelo, o Provedor de Recursos executa as operações de criptografia e descriptografia. Por exemplo, o Armazenamento do Azure pode receber dados em operações de texto sem formatação e executará a criptografia e descriptografia internamente. O Provedor de Recursos pode utilizar chave de criptografia que são gerenciadas pela Microsoft ou pelo cliente, dependendo da configuração fornecida. 

![Servidor](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelos de gerenciamento de chave de criptografia do lado do servidor

Cada um dos modelos de criptografia em repouso do lado do servidor implica características distintivas do gerenciamento de chaves. Isso inclui onde e como as chaves de criptografia são criadas e armazenadas, assim como os modelos de acesso e os procedimentos de rotação de chave. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Criptografia do lado do servidor utilizando chaves de serviço gerenciado

Para muitos clientes, o requisito essencial é garantir que os dados sejam criptografados sempre que estiver em repouso. A criptografia do lado do servidor utilizando chaves gerenciadas pelo serviço habilita esse modelo permitindo que os clientes marquem o recurso específico (Conta de Armazenamento, BD SQL etc.) para criptografia e deixando todos os aspectos de gerenciamento de chaves, como emissão de chave, rotação e backup, para a Microsoft. A maioria dos Serviços do Azure com suporte para criptografia em repouso, geralmente fornecem suporte para esse modelo de descarregamento do gerenciamento das chaves de criptografia para o Azure. O provedor de recursos do Azure cria as chaves, coloca-as em um armazenamento seguro e recupera as chaves quando necessário. Isso significa que o serviço possui acesso completo às chaves e o serviço tem o controle total sobre o gerenciamento do ciclo de vida de credenciais.

![gerenciado](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

A criptografia do lado do servidor utilizando chaves gerenciadas pelo serviço, portanto, soluciona rapidamente a necessidade de ter criptografia em repouso com baixa sobrecarga para o cliente. Quando disponível, um cliente normalmente abre o portal do Azure para a assinatura de destino e para o provedor de recursos e marca uma caixa indicando que gostaria que os dados fossem criptografados. Em alguns Gerenciadores de Recursos, a criptografia do lado do servidor com chaves gerenciadas pelo serviço é ativada por padrão.

A criptografia do lado do servidor com chaves gerenciadas pela Microsoft implica que o serviço tenha acesso completo para armazenar e gerenciar as chaves. Embora alguns clientes desejem gerenciar as chaves porque sentem que podem ter mais segurança, o custo e o risco associados a uma solução de armazenamento de chaves personalizada devem ser considerados ao avaliar esse modelo. Em muitos casos, uma organização pode determinar que as restrições de recursos ou os riscos de uma solução local podem ser maiores que o risco do gerenciamento em nuvem das chaves de criptografia em repouso.  No entanto, esse modelo pode não ser suficiente para organizações que têm requisitos para controlar a criação ou o ciclo de vida das chaves de criptografia ou requisitos para que a equipe que gerencia as chaves de criptografia do serviço seja diferente daquela que gerencia o serviço (ou seja, de segregar o gerenciamento de chaves do modelo de gerenciamento geral do serviço).

##### <a name="key-access"></a>Acesso à chave

Quando a criptografia do lado do servidor com chaves gerenciadas pelo serviço é usada, a criação, o armazenamento e o acesso ao serviço das chaves são gerenciados pelo serviço. Normalmente, os provedores de recursos do Azure fundamentais irão armazenar as Chaves de Criptografia de Dados em um repositório próximo aos dados e rapidamente disponíveis e acessíveis, enquanto as Chaves de Criptografia de Chave são armazenadas em um repositório interno seguro.

**Vantagens**

- Configuração simples
- A Microsoft gerencia a rotação, o backup e a redundância de chaves
- O cliente não tem o custo associado à implementação ou o risco de um esquema de gerenciamento de chaves personalizado.

**Desvantagens**

- Não há controle do cliente sobre as chaves de criptografia (especificação de chave, ciclo de vida, revogação, etc.)
- Nenhuma capacidade para segregar o gerenciamento de chaves do modelo de gerenciamento geral para o serviço

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Azure Key Vault 

Para cenários em que o requisito é criptografar os dados em repouso e controlar as chaves de criptografia, os clientes podem utilizar criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Key Vault. Alguns serviços podem armazenar apenas a Chave de Criptografia de Chave raiz no Azure Key Vault e armazenar a Chave de Criptografia de Dados criptografada em um local interno mais próximo dos dados. Nesse cenário, os clientes podem trazer suas próprias chaves para o Key Vault (BYOK - Traga Sua Própria Chave), ou gerar novas, e utilizá-las para criptografar os recursos desejados. Enquanto o Provedor de Recursos executa as operações de criptografia e descriptografia, ele utiliza a chave configurada como a chave raiz para todas as operações de criptografia. 

##### <a name="key-access"></a>Acesso à chave

O modelo de criptografia do lado do servidor com chaves gerenciadas pelo cliente no Azure Key Vault envolve o acesso das chaves pelo serviço para criptografar e descriptografar, conforme necessário. As chaves de criptografia em repouso são acessíveis para um serviço através de uma política de controle de acesso. Essa política concede o acesso de identidade de serviço para receber a chave. Um serviço do Azure executado em nome de uma assinatura associada pode ser configurado com uma identidade dentro dessa assinatura. O serviço pode executar a autenticação do Azure Active Directory e receber um token de autenticação identificando-se como esse serviço agindo em nome da assinatura. Esse token pode, então, ser apresentado ao Key Vault para obter uma chave para a qual tenha tido acesso.

Para operações que utilizam chaves de criptografia, uma identidade do serviço pode ter acesso a quaisquer das seguintes operações: descriptografar, criptografar, unwrapKey, wrapKey, verificar, assinar, obter, listar, atualizar, criar, importar, excluir, fazer backup e restaurar.

Para obter uma chave para utilizar em criptografia ou descriptografia de dados em repouso, a identidade do serviço que a instância de serviço do Gerenciador de Recursos executará como deverá ter UnwrapKey (para obter a chave para descriptografar) e WrapKey (para inserir uma chave no cofre de chaves ao criar uma nova chave).


>[!NOTE] 
>Para obter mais detalhes sobre a autorização do Key Vault, consulte a página segura do cofre de chaves na [documentação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Vantagens**

- Controle total sobre as chaves utilizadas – as chaves de criptografia são gerenciadas no Key Vault do cliente sob o controle do cliente.
- Capacidade para criptografar vários serviços para um mestre
- É possível separar o gerenciamento de chaves do modelo de gerenciamento geral para o serviço
- É possível definir o serviço e a localização de chave entre regiões

**Desvantagens**

- O cliente tem total responsabilidade pelo gerenciamento de acesso de chave
- O cliente tem total responsabilidade pelo gerenciamento do ciclo de vida da chave
- Configuração adicional e sobrecarga de configuração

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Criptografia no servidor utilizando chaves gerenciadas pelo serviço em hardware controlado pelo cliente

Alguns serviços do Azure habilitam o modelo de gerenciamento de chaves HYOK (hospede sua própria chave). Este modo de gerenciamento é útil em cenários em que há uma necessidade de criptografar dados em repouso e gerenciar as chaves em um repositório proprietário fora do controle da Microsoft. Nesse modelo, o serviço deve recuperar a chave de um site externo. Garantias de desempenho e de disponibilidade são afetadas, e a configuração é ainda mais complexa. Além disso, uma vez que o serviço tenha acesso ao DEK durante as operações de criptografia e descriptografia, as garantias de segurança gerais desse modelo serão semelhantes a quando as chaves são gerenciadas pelo cliente no Azure Key Vault.  Como resultado, esse modelo não é apropriado para a maioria das organizações, exceto se possuírem requisitos específicos de gerenciamento de chaves. Devido a essas limitações, a maioria dos Serviços do Azure não é compatível com a criptografia no servidor que usa chaves gerenciadas por servidor em hardware controlado pelo cliente.

##### <a name="key-access"></a>Acesso à chave

No caso da criptografia no lado do servidor que usa chaves gerenciadas pelo serviço em hardware controlado pelo cliente, as chaves são mantidas em um sistema configurado pelo cliente. Os serviços do Azure com suporte para esse modelo fornecem um meio de estabelecer uma conexão segura com um repositório de chaves fornecido pelo cliente.

**Vantagens**

- Controle total sobre a chave raiz utilizada – as chaves de criptografia são gerenciadas por um repositório fornecido pelo cliente
- Capacidade para criptografar vários serviços para um mestre
- É possível separar o gerenciamento de chaves do modelo de gerenciamento geral para o serviço
- É possível definir o serviço e a localização de chave entre regiões

**Desvantagens**

- Total responsabilidade pela segurança, desempenho, disponibilidade e armazenamento de chaves
- Total responsabilidade pelo gerenciamento de acesso à chave
- Total responsabilidade pelo gerenciamento do ciclo de vida da chave
- Configuração significativa, configuração e custos de manutenção contínuos
- Aumento da dependência da disponibilidade de rede entre o datacenter do cliente e os datacenters do Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Criptografia em repouso em serviços em nuvem da Microsoft

Os serviços do Microsoft Cloud são utilizados em todos os três modelos de nuvem: IaaS, PaaS, SaaS. A seguir, são apresentados exemplos de como ajustam-se em cada modelo:

- Serviços de software, designados como Software como Servidor ou SaaS, que possuem aplicativos fornecidos pela nuvem, como o Office 365.
- Serviços de plataforma nos quais os clientes aproveitam a nuvem em seus aplicativos, utilizando-a para armazenamento, análise e funcionalidade de barramento de serviço.
- Serviços de infraestrutura, ou IaaS (Infraestrutura como Serviço), em que o cliente implanta sistemas operacionais e aplicativos hospedados na nuvem e que possivelmente usam outros serviços de nuvem.

### <a name="encryption-at-rest-for-saas-customers"></a>Criptografia em repouso para clientes SaaS

Os clientes de Software como Serviço (SaaS), geralmente possuem criptografia em repouso habilitada ou disponível em cada serviço. O Office 365 tem várias opções para que os clientes verifiquem ou habilitem criptografia em repouso. Para obter informações sobre os serviços do Office 365, confira [Criptografia no Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Criptografia em repouso para clientes de PaaS

Os dados do cliente de Plataforma como Serviço (PaaS) normalmente residem em um ambiente de execução de aplicativos e qualquer Provedor de Recursos do Azure utilizado para armazenar dados do cliente. Para ver as opções de criptografia em repouso disponíveis, examine a tabela abaixo para as plataformas de aplicativos e armazenamento que você usa. Onde houver suporte, serão fornecidos links para instruções sobre como habilitar a Criptografia em Repouso para cada provedor de recursos. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Criptografia em repouso para clientes de IaaS

Os clientes de Infraestrutura como Serviço (IaaS) podem ter uma variedade de serviços e aplicativos em uso. Os serviços de IaaS podem habilitar a criptografia em repouso em suas máquinas virtuais hospedadas no Azure e VHDs utilizando o Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Armazenamento criptografado

Como PaaS, as soluções de IaaS podem aproveitar outros serviços do Azure que armazenam dados criptografados em repouso. Nestes casos, é possível habilitar o suporte para criptografia em repouso, conforme fornecido por cada serviço do Azure consumido. A tabela abaixo enumera as principais plataformas de aplicativos, serviços e armazenamento e o modelo de Criptografia em Repouso compatível. Onde houver suporte, serão fornecidos links para instruções sobre como habilitar a Criptografia em Repouso. 

#### <a name="encrypted-compute"></a>Computação criptografada

Uma solução completa de Criptografia em Repouso exige que os dados nunca sejam persistidos em forma não criptografada. Enquanto estiver em uso, em um servidor que carrega os dados na memória, os dados podem ser persistidos localmente de várias maneiras, incluindo o arquivo de página do Windows, um despejo de memória e quaisquer registros em log que o aplicativo possa executar. Para garantir que esses dados sejam criptografados em repouso, os aplicativos de IaaS podem usar o Azure Disk Encryption em uma máquina virtual de IaaS do Azure (Windows ou Linux) e no disco virtual. 

#### <a name="custom-encryption-at-rest"></a>Criptografia em repouso personalizada

É recomendável, sempre que possível, que os aplicativos de IaaS utilizem as opções de Criptografia em Repouso e o Azure Disk Encryption fornecidos por quaisquer serviços do Azure consumidos. Em alguns casos, como requisitos de criptografia irregulares ou armazenamento não baseado em Azure, um desenvolvedor de um aplicativo de IaaS pode precisar implementar criptografia em repouso. Os desenvolvedores das soluções de IaaS podem integra-se melhor com o gerenciamento do Azure e as expectativas dos clientes, aproveitando determinados componentes do Azure. Especificamente, os desenvolvedores devem utilizar o serviço do Azure Key Vault para fornecer armazenamento de chaves seguras, além de fornecer aos seus clientes opções de gerenciamento de chaves consistentes com a maioria dos serviços da plataforma do Azure. Além disso, as soluções personalizadas devem usar as Identidades de Serviço Gerenciado do Azure para permitir que as contas do serviço acessem chaves de criptografia. Para obter informações de desenvolvedor sobre o Azure Key Vault e as Identidades de Serviço Gerenciado, confira seus respectivos SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Suporte ao modelo de criptografia dos provedores de recursos do Azure

Os serviços do Microsoft Azure oferecem suporte para um ou mais dos modelos de criptografia em repouso. Para alguns serviços, no entanto, um ou mais dos modelos de criptografia podem não ser aplicáveis. Para serviços que dão suporte a cenários de chave gerenciada pelo cliente, eles somente podem dar suporte a um subconjunto dos tipos de chave cujo Azure Key Vault dá suporte para chaves de criptografia de chaves. Além disso, os serviços podem liberar suporte a esses cenários e tipos de chaves em diferentes agendamentos. Esta seção descreve o suporte de criptografia em repouso no momento desta escrita para cada um dos principais serviços de armazenamento de dados do Azure.

### <a name="azure-disk-encryption"></a>Criptografia de disco do Azure

Qualquer cliente que utiliza recursos de IaaS (Infraestrutura como Serviço) pode alcançar criptografia em repouso para seus discos e VMs de IaaS através do Azure Disk Encryption. Para obter mais informações sobre o Azure Disk Encryption, confira a [documentação do Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Armazenamento do Azure

Todos os serviços de Armazenamento do Azure (armazenamento de Blob, armazenamento de fila, armazenamento de tabelas e arquivos do Azure) oferecem suporte para a criptografia do servidor em repouso, com alguns serviços de suporte a chaves gerenciados pelo cliente e criptografia do cliente.  

- Lado do servidor: Todos os serviços de Armazenamento do Microsoft Azure habilitam a criptografia do servidor por padrão que usa chaves de serviço gerenciado, que é transparente para o aplicativo. Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/storage-service-encryption). O Armazenamento de Blobs do Azure e os Arquivos do Azure também dão suporte a chaves gerenciadas pelo cliente RSA de 2048 bits no Azure Key Vault. Para obter mais informações, consulte [Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).
- Lado do cliente: Blobs do Azure, tabelas e filas oferecem suporte para criptografia de cliente. Ao usar criptografia do cliente, os clientes criptografam os dados e carregam os dados como um blob criptografado. O gerenciamento de chaves é feito pelo cliente. Para obter mais informações, consulte [Criptografia do lado do cliente e Azure Key Vault para o Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure dá suporte à criptografia em repouso para cenários de criptografia gerenciada pela Microsoft no lado do cliente e no lado do serviço.

O suporte para criptografia do servidor atualmente é fornecido através do recurso SQL chamado Transparent Data Encryption. Quando um cliente do Banco de Dados SQL do Azure habilita a chave de TDE, elas são criadas e gerenciadas automaticamente. A criptografia em repouso pode ser habilitada nos níveis de servidor e banco de dados. A partir de junho de 2017, a [TDE (Transparent Data Encryption)](https://msdn.microsoft.com/library/bb934049.aspx) estará habilitada por padrão em bancos de dados criados recentemente. O Banco de Dados SQL do Azure dá suporte a chaves gerenciadas pelo cliente RSA de 2048 bits no Azure Key Vault. Para obter mais informações, consulte [Transparent Data Encryption com suporte para Bring Your Own Key para Banco de Dados SQL do Azure e Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

A criptografia do cliente dos dados do Banco de Dados SQL do Azure tem suporte por meio do recurso [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Always Encrypted utiliza uma chave criada e armazenada pelo cliente. Os clientes podem armazenar a chave mestre em um repositório de certificados do Windows, no Azure Key Vault ou em um Módulo de Segurança de Hardware local. Utilizando o SQL Server Management Studio, os usuários de SQL escolhem que chave gostariam de usar para criptografar determinada coluna.

|                                  |                    | **Modelo de criptografia e gerenciamento de chaves** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Lado do servidor usando chave de serviço gerenciado**     | **Lado do servidor usando gerenciado pelo cliente no Key Vault**             | **Lado do cliente usando gerenciado pelo cliente**      |
| **Armazenamento e banco de dados**        |                    |                    |                    |
| Disco (IaaS)                      | -                  | Sim, RSA 2048-bit  | -                  |
| SQL Server (IaaS)                | Sim                | Sim, RSA 2048-bit  | Sim                |
| Banco de Dados SQL/SQL Data Warehouse do Azure | Sim                | Sim, RSA 2048-bit  | Sim                |
| Instância Gerenciada do (Banco de Dados SQL do Azure) | Sim                | Versão prévia, RSA de 2048 bits  | Sim                |
| Armazenamento do Microsoft Azure (Blobs de páginas e Bloco) | Sim                | Sim, RSA 2048-bit  | Sim                |
| Armazenamento do Microsoft Azure (Arquivos)            | Sim                | Sim, RSA 2048-bit  | -                  |
| Armazenamento do Microsoft Azure (Tabelas, Consultas)   | Sim                | -                  | Sim                |
| Cosmos DB (DocumentDB)          | Sim                | -                  | -                  |
| StorSimple                       | Sim                | -                  | Sim                |
| Backup                           | Sim                | -                  | Sim                |
| **Inteligência e Análise**   |                    |                    |                    |
| Fábrica de dados do Azure               | Sim                | -                  | -                  |
| Azure Machine Learning           | -                  | Versão prévia, RSA de 2048 bits | -                  |
| Stream Analytics do Azure           | Sim                | -                  | -                  |
| HDInsight (Armazenamento de Blobs do Azure)   | Sim                | -                  | -                  |
| HDInsight (Armazenamento de Data Lake)    | Sim                | -                  | -                  |
| Apache Kafka para HDInsight       | Sim                | Visualização, todos os comprimentos de RSA | -                  |
| Repositório Azure Data Lake            | Sim                | Sim, RSA 2048-bit  | -                  |
| Catálogo de Dados do Azure               | Sim                | -                  | -                  |
| Power BI                         | Sim                | -                  | -                  |
| **Serviços de IoT**                 |                    |                    |                    |
| Hub IoT                          | -                  | -                  | Sim                |
| Barramento de Serviço                      | Sim                | -                  | Sim                |
| Hubs de Eventos                       | Sim                | -                  | -                  |
| Grade de Eventos                       | Sim                | -                  | -                  |


## <a name="conclusion"></a>Conclusão

A proteção dos dados do cliente armazenados nos Serviços do Azure é de fundamental importância para a Microsoft. Todos os serviços hospedados do Azure estão comprometido em fornecer opções de criptografia em repouso. Os serviços fundamentais, como o Armazenamento do Azure, o Banco de Dados SQL do Azure e os principais serviços de análise e de inteligência já fornecem opções de criptografia em repouso. Alguns desses serviços dão suporte a chaves controladas pelo cliente e criptografia no cliente, assim como criptografia e chaves de serviço gerenciado. Os serviços do Microsoft Azure estão aprimorando amplamente a disponibilidade de Criptografia em Repouso e novas opções estarão planejadas para versão prévia e disponibilidade geral nos próximos meses.
