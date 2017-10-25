---
title: Criptografia de dados em repouso no Microsoft Azure | Microsoft Docs
description: "Este artigo fornece uma visão geral da criptografia de dados em repouso no Microsoft Azure, os recursos gerais e as considerações gerais."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: yurid
ms.openlocfilehash: 53b6f03d43b5525e5c5dea42e6a9a36042b65d52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-encryption-at-rest"></a>Criptografia de dados em repouso no Azure
Há várias ferramentas no Microsoft Azure para proteger os dados de acordo com as necessidades de segurança e a conformidade de sua empresa. Este artigo concentra-se em como os dados são protegidos em repouso no Microsoft Azure, discute os vários componentes que participam da implementação da proteção de dados e revisa os prós e contras das diferentes abordagens de proteção de gerenciamento de chaves. 

A criptografia em repouso é um requisito de segurança comum. Um benefício do Microsoft Azure é que as organizações podem alcançar a criptografia em repouso sem ter o custo de implementação e gerenciamento e o risco de uma solução de gerenciamento de chaves personalizada. As organizações têm a opção de permitir que o Azure gerencie completamente a criptografia em repouso. Além disso, as organizações possuem várias opções para gerenciar de maneira rigorosa a criptografia ou as chaves de criptografia.

## <a name="what-is-encryption-at-rest"></a>O que é criptografia em repouso?
A criptografia em Repouso refere-se à codificação criptográfica (criptografia) de dados quando é persistente. Os designs de Criptografia em Repouso no Azure utilizam criptografia simétrica para criptografar e descriptografar grandes quantidades de dados rapidamente de acordo com um modelo conceitual simples:

- Uma chave de criptografia simétrica é utilizada para criptografar dados, pois é persistente 
- A mesma chave de criptografia é utilizada para descriptografar esses dados conforme estiver pronto para uso na memória
- Os dados podem ser particionados e diferentes chaves podem ser utilizadas para cada partição
- As chaves devem ser armazenadas em um local seguro, com políticas de controle de acesso limitando o acesso a determinadas identidades e o uso da chave de registro em log. As chaves de criptografia de dados são frequentemente criptografadas com criptografia assimétrica para limitar ainda mais o acesso (discutido posteriormente neste artigo em *Hierarquia de Chave*)

Acima, descreve-se os elementos de alto nível de Criptografia em Repouso comuns. Na prática, os principais cenários de controle e gerenciamento, assim como garantias de disponibilidade e escala, requerem construções adicionais. Os conceitos e componentes de Criptografia em Repouso no Microsoft Azure são descritos abaixo.

## <a name="the-purpose-of-encryption-at-rest"></a>A finalidade da criptografia em repouso
A criptografia em repouso destina-se a fornecer proteção de dados para dados em repouso (conforme descrito acima). Os ataques contra dados em repouso incluem tentativas de obter acesso físico ao hardware em que os dados são armazenados e, em seguida, comprometer os dados contidos. Nesse ataque, o disco rígido de um servidor pode ter sido manipulado incorretamente durante a manutenção, permitindo que um invasor remova o disco rígido. Mais tarde, o invasor poderia colocar o disco rígido em um computador sob seu controle para tentar acessar os dados. 

A criptografia em repouso é projetada para impedir que o invasor acesse os dados não criptografados, assegurando que os dados sejam criptografados quando em disco. Se um invasor pudesse obter um disco rígido com esses dados criptografados e nenhum acesso às chaves de criptografia, o invasor não comprometia os dados sem grande dificuldade. Nesse cenário, um invasor teria que tentar ataques contra dados criptografados que são muito mais complexos e consomem recursos, do que acessar dados não criptografados em um disco rígido. Por esse motivo, a criptografia em repouso é altamente recomendada e é um requisito de alta prioridade para muitas organizações. 

Em alguns casos, a criptografia em repouso também é requerida pela necessidade de uma organização para governança de dados e esforços de conformidade. As regulamentações governamentais e do setor como HIPAA, PCI e FedRAMP, e os requisitos regulamentares internacionais, estabelecem proteções específicas por meio de processos e políticas relacionados à proteção de dados e requisitos de criptografia. Para muitos dessas regulamentações, a criptografia em repouso é uma medida obrigatória necessária para proteção e gerenciamento de dados compatíveis. 

Além dos requisitos de regulamentação e conformidade, a criptografia em repouso deve ser percebida como um recurso de plataforma de defesa em profundidade. Embora a Microsoft ofereça uma plataforma compatível para serviços, aplicativos e dados, instalações abrangentes e segurança física, controle de acesso a dados e auditoria, é importante fornecer medidas de segurança "sobrepostas" adicionais no caso de uma das outras medidas de segurança falhar. A criptografia em repouso fornece esse mecanismo de defesa adicional.

A Microsoft está comprometida em oferecer opções de criptografia em repouso em todos os serviços em nuvem e fornecer aos clientes uma capacidade de gerenciamento de chaves de criptografia e acesso a registros adequados, mostrando quando as chaves de criptografia são utilizada. Além disso, a Microsoft está trabalhando para atingir a meta de tornar todos os dados do cliente criptografados em repouso por padrão.

## <a name="azure-encryption-at-rest-components"></a>Componentes da Criptografia em Repouso no Azure

Conforme descrito anteriormente, a meta da criptografia em repouso é que os dados que são persistentes no disco sejam criptografados com uma chave de criptografia secreta. Para atingir essa meta, é necessário fornecer criação de chave segura, armazenamento, controle de acesso e gerenciamento das chaves de criptografia. Embora os detalhes possam variar, os serviços de implementações de Criptografia em Repouso do Azure podem ser descritos em termos dos conceitos abaixo que são, então, ilustrados no diagrama a seguir.

![Componentes](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Cofre da Chave do Azure

O local de armazenamento das chaves de criptografia e controle de acesso a essas chaves é central para um modelo de criptografia em repouso. As chaves precisam ser altamente seguras, mas gerenciáveis por usuários especificados e disponíveis para serviços específicos. Para os serviços do Azure, o Azure Key Vault é a solução de armazenamento de chave recomendada e fornece uma experiência de gerenciamento comum em todos os serviços. As chaves são armazenadas e gerenciadas em cofres de chaves e o acesso a um cofre de chave pode ser fornecido para usuários ou serviços. O Azure Key Vault fornece suporte a criação de chaves do cliente ou importação de chaves do cliente para uso em cenários de chave de criptografia de cliente gerenciado.

### <a name="azure-active-directory"></a>Azure Active Directory

Permissões para utilizar as chaves armazenadas no Azure Key Vault, seja para gerenciá-las ou acessá-las para criptografia e descriptografia da Criptografia em Repouso, podem ser fornecidas para contas do Azure Active Directory. 

### <a name="key-hierarchy"></a>Hierarquia de Chave

Geralmente, mais de uma chave de criptografia é utilizada em uma implementação de criptografia em repouso. A criptografia assimétrica é útil para estabelecer a confiança e a autenticação necessárias para acesso e gerenciamento de chaves. A criptografia simétrica é mais eficiente para criptografia e descriptografia em massa, permitindo uma criptografia mais forte e melhor desempenho. Além disso, limitar o uso de uma chave de criptografia única diminui o risco de que a chave seja comprometida e o custo de nova criptografia quando uma chave precisar ser substituída. Para aproveitar os benefícios da criptografia assimétrica e simétrica e limitar o uso e a exposição de uma chave única, os modelos de criptografia em repouso do Azure utilizam uma hierarquia de chave composta pelos seguintes tipos de chaves:

- **DEK (Chave de Criptografia de Dados)** – Uma chave simétrica AES256 utilizada para criptografar uma partição ou bloco de dados.  Um recurso único pode ter muitas partições e muitas Chaves de Criptografia de Dados. Criptografar cada bloco de dados com uma chave diferente torna os ataques de análise de criptografia mais difíceis. O acesso a DEKs é necessário pelo provedor de recursos ou instância do aplicativo que criptografa e descriptografa um bloco específico. Quando um DEK é substituído por uma nova chave, apenas os dados em seu bloco associado devem ser reciclados com a nova chave.
- **KEK (Chave de Criptografia de Chave)** – Uma chave de criptografia assimétrica utilizada para criptografar as Chaves de Criptografia de Dados. O uso de uma Chave de Criptografia de Chave permite que as chaves de criptografia de dados sejam criptografadas e controladas. A entidade que tem acesso ao KEK pode ser diferente da entidade que requer o DEK. Isso permite que um agente administre o acesso ao DEK com a finalidade de garantir o acesso limitado de cada DEK para a partição específica. Uma vez que o KEK é necessário para descriptografar os DEKs, o KEK é efetivamente um ponto único pelo qual os DEKs podem ser efetivamente excluídos pela exclusão do KEK.

As Chaves de Criptografia de Dados, criptografadas com as Chaves de Criptografia de Chave são armazenadas separadamente e somente uma entidade com acesso à Chave de Criptografia de Chave pode obter quaisquer Chaves de Criptografia de Dados com essa chave. Há suporte para diferentes modelos de armazenamento de chaves. Cada modelo será discutido com mais detalhes na próxima seção.

## <a name="data-encryption-models"></a>Modelos de Criptografia de Dados

Compreender os vários modelos de criptografia e seus prós e contras é fundamental para entender como os vários provedores de recursos no Azure implementam a criptografia em repouso. Essas definições são compartilhadas em todos os provedores de recursos no Azure para garantir linguagem e taxonomia comuns. 

Há três cenários para criptografia do lado do servidor:

- Criptografia do lado do servidor utilizando chaves de Serviço Gerenciado
    - Provedores de recursos do Azure executam as operações de criptografia e descriptografia
    - A Microsoft gerencia as chaves
    - Funcionalidade completa na nuvem

- Criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Azure Key Vault
    - Provedores de recursos do Azure executam as operações de criptografia e descriptografia
    - O cliente controla as chaves por meio do Azure Key Vault
    - Funcionalidade completa na nuvem

- Criptografia do lado do servidor utilizando chaves gerenciadas pelo cliente em hardware controlado pelo cliente
    - Provedores de recursos do Azure executam as operações de criptografia e descriptografia
    - Chaves de controle do cliente no hardware controlado pelo cliente
    - Funcionalidade completa na nuvem

Para criptografia do lado do cliente, considere o seguinte:

- Os serviços do Azure não podem ver dados descriptografados
- Os clientes gerenciam e armazenam chaves no local (ou em outros repositórios seguros). As chaves não estão disponíveis para os serviços do Azure
- Funcionalidade reduzida na nuvem

Os modelos de criptografia com suporte no Azure dividem-se em dois grupos principais: "Criptografia do Cliente" e "Criptografia do lado do Servidor", conforme mencionado anteriormente. Observe que, independentemente do modelo de criptografia em repouso utilizado, os serviços do Azure recomendam sempre o uso de um transporte seguro, como TLS ou HTTPS. Portanto, a criptografia em transporte deve ser abordada pelo protocolo de transporte e não deve ser um fator importante na determinação do modelo de criptografia em repouso a ser utilizado.

### <a name="client-encryption-model"></a>Modelo de criptografia de cliente

O modelo de Criptografia de Cliente refere-se à criptografia que é realizada fora do Provedor de Recursos ou Azure pelo aplicativo de chamada ou serviço. A criptografia pode ser realizada pelo aplicativo de serviço no Azure ou por um aplicativo em execução no data center do cliente. Em ambos os casos, ao aproveitar esse modelo de criptografia, o Provedor de Recursos do Azure recebe um blob de dados criptografado sem a capacidade de descriptografar os dados de maneira nenhuma ou ter acesso às chaves de criptografia. Nesse modelo, o gerenciamento de chaves é feito pelo aplicativo de chamada/serviço e é completamente opaco para o serviço do Azure.

![Cliente](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modelo de criptografia do lado do servidor

Os modelos de criptografia do lado do servidor referem-se à criptografia que é executada pelo serviço do Azure. Nesse modelo, o Provedor de Recursos executa as operações de criptografia e descriptografia. Por exemplo, o Armazenamento do Azure pode receber dados em operações de texto sem formatação e executará a criptografia e descriptografia internamente. O Provedor de Recursos pode utilizar chave de criptografia que são gerenciadas pela Microsoft ou pelo cliente, dependendo da configuração fornecida. 

![Servidor](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelos de gerenciamento de chave de criptografia do lado do servidor

Cada um dos modelos de criptografia em repouso do lado do servidor implica características distintivas do gerenciamento de chaves. Isso inclui onde e como as chaves de criptografia são criadas e armazenadas, assim como os modelos de acesso e os procedimentos de rotação de chave. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Criptografia do lado do servidor utilizando chaves de serviço gerenciado

Para muitos clientes, o requisito essencial é garantir que os dados sejam criptografados sempre que estiver em repouso. A criptografia do lado do servidor utilizando Chaves de Serviço Gerenciado habilita esse modelo, permitindo que os clientes marquem o recurso específico (Conta de Armazenamento, BD SQL, etc.) para criptografia e deixando todos os aspectos de gerenciamento de chaves, como emissão de chave, rotação e backup para a Microsoft. A maioria dos Serviços do Azure com suporte para criptografia em repouso, geralmente fornecem suporte para esse modelo de descarregamento do gerenciamento das chaves de criptografia para o Azure. O provedor de recursos do Azure cria as chaves, coloca-as em um armazenamento seguro e recupera as chaves quando necessário. Isso significa que o serviço possui acesso completo às chaves e o serviço tem o controle total sobre o gerenciamento do ciclo de vida de credenciais.

![gerenciado](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

A criptografia do lado do servidor utilizando chaves de serviço gerenciado, portanto, aborda rapidamente a necessidade de ter criptografia em repouso com baixa sobrecarga para o cliente. Quando disponível, um cliente normalmente abre o portal do Azure para a assinatura de destino e provedor de recursos e verifica uma caixa indicando que gostaria que os dados fossem criptografados. Em algumas criptografias do lado do servidor de Gerenciadores de Recursos com chaves de serviço gerenciado são ativadas por padrão. 

A criptografia do lado do servidor com as chaves gerenciadas da Microsoft implica que o serviço possui acesso completo para armazenar e gerenciar as chaves. Embora alguns clientes desejam gerenciar as chaves porque sentem que podem garantir uma maior segurança, o custo e o risco associados a uma solução de armazenamento de chaves personalizada devem ser considerados ao avaliar esse modelo. Em muitos casos, uma organização pode determinar que restrições de recursos ou riscos de uma solução local podem ser maiores que o risco de gerenciamento de nuvem das chaves de criptografia em repouso.  No entanto, esse modelo pode não ser suficiente para as organizações que possuem requisitos para controlar a criação ou o ciclo de vida das chaves de criptografia ou têm uma equipe diferente para gerenciar chaves de criptografia do serviço do que aquelas que gerenciam o serviço (ou seja, segregação de gerenciamento de chaves do modelo de gerenciamento geral para o serviço).

##### <a name="key-access"></a>Acesso à chave

Quando a criptografia do lado do Servidor com chaves de Serviço Gerenciado for utilizada, a criação de chave, o acesso ao serviço e armazenamento serão gerenciados pelo serviço. Normalmente, os provedores de recursos do Azure fundamentais irão armazenar as Chaves de Criptografia de Dados em um repositório próximo aos dados e rapidamente disponíveis e acessíveis, enquanto as Chaves de Criptografia de Chave são armazenadas em um repositório interno seguro.

**Vantagens**

- Configuração simples
- A Microsoft gerencia backup, redundância e rotação de chave
- O cliente não tem o custo associado à implementação ou o risco de um esquema de gerenciamento de chaves personalizado.

**Desvantagens**

- Não há controle do cliente sobre as chaves de criptografia (especificação de chave, ciclo de vida, revogação, etc.)
- Nenhuma capacidade para segregar o gerenciamento de chaves do modelo de gerenciamento geral para o serviço

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia do lado do servidor utilizando chaves de cliente gerenciado no Azure Key Vault 

Para cenários em que o requisito é criptografar os dados em repouso e controlar as chaves de criptografia, os clientes podem utilizar criptografia do lado do servidor usando as Chaves de Cliente Gerenciado no Key Vault. Alguns serviços podem armazenar apenas a Chave de Criptografia de Chave raiz no Azure Key Vault e armazenar a Chave de Criptografia de Dados criptografada em um local interno mais próximo dos dados. Nesse cenário, os clientes podem trazer suas próprias chaves para o Key Vault (BYOK - Traga Sua Própria Chave), ou gerar novas, e utilizá-las para criptografar os recursos desejados. Enquanto o Provedor de Recursos executa as operações de criptografia e descriptografia, ele utiliza a chave configurada como a chave raiz para todas as operações de criptografia. 

##### <a name="key-access"></a>Acesso à chave

O modelo de criptografia do lado do servidor com chaves de cliente gerenciado no Azure Key Vault envolve o serviço acessando as chaves para criptografar e descriptografar, conforme necessário. As chaves de criptografia em repouso são acessíveis para um serviço através de uma política de controle de acesso que concede esse acesso de identidade do serviço para receber a chave. Um serviço do Azure executado em nome de uma assinatura associada pode ser configurado com uma identidade para esse serviço dentro dessa assinatura. O serviço pode executar a autenticação do Azure Active Directory e receber um token de autenticação identificando-se como esse serviço agindo em nome da assinatura. Esse token pode, então, ser apresentado ao Key Vault para obter uma chave para a qual tenha tido acesso.

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

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Criptografia do lado do servidor utilizando chaves de serviço gerenciado em hardware controlado pelo cliente

Para cenários onde o requisito é criptografar os dados em repouso e gerenciar as chaves em um repositório proprietário fora do controle da Microsoft, alguns serviços do Azure habilitam o modelo de gerenciamento de chaves HYOK (Hospede Sua Própria Chave). Nesse modelo, o serviço deve recuperar a chave de um site externo e, portanto, as garantias de desempenho e disponibilidade são afetadas e a configuração é mais complexa. Além disso, uma vez que o serviço tenha acesso ao DEK durante as operações de criptografia e descriptografia, as garantias de segurança gerais desse modelo são semelhantes quando as chaves são gerenciadas pelo cliente no Azure Key Vault.  Como resultado, esse modelo não é apropriado para a maioria das organizações, exceto se possuírem requisitos específicos de gerenciamento de chaves que o exijam. Devido a essas limitações, a maioria dos Serviços do Azure não fornece suporte para criptografia do lado do servidor utilizando chaves gerenciadas por servidor em hardware controlado pelo cliente.

##### <a name="key-access"></a>Acesso à chave

Quando a criptografia do lado do servidor utilizando chaves de serviço gerenciado em hardware controlado pelo cliente, as chaves utilizadas são mantidas em um sistema configurado pelo cliente. Os serviços do Azure com suporte para esse modelo fornecem um meio de estabelecer uma conexão segura com um repositório de chaves fornecido pelo cliente.

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
- Serviços de plataforma que os clientes aproveitam a nuvem em seus aplicativos, utilizando a nuvem para armazenamento, análise e funcionalidade de barramento de serviço.
- Serviços de infraestrutura, ou Infraestrutura como Serviço (IaaS), em que o cliente implanta sistemas operacionais e aplicativos hospedados na nuvem e possivelmente aproveitando outros serviços em nuvem.

### <a name="encryption-at-rest-for-saas-customers"></a>Criptografia em repouso para clientes SaaS

Os clientes de Software como Serviço (SaaS), geralmente possuem criptografia em repouso habilitada ou disponível em cada serviço. Os serviços do Office 365 possuem várias opções para que os clientes verifiquem ou habilitem criptografia em repouso. Para obter informações sobre os serviços do Office 365, consulte Tecnologias de Criptografia de Dados para Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>Criptografia em repouso para clientes de PaaS

Os dados do cliente de Plataforma como Serviço (PaaS) normalmente residem em um ambiente de execução de aplicativos e qualquer Provedor de Recursos do Azure utilizado para armazenar dados do cliente. Para consultar as opções de criptografia em repouso disponíveis, examine a tabela abaixo para as plataformas de aplicativos e armazenamento que você utiliza. Onde houver suporte, serão fornecidos links para instruções sobre como habilitar a Criptografia em Repouso para cada provedor de recursos. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Criptografia em repouso para clientes de IaaS

Os clientes de Infraestrutura como Serviço (IaaS) podem ter uma variedade de serviços e aplicativos em uso. Os serviços de IaaS podem habilitar a criptografia em repouso em suas máquinas virtuais hospedadas no Azure e VHDs utilizando o Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Armazenamento criptografado

Como PaaS, as soluções de IaaS podem aproveitar outros serviços do Azure que armazenam dados criptografados em repouso. Nestes casos, é possível habilitar o suporte para criptografia em repouso, conforme fornecido por cada serviço do Azure consumido. A tabela abaixo enumera as principais plataformas de aplicativos, serviços e armazenamento e o modelo de Criptografia em Repouso com suporte. Onde houver suporte, serão fornecidos links para instruções sobre como habilitar a Criptografia em Repouso. 

#### <a name="encrypted-compute"></a>Computação criptografada

Uma solução completa de Criptografia em Repouso exige que os dados nunca sejam persistidos em forma não criptografada. Enquanto estiver em uso, em um servidor que carrega os dados na memória, os dados podem ser persistidos localmente de várias maneiras, incluindo o arquivo de página do Windows, um despejo de memória e quaisquer registros em log que o aplicativo possa executar. Para garantir que esses dados sejam criptografados em repouso, os aplicativos de IaaS podem utilizar o Azure Disk Encryption em uma máquina virtual de IaaS do Azure (Windows ou Linux) e no disco virtual. 

#### <a name="custom-encryption-at-rest"></a>Criptografia em repouso personalizada

É recomendável, sempre que possível, que os aplicativos de IaaS utilizem as opções de Criptografia em Repouso e o Azure Disk Encryption fornecidos por quaisquer serviços do Azure consumidos. Em alguns casos, como requisitos de criptografia irregulares ou armazenamento não baseado em Azure, um desenvolvedor de um aplicativo de IaaS pode precisar implementar criptografia em repouso. Os desenvolvedores das soluções de IaaS podem integra-se melhor com o gerenciamento do Azure e as expectativas dos clientes, aproveitando determinados componentes do Azure. Especificamente, os desenvolvedores devem utilizar o serviço do Azure Key Vault para fornecer armazenamento de chaves seguras, além de fornecer aos seus clientes opções de gerenciamento de chaves consistentes com a maioria dos serviços da plataforma do Azure. Além disso, as soluções personalizadas devem utilizar as Identidades de Serviço Gerenciado do Azure para permitir que as contas do serviço acessem chaves de criptografia. Para informações de desenvolvedor sobre o Azure Key Vault e as Identidades de Serviço Gerenciado, consulte seus respectivos SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Suporte ao modelo de criptografia dos provedores de recursos do Azure

Os serviços do Microsoft Azure oferecem suporte para um ou mais dos modelos de criptografia em repouso. Para alguns serviços, no entanto, um ou mais dos modelos de criptografia podem não ser aplicáveis. Além disso, os serviços podem liberar suporte para esses cenários em horários diferentes. Esta seção descreve o suporte de criptografia em repouso no momento desta escrita para cada um dos principais serviços de armazenamento de dados do Azure.

### <a name="azure-disk-encryption"></a>Criptografia de disco do Azure

Qualquer cliente que utiliza recursos de IaaS (Infraestrutura como Serviço) pode alcançar criptografia em repouso para seus discos e VMs de IaaS através do Azure Disk Encryption. Para obter mais informações sobre o Azure Disk Encryption, consulte a [documentação do Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Armazenamento do Azure

O Arquivo e Blob do Azure oferecem suporte de criptografia em repouso para cenários criptografados do lado do servidor, assim como dados criptografados do cliente (criptografia do lado do cliente).

- Lado do servidor: os clientes que utilizam o armazenamento de Blobs do Azure podem habilitar a criptografia em repouso em cada conta do recurso de armazenamento do Azure. Quando a criptografia do lado do servidor é feita de forma transparente para o aplicativo. Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso ](https://docs.microsoft.com/azure/storage/storage-service-encryption).
- Lado do cliente: a criptografia do lado do cliente de Blobs do Azure tem suporte. Quando clientes de criptografia do lado do cliente para criptografar os dados e carregar os dados como um blob criptografado. O gerenciamento de chaves é feito pelo cliente. Para obter mais informações, consulte [Criptografia do lado do cliente e Azure Key Vault para Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="sql-azure"></a>SQL Azure

Atualmente, o SQL Azure oferece suporte para criptografia em repouso para cenários de criptografia do lado do cliente e lado do serviço gerenciado da Microsoft.

O suporte para criptografia do servidor atualmente é fornecido através do recurso SQL chamado Transparent Data Encryption. Quando um cliente SQL Azure habilita a chave de TDE, são criadas e gerenciadas automaticamente. A criptografia em repouso pode ser habilitada nos níveis de servidor e banco de dados. A partir de junho de 2017, a [TDE (Transparent Data Encryption)](https://msdn.microsoft.com/library/bb934049.aspx) estará habilitada por padrão em bancos de dados recém-criados.

A criptografia do lado do cliente dos dados do SQL Azure possui suporte através do recurso [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Always Encrypted utiliza uma chave criada e armazenada pelo cliente. Os clientes podem armazenar a chave mestre em um repositório de certificados do Windows, no Azure Key Vault ou em um Módulo de Segurança de Hardware local. Utilizando o SQL Server Management Studio, os usuários de SQL escolhem que chave gostariam de usar para criptografar determinada coluna.

|                                  |                |                     | **Modelo de criptografia**             |                              |        |
|----------------------------------|----------------|---------------------|------------------------------|------------------------------|--------|
|                                  |                |                     |                              |                              | **Cliente** |
|                                  | **Gerenciamento de Chaves** | **Chaves de serviço gerenciado** | **Cliente gerenciado no Key Vault** | **Cliente gerenciado no local** |        |
| **Armazenamento e banco de dados**            |                |                     |                              |                              |        |
| Disco (IaaS)                      |                | -                   | Sim                          | Sim*                         | -      |
| SQL Server (IaaS)                |                | Sim                 | Sim                          | Sim                          | Sim    |
| SQL Azure (PaaS)                 |                | Sim                 | Visualização                      | -                            | Sim    |
| Armazenamento do Microsoft Azure (Blobs de páginas e Bloco) |                | Sim                 | Visualização                      | -                            | Sim    |
| Armazenamento do Microsoft Azure (Arquivos)            |                | Sim                 | -                            | -                            | -      |
| Armazenamento do Microsoft Azure (Tabelas, Consultas)   |                | -                   | -                            | -                            | Sim    |
| Cosmos DB (DocumentDB)          |                | Sim                 | -                            | -                            | -      |
| StorSimple                       |                | Sim                 | -                            | -                            | Sim    |
| Backup                           |                | -                   | -                            | -                            | Sim    |
| **Inteligência e Análise**       |                |                     |                              |                              |        |
| Fábrica de dados do Azure               |                | Sim                 | -                            | -                            | -      |
| Azure Machine Learning           |                | -                   | Visualização                      | -                            | -      |
| Stream Analytics do Azure           |                | Sim                 | -                            | -                            | -      |
| HDInsights (Armazenamento de Blobs)  |                | Sim                 | -                            | -                            | -      |
| HDInsights (Armazenamento de Data Lake)   |                | Sim                 | -                            | -                            | -      |
| Repositório Azure Data Lake            |                | Sim                 | Sim                          | -                            | -      |
| Catálogo de Dados do Azure               |                | Sim                 | -                            | -                            | -      |
| Power BI                         |                | Sim                 | -                            | -                            | -      |
| **Serviços de IoT**                     |                |                     |                              |                              |        |
| Hub IoT                          |                | -                   | -                            | -                            | Sim    |
| Barramento de Serviço                      |                | Sim (camada Premium)              | -                            | -                            | Sim    |
| Hubs de Eventos                       |                | Sim             | -                            | -                            | -      |


## <a name="conclusion"></a>Conclusão

A proteção dos dados do cliente armazenados nos Serviços do Azure é de fundamental importância para a Microsoft. Todos os serviços hospedados do Azure estão comprometido em fornecer opções de criptografia em repouso. Os serviços fundamentais, como o Armazenamento do Microsoft Azure, SQL Azure e os principais serviços de análise e inteligência, já oferecem opções de criptografia em repouso. Alguns desses serviços oferecem suporte para chaves controladas pelo cliente e criptografia do lado do cliente, assim como criptografia e chaves de serviço gerenciado. Os serviços do Microsoft Azure estão aprimorando amplamente a disponibilidade de Criptografia em Repouso e novas opções estarão planejadas para versão prévia e disponibilidade geral nos próximos meses.

