---
title: Como usar Importação/Exportação do Azure para transferir dados bidirecionalmente do armazenamento de blobs | Microsoft Docs
description: Saiba como criar trabalhos de importação e exportação no Portal do Azure para transferir dados de e para o Armazenamento do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b1f1560fc1a00577e1e4b30d922fc7d4cae0ab92
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181820"
---
# <a name="what-is-azure-importexport-service"></a>O que é o serviço de Importação/exportação do Azure?

O serviço de Importação/Exportação do Azure permite transferir com segurança grandes quantidades de dados para o armazenamento de blobs do Azure e arquivos do Azure por meio do envio de unidades de disco rígido para um data center do Azure. Este serviço também pode ser usado para transferir dados do armazenamento de Blobs do Azure para as unidades de disco e enviar para seu site local. Os dados de uma ou mais unidades de disco podem ser importados para o armazenamento de Blobs do Azure ou para os Arquivos do Azure.

Forneça suas próprias unidades de disco e transfira dados com o serviço de Importação/Exportação do Microsoft Azure. Também é possível usar unidades de disco fornecidas pela Microsoft.

Se você quiser transferir dados usando unidades de disco fornecidas pela Microsoft, é possível usar o [Azure Data Box Disk](../databox/data-box-disk-overview.md) para importar dados para o Azure. A Microsoft envia até 5 SSDs (unidades de disco de estado sólido) criptografadas com uma capacidade total de 40 TB por pedido, para seu datacenter através de uma operadora regional. É possível configurar rapidamente unidades de disco, copiar dados em unidades de disco por uma conexão USB 3.0 e enviar as unidades de disco de volta ao Azure. Para obter mais informações, acesse [Visão geral do Azure Data Box Disk](../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Casos de uso de importação/exportação do Azure

Considere o uso do serviço de Importação/Exportação do Azure quando o upload ou download dos dados pela rede estiver muito lento, ou quando a largura de banda de rede adicional for dispendiosa. Use esse serviço nos cenários a seguir:

* **Migração de dados para a nuvem**: mover grandes quantidades de dados para o Microsoft Azure de forma rápida e econômica.
* **Distribuição de conteúdo**: enviar dados rapidamente para seus sites de clientes.
* **Backup**: Faça backups de seus dados locais para armazenar no armazenamento do Azure.
* **Recuperação de dados**: recuperar uma grande quantidade de dados guardados no armazenamento e enviá-los para seu local.

## <a name="importexport-components"></a>Componentes de importação/exportação

O serviço de importação/exportação usa os seguintes componentes:

* **Serviço de Importação/Exportação**: esse serviço disponível no portal do Azure ajuda o usuário a criar e acompanhar trabalhos de importação (upload) e exportação (download) de dados.  

* **Ferramenta WAImportExport**: esta é uma ferramenta de linha de comando que faz o seguinte:
  * Prepara as unidades de disco que são enviadas para importação.
  * Facilita a cópia de seus dados para a unidade.
  * Criptografa os dados na unidade com o BitLocker de 256 bits do AES. Você pode usar um protetor de chave externa para proteger sua chave do BitLocker.
  * Gera os arquivos de diário da unidade usados durante a criação de importação.
  * Ajuda a identificar os números de unidades necessárias para trabalhos de exportação.

> [!NOTE]
> A ferramenta WAImportExport está disponível em duas versões, versão 1 e 2. É recomendável usar:
>
> * Versão 1 de importação/exportação no armazenamento de blobs do Azure.
> * Versão 2 para importar dados para arquivos do Azure.
>
> A ferramenta WAImportExport só é compatível com o sistema de operacional do Windows de 64 bits. Para versões específicas do sistema operacional com suporte, vá para [requisitos de importação/exportação do Microsoft Azure](storage-import-export-requirements.md#supported-operating-systems).

* **Unidades de disco**: é possível pode enviar SSDs (unidades de estado sólido) ou HDDs (unidades de disco rígido) para o datacenter do Azure. Ao criar um trabalho de importação, você pode enviar as unidades de disco que contém seus dados. Ao criar um trabalho de exportação, você pode enviar unidades vazias para o datacenter do Microsoft Azure. Para tipos de disco específico, vá para [Tipos de disco compatíveis](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Como funciona a importação/exportação?

O serviço de importação/exportação do Microsoft Azure permite a transferência de dados em nlobs do Azure e arquivos do Azure, criando trabalhos. Use o portal do Azure ou API de REST do Azure Resource Manager para criar trabalhos. Cada trabalho é associado uma única conta de armazenamento.

Os trabalhos podem ser importa ou exportar trabalhos. Um trabalho de importação permite importar dados para blobs do Azure ou arquivos do Azure, enquanto o trabalho de exportação permite que os dados sejam exportados de blobs do Azure. Ao criar um trabalho de importação, você pode enviar as unidades de disco que contém seus dados. Ao criar um trabalho de exportação, você pode enviar unidades vazias para o datacenter do Microsoft Azure. Em cada caso, você pode fornecer até 10 unidades de disco rígido por trabalho.

### <a name="inside-an-import-job"></a>Dentro de um trabalho de importação

Em um alto nível, um trabalho de importação envolve as seguintes etapas:

1. Determine os dados a serem importados, número de unidades necessárias, local de blob de destino para os dados no armazenamento do Azure.
2. Use a ferramenta WAImportExport para copiar dados na unidade. Criptografe as unidades de disco com BitLocker.
3. Crie um trabalho de importação na sua conta de armazenamento de destino no portal do Azure. Carregue os arquivos no diário da unidade.
4. Forneça o endereço de retorno e o número da conta da operadora a serem usados para enviar de volta as unidades para você.
5. Envie as unidades de disco para o endereço de envio fornecido durante a criação do trabalho.
6. Atualize o número de acompanhamento de entrega nos detalhes do trabalho de importação de acompanhamento e envie o trabalho de importação.
7. As unidades são recebidas e processadas no data center do Azure.
8. As unidades são enviadas usando sua conta da transportadora para o endereço de retorno fornecido no trabalho de importação.

> [!NOTE]
> Para remessas locais (em data center país/região), compartilhe uma conta de operadora doméstica.
>
> Para remessas do exterior (fora do data center país/região), compartilhe uma conta da operadora internacional.

 ![Figura 1: Importar o fluxo de trabalho](./media/storage-import-export-service/import-job.png)

Para obter instruções passo a passo sobre importar dados, vá para:

* [Importar dados nos blobs do Azure](storage-import-export-data-to-blobs.md)
* [Importar dados nos arquivos do Azure](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Dentro de um trabalho de exportação

> [!IMPORTANT]
> O serviço só dá suporte à exportação de Blobs do Azure. Não há suporte para a exportação de Arquivos do Azure.

Em um alto nível, um trabalho de exportação envolve as seguintes etapas:

1. Determine os dados a serem exportados, número de unidades necessário, blobs de origem ou caminhos do contêiner de dados no armazenamento de Blob.
2. Crie um trabalho de exportação na sua conta de armazenamento de destino no portal do Azure.
3. Especifique os blobs de origem ou caminhos de contêiner para os dados a serem exportados.
4. Forneça o endereço de retorno e o número da conta da operadora a serem usados para enviar de volta as unidades para você.
5. Envie as unidades de disco para o endereço de envio fornecido durante a criação do trabalho.
6. Atualize o número de acompanhamento de entrega nos detalhes do trabalho de exportação.
7. As unidades são recebidas e processadas no data center do Azure.
8. As unidades são criptografadas com o BitLocker e as chaves estão disponíveis no portal do Azure.  
9. As unidades são enviadas usando sua conta da transportadora para o endereço de retorno fornecido no trabalho de importação.

> [!NOTE]
> Para remessas locais (em data center país/região), compartilhe uma conta de operadora doméstica.
>
> Para remessas do exterior (fora do data center país/região), compartilhe uma conta da operadora internacional.
  
 ![Figura 2: Exportar o fluxo de trabalho](./media/storage-import-export-service/export-job.png)

Para obter instruções passo a passo sobre exportação de dados, vá para [exportar dados de Blobs do Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Disponibilidade de região

O serviço de Importação/Exportação do Azure dá suporte à cópia dos dados para e a partir de todas as contas de armazenamento do Azure. Você pode enviar discos para um dos locais listados. Se sua conta de armazenamento estiver em um local do Microsoft Azure que não é especificado aqui, um local alternativo de remessa é fornecido quando você criar o trabalho.

### <a name="supported-shipping-locations"></a>Locais de envio com suporte

|País/Região  |País/Região  |País/Região  |País/Região  |
|---------|---------|---------|---------|
|Leste dos EUA    | Norte da Europa        | Índia Central        |US Gov Iowa         |
|Oeste dos EUA     |Europa Ocidental         | Sul da Índia        | DoD do Leste dos EUA        |
|Leste dos EUA 2    | Leste da Ásia        |  Oeste da Índia        | DoD Central dos EUA        |
|Oeste dos EUA 2     | Sudeste Asiático        | Canadá Central        | Leste da China         |
|Centro dos EUA     | Leste da Austrália        | Leste do Canadá        | Norte da China        |
|Centro-Norte dos EUA     |  Sudeste da Austrália       | Brazil South        | Sul do Reino Unido        |
|Centro-Sul dos Estados Unidos     | Oeste do Japão        |Coreia Central         | Alemanha Central        |
|Centro-Oeste dos EUA     |  Leste do Japão       | Gov. dos EUA – Virgínia        | Nordeste da Alemanha        |
|Oeste da África do Sul   |  Norte da África do Sul |

## <a name="security-considerations"></a>Considerações de segurança

Os dados na unidade são criptografados usando o AES 256-bit Criptografia de Unidade de Disco BitLocker. Essa criptografia protegerá os dados enquanto eles estiverem em trânsito.

Para trabalhos de importação, as unidades são criptografadas de duas maneiras.  

* Especificar a opção ao usar o arquivo *dataset.csv* durante a execução da ferramenta WAImportExport na preparação da unidade.

* Habilitar manualmente a criptografia do BitLocker na unidade. Especificar a chave de criptografia no *driveset.csv* ao executar a linha de comando da ferramenta WAImportExport durante a preparação da unidade. A chave de criptografia do BitLocker pode ser mais protegida usando um protetor de chave externa (também conhecido como a chave gerenciada da Microsoft) ou uma chave gerenciada pelo cliente. Para obter mais informações, consulte como [usar uma chave gerenciada pelo cliente para proteger sua chave do BitLocker](storage-import-export-encryption-key-portal.md).

Para os trabalhos de exportação, depois que seus dados forem copiados para as unidades, o serviço fará a criptografia da unidade usando o BitLocker antes de enviar para você. A chave de criptografia será fornecida a você por meio do Portal do Azure. A unidade precisa ser desbloqueada usando a ferramenta WAImporExport usando a chave.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Preços

**Taxa de manuseio de unidade**

Há uma taxa de manuseio de unidade para cada unidade processada como parte do trabalho de importação ou exportação. Consulte os detalhes sobre o [Preços de Importação/Exportação do Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Custos de envio**

Quando você envia unidades do Azure, você paga pelo custo de envio para a transportadora. Quando a Microsoft devolver as unidades, o custo do envio será cobrado na conta da transportadora que você forneceu no momento da criação do trabalho.

**Custos de transação**

A [cobrança da transação de armazenamento padrão](https://azure.microsoft.com/pricing/details/storage/) é aplicada durante a importação, bem como a exportação de dados. Os encargos de saída padrão também são aplicáveis com cobranças de transações de armazenamento quando os dados são exportados do armazenamento do Azure. Para obter mais informações sobre custos de egresso, consulte [preços de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="next-steps"></a>Próximas etapas

Saiba como usar o serviço de Importação/Exportação:

* [Importar dados nos blobs do Azure](storage-import-export-data-to-blobs.md)
* [Exportar dados nos blobs do Azure](storage-import-export-data-from-blobs.md)
* [Importar dados nos arquivos do Azure](storage-import-export-data-to-files.md)
