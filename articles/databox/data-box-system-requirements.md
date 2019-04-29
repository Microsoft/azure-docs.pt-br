---
title: Requisitos de sistema do Microsoft Azure Data Box | Microsoft Docs
description: Aprenda sobre os requisitos de software e de rede para seu Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 7d52af9e3948f40936795efab5b6671c3f71007a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746923"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos de sistema do Azure Data Box

Este artigo descreve os requisitos de sistema importantes para o Microsoft Azure Data Box e para os clientes se conectando ao Data Box. Recomendamos que você examine as informações com atenção antes de implantar o Data Box e consulte-as, quando necessário, durante a implantação e a subsequente operação.

Os requisitos do sistema incluem:

* **Requisitos de software para hosts que se conectam ao Data Box** – descreve plataformas com suporte, navegadores da interface do usuário Web local, clientes SMB e quaisquer requisitos adicionais para hosts que podem se conectar ao Data Box.
* **Requisitos de rede para o Data Box** – fornece informações sobre os requisitos de rede para a operação ideal do Data Box.


## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem as informações sobre os sistemas operacionais com suporte, os navegadores com suporte para a interface do usuário Web local e os clientes SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operacionais compatíveis para clientes

Aqui está uma lista dos sistemas operacionais com suporte para a operação de cópia de dados por meio de clientes conectados ao dispositivo Data Box.

| **Sistema operacional** | **Versões** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Sistemas de arquivos com suporte para clientes Linux

| **Protocolos** | **Versões** | 
| --- | --- | 
| SMB |2.X e posterior |
| NFS | Todas as versões até e incluindo a 4.1|

### <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

A seguir, há uma lista dos tipos de armazenamento com suporte para o dispositivo Data Box. Para obter uma lista completa de todos os diferentes tipos de contas de armazenamento e suas funcionalidades completas, confira [Types of storage accounts](/azure/storage/common/storage-account-overview#types-of-storage-accounts) (Tipos de contas de armazenamento).

| **Conta de armazenamento/Tipos de armazenamento com suporte** | **Blob de blocos** |**Blob de páginas*** |**Arquivos do Azure** |**Observações**|
| --- | --- | -- | -- | -- |
| Clássico Standard | S | S | S |
| Uso geral v1 Standard  | S | S | S | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v1 Premium  |  | S| | |
| Uso geral v2 Standard  | S | S | S | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v2 Premium  |  |S | | |
| Armazenamento de blobs Standard |S | | |Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente. |

\* *– Os dados carregados para blobs de páginas devem ser de 512 bytes alinhados, como vhds.*

>[!NOTE]
> Não há suporte para contas do Azure Data Lake Storage Gen2.


### <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

Aqui está uma lista dos tipos de armazenamento com suporte o dispositivo Data Box.

| **Formato de arquivo** | **Observações** |
| --- | --- |
| Blob de blocos do Azure | |
| Blob de páginas do Azure  | Os dados devem ser alinhados com 512 bytes.|
| Arquivos do Azure | |


### <a name="supported-web-browsers"></a>Navegadores da Web com suporte

Aqui está uma lista de navegadores da Web com suporte para a interface do usuário Web local.

| **Navegador** | **Versões** | **Requisitos/observações adicionais** |
| --- | --- | --- |
| Google Chrome |Última versão |Testado com o Chrome|
| Microsoft Edge |Última versão | |
| Firefox | Última versão | Testado com o Firefox|
| Internet Explorer |Última versão |Se você não conseguir entrar, verifique se o JavaScript e os cookies estão habilitados. Para habilitar o acesso da interface do usuário, adicione o IP do dispositivo em **Ações de privacidade** para que o dispositivo possa acessar os cookies. |


## <a name="networking-requirements"></a>Requisitos de rede

Seu datacenter deve ter uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, um link de dados de 1 GbE poderá ser usado para copiar dados, mas as velocidades de cópia serão afetadas.

## <a name="next-step"></a>Próxima etapa

* [Implante seu Azure Data Box](data-box-deploy-ordered.md)

