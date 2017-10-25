---
title: "Como usar o armazenamento do Azure para o backup e a restauração do SQL Server | Microsoft Docs"
description: "Saiba como fazer backup do SQL Server no Armazenamento do Azure. Explica os benefícios de fazer backup de bancos de dados SQL no Armazenamento do Azure."
services: virtual-machines-windows
documentationcenter: 
author: MikeRayMSFT
manager: jhubbard
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: d3df6b25fe524c500cf1a1333ac136e8a29d1484
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Usar o armazenamento do Azure para o backup e restauração do SQL Server
## <a name="overview"></a>Visão geral
A partir do SQL Server 2012 SP1 CU2, agora você pode escrever backups do SQL Server diretamente para o serviço de armazenamento de Blobs do Azure. Você pode usar essa funcionalidade para fazer backup e restauração do serviço Blob do Azure com um banco de dados local do SQL Server ou um banco de dados do SQL Server em uma máquina virtual do Azure. Backup em nuvem oferece benefícios de disponibilidade, armazenamento externo replicados geo ilimitado e facilidade de migração de dados para e da nuvem. Você pode emitir declarações de BACKUP ou RESTAURAÇÃO usando Transact-SQL ou o SMO.

O SQL Server 2016 apresenta novos recursos: você pode usar o [backup de instantâneo de arquivo](http://msdn.microsoft.com/library/mt169363.aspx) para executar backups quase imediatos e restaurações incrivelmente rápidas.

Este tópico explica por que você pode optar por usar o armazenamento do Azure para backups do SQL e descreve os componentes envolvidos. Você pode usar os recursos fornecidos no final do artigo para acessar o passo a passo e informações adicionais para começar a usar esse serviço com os backups do SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Vantagens de usar o serviço Blob Azure para Backups do SQL Server
Há vários desafios que enfrentamos ao fazer o backup do SQL Server. Eles incluem gerenciamento de armazenamento, risco de falha de armazenamento, acesso a armazenamento externo e configuração de hardware. Muitos desses desafios são resolvidos ao usar o serviço de armazenamento de Blobs do Azure para backups do SQL Server. Considere as vantagens a seguir:

* **Fácil utilização**: armazenar seus backups no serviço Blobs do Azure pode ser uma opção conveniente, flexível e de fácil acesso externo. Criar armazenamento externo para os backups do SQL Server pode ser tão fácil quanto modificar seus scripts/trabalhos existentes para usar a sintaxe **BACKUP TO URL** . Armazenamento externo deve ser tipicamente longe o suficiente da localização do banco de dados de produção para evitar um desastre único que pode afetar os dois locais de banco de dados externo e produção. Ao optar por fazer a [replicação geográfica de seus blobs do Azure](../../../storage/common/storage-redundancy.md), você tem uma camada extra de proteção em caso de um desastre que poderia afetar a região inteira.
* **Arquivo de backup**: o serviço de armazenamento de Blobs do Azure oferece uma alternativa melhor que a opção de fita usadas com frequência para arquivar backups. Armazenamento em fita pode exigir transporte físico para um recurso externo e medidas para proteger a mídia. Armazenar os backups em armazenamento de BLOBs do Azure fornece um instante e altamente disponível, e a opção de arquivamento de um duráveis.
* **Hardware gerenciado**: não há nenhuma sobrecarga de gerenciamento de hardware com os serviços do Azure. Serviços Azure gerenciar o hardware e fornecem replicação geográfica para redundância e proteção contra falhas de hardware.
* **Armazenamento ilimitado**: ao habilitar o backup direto para blobs do Azure, você terá acesso a um armazenamento praticamente ilimitado. Como alternativa, o backup em um disco de máquina virtual do Azure tem limites com base no tamanho da máquina. Há um limite para o número de discos que você pode anexar a uma máquina virtual do Azure para backups. Esse limite é de 16 discos para uma instância extra grande e menos para instâncias menores.
* **Disponibilidade de backup**: os backups armazenados nos blobs do Azure estão disponíveis em qualquer lugar e a qualquer momento e podem ser acessados facilmente para restaurações em um SQL Server local ou em outro SQL Server em execução em uma Máquina Virtual do Azure, sem a necessidade de desanexar/anexar o banco de dados ou baixar e anexar o VHD.
* **Custo**: pague somente pelo serviço usado. Pode ser econômico como uma opção de arquivamento externo e backup. Consulte [Calculadora de preços do Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de preços") e [Artigo de preços do Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Artigo de preços") para obter mais informações.
* **Instantâneos de armazenamento**: quando arquivos de banco de dados são armazenados em um blob do Azure e você está usando o SQL Server 2016, você pode usar o [backup de arquivo de instantâneo](http://msdn.microsoft.com/library/mt169363.aspx) para fazer backups quase instantâneos e restaurações incrivelmente rápidas.

Para obter mais detalhes, consulte [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

As seções a seguir apresentam o serviço de armazenamento de Blobs do Azure, incluindo os componentes necessários do SQL Server. É importante compreender os componentes e a interação entre eles para fazer um backup e restauração com êxito do serviço de armazenamento de Blobs do Azure.

## <a name="azure-blob-storage-service-components"></a>Componentes do serviço de armazenamento de blobs do Azure
Os seguintes componentes do Azure são usados durante o backup para o serviço de armazenamento de Blobs do Azure.

| Componente | Descrição |
| --- | --- |
| **Conta de armazenamento** |A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para acessar um serviço de armazenamento de BLOBs do Azure, primeiro crie uma conta de armazenamento do Azure. Para obter mais informações sobre os serviços de armazenamento de blobs do Azure, consulte [Como usar o serviço de armazenamento de blobs do Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contêiner** |Um contêiner fornece um agrupamento de um conjunto de blobs e pode armazenar um número ilimitado de blobs. Para escrever um SQL Server backup para um serviço Blob do Azure, você deve ter pelo menos o contêiner raiz criado. |
| **Blob** |Um arquivo de qualquer tipo e tamanho. Blobs são endereçáveis usando o seguinte formato de URL: **https://[conta de armazenamento].blob.core.windows.net/[contêiner]/[blob]**. Para obter mais informações sobre blobs de páginas, consulte [Noções gerais sobre blobs de blocos e blobs de páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes do SQL Server
Os seguintes componentes do SQL Server são usados durante o backup para o serviço de armazenamento de Blobs do Azure.

| Componente | Descrição |
| --- | --- |
| **URL** |Uma URL especifica um URI (Uniform Resource Identifier) para um único arquivo de backup. A URL é usada para fornecer o local e o nome do arquivo de backup do SQL Server. A URL deve apontar para um Blob real, não apenas um contêiner. Se o blob não existir, ele será criado. Se um blob existente for especificado, o BACKUP falhará, a menos que a > opção COM FORMATO seja especificada. A seguir está um exemplo da URL que deve ser especificada com o comando BACKUP: **http[s]://[contadearmazenamento].blob.core.windows.net/[contêiner]/[FILENAME.bak]**. HTTPS é recomendável, mas não obrigatório. |
| **Credencial** |As informações necessárias para se conectar e autenticar ao serviço de armazenamento de blobs do Azure são armazenadas como uma credencial.  Para o SQL Server gravar backups para uma restauração ou BLOBs do Azure dela, uma credencial do SQL Server deve ser criada. Para obter mais informações, veja [Credencial do SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Se você optar por copiar e carregar um arquivo de backup para o serviço de armazenamento de Blob do Azure, você deve usar um tipo de blob de páginas como sua opção de armazenamento se você planeja usar este arquivo para operações de restauração. RESTAURAÇÃO por meio de um tipo de blob de blocos falhará com um erro.
> 
> 

## <a name="next-steps"></a>Próximas etapas
1. Crie uma conta do Azure caso você ainda não tenha uma. Se você estiver avaliando o Azure, considere usar a [avaliação gratuita](https://azure.microsoft.com/free/).
2. Em seguida, percorra um dos seguintes tutoriais que mostram como criar uma conta de armazenamento e realizar uma restauração.
   
   * **SQL Server 2014**: [Tutorial: backup e restauração do SQL Server 2014 para o Serviço de Armazenamento de Blobs do Microsoft Azure](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Tutorial: usando o serviço de armazenamento de Blobs do Microsoft Azure com bancos de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Examine a documentação adicional começando com [Backup e Restauração do SQL Server com o Serviço de Armazenamento de Blobs do Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Se você tiver problemas, examine o tópico [Práticas recomendadas e solução de problemas de backup do SQL Server para URL](https://msdn.microsoft.com/library/jj919149.aspx).

Para ver outras opções de backup e restauração do SQL Server, consulte [Backup e Restauração do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

