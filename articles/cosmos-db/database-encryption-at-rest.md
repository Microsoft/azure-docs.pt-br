---
title: Criptografia em repouso do Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece criptografia de dados em repouso e como ela é implementada.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: abe4bb3a53e3177857cb8041fd255ec916985054
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93089858"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Criptografia de dados do Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A criptografia em repouso é uma frase que normalmente se refere à criptografia de dados em dispositivos de armazenamento não volátil, como SSDs (unidades de estado sólido) e HDDs (discos rígidos). O Cosmos DB armazena seus bancos de dados principais em SSDs. Seus anexos de mídia e backups são armazenados no armazenamento de Blobs do Azure, do qual geralmente é feito backup por HDDs. Com o lançamento da criptografia em repouso no Cosmos DB, todos os bancos de dados, anexos de mídia e backups são criptografados. Os dados agora são criptografados em trânsito (pela rede) e em repouso (armazenamento não volátil), fornecendo criptografia de ponta a ponta.

Como um serviço PaaS, o Azure Cosmos DB é muito fácil de usar. Como todos os dados de usuário armazenados no Azure Cosmos DB são criptografados em repouso e transporte, você não precisa realizar nenhuma ação. Em outras palavras, a criptografia em repouso é "ativada" por padrão. Não existem controles para ativá-lo ou desativá-lo. O Azure Cosmos DB usa a criptografia AES-256 em todas as regiões onde a conta está em execução. Fornecemos esse recurso enquanto continuamos atendendo a nossos [SLAs de desempenho e disponibilidade](https://azure.microsoft.com/support/legal/sla/cosmos-db). Os dados armazenados em sua conta do Azure Cosmos são criptografados de forma automática e direta, com as chaves gerenciadas pela Microsoft (chaves gerenciadas pelo serviço). Opcionalmente, você pode optar por adicionar uma segunda camada de criptografia com suas próprias chaves, conforme descrito no artigo [chaves gerenciadas pelo cliente](how-to-setup-cmk.md).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementação de criptografia em repouso para Azure Cosmos DB

A criptografia em repouso é implementada usando várias tecnologias de segurança, incluindo sistemas seguros de armazenamento de chaves, redes criptografadas e APIs criptográficas. Os sistemas que descriptografam e processam dados precisam se comunicar com sistemas que gerenciam chaves. O diagrama mostra como o armazenamento de dados criptografados e o gerenciamento de chaves são separados. 

:::image type="content" source="./media/database-encryption-at-rest/design-diagram.png" alt-text="Diagrama de design" border="false":::

O fluxo básico de uma solicitação de usuário é o seguinte:
- A conta do banco de dados do usuário é preparada e as chaves de armazenamento são recuperadas por meio de uma solicitação ao Provedor de Recursos do Serviço de Gerenciamento.
- Um usuário cria uma conexão ao Cosmos DB por meio de HTTPS/transporte seguro. (Os SDKs abstraem os detalhes).
- O usuário envia um documento JSON para ser armazenado pela conexão segura criada anteriormente.
- O documento JSON é indexado, a menos que o usuário tenha desativado a indexação.
- O documento JSON e os dados de índice são gravados no armazenamento seguro.
- Periodicamente, os dados são lidos no armazenamento seguro e o backup é feito Repositório de Blobs Criptografados do Azure.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P: Quanto custa a mais o Armazenamento do Azure se a Criptografia do Serviço de Armazenamento for habilitada?
A: Não há nenhum custo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: Quem gerencia as chaves de criptografia?
A: As chaves são gerenciadas pela Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: Com que frequência as chaves de criptografia são trocadas?
A: A Microsoft tem um conjunto de diretrizes internas para rotação de chave de criptografia, que o Cosmos DB segue. As diretrizes específicas não são publicadas. A Microsoft publica o [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx), que é visto como um subconjunto de orientação interna e tem práticas recomendadas úteis para desenvolvedores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: Posso usar minhas próprias chaves de criptografia?
R: Sim, este recurso agora está disponível para novas contas de Azure Cosmos DB e isso deve ser feito no momento da criação da conta. Consulte o documento [chaves gerenciadas pelo cliente](./how-to-setup-cmk.md) para obter mais informações.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: Em quais regiões a criptografia está ativada?
A: A criptografia está ativada para todos os dados do usuário em todas as regiões do Azure Cosmos DB.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: A criptografia afeta os SLAs de latência de desempenho e produtividade?
A: Não há nenhum impacto ou alteração nos SLAs de desempenho agora que a criptografia em repouso está habilitada em todas as contas novas e existentes. Você pode ler mais na página [SLA do Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) para ver as garantias mais recentes.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: O emulador local dá suporte à criptografia em repouso?
A: O emulador é uma ferramenta de desenvolvimento/teste independente e não usa os serviços de gerenciamento de chaves utilizados pelo serviço gerenciado do Cosmos DB. Nossa recomendação é habilitar o BitLocker em unidades em que você está armazenando dados confidenciais de teste do emulador. O [emulador dá suporte à alteração do diretório de dados padrão](local-emulator.md), bem como ao uso de um local conhecido.

## <a name="next-steps"></a>Próximas etapas

* Você pode optar por adicionar uma segunda camada de criptografia com suas próprias chaves; para saber mais, consulte o artigo [chaves gerenciadas pelo cliente](how-to-setup-cmk.md).
* Para ter uma visão geral da segurança do Cosmos DB e as últimas melhorias, consulte [Segurança do Azure Cosmos DB](database-security.md).
* Para obter mais informações sobre as certificações da Microsoft, confira a [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).