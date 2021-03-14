---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campos obrigatórios. Veja mais em aka.ms/skyeye/meta.
Título: contas de armazenamento do Azure: descrição dos serviços de mídia do Azure: saiba como criar uma conta de armazenamento do Azure para usar com os serviços de mídia do Azure.
serviços: Media-Services documentationcenter: ' ' autor: IngridAtMicrosoft gerente: femila editor: ' ' MS. Service: Media-Services MS. Workload: MS. tópico: conceptual MS. Date: 01/29/2021 MS. Author: inhenkel
---

# <a name="azure-storage-accounts"></a>Contas de armazenamento do Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia.

A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É altamente recomendável usar contas de armazenamento no mesmo local que a conta dos serviços de mídia para evitar custos adicionais de latência e de saída de dados.

Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). As contas somente BLOB não são permitidas como **primárias**.

Recomendamos que você use o GPv2, para que você possa aproveitar os recursos e o desempenho mais recentes. Para saber mais sobre contas de armazenamento, confira [visão geral da conta de armazenamento do Azure](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Somente a camada de acesso quente tem suporte para uso com os serviços de mídia do Azure, embora as outras camadas de acesso possam ser usadas para reduzir os custos de armazenamento no conteúdo que não está sendo usado ativamente.

Há SKUs diferentes que você pode escolher para sua conta de armazenamento. Caso deseje fazer experimentos com contas de armazenamento, use `--sku Standard_LRS`. No entanto, ao escolher uma SKU para produção, você deve considerar `--sku Standard_RAGRS` , que fornece replicação geográfica para continuidade dos negócios.

## <a name="assets-in-a-storage-account"></a>Ativos em uma conta de armazenamento

Nos serviços de mídia v3, as APIs de armazenamento são usadas para carregar arquivos em ativos. Para obter mais informações, consulte [ativos nos serviços de mídia do Azure v3](assets-concept.md).

> [!Note]
> Não tente alterar o conteúdo dos contêineres de BLOB que foram gerados pelo SDK dos serviços de mídia sem usar as APIs dos serviços de mídia.

## <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger seus ativos em repouso, os ativos devem ser criptografados pela criptografia do lado do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia v3:

|Opção de criptografia|Descrição|Serviços de Mídia v3|
|---|---|---|
|Criptografia de armazenamento dos serviços de mídia| Criptografia AES-256, chave gerenciada pelos serviços de mídia. |Sem suporte. <sup>1</sup>|
|[Criptografia do serviço de armazenamento para dados em repouso](../../storage/common/storage-service-encryption.md)|Criptografia do lado do servidor oferecida pelo armazenamento do Azure, chave gerenciada pelo Azure ou por cliente.|Com suporte.|
|[Criptografia do lado do cliente de armazenamento](../../storage/common/storage-client-side-encryption.md)|Criptografia do lado do cliente oferecida pelo armazenamento do Azure, chave gerenciada por cliente no Key Vault.|Sem suporte.|

<sup>1</sup> no Media Services V3, a criptografia de armazenamento (criptografia AES-256) tem suporte apenas para compatibilidade com versões anteriores quando seus ativos foram criados com os serviços de mídia v2, o que significa que o V3 funciona com ativos criptografados de armazenamento existentes, mas não permitirá a criação de novos.

## <a name="storage-account-double-encryption"></a>Criptografia dupla da conta de armazenamento

As contas de armazenamento dão suporte à criptografia dupla, mas a segunda camada deve ser habilitada explicitamente. Consulte [criptografia de armazenamento do Azure para dados em repouso](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption).  

## <a name="storage-account-errors"></a>Erros de conta de armazenamento

O estado "Desconectado" de uma conta de Serviços de Mídia indica que a conta não tem mais acesso a uma ou mais contas de armazenamento anexadas, devido a uma alteração nas chaves de acesso de armazenamento. Os Serviços de Mídia exigem chaves de acesso de armazenamento atualizadas para realizar várias tarefas na conta.

Veja a seguir os principais cenários que fariam com que a conta de Serviços de Mídia não tivesse acesso às contas de armazenamento anexadas.

|Problema|Solução|
|---|---|
|A conta de Serviços de Mídia ou as contas de armazenamento anexadas foram migradas para assinaturas separadas. |Migre as contas de armazenamento ou a conta de serviços de mídia para que elas estejam todas na mesma assinatura. |
|A conta de Serviços de Mídia está usando uma conta de armazenamento anexada em uma assinatura diferente, já que ela era uma conta Serviços de Mídia inicial compatível com este cenário. Todas as contas de serviços de mídia anteriores foram convertidas em contas baseadas no Gerenciador de recursos do Azure modernas e terão um estado desconectado. |Migrar a conta de armazenamento ou a conta dos serviços de mídia para que elas estejam todas na mesma assinatura.|

## <a name="next-steps"></a>Próximas etapas

Para saber como anexar uma conta de armazenamento para sua conta de Serviços de Mídia, consulte [Criar uma conta](./create-account-howto.md).