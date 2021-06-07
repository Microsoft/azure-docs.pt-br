---
title: Criptografia do Armazenamento do Azure para dados em repouso
description: O armazenamento do Azure protege seus dados criptografando-os automaticamente antes de mantê-los para a nuvem. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia dos dados em sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 03/23/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 0688e14b77d885132d6c3fbaa44bed117cc7cf9d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641130"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Criptografia do Armazenamento do Azure para dados em repouso

O armazenamento do Azure usa a SSE (criptografia do lado do servidor) para criptografar automaticamente os dados quando eles são persistidos para a nuvem. A criptografia de armazenamento do Azure protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização.

## <a name="about-azure-storage-encryption"></a>Sobre a criptografia de armazenamento do Azure

Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. A criptografia de armazenamento do Azure é semelhante à criptografia BitLocker no Windows.

A criptografia de armazenamento do Azure está habilitada para todas as contas de armazenamento, incluindo as contas do Resource Manager e do armazenamento clássico. A criptografia de armazenamento do Azure não pode ser desabilitada. Como os dados são protegidos por padrão, você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia de armazenamento do Azure.

Os dados em uma conta de armazenamento são criptografados independentemente do nível de desempenho (Standard ou Premium), camada de acesso (quente ou frio) ou modelo de implantação (Azure Resource Manager ou clássico). Todos os BLOBs na camada de arquivo também são criptografados. Todas as opções de redundância de armazenamento do Azure dão suporte à criptografia e todos os dados nas regiões primária e secundária são criptografados quando a replicação geográfica está habilitada. Todos os recursos de armazenamento do Azure são criptografados, incluindo BLOBs, discos, arquivos, filas e tabelas. Todos os metadados de objeto também são criptografados. Não há nenhum custo adicional para a criptografia de armazenamento do Azure.

Todos os blobs de blocos, BLOB de acréscimo ou BLOB de páginas que foram gravados no armazenamento do Azure após 20 de outubro de 2017 são criptografados. Os BLOBs criados antes dessa data continuam a ser criptografados por um processo em segundo plano. Para forçar a criptografia de um blob que foi criado antes de 20 de outubro de 2017, você pode reescrever o blob. Para saber como verificar o status de criptografia de um blob, consulte [verificar o status de criptografia de um blob](../blobs/storage-blob-encryption-status.md).

Para obter mais informações sobre os módulos de criptografia subjacentes à criptografia de armazenamento do Azure, consulte [API de criptografia: próxima geração](/windows/desktop/seccng/cng-portal).

Para obter informações sobre criptografia e gerenciamento de chaves para o Azure Managed disks, consulte [criptografia do servidor de Azure Managed disks](../../virtual-machines/disk-encryption.md).

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Os dados em uma nova conta de armazenamento são criptografados com chaves gerenciadas pela Microsoft por padrão. Você pode continuar a contar com chaves gerenciadas pela Microsoft para a criptografia de seus dados ou pode gerenciar a criptografia com suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, terá duas opções. Você pode usar qualquer um dos dois tipos de gerenciamento de chaves, ou ambos:

- Você pode especificar uma *chave gerenciada pelo cliente* a ser usada para criptografar e descriptografar dados no armazenamento de BLOBs e nos arquivos do Azure. <sup>1, 2</sup> as chaves gerenciadas pelo cliente devem ser armazenadas em Azure Key Vault ou Azure Key Vault modelo de segurança de hardware gerenciado (HSM) (versão prévia). Para obter mais informações sobre chaves gerenciadas pelo cliente, consulte [usar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure](./customer-managed-keys-overview.md).
- Você pode especificar uma *chave fornecida pelo cliente* em operações de armazenamento de BLOBs. Um cliente que faz uma solicitação de leitura ou gravação no armazenamento de blob pode incluir uma chave de criptografia na solicitação de controle granular sobre como os dados de blob são criptografados e descriptografados. Para obter mais informações sobre chaves fornecidas pelo cliente, consulte [fornecer uma chave de criptografia em uma solicitação para o armazenamento de BLOBs](../blobs/encryption-customer-provided-keys.md).

A tabela a seguir compara as principais opções de gerenciamento de criptografia do armazenamento do Azure.

| Parâmetro de gerenciamento de chaves | Chaves gerenciadas pela Microsoft | Chaves gerenciadas pelo cliente | Chaves fornecidas pelo cliente |
|--|--|--|--|
| Operações de criptografia/descriptografia | Azure | Azure | Azure |
| Serviços de armazenamento do Azure com suporte | Tudo | Armazenamento de BLOBs, arquivos do Azure<sup>1, 2</sup> | Armazenamento de blob |
| Armazenamento de chave | Repositório de chaves da Microsoft | Azure Key Vault ou Key Vault HSM | Repositório de chaves próprio do cliente |
| Responsabilidade de rotação de chave | Microsoft | Cliente | Cliente |
| Controle de chave | Microsoft | Cliente | Cliente |

<sup>1</sup> para obter informações sobre como criar uma conta que ofereça suporte ao uso de chaves gerenciadas pelo cliente com o armazenamento de filas, consulte [criar uma conta que dê suporte a chaves gerenciadas pelo cliente para filas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> para obter informações sobre como criar uma conta que ofereça suporte ao uso de chaves gerenciadas pelo cliente com o armazenamento de tabelas, consulte [criar uma conta que dê suporte a chaves gerenciadas pelo cliente para tabelas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> As chaves gerenciadas pela Microsoft são giradas adequadamente de acordo com os requisitos de conformidade. Se você tiver requisitos específicos de rotação de chaves, a Microsoft recomenda que você mova para chaves gerenciadas pelo cliente para que você possa gerenciar e auditar a rotação por conta própria.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Criptografar dados duplicados com criptografia de infraestrutura

Os clientes que precisam de altos níveis de garantia de que seus dados são seguros também podem habilitar a criptografia AES de 256 bits no nível de infraestrutura de armazenamento do Azure. Quando a criptografia de infraestrutura está habilitada, os dados em uma conta de armazenamento são criptografados duas vezes &mdash; uma vez no nível de serviço e uma vez no nível de infraestrutura &mdash; com dois algoritmos de criptografia diferentes e duas chaves diferentes. A criptografia dupla de dados do armazenamento do Azure protege contra um cenário em que um dos algoritmos ou chaves de criptografia pode ser comprometido. Nesse cenário, a camada adicional de criptografia continua a proteger seus dados.

A criptografia de nível de serviço dá suporte ao uso de chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente com Azure Key Vault. A criptografia no nível de infraestrutura depende de chaves gerenciadas pela Microsoft e sempre usa uma chave separada.

Para obter mais informações sobre como criar uma conta de armazenamento que habilita a criptografia de infraestrutura, consulte [criar uma conta de armazenamento com criptografia de infraestrutura habilitada para criptografia dupla de dados](infrastructure-encryption-enable.md).

## <a name="next-steps"></a>Próximas etapas

- [O que é o Cofre da Chave do Azure?](../../key-vault/general/overview.md)
- [Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure](customer-managed-keys-overview.md)
- [Escopos de criptografia para armazenamento de BLOBs](../blobs/encryption-scope-overview.md)
- [Fornecer uma chave de criptografia em uma solicitação para o armazenamento de BLOBs](../blobs/encryption-customer-provided-keys.md)
