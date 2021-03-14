---
title: Criptografia do Armazenamento do Azure para dados em repouso
description: O armazenamento do Azure protege seus dados criptografando-os automaticamente antes de mantê-los para a nuvem. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia dos dados em sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b2471ccd2a412c7cbae9d4e59412ac055697e3d7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180353"
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
| Serviços de armazenamento do Azure com suporte | Todos | Armazenamento de BLOBs, arquivos do Azure<sup>1, 2</sup> | Armazenamento de blob |
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

## <a name="encryption-scopes-for-blob-storage-preview"></a>Escopos de criptografia para armazenamento de BLOBs (visualização)

Por padrão, uma conta de armazenamento é criptografada com uma chave que tem o escopo definido para a conta de armazenamento. Você pode optar por usar chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente armazenadas em Azure Key Vault para proteger e controlar o acesso à chave que criptografa os dados.

Os escopos de criptografia permitem que você gerencie opcionalmente a criptografia no nível do contêiner ou de um blob individual. Você pode usar escopos de criptografia para criar limites seguros entre os dados que residem na mesma conta de armazenamento, mas que pertencem a clientes diferentes.

Você pode criar um ou mais escopos de criptografia para uma conta de armazenamento usando o provedor de recursos de armazenamento do Azure. Ao criar um escopo de criptografia, você especifica se o escopo é protegido com uma chave gerenciada pela Microsoft ou com uma chave gerenciada pelo cliente que é armazenada em Azure Key Vault. Escopos de criptografia diferentes na mesma conta de armazenamento podem usar chaves gerenciadas pela Microsoft ou gerenciadas pelo cliente.

Depois de criar um escopo de criptografia, você pode especificar esse escopo de criptografia em uma solicitação para criar um contêiner ou um blob. Para obter mais informações sobre como criar um escopo de criptografia, consulte [criar e gerenciar escopos de criptografia (versão prévia)](../blobs/encryption-scope-manage.md).

> [!NOTE]
> Não há suporte para escopos de criptografia com o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) e contas de armazenamento com redundância de acesso de leitura (RA-GZRS) durante a visualização.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> A visualização dos escopos de criptografia é destinada somente ao uso de não produção. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente.
>
> Para evitar custos inesperados, certifique-se de desabilitar os escopos de criptografia que não são necessários no momento.

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Criar um contêiner ou BLOB com um escopo de criptografia

Os BLOBs criados sob um escopo de criptografia são criptografados com a chave especificada para esse escopo. Você pode especificar um escopo de criptografia para um blob individual ao criar o BLOB ou pode especificar um escopo de criptografia padrão ao criar um contêiner. Quando um escopo de criptografia padrão é especificado no nível de um contêiner, todos os BLOBs nesse contêiner são criptografados com a chave associada ao escopo padrão.

Quando você cria um blob em um contêiner que tem um escopo de criptografia padrão, você pode especificar um escopo de criptografia que substitui o escopo de criptografia padrão se o contêiner estiver configurado para permitir substituições do escopo de criptografia padrão. Para evitar substituições do escopo de criptografia padrão, configure o contêiner para negar substituições para um blob individual.

As operações de leitura em um blob que pertence a um escopo de criptografia ocorrem de forma transparente, desde que o escopo de criptografia não esteja desabilitado.

### <a name="disable-an-encryption-scope"></a>Desabilitar um escopo de criptografia

Quando você desabilita um escopo de criptografia, todas as operações de leitura ou gravação subsequentes feitas com o escopo de criptografia falharão com o código de erro HTTP 403 (proibido). Se você reabilitar o escopo de criptografia, as operações de leitura e gravação continuarão normalmente novamente.

Quando um escopo de criptografia estiver desabilitado, você não será mais cobrado por ele. Desabilite os escopos de criptografia que não são necessários para evitar encargos desnecessários.

Se o escopo de criptografia estiver protegido com chaves gerenciadas pelo cliente para Azure Key Vault, você também poderá excluir a chave associada no cofre de chaves para desabilitar o escopo de criptografia. Tenha em mente que as chaves gerenciadas pelo cliente no Azure Key Vault são protegidas pela exclusão reversível e pela proteção de limpeza, e uma chave excluída está sujeita ao comportamento definido por essas propriedades. Para obter mais informações, consulte um dos seguintes tópicos na documentação do Azure Key Vault:

- [Como usar a exclusão reversível com o PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Como usar a exclusão temporária com a CLI](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> Não é possível excluir um escopo de criptografia.

## <a name="next-steps"></a>Próximas etapas

- [O que é o Cofre da Chave do Azure?](../../key-vault/general/overview.md)
- [Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure](customer-managed-keys-overview.md)
- [Escopos de criptografia para armazenamento de BLOBs (visualização)](../blobs/encryption-scope-overview.md)
