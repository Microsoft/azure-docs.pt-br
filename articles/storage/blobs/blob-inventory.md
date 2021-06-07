---
title: Usar o inventário de Armazenamento do Azure para gerenciar dados de blob (versão prévia)
description: O inventário de Armazenamento do Azure é uma ferramenta que ajuda você a obter uma visão geral de todos os seus dados de blob em uma conta de armazenamento.
services: storage
author: twooley
ms.service: storage
ms.date: 04/01/2021
ms.topic: conceptual
ms.author: twooley
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 33d50d1a6b5e84d178b522851795bcc42f5fc169
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277259"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Usar o inventário de blobs do Armazenamento do Azure para gerenciar os dados de blob (versão prévia)

O recurso de inventário de blobs do Armazenamento do Azure fornece uma visão geral dos dados de blob em uma conta de armazenamento. Use o relatório de inventário para entender a idade, o tamanho, o status de criptografia e o tamanho total dos dados, entre outros. O relatório fornece uma visão geral dos seus dados para que eles atendam aos requisitos de negócios e de conformidade. Uma vez habilitado, um relatório de inventário será criado diariamente de modo automático.

## <a name="availability"></a>Disponibilidade

O inventário de blobs tem suporte para contas de armazenamento de blobs de blocos premium e da GPv2 (versão de uso geral 2). Esse recurso tem suporte com ou sem o recurso de [namespace hierárquico](data-lake-storage-namespace.md) habilitado.

> [!IMPORTANT]
> No momento, o inventário de blobs está em **VERSÃO PRÉVIA**. Veja os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter termos legais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

### <a name="preview-regions"></a>Regiões de versão prévia

A versão prévia do inventário de blobs está disponível em contas de armazenamento nas seguintes regiões:

- França Central
- Canadá Central
- Leste do Canadá
- Leste dos EUA
- Leste dos EUA 2
- Europa Ocidental

### <a name="pricing-and-billing"></a>Preços e cobrança

O valor dos relatórios de inventário não é cobrado durante o período de versão prévia. O preço será determinado quando esse recurso estiver em disponibilidade geral.

## <a name="enable-inventory-reports"></a>Habilitar relatórios de inventário

Habilite os relatórios de inventário de blobs adicionando uma política à sua conta de armazenamento. Adicione, edite ou remova uma política usando o [portal do Azure](https://portal.azure.com/).

1. Navegue até o [portal do Azure](https://portal.azure.com/)
1. Selecione uma das suas contas de armazenamento
1. Em **Serviço Blob**, selecione **Inventário de blobs**
1. Verifique se o **Inventário de blobs habilitado** está selecionado
1. Selecione **Adicionar uma regra**
1. Nomeie a sua nova regra
1. Selecione os **Tipos de blob** para o seu relatório de inventário
1. Adicionar uma correspondência de prefixo para filtrar o seu relatório de inventário
1. Selecione se deseja **Incluir versões de blob** e **Incluir instantâneos** no seu relatório de inventário. Versões e instantâneos precisam estar habilitados na conta para salvar uma nova regra com a opção correspondente habilitada.
1. Selecione **Salvar**

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Captura de tela mostrando como adicionar uma regra de inventário de blobs usando o portal do Azure":::

As políticas de inventário são lidas ou gravadas integralmente. Não há suporte para atualizações parciais.

> [!IMPORTANT]
> Se você habilitar as regras de firewall para sua conta de armazenamento, as solicitações inventário poderão ser bloqueadas. Desbloqueie essas solicitações fornecendo exceções para serviços Microsoft confiáveis. Para obter mais informações, confira a seção Exceções em [Configurar firewalls e redes virtuais](../common/storage-network-security.md#exceptions).

Uma execução de inventário de blobs é agendada automaticamente todos os dias. Pode levar até 24 horas para que uma execução de inventário seja concluída. Um relatório de inventário é configurado por meio da adição de uma política de inventário com uma ou mais regras.

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

Veja o JSON para uma política de inventário selecionando a guia **Exibição de código** na seção **Inventário de blobs** do portal do Azure.

| Nome do parâmetro | Tipo de parâmetro        | Observações | Necessário? |
|----------------|-----------------------|-------|-----------|
| destino    | String                | O contêiner de destino em que todos os arquivos de inventário serão gerados. O contêiner de destino já precisa existir. | Yes |
| Habilitado        | Boolean               | Usado para desabilitar toda a política. Quando definido como **true**, o campo com o nível de regra habilitado substitui esse parâmetro. Quando desabilitado, o inventário de todas as regras será desabilitado. | Yes |
| regras          | Matriz de objetos de regra | Pelo menos uma regra é necessária em uma política. Há suporte a até dez regras. | Yes |

## <a name="inventory-rules"></a>Regras de inventário

Uma regra captura as condições de filtragem e os parâmetros de saída necessários para gerar um relatório de inventário. Cada regra cria um relatório de inventário. As regras podem ter prefixos sobrepostos. Um blob pode aparecer em mais de um inventário, dependendo das definições de regra.

Cada regra na política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro                 | Observações | Necessário? |
|----------------|--------------------------------|-------|-----------|
| name           | String                         | Um nome de regra pode incluir até 256 caracteres alfanuméricos que diferenciam maiúsculas de minúsculas. O nome precisa ser exclusivo em uma política. | Yes |
| Habilitado        | Boolean                        | Um sinalizador que permite que uma regra seja habilitada ou desabilitada. O valor padrão é **true**. | Yes |
| definição     | Definição de regra de inventário JSON | Cada definição é composta por um conjunto de filtros de regras. | Yes |

O sinalizador **Habilitado para inventário de blobs** global tem precedência sobre o parâmetro *enabled* em uma regra.

### <a name="rule-filters"></a>Filtros de regra

Vários filtros estão disponíveis para personalizar um relatório de inventário de blobs:

| Nome do filtro         | Tipo de filtro                     | Observações | Necessário? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Matriz de valores de enumeração predefinidos | Os valores válidos são `blockBlob` e `appendBlob` para contas habilitadas para namespace hierárquico e `blockBlob`, `appendBlob` e `pageBlob` para outras contas. | Yes |
| prefixMatch         | Uma matriz de até dez cadeias de caracteres para que os prefixos sejam correspondidos. Um prefixo precisa começar com um nome de contêiner, por exemplo, "container1/foo" | Se você não definir *prefixMatch* ou fornecer um prefixo vazio, a regra se aplicará a todos os blobs na conta de armazenamento. | Não |
| includeSnapshots    | Boolean                         | Especifica se o inventário deve incluir instantâneos. O padrão é **false**. | Não |
| includeBlobVersions | Boolean                         | Especifica se o inventário deve incluir versões de blob. O padrão é **false**. | Não |

Veja o JSON para regras de inventário selecionando a guia **Exibição de código** na seção **Inventário de blobs** do portal do Azure. Os filtros são especificados em uma definição de regra.

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

Cada execução de inventário gera um conjunto de arquivos formatados como CSV no contêiner de destino de inventário especificado. A saída de inventário é gerada no seguinte caminho: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/`, em que:

- *accountName* é o nome da sua conta de Armazenamento de Blobs do Azure
- *inventory-destination-container* é o contêiner de destino especificado na política de inventário
- *YYYY/MM/DD/HH-MM-SS* é a hora em que o inventário começou a ser executado

### <a name="inventory-files"></a>Arquivos de inventário

Cada execução de inventário gera os seguintes arquivos:

- **Arquivo CSV de inventário**: um arquivo CSV (valores separados por vírgula) para cada regra de inventário. Cada arquivo contém objetos correspondentes e os respectivos metadados. A primeira linha em cada arquivo formatado em CSV sempre é a linha de esquema. A imagem a seguir mostra um arquivo CSV de inventário aberto no Microsoft Excel.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Captura de tela de um arquivo CSV de inventário aberto no Microsoft Excel":::

- **Arquivo de manifesto**: um arquivo manifest.json que contém os detalhes dos arquivos de inventário gerados para cada regra na execução. O arquivo de manifesto também captura a definição de regra fornecida pelo usuário e o caminho para o inventário dessa regra.

- **Arquivo de soma de verificação**: um arquivo manifest.checksum que contém a soma de verificação MD5 do conteúdo do arquivo manifest.json. A geração do arquivo manifest.checksum marca a conclusão de uma execução de inventário.

## <a name="inventory-completed-event"></a>Evento de conclusão de inventário

Assine o evento de conclusão de inventário para ser notificado quando a execução do inventário for concluída. Esse evento será gerado quando o arquivo de soma de verificação do manifesto for criado. O evento de conclusão de inventário também ocorrerá se a execução do inventário falhar com um erro do usuário antes de começar a ser executada. Por exemplo, uma política inválida ou um erro de contêiner de destino ausente dispara o evento. O evento é publicado no Tópico de Inventário de Blobs.

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

## <a name="known-issues"></a>Problemas conhecidos

Esta seção descreve as limitações e problemas conhecidos do recurso de inventário de blobs do Armazenamento do Azure.

### <a name="inventory-job-fails-to-complete"></a>Falha na conclusão do trabalho de inventário

O trabalho de inventário talvez não seja concluído em 24 horas se a conta tiver milhões de blobs e namespaces hierárquicos habilitados. Se isso acontecer, nenhum arquivo de inventário será criado.

## <a name="next-steps"></a>Próximas etapas

- [Calcular a contagem e o tamanho total de blobs por contêiner](calculate-blob-count-size.md)
- [Gerenciar o ciclo de vida de Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)
