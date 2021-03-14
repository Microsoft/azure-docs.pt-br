---
title: Usar o inventário de armazenamento do Azure para gerenciar dados de BLOB (versão prévia)
description: O inventário de armazenamento do Azure é uma ferramenta para ajudar a obter uma visão geral de todos os seus dados de BLOB em uma conta de armazenamento.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: a625ad780d01f3d19d26f2b9626ead3ae455b86b
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631465"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Usar o inventário de blob do armazenamento do Azure para gerenciar dados de BLOB (versão prévia)

O recurso de inventário de blob de armazenamento do Azure fornece uma visão geral dos dados de BLOB em uma conta de armazenamento. Use o relatório de inventário para entender o tamanho total dos dados, a idade, o status de criptografia e assim por diante. O relatório fornece uma visão geral dos seus dados para atender aos requisitos de negócios e de conformidade. Uma vez habilitado, um relatório de inventário é automaticamente criado diariamente.

## <a name="availability"></a>Disponibilidade

O inventário de BLOBs tem suporte para contas de armazenamento de blob de blocos versão 2 (GPv2) e Premium de uso geral. Esse recurso tem suporte com ou sem o recurso de [namespace hierárquico](data-lake-storage-namespace.md) habilitado.

> [!IMPORTANT]
> No momento, o inventário de Bob está em **Visualização**. Consulte os [termos de uso complementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

### <a name="preview-regions"></a>Regiões de visualização

A visualização de inventário de blob está disponível em contas de armazenamento nas seguintes regiões:

- França Central
- Canadá Central
- Leste do Canadá
- Leste dos EUA
- Leste dos EUA 2

### <a name="pricing-and-billing"></a>Preços e cobrança

A taxa de relatórios de inventário não é cobrada durante o período de visualização. O preço será determinado quando esse recurso estiver disponível para o público geral.

## <a name="enable-inventory-reports"></a>Habilitar relatórios de inventário

Habilite os relatórios de inventário de blob adicionando uma política à sua conta de armazenamento. Adicione, edite ou remova uma política usando o [portal do Azure](https://portal.azure.com/).

1. Navegue até o [portal do Azure](https://portal.azure.com/)
1. Selecione uma das suas contas de armazenamento
1. Em **serviço blob**, selecione **inventário de blob**
1. Verifique se o **inventário de blob habilitado** está selecionado
1. Selecione **Adicionar uma regra**
1. Nomeie sua nova regra
1. Selecione os **tipos de blob** para seu relatório de inventário
1. Adicionar uma correspondência de prefixo para filtrar seu relatório de inventário
1. Selecione se deseja **incluir versões de blob** e **incluir instantâneos** em seu relatório de inventário. Versões e instantâneos devem ser habilitados na conta para salvar uma nova regra com a opção correspondente habilitada.
1. Selecione **Salvar**

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Captura de tela mostrando como adicionar uma regra de inventário de BLOB usando o portal do Azure":::

As políticas de inventário são lidas ou gravadas integralmente. Não há suporte para atualizações parciais.

> [!IMPORTANT]
> Se você habilitar as regras de firewall para sua conta de armazenamento, as solicitações inventário poderão ser bloqueadas. Desbloqueie essas solicitações fornecendo exceções para serviços Microsoft confiáveis. Para obter mais informações, consulte a seção exceções em [Configurar firewalls e redes virtuais](../common/storage-network-security.md#exceptions).

Uma execução de inventário de blob é agendada automaticamente todos os dias. Pode levar até 24 horas para que uma execução de estoque seja concluída. Um relatório de inventário é configurado pela adição de uma política de inventário com uma ou mais regras.

## <a name="inventory-policy"></a>Política de inventário

Uma política de inventário é uma coleção de regras em um documento JSON.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Exiba o JSON para uma política de inventário selecionando a guia **exibição de código** na seção **inventário de blob** do portal do Azure.

| Nome do parâmetro | Tipo de parâmetro        | Observações | Necessário? |
|----------------|-----------------------|-------|-----------|
| destino    | String                | O contêiner de destino em que todos os arquivos de inventário serão gerados. O contêiner de destino já deve existir. | Sim |
| Habilitado        | Boolean               | Usado para desabilitar a política inteira. Quando definido como **true**, o campo de nível de regra habilitado substitui esse parâmetro. Quando desabilitado, o inventário de todas as regras será desabilitado. | Sim |
| regras          | Matriz de objetos de regra | Pelo menos uma regra é necessária em uma política. Há suporte para até 10 regras. | Sim |

## <a name="inventory-rules"></a>Regras de inventário

Uma regra captura as condições de filtragem e os parâmetros de saída para gerar um relatório de inventário. Cada regra cria um relatório de inventário. As regras podem ter prefixos sobrepostos. Um blob pode aparecer em mais de um inventário, dependendo das definições de regra.

Cada regra na política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro                 | Observações | Necessário? |
|----------------|--------------------------------|-------|-----------|
| name           | String                         | Um nome de regra pode incluir até 256 caracteres alfanuméricos que diferenciam maiúsculas de minúsculas. O nome deve ser exclusivo dentro de uma política. | Sim |
| Habilitado        | Boolean                        | Um sinalizador que permite que uma regra seja habilitada ou desabilitada. O valor padrão é **true**. | Sim |
| definição     | Definição de regra de inventário JSON | Cada definição é composta por um conjunto de filtros de regra. | Sim |

O sinalizador **habilitado de inventário de blob** global tem precedência sobre o parâmetro *habilitado* em uma regra.

### <a name="rule-filters"></a>Filtros de regra

Vários filtros estão disponíveis para personalizar um relatório de inventário de blob:

| Nome do filtro         | Tipo de filtro                     | Observações | Necessário? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Matriz de valores de enumeração predefinidos | Os valores válidos são `blockBlob` e `appendBlob` para contas habilitadas para namespace hierárquico, e `blockBlob` , `appendBlob` e `pageBlob` para outras contas. | Sim |
| prefixMatch         | Matriz de até 10 cadeias de caracteres para que os prefixos sejam correspondidos. Um prefixo deve começar com um nome de contêiner, por exemplo, "Container1/foo" | Se você não definir *prefixMatch* ou fornecer um prefixo vazio, a regra se aplicará a todos os BLOBs na conta de armazenamento. | Não |
| includeSnapshots    | Boolean                         | Especifica se o inventário deve incluir instantâneos. O padrão é **false**. | Não |
| includeBlobVersions | Boolean                         | Especifica se o inventário deve incluir versões de BLOB. O padrão é **false**. | Não |

Exiba o JSON para regras de inventário selecionando a guia **exibição de código** na seção **inventário de blob** do portal do Azure. Os filtros são especificados dentro de uma definição de regra.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Saída de inventário

Cada execução de inventário gera um conjunto de arquivos CSV formatados no contêiner de destino de inventário especificado. A saída de inventário é gerada sob o seguinte caminho: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` em que:

- *accountName* é o nome da sua conta de armazenamento de BLOBs do Azure
- *inventário-destino-contêiner* é o contêiner de destino especificado na política de inventário
- *Aaaa/mm/dd/hh-mm-SS* é a hora em que o inventário começou a ser executado

### <a name="inventory-files"></a>Arquivos de inventário

Cada execução de inventário gera os seguintes arquivos:

- **Arquivo CSV de inventário**: um arquivo CSV (valores separados por vírgula) para cada regra de inventário. Cada arquivo contém objetos correspondentes e seus metadados. A primeira linha em cada arquivo formatado em CSV sempre é a linha de esquema. A imagem a seguir mostra um arquivo CSV de inventário aberto no Microsoft Excel.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Captura de tela de um arquivo CSV de inventário aberto no Microsoft Excel":::

- **Arquivo de manifesto**: um manifest.jsno arquivo que contém os detalhes dos arquivos de inventário gerados para cada regra na execução. O arquivo de manifesto também captura a definição de regra fornecida pelo usuário e o caminho para o inventário dessa regra.

- **Arquivo de soma de verificação**: um arquivo manifest. Checksum que contém a soma de verificação MD5 do conteúdo de manifest.jsno arquivo. A geração do arquivo manifest. Checksum marca a conclusão de uma execução de inventário.

## <a name="inventory-completed-event"></a>Evento de inventário concluído

Assine o evento inventário concluído para ser notificado quando a execução do inventário for concluída. Esse evento é gerado quando o arquivo de soma de verificação do manifesto é criado. O evento inventário concluído também ocorrerá se a execução do inventário falhar no erro do usuário antes de começar a ser executada. Por exemplo, uma política inválida ou um erro de contêiner de destino não presente irá disparar o evento. O tópico evento é publicado no inventário de BLOB.

Evento de exemplo:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>Próximas etapas

- [Calcular a contagem e o tamanho total de BLOBs por contêiner](calculate-blob-count-size.md)
- [Gerenciar o ciclo de vida do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)
