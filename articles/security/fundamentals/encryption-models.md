---
title: Modelos de criptografia de dados no Microsoft Azure
description: Este artigo fornece uma visão geral dos modelos de criptografia de dados no Microsoft Azure.
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
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: 141daa485fae5aba2db23647fada30ba5b621cd0
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854607"
---
# <a name="data-encryption-models"></a>Modelos de criptografia de dados

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

Os modelos de criptografia do lado do servidor referem-se à criptografia que é executada pelo serviço do Azure. Nesse modelo, o Provedor de Recursos executa as operações de criptografia e descriptografia. Por exemplo, o Armazenamento do Azure pode receber dados em operações de texto sem formatação e executará a criptografia e descriptografia internamente. O Provedor de Recursos pode utilizar chave de criptografia que são gerenciadas pela Microsoft ou pelo cliente, dependendo da configuração fornecida.

![Servidor](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Cada um dos modelos de criptografia em repouso do lado do servidor implica características distintivas do gerenciamento de chaves. Isso inclui onde e como as chaves de criptografia são criadas e armazenadas, assim como os modelos de acesso e os procedimentos de rotação de chave.  

Para criptografia do lado do cliente, considere o seguinte:

- Os serviços do Azure não podem ver dados descriptografados
- Os clientes gerenciam e armazenam chaves no local (ou em outros repositórios seguros). As chaves não estão disponíveis para os serviços do Azure
- Funcionalidade reduzida na nuvem

Os modelos de criptografia com suporte no Azure são divididos em dois grupos principais: "criptografia de cliente" e "criptografia do lado do servidor", conforme mencionado anteriormente. Independentemente do modelo de criptografia em repouso utilizado, os serviços do Azure recomendam sempre o uso de um transporte seguro, como TLS ou HTTPS. Portanto, a criptografia em transporte deve ser abordada pelo protocolo de transporte e não deve ser um fator importante na determinação do modelo de criptografia em repouso a ser utilizado.

## <a name="client-encryption-model"></a>Modelo de criptografia de cliente

O modelo de Criptografia de Cliente refere-se à criptografia que é realizada fora do Provedor de Recursos ou Azure pelo aplicativo de chamada ou serviço. A criptografia pode ser realizada pelo aplicativo de serviço no Azure ou por um aplicativo em execução no data center do cliente. Em ambos os casos, ao aproveitar esse modelo de criptografia, o Provedor de Recursos do Azure recebe um blob de dados criptografado sem a capacidade de descriptografar os dados de maneira nenhuma ou ter acesso às chaves de criptografia. Nesse modelo, o gerenciamento de chaves é feito pelo aplicativo de chamada/serviço e é opaco para o serviço do Azure.

![Cliente](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Criptografia do lado do servidor utilizando chaves de serviço gerenciado

Para muitos clientes, o requisito essencial é garantir que os dados sejam criptografados sempre que estiver em repouso. A criptografia do lado do servidor utilizando chaves gerenciadas pelo serviço habilita esse modelo permitindo que os clientes marquem o recurso específico (Conta de Armazenamento, BD SQL etc.) para criptografia e deixando todos os aspectos de gerenciamento de chaves, como emissão de chave, rotação e backup, para a Microsoft. A maioria dos serviços do Azure que dão suporte à criptografia em repouso normalmente dá suporte a esse modelo de descarregamento do gerenciamento das chaves de criptografia para o Azure. O provedor de recursos do Azure cria as chaves, coloca-as em um armazenamento seguro e recupera as chaves quando necessário. Isso significa que o serviço possui acesso completo às chaves e o serviço tem o controle total sobre o gerenciamento do ciclo de vida de credenciais.

![gerenciado](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

A criptografia do lado do servidor utilizando chaves gerenciadas pelo serviço, portanto, soluciona rapidamente a necessidade de ter criptografia em repouso com baixa sobrecarga para o cliente. Quando disponível, um cliente normalmente abre o portal do Azure para a assinatura de destino e para o provedor de recursos e marca uma caixa indicando que gostaria que os dados fossem criptografados. Em alguns Gerenciadores de Recursos, a criptografia do lado do servidor com chaves gerenciadas pelo serviço é ativada por padrão.

A criptografia do lado do servidor com chaves gerenciadas pela Microsoft implica que o serviço tenha acesso completo para armazenar e gerenciar as chaves. Embora alguns clientes desejem gerenciar as chaves porque sentem que podem ter mais segurança, o custo e o risco associados a uma solução de armazenamento de chaves personalizada devem ser considerados ao avaliar esse modelo. Em muitos casos, uma organização pode determinar que as restrições de recursos ou os riscos de uma solução local podem ser maiores que o risco do gerenciamento em nuvem das chaves de criptografia em repouso.  No entanto, esse modelo pode não ser suficiente para organizações que têm requisitos para controlar a criação ou o ciclo de vida das chaves de criptografia ou para que pessoas diferentes gerenciem chaves de criptografia de um serviço do que aquelas que gerenciam o serviço (ou seja, segregação de gerenciamento de chaves do modelo de gerenciamento geral para o serviço).

### <a name="key-access"></a>Acesso à chave

Quando a criptografia do lado do servidor com chaves gerenciadas pelo serviço é usada, a criação, o armazenamento e o acesso ao serviço das chaves são gerenciados pelo serviço. Normalmente, os provedores de recursos do Azure fundamentais irão armazenar as Chaves de Criptografia de Dados em um repositório próximo aos dados e rapidamente disponíveis e acessíveis, enquanto as Chaves de Criptografia de Chave são armazenadas em um repositório interno seguro.

**Vantagens**

- Configuração simples
- A Microsoft gerencia a rotação, o backup e a redundância de chaves
- O cliente não tem o custo associado à implementação ou o risco de um esquema de gerenciamento de chaves personalizado.

**Desvantagens**

- Não há controle do cliente sobre as chaves de criptografia (especificação de chave, ciclo de vida, revogação, etc.)
- Nenhuma capacidade para segregar o gerenciamento de chaves do modelo de gerenciamento geral para o serviço

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Azure Key Vault

Para cenários em que o requisito é criptografar os dados em repouso e controlar as chaves de criptografia, os clientes podem utilizar criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Key Vault. Alguns serviços podem armazenar apenas a Chave de Criptografia de Chave raiz no Azure Key Vault e armazenar a Chave de Criptografia de Dados criptografada em um local interno mais próximo dos dados. Nesse cenário, os clientes podem trazer suas próprias chaves para o Key Vault (BYOK - Traga Sua Própria Chave), ou gerar novas, e utilizá-las para criptografar os recursos desejados. Enquanto o provedor de recursos executa as operações de criptografia e descriptografia, ele usa a chave de criptografia de chave configurada como a chave raiz para todas as operações de criptografia.

A perda de chaves de criptografia de chave significa perda de dados. Por esse motivo, as chaves não devem ser excluídas. O backup das chaves deve ser feito sempre que for criado ou girado. [A exclusão reversível](../../key-vault/general/soft-delete-overview.md) deve ser habilitada em qualquer cofre que armazene chaves de criptografia de chave. Em vez de excluir uma chave, defina habilitado como falso ou defina a data de expiração.

### <a name="key-access"></a>Acesso à chave

O modelo de criptografia do lado do servidor com chaves gerenciadas pelo cliente no Azure Key Vault envolve o acesso das chaves pelo serviço para criptografar e descriptografar, conforme necessário. As chaves de criptografia em repouso são acessíveis para um serviço através de uma política de controle de acesso. Essa política concede o acesso de identidade de serviço para receber a chave. Um serviço do Azure executado em nome de uma assinatura associada pode ser configurado com uma identidade dentro dessa assinatura. O serviço pode executar a autenticação do Azure Active Directory e receber um token de autenticação identificando-se como esse serviço agindo em nome da assinatura. Esse token pode, então, ser apresentado ao Key Vault para obter uma chave para a qual tenha tido acesso.

Para operações que utilizam chaves de criptografia, uma identidade do serviço pode ter acesso a quaisquer das seguintes operações: descriptografar, criptografar, unwrapKey, wrapKey, verificar, assinar, obter, listar, atualizar, criar, importar, excluir, fazer backup e restaurar.

Para obter uma chave para utilizar em criptografia ou descriptografia de dados em repouso, a identidade do serviço que a instância de serviço do Gerenciador de Recursos executará como deverá ter UnwrapKey (para obter a chave para descriptografar) e WrapKey (para inserir uma chave no cofre de chaves ao criar uma nova chave).

>[!NOTE]
>Para obter mais detalhes sobre a autorização do Key Vault, consulte a página segura do cofre de chaves na [documentação do Azure Key Vault](../../key-vault/general/secure-your-key-vault.md).

**Vantagens**

- Controle total sobre as chaves usadas – as chaves de criptografia são gerenciadas no Key Vault do cliente sob o controle do cliente.
- Capacidade para criptografar vários serviços para um mestre
- É possível separar o gerenciamento de chaves do modelo de gerenciamento geral para o serviço
- É possível definir o serviço e a localização de chave entre regiões

**Desvantagens**

- O cliente tem total responsabilidade pelo gerenciamento de acesso de chave
- O cliente tem total responsabilidade pelo gerenciamento do ciclo de vida da chave
- Configuração adicional e sobrecarga de configuração

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Criptografia do lado do servidor usando chaves gerenciadas pelo cliente em hardware controlado pelo cliente

Alguns serviços do Azure habilitam o modelo de gerenciamento de chaves HYOK (hospede sua própria chave). Esse modo de gerenciamento é útil em cenários em que há a necessidade de criptografar os dados em repouso e gerenciar as chaves em um repositório proprietário fora do controle da Microsoft. Nesse modelo, o serviço deve recuperar a chave de um site externo. Garantias de desempenho e de disponibilidade são afetadas, e a configuração é ainda mais complexa. Além disso, uma vez que o serviço tenha acesso ao DEK durante as operações de criptografia e descriptografia, as garantias de segurança gerais desse modelo serão semelhantes a quando as chaves são gerenciadas pelo cliente no Azure Key Vault.  Como resultado, esse modelo não é apropriado para a maioria das organizações, exceto se possuírem requisitos específicos de gerenciamento de chaves. Devido a essas limitações, a maioria dos serviços do Azure não oferece suporte à criptografia do lado do servidor usando chaves gerenciadas pelo servidor em hardware controlado pelo cliente.

### <a name="key-access"></a>Acesso à chave

Quando a criptografia do lado do servidor usando chaves gerenciadas pelo serviço no hardware controlado pelo cliente é usada, as chaves são mantidas em um sistema configurado pelo cliente. Os serviços do Azure com suporte para esse modelo fornecem um meio de estabelecer uma conexão segura com um repositório de chaves fornecido pelo cliente.

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

## <a name="supporting-services"></a>Serviços de suporte
Os serviços do Azure que oferecem suporte a cada modelo de criptografia:

| Produto, recurso ou serviço | Lado do servidor usando chave de serviço gerenciado   | Server-Side usando Customer-Managed chave | Client-Side usando Client-Managed chave  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **IA e Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Sim                | Sim                | -                  |
| Serviços Cognitivos do Azure         | Sim                | Sim                | -                  |
| Azure Machine Learning           | Sim                | Sim                | -                  |
| Azure Machine Learning Studio (clássico) | Sim         | Versão prévia, RSA de 2048 bits | -               |
| Content Moderator                | Sim                | Sim                | -                  |
| Face                             | Sim                | Sim                | -                  |
| Reconhecimento Vocal           | Sim                | Sim                | -                  |
| Personalizador                     | Sim                | Sim                | -                  |
| QnA Maker                        | Sim                | Sim                | -                  |
| Serviços de Fala                  | Sim                | Sim                | -                  |
| Tradução de Texto                  | Sim                | Sim                | -                  |
| Power BI                         | Sim                | Sim, RSA 4096 bits  | -                  |
| **Análise**                    |                    |                    |                    |
| Stream Analytics do Azure           | Sim                | Sim\*\*            | -                  |
| Hubs de Eventos                       | Sim                | Sim                | -                  |
| Funções                        | Sim                | Sim                | -                  |
| Azure Analysis Services          | Sim                | -                  | -                  |
| Catálogo de Dados do Azure               | Sim                | -                  | -                  |
| Azure HDInsight                  | Sim                | Tudo                | -                  |
| Azure Monitor Application Insights | Sim                | Sim                | -                  |
| Azure Monitor Log Analytics      | Sim                | Sim                | -                  |
| Azure Data Explorer              | Sim                | Sim                | -                  |
| Fábrica de dados do Azure               | Sim                | Sim                | -                  |
| Repositório Azure Data Lake            | Sim                | Sim, RSA 2048-bit  | -                  |
| **Contêineres**                   |                    |                    |                    |
| Serviço de Kubernetes do Azure         | Sim                | Sim                | -                  |
| Instâncias de Contêiner              | Sim                | Sim                | -                  |
| Registro de Contêiner               | Sim                | Sim                | -                  |
| **Compute**                      |                    |                    |                    |
| Máquinas Virtuais                 | Sim                | Sim                | -                  |
| Conjunto de dimensionamento de máquinas virtuais        | Sim                | Sim                | -                  |
| SAP HANA                         | Sim                | Sim                | -                  |
| Serviço de Aplicativo                      | Sim                | Sim\*\*            | -                  |
| Automação                       | Sim                | Sim\*\*            | -                  |
| Funções do Azure                  | Sim                | Sim\*\*            | -                  |
| Portal do Azure                     | Sim                | Sim\*\*            | -                  |
| Aplicativos Lógicos                       | Sim                | Sim                | -                  |
| Aplicativos gerenciados pelo Azure       | Sim                | Sim\*\*            | -                  |
| Barramento de Serviço                      | Sim                | Sim                | -                  |
| Site Recovery                    | Sim                | Sim                | -                  |
| **Bancos de dados**                    |                    |                    |                    |
| SQL Server em Máquinas Virtuais   | Sim                | Sim                | Sim                |
| Banco de Dados SQL do Azure               | Sim                | Sim, RSA 3072 bits  | Sim                |
| Banco de dados SQL do Azure para MariaDB   | Sim                | -                  | -                  |
| Banco de dados SQL do Azure para MySQL     | Sim                | Sim                | -                  |
| Banco de dados SQL do Azure para PostgreSQL | Sim               | Sim                | -                  |
| Azure Synapse Analytics          | Sim                | Sim, RSA 3072 bits  | -                  |
| SQL Server Stretch Database      | Sim                | Sim, RSA 3072 bits  | Sim                |
| Armazenamento de Tabelas                    | Sim                | Sim                | Sim                |
| Azure Cosmos DB                  | Sim                | Sim                | -                  |
| Azure Databricks                 | Sim                | Sim                | -                  |
| Serviço de Migração de Banco de Dados do Azure | Sim                | N/D\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Sim                | -                  | Sim                |
| Azure Repos                      | Sim                | -                  | Sim                |
| **Identidade**                     |                    |                    |                    |
| Azure Active Directory           | Sim                | -                  | -                  |
| Azure Active Directory Domain Services | Sim          | Sim                | -                  |
| **Integração**                  |                    |                    |                    |
| Barramento de Serviço                      | Sim                | Sim                | Sim                |
| Grade de Eventos                       | Sim                | -                  | -                  |
| Gerenciamento de API                   | Sim                | -                  | -                  |
| **Serviços de IoT**                 |                    |                    |                    |
| Hub IoT                          | Sim                | Sim                | Sim                |
| Provisionamento de dispositivos no Hub IoT      | Sim                | Sim                | -                  |
| **Gerenciamento e Governança**    |                    |                    |                    |
| Azure Site Recovery              | Sim                | -                  | -                  |
| Migrações para Azure                    | Sim                | Sim                | -                  |
| **Mídia**                        |                    |                    |                    |
| Serviços de mídia                   | Sim                | Sim                | Sim                |
| **Segurança**                     |                    |                    |                    |
| Central de Segurança do Azure para IoT    | Sim                | Sim                | -                  |
| Azure Sentinel                   | Sim                | Sim                | -                  |
| **Storage**                      |                    |                    |                    |
| Armazenamento de Blobs                     | Sim                | Sim                | Sim                |
| Armazenamento de blob Premium             | Sim                | Sim                | Sim                |
| Armazenamento em Disco                     | Sim                | Sim                | -                  |
| Ultra Armazenamento em Disco               | Sim                | Sim                | -                  |
| Armazenamento em Disco gerenciados             | Sim                | Sim                | -                  |
| Armazenamento de Arquivos                     | Sim                | Sim                | -                  |
| Armazenamento Premium de arquivo             | Sim                | Sim                | -                  |
| Sincronização de Arquivos                        | Sim                | Sim                | -                  |
| Armazenamento de Filas                    | Sim                | Sim                | Sim                |
| Avere vFXT                       | Sim                | -                  | -                  |
| Cache Redis do Azure            | Sim                | N/D\*              | -                  |
| Azure NetApp Files               | Sim                | Sim                | -                  |
| Armazenamento de Arquivos                  | Sim                | Sim                | -                  |
| StorSimple                       | Sim                | Sim                | Sim                |
| Serviço de Backup do Azure                     | Sim                | Sim                | Sim                |
| Data Box                         | Sim                | -                  | Sim                |
| Data Box Edge                    | Sim                | Sim                | -                  |

\* Esse serviço não mantém os dados. Os caches transitórios, se houver, são criptografados com uma chave da Microsoft.

\*\* Esse serviço oferece suporte ao armazenamento de dados em seu próprio Key Vault, conta de armazenamento ou outros serviços de persistência de dados que já dão suporte à criptografia de Server-Side com Customer-Managed chave.

## <a name="next-steps"></a>Próximas etapas

- Saiba como a [criptografia é usada no Azure](encryption-overview.md).
- Saiba como o Azure usa a [criptografia dupla](double-encryption.md) para atenuar as ameaças que vêm com a criptografia de dados.
