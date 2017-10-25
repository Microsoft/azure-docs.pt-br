---
title: "Criar um Trabalho de exportação para a Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba como criar um trabalho de exportação para o serviço de Importação/Exportação do Microsoft Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 613d480b-a8ef-4b28-8f54-54174d59b3f4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: bdeac373aa8270bd9de8f135ec7166d744fd83ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Criando um trabalho de exportação para o serviço de Importação/Exportação do Azure
A criação de um trabalho de exportação do serviço de Importação/Exportação do Microsoft Azure usando a API REST envolve as seguintes etapas:

-   Selecionar os blobs a serem exportados.

-   Obter uma localização de envio.

-   Criar o trabalho de exportação.

-   Enviar as unidades vazias para a Microsoft por meio de um serviço de carrier com suporte.

-   Atualizar o trabalho de exportação com as informações do pacote.

-   Receber as unidades de volta da Microsoft.

 Consulte [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o Armazenamento de Blobs](storage-import-export-service.md) para obter uma visão geral do serviço de Importação/Exportação e um tutorial que demonstra como usar o [portal do Azure](https://portal.azure.com/) para criar e gerenciar trabalhos de importação e exportação.

## <a name="selecting-blobs-to-export"></a>Selecionando os blobs a serem exportados
 Para criar um trabalho de exportação, você precisará fornecer uma lista de blobs que deseja exportar de sua conta de armazenamento. Há algumas maneiras para selecionar os blobs a serem exportados:

-   É possível usar um caminho de blob relativo para selecionar um único blob e todos os seus instantâneos.

-   É possível usar um caminho de blob relativo para selecionar um único blob, excluindo seus instantâneos.

-   É possível usar um caminho de blob relativo e um tempo de instantâneo para selecionar um único instantâneo.

-   É possível usar um prefixo de blob para selecionar todos os blobs e instantâneos com o prefixo especificado.

-   É possível exportar todos os blobs e instantâneos na conta de armazenamento.

 Para obter mais informações sobre como especificar os blobs a serem exportados, consulte a operação [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate).

## <a name="obtaining-your-shipping-location"></a>Obtendo o local de envio
Antes de criar um trabalho de exportação, você precisa obter um nome e o endereço de uma localização de envio chamando a operação [Get Location](https://portal.azure.com) ou [List Locations](/rest/api/storageimportexport/listlocations). `List Locations` retornará uma lista de localizações e seus endereços para correspondência. É possível selecionar uma localização na lista retornada e enviar os discos rígidos para esse endereço. Você também pode usar a operação `Get Location` para obter o endereço para entrega de uma localização específica diretamente.

Siga as etapas abaixo para obter a localização de envio:

-   Identifique o nome da localização de sua conta de armazenamento. Esse valor pode ser encontrado no campo **Local** do **Painel** da conta de armazenamento no portal clássico ou consultado usando a operação [Get Storage Account Properties](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties) da API de gerenciamento de serviços.

-   Recupere a localização que está disponível para processar essa conta de armazenamento chamando a operação `Get Location`.

-   Se a propriedade `AlternateLocations` da localização contiver a própria localização, será seguro usá-la. Caso contrário, chame a operação `Get Location` novamente com uma das localizações alternativas. O local original pode estar fechado temporariamente para manutenção.

## <a name="creating-the-export-job"></a>Criando o trabalho de exportação
 Para criar o trabalho de exportação, chame a operação [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate). Você precisará fornecer as seguintes informações:

-   Um nome para o trabalho.

-   O nome da conta de armazenamento.

-   O nome da localização de envio, obtido na etapa anterior.

-   Um tipo de trabalho (Exportação).

-   O endereço do remetente para o qual as unidades deverão ser enviadas após a conclusão do trabalho de exportação.

-   A lista de blobs (ou os prefixos de blob) a ser exportada.

## <a name="shipping-your-drives"></a>Enviando suas unidades
 Em seguida, use a Ferramenta de Importação/Exportação do Azure para determinar o número de unidades que precisam ser enviadas, com base nos blobs selecionados para serem exportados e no tamanho da unidade. Consulte [Referência da Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-how-to-v1.md) para obter detalhes.

 Empacote as unidades em um único pacote e envie-as para o endereço obtido na etapa anterior. Anote o número de acompanhamento do pacote para a próxima etapa.

> [!NOTE]
>  Você deve enviar suas unidades por meio de um serviço de transporte compatível, que fornecerá um número de rastreamento para seu pacote.

## <a name="updating-the-export-job-with-your-package-information"></a>Atualizando o trabalho de exportação com as informações do pacote
 Depois que tiver o número de acompanhamento, chame a operação [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) para atualizar o nome da carrier e o número de acompanhamento do trabalho. Opcionalmente, você pode especificar o número de unidades, o endereço do remetente, bem como a data de remessa.

## <a name="receiving-the-package"></a>Recebendo o pacote
 Após o processamento do trabalho de exportação, as unidades serão retornadas para você com os dados criptografados. É possível recuperar a chave do BitLocker para cada uma das unidades chamando a operação [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). Em seguida, você poderá desbloquear a unidade usando a chave. O arquivo de manifesto da unidade em cada unidade contém a lista de arquivos na unidade, bem como o endereço original do blob para cada arquivo.

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
