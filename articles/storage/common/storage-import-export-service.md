---
title: "Usando a Importação/Exportação do Azure para transferir dados bidirecionalmente do armazenamento de blobs | Microsoft Docs"
description: "Saiba como criar trabalhos de importação e exportação no Portal do Azure para transferir dados bidirecionalmente no armazenamento de blobs."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 668f53f2-f5a4-48b5-9369-88ec5ea05eb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: muralikk
ms.openlocfilehash: 8fb4713589963c649d650a7661c2a6b540b65a5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o armazenamento do Azure
O serviço de Importação/Exportação do Azure permite a você transferir com segurança grandes quantidades de dados para o armazenamento do Azure por meio do envio de unidades de disco rígido para um data center do Azure. Você também pode usar esse serviço para transferir dados do armazenamento do Azure para as unidades de disco rígido e enviar para seu site local. Esse serviço é adequado em situações em que você deseja transferir vários terabytes (TB) de dados para dentro ou fora do Azure, mas o upload ou download pela rede não é viável devido à largura de banda limitada ou aos os altos custos de rede.

O serviço requer que as unidades de disco rígido sejam criptografadas com BitLocker para a segurança dos seus dados. O serviço oferece suporte às contas de armazenamento Clássica e do Azure Resource Manager (tipo padrão e dinâmico) presentes em todas as regiões do Azure Público. Você deve enviar as unidades de disco rígido para um dos locais com suporte especificados posteriormente neste artigo.

Neste artigo, você aprenderá mais sobre o serviço de Importação/Exportação do Azure e como enviar unidades para copiar os dados para dentro e fora do armazenamento de blobs do Azure.

## <a name="when-should-i-use-the-azure-importexport-service"></a>Quando devo usar o serviço de Importação/Exportação do Azure?
Considere o uso do serviço de Importação/Exportação do Azure quando o upload ou download dos dados pela rede estiver muito lento, ou quando a largura de banda de rede adicional for dispendiosa.

Você pode usar esse serviço em cenários como:

* Migrando dados para a nuvem: mover grandes quantidades de dados para Azure de forma rápida e econômica.
* Distribuição de conteúdo: enviar rapidamente dados para seus sites de clientes.
* Backup: fazer backups dos seus dados locais para guardar no armazenamento de blobs do Azure.
* Recuperação de dados: recuperar uma grande quantidade de dados guardados no armazenamento e enviá-los para seu local.

## <a name="prerequisites"></a>Pré-requisitos
Nesta seção, listamos os pré-requisitos necessários para usar esse serviço. Examine-os cuidadosamente antes de enviar suas unidades.

### <a name="storage-account"></a>Conta de armazenamento
Você deve ter uma assinatura do Azure existente e uma ou mais contas de armazenamento para usar o serviço de Importação/Exportação. Cada trabalho pode ser usado para transferir dados para apenas uma conta de armazenamento, ou por meio dela. Em outras palavras, um único trabalho de importação/exportação não pode estender-se por várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

### <a name="data-types"></a>Tipos de dados
Você pode usar o serviço de Importação/Exportação do Azure para copiar os dados para blobs de **Bloco** ou blobs de **Página** ou **Arquivos**. Por outro lado, você pode exportar apenas os blobs de **Bloco**, blobs de **Página** ou blobs de **Acréscimo** do armazenamento do Azure usando esse serviço. O serviço não dá suporte à exportação de arquivos do Azure e só pode importar arquivos no armazenamento do Azure.

### <a name="job"></a>Trabalho
Para começar o processo de importação para o armazenamento ou de exportação desse armazenamento, primeiro crie um trabalho. que pode ser um trabalho de importação ou um trabalho de exportação:

* Crie um trabalho de importação quando desejar transferir dados locais para blobs em sua conta de armazenamento do Azure.
* Crie um trabalho de exportação quando desejar transferir os dados armazenados atualmente como blobs na sua conta de armazenamento para discos rígidos enviados a nós. Ao criar um trabalho, você notifica o serviço de Importação/Exportação de que enviará um ou mais discos rígidos para um data center do Azure.

* Para um trabalho de importação, você enviará discos rígidos contendo seus dados.
* Para um trabalho de exportação, você enviará discos rígidos vazios.
* Você pode fornecer até 10 unidades de disco rígido por trabalho.

Você pode criar um trabalho de importação ou exportação usando o Portal do Azure ou a [API REST de Importação/Exportação do Armazenamento do Azure](/rest/api/storageimportexport).

### <a name="waimportexport-tool"></a>Ferramenta WAImportExport
A primeira etapa na criação de um trabalho de **importação** é preparar as unidades que serão enviadas para a importação. Para preparar a unidade, você deve conectá-la a um servidor local e executar a Ferramenta WAImportExport no servidor local. Essa ferramenta WAImportExport facilita a cópia dos seus dados para a unidade, criptografando os dados na unidade com BitLocker e gerando os arquivos de diário de unidade.

Os arquivos de diário armazenam informações básicas sobre o trabalho e a unidade, como o número de série da unidade e o nome da conta de armazenamento. Este arquivo de diário não é armazenado na unidade. Ele é usado durante a criação do trabalho de importação. Confira posteriormente neste artigo detalhes do passo a passo sobre a criação de trabalho.

A ferramenta WAImportExport só é compatível com o sistema de operacional do Windows de 64 bits. Consulte a seção [Sistema Operacional](#operating-system) para obter as versões específicas do SO com suporte.

Baixe a versão mais recente da [ferramenta WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). Para obter mais detalhes sobre como usar a ferramenta WAImportExport, consulte [Como usar a ferramenta WAImportExport](storage-import-export-tool-how-to.md).

>[!NOTE]
>**Versão anterior:** [Baixe a versão WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) da ferramenta e consulte [Guia de uso da WAImportExpot V1](storage-import-export-tool-how-to-v1.md). A versão WAImportExpot V1 da ferramenta dá suporte para **Preparação de discos quando os dados já tiverem sido gravados previamente no disco**. Também será necessário usar a ferramenta WAImportExpot V1 se a única chave disponível for a chave de SAS.

>

### <a name="hard-disk-drives"></a>Unidades de disco rígido
Há suporte apenas para o uso de SSDs de 2,5 polegadas ou HDDs internos SATA II ou III de 2,5 ou 3,5 polegadas no serviço Importação/Exportação. Um único trabalho de importação/exportação pode ter um máximo de 10 HDD/SSDs e cada HDD/SSD individual pode ter qualquer tamanho. Um grande número de unidades pode ser distribuído em vários trabalhos e não há nenhum limite no número de trabalhos que podem ser criados. 

Para trabalhos de importação, somente o primeiro volume de dados na unidade será processado. O volume de dados deve ser formatado com NTFS.

> [!IMPORTANT]
> As unidades de disco rígido externas que vêm com um adaptador USB interno não são suportadas por esse serviço. Além disso, o disco dentro da proteção de um HDD externo não pode ser usado; não envie HDDs externos.
> 
> 

Abaixo está uma lista de adaptadores USB externos usados para copiar dados em HDDs internos. Anker 68UPSATAA - 02BU Anker BU 68UPSHHDS Startech SATADOCK22UE Orico 6628SUS3-C-BK (série 6628) Estação dock da unidade de disco rígido externa SATA BlacX Hot Swap Thermaltake (USB 2.0 e eSATA)

### <a name="encryption"></a>Criptografia
Os dados na unidade devem ser criptografados com a Criptografia de Unidade de Disco BitLocker. Isso protegerá os dados enquanto eles estiverem em trânsito.

Para os trabalhos de importação, há duas maneiras de executar a criptografia. A primeira maneira é especificar a opção ao usar o arquivo CSV de conjunto de dados durante a execução da ferramenta WAImportExport na preparação da unidade. A segunda é habilitar manualmente a criptografia BitLocker na unidade e especificar a chave de criptografia no CSV driveset ao executar a linha de comando da ferramenta WAImportExport durante a preparação da unidade.

Para os trabalhos de exportação, depois que seus dados forem copiados para as unidades, o serviço fará a criptografia da unidade usando o BitLocker antes de enviar para você. A chave de criptografia será fornecida a você por meio do Portal do Azure.  

### <a name="operating-system"></a>Sistema operacional
Você pode usar um dos seguintes Sistemas Operacionais de 64 bits para preparar o disco rígido usando a Ferramenta WAImportExport antes de enviar a unidade para o Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Todos esses sistemas operacionais dão suporte à Criptografia de Unidade de Disco BitLocker.

### <a name="locations"></a>Locais
O serviço de Importação/Exportação do Azure dá suporte à cópia dos dados para e a partir de todas as contas de armazenamento do Azure Públicas. Você pode enviar discos rígidos para um dos seguintes locais. Se sua conta de armazenamento estiver em um local público do Azure não especificado aqui, um local alternativo de envio será fornecido quando você estiver criando o trabalho no Portal do Azure ou por meio da API REST de Importação/Exportação.

Locais de envio com suporte:

* Leste dos EUA
* Oeste dos EUA
* Leste dos EUA 2
* Oeste dos EUA 2
* Centro dos EUA
* Centro-Norte dos EUA
* Centro-Sul dos Estados Unidos
* Centro-Oeste dos EUA
* Norte da Europa
* Europa Ocidental
* Ásia Oriental
* Sudeste Asiático
* Leste da Austrália
* Sudeste da Austrália
* Oeste do Japão
* Leste do Japão
* Índia Central
* Sul da Índia
* Índia Ocidental
* Canadá Central
* Leste do Canadá
* Sul do Brasil
* Coreia Central
* Gov. dos EUA – Virgínia
* Gov do Iowa nos EUA
* DoD do Leste dos EUA
* DoD Central dos EUA
* Leste da China
* Norte da China
* Sul do Reino Unido

### <a name="shipping"></a>Remessa
**Unidades de envio para o data center:**

Ao criar um trabalho de importação ou exportação, você receberá um endereço de envio de um dos locais com suporte para enviar suas unidades. O endereço de envio fornecido depende do local de sua conta de armazenamento, mas não pode ser o mesmo que o local da conta de armazenamento.

FedEx, UPS ou DHL pode ser usado para enviar suas unidades para o endereço de envio.

**Enviando unidades a partir do data center:**

Ao criar um trabalho de importação ou exportação, você deve fornecer um endereço de retorno para a Microsoft usar ao enviar de volta as unidades após a conclusão do trabalho. Certifique-se de que fornecer um endereço de retorno válido para evitar atrasos no processamento.

A transportadora deve ter o controle apropriado para manter a cadeia de custódia. Você deve fornecer um número de conta da transportadora FedEx, UPS ou DHL válido para ser usado pela Microsoft para enviar de volta as unidades. Um número de conta FedEx, UPS ou DHL é necessário para enviar de volta as unidades de locais nos EUA e na Europa. Um número de conta DHL é necessário para enviar de volta as unidades de locais na Ásia e na Austrália. Você poderá criar uma conta da carrier [FedEx](http://www.fedex.com/us/oadr/) (para os EUA e a Europa) ou [DHL](http://www.dhl.com/) (Ásia e Austrália) se não tiver uma. Se você já tiver um número de conta da transportadora, verifique se ele é válido.

Ao enviar seus pacotes, você deve seguir os [Termos de Serviço do Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Observe que a mídia física que está enviando talvez precise cruzar fronteiras internacionais. Você é responsável por garantir que seus dados e mídia física sejam importados e/ou exportados de acordo com as leis aplicáveis. Antes de enviar a mídia física, peça a seus consultores para verificar se a mídia e os dados podem ser enviados legalmente ao data center identificado. Isso ajudará a garantir que eles cheguem à Microsoft pontualmente. Por exemplo, qualquer pacote que cruzará fronteiras internacionais precisa que uma fatura comercial acompanhe o pacote (exceto se cruzar fronteiras dentro da União Europeia). Você pode imprimir uma cópia preenchida da fatura comercial do site da transportadora. Alguns exemplos de fatura comercial são a [Fatura comercial da DHL](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) e a [Fatura comercial da FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Certifique-se de que a Microsoft não tenha sido indicada como o exportador.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Como funciona o serviço de Importação/Exportação do Azure?
Você pode transferir dados entre o site local e o armazenamento do Azure usando o serviço de Importação/Exportação do Azure criando trabalhos e enviando as unidades de disco rígido para um data center do Azure. Cada unidade de disco rígido que você enviar estará associada a um único trabalho. Cada trabalho é associado uma única conta de armazenamento. Examine a [seção de pré-requisitos](#pre-requisites) cuidadosamente para conhecer as especificações desse serviço, como tipos de dados, tipos de disco, localizações e envio com suporte.

Nesta seção, descrevemos em um nível alto, as etapas envolvidas nos trabalhos de importação e exportação. Posteriormente na [seção Início Rápido](#quick-start), fornecemos instruções passo a passo para criar um trabalho de importação e exportação.

### <a name="inside-an-import-job"></a>Dentro de um trabalho de importação
Em um alto nível, um trabalho de importação envolve as seguintes etapas:

* Determine os dados a serem importados e o número de unidades necessárias.
* Identifique o local do blob ou arquivo de destino para seus dados no Armazenamento do Azure.
* Use a Ferramenta WAImportExport para copiar seus dados para uma ou mais unidades de disco rígido e criptografá-los com o BitLocker.
* Crie um trabalho de importação na sua conta de armazenamento de destino usando o Portal do Azure ou a API REST de Importação/Exportação. Se você usar o Portal do Azure, carregue os arquivos de diário da unidade.
* Forneça o endereço de retorno e o número da conta da transportadora a ser usado para enviar de volta as unidades para você.
* Envie as unidades de disco rígido para o endereço de envio fornecido durante a criação do trabalho.
* Atualize o número de acompanhamento de entrega nos detalhes do trabalho de importação de acompanhamento e envie o trabalho de importação.
* As unidades são recebidas e processadas no data center do Azure.
* Unidades são enviadas usando sua conta da transportadora para o endereço de retorno fornecido no trabalho de importação.
  
    ![Figura 1: Importar o fluxo de trabalho](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Dentro de um trabalho de exportação
> [!IMPORTANT]
> O serviço só oferece suporte à exportação de Blobs do Azure e não oferece suporte à exportação de arquivos do Azure.
> 
>

Em um alto nível, um trabalho de exportação envolve as seguintes etapas:

* Determine os dados a serem exportados e o número de unidades necessárias.
* Identifique os blobs de origem ou os caminhos do contêiner de seus dados no armazenamento de Blobs.
* Crie um trabalho de exportação em sua conta de armazenamento de origem usando o Portal do Azure ou a API REST de Importação/Exportação.
* Especifique os blobs de origem ou os caminhos do contêiner de seus dados no trabalho de exportação.
* Forneça o endereço de retorno e o número da conta da transportadora a serem usados para enviar de volta as unidades para você.
* Envie as unidades de disco rígido para o endereço de envio fornecido durante a criação do trabalho.
* Atualize o número de acompanhamento de entrega nos detalhes do trabalho de exportação.
* As unidades são recebidas e processadas no data center do Azure.
* As unidades são criptografadas com o BitLocker e as chaves estão disponíveis no Portal do Azure.  
* As unidades são enviadas usando sua conta da transportadora para o endereço de retorno fornecido no trabalho de importação.
  
    ![Figura 2: Exportar o fluxo de trabalho](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>Exibir o status do trabalho e da unidade
Você pode acompanhar o status dos seus trabalhos de importação ou exportação no Portal do Azure. Clique na guia **Importação/Exportação**. Uma lista dos seus trabalhos será exibida na página.

![Exibir o estado do trabalho](./media/storage-import-export-service/jobstate.png)

Você verá um dos seguintes status de trabalho, dependendo de onde a unidade está no processo.

| Status do Trabalho | Descrição |
|:--- |:--- |
| Criando | Após a criação de um trabalho, seu estado será definido como Criando. Enquanto o trabalho está no estado Criando, o serviço de Importação/Exportação considera que as unidades não foram enviadas para o datacenter. Um trabalho pode permanecer no estado Criando por até duas semanas; depois disso será excluído automaticamente pelo serviço. |
| Remessa | Depois que você enviar seu pacote, atualize as informações de rastreamento no Portal do Azure.  Isso mudará o trabalho para "Enviando". O trabalho permanecerá no estado Enviando por até duas semanas. 
| Recebido | Depois que todas as unidades forem recebidas no data center, o estado do trabalho será definido como Recebido. |
| Transferindo | Após pelo menos uma unidade começar o processamento, o estado do trabalho será definido como Transferindo. Consulte a seção Estados da unidade abaixo para saber mais. |
| Empacotamento | Depois que todas as unidades tiverem concluído o processamento, o trabalho será colocado no estado Empacotamento até que as unidades sejam enviadas de volta para você. |
| Concluído | Depois que todas as unidades forem enviadas para o cliente, se o trabalho for concluído sem erros, será definido com o estado Concluído. O trabalho será excluído automaticamente após 90 dias no estado Concluído. |
| Fechado | Depois que todas as unidades forem enviadas para o cliente, se houver erros durante o processamento do trabalho, será definido o estado Fechado. O trabalho será excluído automaticamente após 90 dias no estado Fechado. |

A tabela a seguir descreve o ciclo de vida de uma unidade individual conforme ela passa por um trabalho de importação ou exportação. O estado atual de cada unidade em um trabalho agora fica visível no Portal do Azure.
A tabela a seguir descreve cada estado pelo qual cada unidade em um trabalho pode passar.

| Estado da unidade | Descrição |
|:--- |:--- |
| Especificado | Para um trabalho de importação, quando o trabalho é criado no Portal do Azure, o estado inicial de uma unidade é o estado Especificado. Para um trabalho de exportação, como nenhuma unidade é especificada quando o trabalho é criado, o estado inicial da unidade é Recebido. |
| Recebido | A unidade passa para o estado Recebido quando o operador do serviço de importação/exportação tiver processado as unidades que foram recebidas da empresa transportadora para um trabalho de importação. Para um trabalho de exportação, o estado inicial da unidade é o estado Recebido. |
| Nunca recebido | A unidade passará para o estado Nunca recebido quando o pacote de um trabalho chegar, mas não contiver a unidade. Uma unidade também pode passar para esse estado se tiver passado duas semanas desde que o serviço recebeu as informações de envio, mas o pacote ainda não foi entregue no data center. |
| Transferindo | Uma unidade passará para o estado Transferindo quando o serviço começar a transferir dados da unidade para o armazenamento do Windows Azure. |
| Concluído | Uma unidade passará para o estado Concluído quando o serviço tiver transferido com êxito todos os dados sem erros.
| Concluído mais informações | Uma unidade passará para o estado Concluído mais informações quando o serviço encontrar alguns problemas ao copiar dados de ou para a unidade. As informações podem incluir erros, avisos ou mensagens informativas sobre a substituição de blobs.
| Enviado de volta | A unidade passará para o estado Enviado de volta quando for enviada do data center para o endereço de retorno. |

Essa imagem no Portal do Azure exibe o estado da unidade de um trabalho de exemplo:

![Exibir estado da unidade](./media/storage-import-export-service/drivestate.png)

A tabela a seguir descreve os estados de falha de unidade e as ações executadas para cada estado.

| Estado da unidade | Evento | Resolução/Próxima etapa |
|:--- |:--- |:--- |
| Nunca recebido | Uma unidade que está marcada como Nunca recebido (porque não foi recebida como parte da remessa do trabalho) chega em outra remessa. | A equipe de operações moverá a unidade para o estado Recebido. |
| N/D | Uma unidade que não é parte de qualquer trabalho chega no data center como parte de outro trabalho. | A unidade será marcada como uma unidade adicional e será retornada ao cliente quando o trabalho associado ao pacote original for concluído. |

### <a name="time-to-process-job"></a>Tempo para processar o trabalho
O tempo necessário para processar um trabalho de importação/exportação varia de acordo com diversos fatores, como tempo de envio, tipo do trabalho, tipo e tamanho dos dados sendo copiados, e tamanho dos discos fornecidos. O serviço de Importação/Exportação não tem um SLA, mas depois que os discos forem recebidos, o serviço se esforçará para concluir a cópia no prazo de 7 a 10 dias. Você pode usar a API REST para acompanhar o andamento do trabalho mais de perto. Há um parâmetro de porcentagem completa na operação Listar Trabalhos que fornece uma indicação do andamento da cópia. Contate-nos se precisar de uma estimativa para concluir um trabalho de importação/exportação de tempo crítico.

### <a name="pricing"></a>Preços
**Taxa de manuseio de unidade**

Há uma taxa de manuseio de unidade para cada unidade processada como parte do trabalho de importação ou exportação. Consulte os detalhes sobre o [Preços de Importação/Exportação do Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Custos de envio**

Quando você envia unidades do Azure, você paga pelo custo de envio para a transportadora. Quando a Microsoft devolver as unidades, o custo do envio será cobrado na conta da transportadora que você forneceu no momento da criação do trabalho.

**Custos de transação**

Não há nenhum custo de transação ao importar dados para o armazenamento de blobs. Os encargos de saída padrão são aplicáveis quando dados são exportados do armazenamento de blobs. Para obter mais detalhes sobre os custos da transação, consulte [Preços de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Início rápido
Nesta seção, fornecemos instruções passo a passo para criar um trabalho de importação e exportação. Atenda a todos os [pré-requisitos](#pre-requisites) antes de prosseguir.

> [!IMPORTANT]
> O serviço dá suporte a uma conta de armazenamento Standard por trabalho de importação ou exportação e não dá suporte a contas de armazenamento Premium. 
> 
> 
## <a name="create-an-import-job"></a>Criar um trabalho de importação
Crie um trabalho de importação para copiar os dados para sua conta de armazenamento do Azure enviando uma ou mais unidades contendo dados para o data center especificado. O trabalho de importação transmite detalhes sobre os discos rígidos, dados a serem copiados, conta de armazenamento de destino e envio de informações para o serviço de Importação/Exportação do Azure. A criação de um trabalho de importação é um processo de três etapas. Primeiro, prepare suas unidades usando a ferramenta WAImportExport. Segundo, envie um trabalho de importação usando o Portal do Azure. Terceiro, envie as unidades para o endereço de envio fornecido durante a criação do trabalho e atualize as informações de envio nos detalhes do seu trabalho.   

### <a name="prepare-your-drives"></a>Preparar suas unidades
A primeira etapa ao importar os dados usando o serviço de Importação/Exportação do Azure é preparar suas unidades usando a ferramenta WAImportExport. Siga as etapas abaixo para preparar suas unidades.

1. Identifique os dados a serem importados. Isso pode ser diretórios e arquivos autônomos no servidor local ou em um compartilhamento de rede.  
2. Determine o número de unidades que você precisará, dependendo do tamanho total dos dados. Adquira o número necessário de unidades de disco rígido SSD 2,5 polegadas ou SATA II ou III de 2,5 ou 3,5 polegadas.
3. Identifique a conta de armazenamento de destino, contêiner, diretórios virtuais e blobs.
4.  Determine os diretórios e/ou os arquivos independentes que serão copiados para cada unidade de disco rígido.
5.  Crie os arquivos CSV para o conjunto de dados e driveset.
    
    **Arquivo CSV do conjunto de dados**
    
  Abaixo está um exemplo de arquivo CSV de conjunto de dados para importar dados como blobs do Azure:
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
    "F:\50M_original\","containername/",BlockBlob,rename,"None",None 
    ```
  
  Abaixo está um exemplo de arquivo CSV de conjunto de dados para importar dados como arquivos do Azure:
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   No exemplo acima, 100M_1.csv.txt será copiado para a raiz do contêiner chamado “containername” ou “fileshare”. Se o nome do contêiner “containername” ou “Fileshare” não existir, ele será criado. Todos os arquivos e pastas em 50M_original serão copiados recursivamente em containername ou fileshare. A estrutura de pastas será mantida.

    Saiba mais sobre [como preparar o arquivo CSV de conjunto de dados](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    
    **Lembre-se**: por padrão, os dados serão importados como Blobs de Blocos. Você pode usar o valor do campo BlobType para importar dados como os Blobs de Página. Por exemplo, se você estiver importando arquivos VHD que serão montados como discos em uma VM do Azure, deverá importá-los como Blobs de Página.

    **Arquivo CSV driveset**

    O valor do sinalizador driveset é um arquivo CSV que contém a lista de discos para os quais as letras de unidade estão mapeadas, de modo que a ferramenta possa selecionar corretamente a lista de discos para preparação. 

    Veja abaixo o exemplo de arquivo CSV de driveset:
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    No exemplo acima, presume-se que dois discos estejam conectados, e volumes NTFS básicos com as letras de volume G:\ e H:\ foram criados. A ferramenta formatará e criptografará o disco que hospeda H:\ e não formatará ou criptografará o disco que hospeda o volume G:\.

    Saiba mais sobre [como preparar o arquivo CSV de driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.  Use a [ferramenta WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) para copiar seus dados para um ou mais discos rígidos.
7.  Você pode especificar "Encrypt" no campo Criptografia para habilitar a criptografia do Bitlocker no disco rígido. Como alternativa, você também pode habilitar manualmente a criptografia do Bitlocker no disco rígido, especificar "AlreadyEncrypted" e fornecer a chave no CSV do driveset durante a execução da ferramenta.

8. Não modifique os dados em unidades de disco rígido ou o arquivo de diário depois de concluir a preparação do disco.

> [!IMPORTANT]
> Cada unidade de disco rígido preparada resultará em um arquivo de diário. Ao criar o trabalho de importação usando o Portal do Azure, você deverá carregar todos os arquivos de diário das unidades que fazem parte do trabalho de importação. Unidades sem arquivos de diário não serão processadas.
> 
>

Veja abaixo os comandos e exemplos para preparar o disco rígido usando a ferramenta WAImportExport.

O comando PrepImport da ferramenta WAImportExport para a primeira sessão de cópia para copiar diretórios e/ou arquivos com uma nova sessão de cópia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Exemplo de importação 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Para **adicionar mais unidades**, é possível criar um novo arquivo de driveset e executar o comando conforme exibido a seguir. Para sessões de cópia subsequentes nas unidades de disco diferentes especificadas no arquivo .csv de InitialDriveset, especifique um novo arquivo CSV de driveset e forneça-o como um valor para o parâmetro "AdditionalDriveSet". Use o nome **mesmo arquivo de diário** e forneça uma **nova ID de sessão**. O formato do arquivo CSV AdditionalDriveset é igual ao formato InitialDriveSet.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Exemplo de importação 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

Para adicionar outros dados ao mesmo driveset, é possível chamar o comando PrepImport da ferramenta WAImportExport para sessões de cópia subsequentes a fim de copiar outros arquivos/diretórios: para sessões de cópia subsequentes nas mesmas unidades de disco rígido especificadas no arquivo .csv de InitialDriveset, especifique o **mesmo nome de arquivo de diário** e forneça uma **nova ID de sessão**; não é necessário fornecer a chave da conta de armazenamento.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Exemplo de importação 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Veja mais detalhes sobre como usar a ferramenta WAImportExport em [Preparação de discos rígidos para importação](storage-import-export-tool-preparing-hard-drives-import.md).

Além disso, confira o [Fluxo de trabalho de exemplo para preparo dos discos rígidos para um trabalho de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) para obter instruções passo a passo mais detalhadas.  

### <a name="create-the-import-job"></a>Criar o trabalho de importação
1. Depois de preparar a unidade, navegue até Mais serviços -> ARMAZENAMENTO -> "Trabalhos de importação/exportação" no Portal do Azure. Clique em **Criar Trabalho de Importação/Exportação**.

2. Na Etapa 1, Básico, selecione "Importar para o Azure", digite uma cadeia de caracteres para nome do trabalho, selecione uma assinatura, insira ou selecione um grupo de recursos. Digite um nome descritivo para o trabalho de importação. Observe que o nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar com letra e não pode conter espaços. Você usará o nome escolhido para acompanhar os trabalhos enquanto eles estiverem em andamento e quando eles estiverem concluídos.

3. Na Etapa 2, Detalhes do trabalho, carregue os arquivos de diário de unidade obtidos durante a etapa de preparação de unidade. Se a versão1 de waimportexport.exe foi usada, você precisa carregar um arquivo para cada unidade preparada. Selecione a conta de armazenamento para a qual os dados serão importados na seção Conta de armazenamento de "Destino de importação". O local de redistribuição será populado automaticamente com base na região da conta de armazenamento especificada.
   
   ![Criar o trabalho de importação - Etapa 3](./media/storage-import-export-service/import-job-03.png)
4. Na Etapa 3, Retornar informações de envio, selecione a carrier na lista suspensa e insira um número de conta da carrier válido que você criou com essa carrier. A Microsoft usará essa conta para enviar de volta as unidades para você após a conclusão do seu trabalho de importação. Forneça um nome de contato válido e completo, bem como telefone, email, endereço, cidade, zip, estado/município e país/região.
   
5. Na Página de Resumo, o endereço de envio do Azure DataCenter é fornecido para ser usado para envio de discos para o controlador de domínio do Azure. Certifique-se de que o nome do trabalho e o endereço completo são mencionados no rótulo de envio. 

6. Clique em OK na Página de Resumo para concluir a criação do trabalho de Importação.

7. Após o envio dos discos, retorne para a página **Importação/Exportação** no Portal do Azure, a) Navegue e clique no trabalho de importação b) Clique em **Atualizar status do trabalho e informações de acompanhamento quando as unidades são enviadas**. 
     c) Selecione a caixa de seleção "Marcar como enviado" d) Informe a Carrier e Número de controle.
    
   Se o número de acompanhamento não está atualizado em 2 semanas após a criação do trabalho, este irá expirar.
   
8. Você pode acompanhar o andamento do trabalho no painel do portal. Veja o que significa cada estado do trabalho na seção anterior em [Exibindo o status do trabalho](#viewing-your-job-status).

## <a name="create-an-export-job"></a>Criar um trabalho de exportação
Crie um trabalho de exportação para notificar o serviço de Importação/Exportação que você enviará uma ou mais unidades vazias para o data center para que os dados possam ser exportados de sua conta de armazenamento para as unidades e as unidades, então, sejam enviadas para você.

### <a name="prepare-your-drives"></a>Preparar suas unidades
As pré-verificações a seguir são recomendadas para preparar suas unidades para um trabalho de exportação:

1. Verifique o número de discos necessários usando o comando PreviewExport da ferramenta WAImportExport. Para obter mais informações, consulte [Visualizando o uso da unidade para um trabalho de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). A ferramenta ajuda você a visualizar o uso da unidade para os blobs que você selecionou, com base no tamanho das unidades que você pretende usar.
2. Verifique se você pode ler/gravar no disco rígido que será enviado para o trabalho de exportação.

### <a name="create-the-export-job"></a>Criar o trabalho de exportação
1. Para criar um trabalho de exportação, navegue até Mais serviços -> ARMAZENAMENTO -> "Trabalhos de importação/exportação" no Portal do Azure. Clique em **Criar Trabalho de Importação/Exportação**.
2. Na Etapa 1, Básico, selecione "Exportar do Azure", digite uma cadeia de caracteres para nome do trabalho, selecione uma assinatura, insira ou selecione um grupo de recursos. Digite um nome descritivo para o trabalho de importação. Observe que o nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar com letra e não pode conter espaços. Você usará o nome escolhido para acompanhar os trabalhos enquanto eles estiverem em andamento e quando eles estiverem concluídos. Forneça as informações de contato da pessoa responsável por esse trabalho de exportação. 

3. Nos Detalhes do trabalho da Etapa 2, selecione a conta de armazenamento da qual os dados serão exportados na seção Conta de armazenamento. O local de redistribuição será populado automaticamente com base na região da conta de armazenamento especificada. Especifique quais dados de blob deseja exportar da sua conta de armazenamento para a(s) unidade(s) em branco. Você pode optar por exportar todos os dados de blob na conta de armazenamento ou especificar quais blobs ou conjuntos de blobs serão exportados.
   
   Para especificar um blob para exportação, use o seletor **Igual a** e especifique o caminho relativo do blob, começando pelo nome do contêiner. Use *$root* para especificar o contêiner raiz.
   
   Para especificar todos os blobs que começam com um prefixo, use o seletor **Começa com** e especifique o prefixo, começando com uma barra '/'. O prefixo pode ser do nome do contêiner, o nome do contêiner completo ou o nome do contêiner completo seguido do prefixo do nome do blob.
   
   A tabela a seguir mostra exemplos de caminhos de blob válidos:
   
   | Seletor | Caminho do Blob | Descrição |
   | --- | --- | --- |
   | Começa com |/ |Exporta todos os blobs na conta de armazenamento |
   | Começa com |/$root/ |Exporta todos os blobs no contêiner raiz |
   | Começa com |/book |Exporta todos os blobs em qualquer contêiner que comece com o prefixo **book** |
   | Começa com |/music/ |Exporta todos os blobs no contêiner **music** |
   | Começa com |/music/love |Exporta todos os blobs no contêiner **music** que comecem com o prefixo **love** |
   | Igual a |$root/logo.bmp |Exporta o blob **logo.bmp** no contêiner raiz |
   | Igual a |videos/story.mp4 |Exporta o blob **story.mp4** no contêiner **videos** |
   
   Você deve fornecer os caminhos de blob nos formatos válidos para evitar erros durante o processamento, como mostrado nesta captura de tela.
   
   ![Criar o trabalho de exportação - Etapa 3](./media/storage-import-export-service/export-job-03.png)

4. Na Etapa 3, Retornar informações de envio, selecione a carrier na lista suspensa e insira um número de conta da carrier válido que você criou com essa carrier. A Microsoft usará essa conta para enviar de volta as unidades para você após a conclusão do seu trabalho de importação. Forneça um nome de contato válido e completo, bem como telefone, email, endereço, cidade, zip, estado/município e país/região.
   
 5. Na Página de Resumo, o endereço de envio do Azure DataCenter é fornecido para ser usado para envio de discos para o controlador de domínio do Azure. Certifique-se de que o nome do trabalho e o endereço completo são mencionados no rótulo de envio. 

6. Clique em OK na Página de Resumo para concluir a criação do trabalho de Importação

7. Após o envio dos discos, retorne para a página **Importação/Exportação** no Portal do Azure, a) Navegue e clique no trabalho de importação b) Clique em **Atualizar status do trabalho e informações de acompanhamento quando as unidades são enviadas**. 
     c) Selecione a caixa de seleção "Marcar como enviado" d) Informe a Carrier e Número de controle.
    
   Se o número de acompanhamento não está atualizado em 2 semanas após a criação do trabalho, este irá expirar.
   
8. Você pode acompanhar o andamento do trabalho no painel do portal. Veja o que significa cada estado do trabalho na seção anterior em [Exibindo o status do trabalho](#viewing-your-job-status).

   > [!NOTE]
   > Se o blob a ser exportado estiver em uso no momento da cópia do disco rígido, o serviço de Importação/Exportação do Azure tira um instantâneo do blob e copia o instantâneo.
   > 
   > 
 
9. Depois de receber as unidades com os dados exportados, você poderá exibir e copiar as chaves do BitLocker geradas pelo serviço para a unidade. No Portal do Azure, navegue até o Portal do Azure e clique na guia Importação/Exportação. Selecione o trabalho de exportação na lista e clique na opção Chaves do BitLocker. As chaves do BitLocker aparecem como mostrado abaixo:
   
   ![Exibir chaves do BitLocker para um trabalho de exportação](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Leia a seção de perguntas frequentes abaixo, que abrange as perguntas mais comuns que os clientes encontram ao usar esse serviço.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Posso copiar o armazenamento de Arquivos do Azure usando o serviço de Importação/Exportação do Azure?**

Sim, o serviço de importação/exportação do Azure dá suporte à importação no Armazenamento de Arquivo do Azure. Ele não dá suporte à exportação de Arquivos do Azure no momento.

**O serviço de Importação/Exportação do Azure está disponível para as assinaturas de CSP?**

Não, o serviço de Importação/Exportação do Azure não dá suporte a assinaturas de CSP.

**Posso ignorar a etapa de preparação da unidade para um trabalho de importação ou posso me preparar uma unidade sem copiar?**

Qualquer unidade que você deseja enviar para importar os dados deve ser preparada usando a ferramenta WAImportExport do Azure. Você deve usar a ferramenta WAImportExport para copiar dados na unidade.

**É necessário executar alguma preparação de disco ao criar um trabalho de exportação?**

Não, mas algumas pré-verificações são recomendadas. Verifique o número de discos necessários usando o comando PreviewExport da ferramenta WAImportExport. Para obter mais informações, consulte [Visualizando o uso da unidade para um trabalho de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). A ferramenta ajuda você a visualizar o uso da unidade para os blobs que você selecionou, com base no tamanho das unidades que você pretende usar. Verifique também se você pode ler e gravar no disco rígido que será enviado para o trabalho de exportação.

**O que acontecerá se eu enviar por engano uma unidade de disco rígido que não esteja em conformidade com os requisitos com suporte?**

O data center do Azure devolverá a unidade que não estiver em conformidade com os requisitos com suporte. Se apenas algumas unidades no pacote atenderem aos requisitos de suporte, elas serão processadas e as unidades que não atenderem aos requisitos serão retornadas para você.

**Posso cancelar meu trabalho?**

Você pode cancelar um trabalho quando seu status for Criando ou Enviando.

**Durante quanto tempo consigo exibir o status dos trabalhos concluídos no Portal do Azure?**

Você pode exibir o status dos trabalhos concluídos por até 90 dias. Os trabalhos concluídos serão excluídos após 90 dias.

**Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?**

Um trabalho de importação ou de exportação pode fazer referência a apenas 10 unidades em um único trabalho para o serviço de Importação/Exportação. Se quiser enviar mais de 10 unidades, você poderá criar vários trabalhos. Unidades que estão associadas com o mesmo trabalho devem ser enviadas juntas no mesmo pacote.
A Microsoft oferece orientações e assistência quando a capacidade dos dados se estender por vários trabalhos de importação de disco. Entre em contato com bulkimport@microsoft.com para saber mais

**O serviço formata as unidades antes de retorná-las?**

Não. Todas as unidades são criptografadas com o BitLocker.

**Pode adquirir unidades para os trabalhos de importação/exportação da Microsoft?**

Não. Você precisará enviar suas próprias unidades para os trabalhos de importação e exportação.

**Como fazer para acessar dados importados por este serviço**

Os dados em sua conta de armazenamento do Azure podem ser acessados por meio do Portal do Azure ou usando uma ferramenta autônoma chamada Gerenciador de Armazenamento. https://docs.microsoft.com/pt-br/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**Depois de o trabalho de importação ser concluído, como ficarão meus dados na conta de armazenamento? A hierarquia de diretório será preservada?**

Ao preparar um disco rígido para um trabalho de importação, o destino será especificado pelo parâmetro DstBlobPathOrPrefix no CSV do conjunto de dados. É o contêiner de destino na conta de armazenamento para o qual os dados do disco rígido serão copiados. Dentro desse contêiner de destino, diretórios virtuais são criados para as pastas do disco rígido e blobs são criados para os arquivos. 

**Se a unidade tiver arquivos que já existem na minha conta de armazenamento, o serviço substituirá os blobs existentes em minha conta de armazenamento?**

Ao preparar a unidade, você pode especificar se os arquivos de destino devem ser substituídos ou ignorados usando o campo no arquivo CSV chamado Disposition:<rename|no-overwrite|overwrite>. Por padrão, o serviço irá renomear os novos arquivos em vez de substituir os blobs existentes.

**A ferramenta WAImportExport só é compatível com sistemas operacionais de 32 bits?**
Nº A ferramenta WAImportExport só é compatível com o sistema de operacional do Windows de 64 bits. Confira a seção Sistemas Operacionais em [pré-requisitos](#pre-requisites) para obter uma lista completa de versões do sistema operacional com suporte.

**Devo incluir algo diferente de unidade de disco rígido no meu pacote?**

Envie somente seus discos rígidos. Não inclua itens como cabos de alimentação ou cabos USB.

**É necessário enviar minhas unidades usando FedEx ou DHL?**

Você pode enviar as unidades para o data center usando qualquer transportadora conhecida, como FedEx, DHL, UPS ou Serviço Postal. No entanto, para enviar de volta as unidades para você a partir do data center, você deverá fornecer um número de conta FedEx nos EUA e UE ou um número de conta DHL nas regiões da Ásia e Austrália.

**Há restrições quanto ao envio de minha unidade internacionalmente?**

Observe que a mídia física que está enviando talvez precise cruzar fronteiras internacionais. Você é responsável por garantir que seus dados e mídia física sejam importados e/ou exportados de acordo com as leis aplicáveis. Antes de enviar a mídia física, verifique com seus consultores se a mídia e os dados podem ser enviados legalmente ao data center identificado. Isso ajudará a garantir que eles cheguem à Microsoft pontualmente.

**Ao criar um trabalho, o endereço de envio será uma localização diferente da localização da minha conta de armazenamento. O que devo fazer?**

Alguns locais de armazenamento de conta são mapeados para os locais de entrega alternativos. Locais de envio disponíveis anteriormente também podem ser temporariamente mapeados para locais alternativos. Sempre verifique o endereço de envio fornecido durante a criação do trabalho antes de enviar suas unidades.

**Ao enviar a unidade, a transportadora solicitará o endereço e o número de telefone de contato do data center. O que devo fornecer?**

O número de telefone e o endereço DC são fornecidos como parte da criação do trabalho.

**Posso usar o serviço de Importação/Exportação do Azure para copiar os dados do SharePoint e as caixas de correio PST para O365?**

Confira [Import PST files or SharePoint data to Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx)(Importar arquivos PST ou dados do SharePoint para o Office 365).

**Posso usar o serviço de Importação/Exportação do Azure para copiar meus backups offline para o Serviço de Backup do Azure?**

Confira o [Fluxo de trabalho de backup offline no Backup do Azure](../../backup/backup-azure-backup-import-export.md).

**Qual é o número máximo de HDDs em uma remessa?**

Qualquer número de HDDs pode ser em uma remessa e se os discos pertencerem a vários trabalhos, é recomendável a) Rotular os discos com os nomes de trabalho correspondentes. b) Atualizar os trabalhos com um número de acompanhamento com um sufixo -1,-2, etc.
  
**Qual é o Tamanho Máximo de Blob de Blocos e de Blob de Páginas com suporte na Importação/Exportação de Disco?**

O tamanho máximo de Blob de Blocos é de aproximadamente 4,768 TB ou 5.000.000 MB.
O tamanho máximo de Blob de Páginas é de 1 TB.

**A Importação/Exportação de Disco dá suporte à criptografia AES 256?**

Por padrão, o serviço de Importação/Exportação do Azure criptografa com a criptografia BitLocker AES 128, mas pode ser aumentado para AES 256 com a criptografia manual com o BitLocker antes da cópia dos dados. 

Se estiver usando o [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), veja abaixo um comando de exemplo
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
Se estiver usando a [Ferramenta WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip), especifique “AlreadyEncrypted” e forneça a chave no CSV do driveset.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>Próximas etapas

* [Configuração da ferramenta WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)
* [Exemplo de API REST de importação e exportação do Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

