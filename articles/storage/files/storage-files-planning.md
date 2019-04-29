---
title: Planejando uma implantação de Arquivos do Azure | Microsoft Docs
description: Saiba o que considerar ao planejar uma implantação de Arquivos do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e2b2621ac8ee5b9ee84aaa978e8b915c98c5b702
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61095515"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planejando uma implantação de Arquivos do Azure

O [Arquivos do Azure](storage-files-introduction.md) oferece compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo SMB padrão no setor. Já que o Arquivos do Azure é totalmente gerenciado, implantá-lo em cenários de produção é muito mais fácil do que implantar e gerenciar um servidor de arquivos ou um dispositivo NAS. Este artigo aborda os tópicos a serem considerados ao implantar um compartilhamento de Arquivos do Azure para uso em produção dentro de sua organização.

## <a name="management-concepts"></a>Conceitos de gerenciamento

 O diagrama a seguir ilustra as construções do gerenciamento do Arquivos do Azure:

![Estrutura do Arquivo](./media/storage-files-introduction/files-concepts.png)

* **Conta de Armazenamento**: Todo o acesso ao Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter detalhes sobre a capacidade da conta de armazenamento.

* **Compartilhamento**: Um compartilhamento do Armazenamento de Arquivos é um compartilhamento de arquivo SMB no Azure. Todos os arquivos e diretórios devem ser criados em um compartilhamento pai. Uma conta pode conter um número ilimitado de compartilhamentos e um compartilhamento pode armazenar um número ilimitado de arquivos, até a capacidade total de 5 TiB do compartilhamento de arquivos.

* **Diretório**: Uma hierarquia opcional de diretórios.

* **Arquivo**: Um arquivo no compartilhamento. Um arquivo pode ter até 1 TiB de tamanho.

* **Formato de URL**: Para solicitações a um compartilhamento de arquivo do Azure feitas com o protocolo REST de Arquivo, os arquivos são endereçáveis usando o seguinte formato de URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Método de acesso a dados

O Arquivos do Azure oferece dois métodos de acesso a dados internos e práticos, que você pode usar separadamente ou combinados entre si para acessar seus dados:

1. **Acesso direto à nuvem**: Qualquer compartilhamento de arquivo do Azure pode ser montado por [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) e/ou [Linux](storage-how-to-use-files-linux.md) com o protocolo SMB padrão do setor ou por meio da API REST de Arquivo. Com o SMB, leituras e gravações para arquivos no compartilhamento são feitas diretamente no compartilhamento de arquivos no Azure. Para montar uma VM no Azure, o cliente SMB no sistema operacional deve dar suporte ao menos ao SMB 2.1. Para montar localmente, por exemplo, na estação de trabalho do usuário, o cliente SMB com suporte pela estação de trabalho deverá, por sua vez, dar suporte ao menos ao SMB 3.0 (com criptografia). Além de SMB, novos aplicativos ou serviços podem acessar diretamente o compartilhamento de arquivos por meio de REST de Arquivo, que fornece uma interface de programação de aplicativo fácil e escalonável para desenvolvimento de software.
2. **Sincronização de Arquivos do Azure**: Com a Sincronização de Arquivos do Azure, os compartilhamentos podem ser replicados para Servidores Windows locais ou no Azure. Seus usuários acessariam o compartilhamento de arquivos por meio do Windows Server, por exemplo, por meio de um compartilhamento NFS ou SMB. Isso é útil para cenários nos quais os dados serão acessados e modificados longe de um datacenter do Azure, como em um cenário de filial. Os dados podem ser replicados entre vários pontos de extremidade do Windows Server, por exemplo, entre várias filiais. Finalmente, dados podem ser divididos em camadas para arquivos do Azure, de modo que todos os dados ainda serão acessíveis por meio do servidor, mas ele não terá uma cópia completa dos dados. Em vez disso, os dados são recuperados diretamente quando abertos pelo usuário.

A tabela a seguir ilustra como os usuários e aplicativos podem acessar o compartilhamento de Arquivos do Azure:

| | Acesso direto à nuvem | Sincronização de Arquivos do Azure |
|------------------------|------------|-----------------|
| Quais protocolos você precisa usar? | O Arquivos do Azure dá suporte a SMB 2.1, a SMB 3.0 e à API REST de arquivo. | Acessar o compartilhamento de arquivos do Azure por meio de qualquer protocolo com suporte no Windows Server (SMB, NFS, FTPS, etc.) |  
| Onde você está executando a carga de trabalho? | **No Azure**: Os Arquivos do Azure oferecem acesso direto aos seus dados. | **Localmente, com rede lenta**: Clientes Windows, Linux e macOS podem montar um compartilhamento de arquivo do Windows local localmente como um cache rápido do seu compartilhamento de arquivo do Azure. |
| De que nível de ACLs você precisa? | Nível de compartilhamento e de arquivo. | Nível de compartilhamento, de arquivo e de usuário. |

## <a name="data-security"></a>Segurança de dados

O Arquivos do Azure tem várias opções integradas para garantir a segurança dos dados:

* Suporte para criptografia em ambos os protocolos over-the-wire: Criptografia SMB 3.0 e REST de Arquivo via HTTPS. Por padrão: 
    * Os clientes que dão suporte à criptografia SMB 3.0 enviar e recebem dados por um canal criptografado.
    * Clientes que não dão suporte a SMB 3.0 com criptografia podem se comunicar interior do datacenter via SMB 2.1 ou SMB 3.0 sem criptografia. Os clientes do SMB não têm permissão para se comunicar no interior do datacenter via SMB 2.1 ou SMB 3.0 sem criptografia.
    * Os clientes podem se comunicar por REST de arquivo via HTTP ou HTTPS.
* Criptografia em repouso ([Criptografia do Serviço de Armazenamento do Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): A SSE (Criptografia do Serviço de Armazenamento) está habilitada para todas as contas de armazenamento. Os dados em repouso são criptografados com chaves totalmente gerenciadas. Criptografia em repouso não aumenta os custos de armazenamento nem reduz o desempenho. 
* Requisito opcional de dados criptografados em trânsito: quando selecionado, o Arquivos do Azure rejeitam o acesso aos dados por canais sem criptografia. Especificamente, são permitidas somente HTTPS e SMB 3.0 com conexões de criptografia.

    > [!Important]  
    > A exigência de transferência segura de dados fará com que clientes SMB mais antigos não sejam capazes de se comunicar com o SMB 3.0 com criptografia. Para obter mais informações, consulte [Montar no Windows](storage-how-to-use-files-windows.md), [Montar no Linux](storage-how-to-use-files-linux.md) e [Montar no macOS](storage-how-to-use-files-mac.md).

Para segurança máxima, recomendamos habilitar ambas a criptografia em repouso e a criptografia de dados em trânsito sempre que você estiver usando clientes modernos para acessar seus dados. Por exemplo, se você precisa montar um compartilhamento em uma VM Windows Server 2008 R2, que só dá suporte a SMB 2.1, você precisa permitir tráfego não criptografado para sua conta de armazenamento, já que o SMB 2.1 não dá suporte a criptografia.

Se você estiver usando a Sincronização de Arquivos do Azure para acessar o compartilhamento de Arquivos do Azure, usaremos sempre HTTPS e SMB 3.0 com criptografia para sincronizar seus dados aos Windows Servers, independentemente de você exigir ou não criptografia de dados em repouso.

## <a name="file-share-performance-tiers"></a>Níveis de desempenho do compartilhamento de arquivos

Arquivos do Azure oferece dois níveis de desempenho: standard e premium.

* Os **compartilhamentos de arquivos padrão** contam com HDDs (unidades de disco rígido) rotacionais que fornecem um desempenho confiável para as cargas de trabalho de E/S que são menos sensíveis à variabilidade de desempenho, como os compartilhamentos de arquivos de finalidade geral e os ambientes de desenvolvimento/teste. Os compartilhamentos de arquivos padrão estão disponíveis apenas em um modelo de cobrança pago conforme o uso.
* Os **compartilhamentos de arquivos Premium (versão prévia)** contam com SSDs (discos de estado sólido) que fornecem alto desempenho consistente e baixa latência, em milissegundos de dígito único para a maioria das operações de E/S e para as cargas de E/S mais intensivas. Isso os torna adequados para uma ampla variedade de cargas de trabalho, como bancos de dados, hospedagem de websites, ambientes de desenvolvimento, etc. Os compartilhamentos de arquivos Premium estão disponíveis em um modelo de cobrança provisionado. Compartilhamentos de arquivos do Premium usam um modelo de implantação separado de compartilhamentos de arquivos padrão. Se você quiser saber como criar um compartilhamento de arquivos do premium, consulte nosso artigo sobre o assunto: [Como criar uma conta de armazenamento de arquivos do Azure premium](storage-how-to-create-premium-fileshare.md).

> [!IMPORTANT]
> Arquivo Premium compartilhamentos ainda estão em visualização, disponível apenas com o LRS e só estão disponíveis em um subconjunto de regiões com suporte de Backup do Azure estejam disponíveis no selecionar regiões:

|Região disponível  |Suporte de Backup do Azure  |
|---------|---------|
|Leste dos EUA 2      | Sim|
|Leste dos EUA       | Sim|
|Oeste dos EUA       | Não  |
|Oeste dos EUA 2      | Não  |
|Centro dos EUA    | Não  |
|Norte da Europa  | Não  |
|Europa Ocidental   | Sim|
|Sudeste da Ásia       | Sim|
|Ásia Oriental     | Não  |
|Leste do Japão    | Não  |
|Oeste do Japão    | Não  |
|Coreia Central | Não  |
|Leste da Austrália| Não  |

### <a name="provisioned-shares"></a>Compartilhamentos provisionados

Os compartilhamentos de arquivos (versão prévia) Premium são provisionados com base em uma taxa fixa de GiB/IOPS/taxa de transferência. Para cada GiB provisionado, o compartilhamento emitirá um IOPS e uma taxa de transferência de 0,1 MiB/s até os limites máximos por compartilhamento. O provisionamento mínimo permitido é de 100 GiB, com um IOPS e uma taxa de transferência mínimos.

Com base no melhor esforço, todos os compartilhamentos poderão acumular até três IOPS por GiB de armazenamento provisionado por 60 minutos ou mais, dependendo do tamanho do compartilhamento. Os novos compartilhamentos começam com o crédito de intermitência completa com base na capacidade provisionada.

Compartilhamentos devem ser provisionados em incrementos de 1 GiB. Tamanho mínimo é de 100 GiB, próximo tamanho é GIB 101 e assim por diante.

> [!TIP]
> Linha de base de IOPS = 1 * provisionado GiB. (Até um máximo de 100.000 IOPS).
>
> Limite de intermitência = 3 * IOPS de linha de base. (Até um máximo de 100.000 IOPS).
>
> taxa de saída = 60 MiB/s + 0,06 * provisionado GiB
>
> taxa de entrada = 40 MiB/s + 0,04 * provisionado GiB

Tamanho do compartilhamento pode ser aumentado a qualquer momento, mas pode ser diminuído apenas após 24 horas desde o último aumento. Após aguardar 24 horas sem um aumento de tamanho, você pode diminuir o tamanho do compartilhamento quantas vezes até que você aumentá-lo novamente. As alterações de escala IOPS/taxa de transferência entrarão em vigor em poucos minutos após a alteração de tamanho.

A tabela a seguir ilustra alguns exemplos dessas fórmulas para os tamanhos do compartilhamento provisionado:

(Tamanhos indicado por um * estão em visualização pública limitada)

|Capacidade (GiB) | IOPS de linha de base | IOPS de intermitente | Saída (MiB/s) | Entrada (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Até 300     | 66   | 44   |
|500         | 500     | Até 1500   | 90   | 60   |
|1.024       | 1.024   | Até 3.072   | 122   | 81   |
|5.120       | 5.120   | Até 15.360  | 368   | 245   |
|10,240 *     | 10,240  | Até 30.720  | 675 | 450   |
|33,792 *     | 33,792  | Até 100.000 | 2,088 | 1,392   |
|51,200 *     | 51,200  | Até 100.000 | 3.132 | 2,088   |
|102,400 *    | 100.000 | Até 100.000 | 6,204 | 4,136   |

Atualmente, tamanhos de compartilhamento de arquivo até 5 TiB estão em visualização pública, enquanto os tamanhos de até 100 TiB estão em visualização pública limitada, para solicitar acesso a visualização pública limitada completa [desta pesquisa.](https://aka.ms/azurefilesatscalesurvey)

### <a name="bursting"></a>Intermitência

Compartilhamentos de arquivos do Premium podem estourar seu IOPS até um fator de três. Intermitência é automatizado e opera com base em um sistema de crédito. Intermitência funciona em uma base de melhor esforço e o limite de intermitência não é uma garantia, compartilhamentos de arquivos podem estourar *até* o limite.

Os créditos são acumulados em um bucket de intermitente sempre que o tráfego para o compartilhamento de arquivos está abaixo da linha de base IOPS. Por exemplo, um compartilhamento de 100 GiB tem a linha de base de 100 IOPS. Se tráfego real no compartilhamento de 40 IOPS para um intervalo específico de 1 segundo, o IOPS não utilizado 60 são creditado para um bucket de intermitente. Esses créditos, em seguida, serão usados posteriormente quando operações excederia a IOPs de linha de base.

> [!TIP]
> Tamanho do bucket de intermitente = Baseline_IOPS * 3600 * 2.

Sempre que um compartilhamento excede a linha de base IOPS e tem créditos em um bucket de intermitente, ele será intermitente. Compartilhamentos podem continuar a intermitência créditos restantes, desde que, embora os compartilhamentos de menores que 50 TiB apenas permanecerão no limite de intermitência para até uma hora. Compartilhamentos de maiores que 50 TiB tecnicamente podem exceder esse limite de uma hora, backup para duas horas, mas, isso se baseia no número de créditos de intermitente vencidas. Cada e/s, além da linha de base de IOPS consome um crédito e depois que todos os créditos são consumidos o compartilhamento retornaria a IOPS de linha de base.

Créditos de compartilhamento tem três estados:

- Acúmulo, quando o compartilhamento de arquivos estiver usando menos que a linha de base IOPS.
- Diminuindo, quando o compartilhamento de arquivos é bursting.
- IOPS restantes constante, quando não houver nenhum créditos ou a linha de base estão em uso.

Início de compartilhamentos de arquivo novo com o número total de créditos em seu bucket de intermitente. Não serão ser acumulados créditos de intermitente se o compartilhamento de IOPS cair abaixo da linha de base IOPS devido à limitação pelo servidor.

## <a name="file-share-redundancy"></a>Redundância de compartilhamento de arquivo

Compartilhamentos de padrão de arquivos do Azure dá suporte a três opções de redundância de dados: armazenamento localmente redundante (LRS), o armazenamento com redundância de zona (ZRS) e o armazenamento com redundância geográfica (GRS).

Premium de arquivos do Azure compartilha só dá suporte a armazenamento localmente redundante (LRS).

As seções a seguir descrevem as diferenças entre as opções de redundância diferentes:

### <a name="locally-redundant-storage"></a>Armazenamento com redundância local

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

> [!Warning]  
> Se estiver usando o compartilhamento de arquivos do Azure como um ponto de extremidade de nuvem em uma conta de armazenamento GRS, você não deve iniciar o failover da conta de armazenamento. Se isso for feito, a sincronização deixará de funcionar e poderá causar a perda inesperada de dados no caso de arquivos recentes em camadas. No caso de perda de uma região do Azure, a Microsoft disparará o failover da conta de armazenamento de modo que seja compatível com a Sincronização de Arquivos do Azure.

O armazenamento com redundância geográfica (GRS) foi desenvolvido para fornecer pelo menos 99.99999999999999% (16 9’s) durabilidade dos objetos em um determinado ano, replicando dados para uma região secundária situada a centenas de milhas de distância da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

Se você optar pelo armazenamento com redundância geográfica de acesso de leitura (RA-GRS), você deve saber que arquivos do Azure não dá suporte a armazenamento com redundância geográfica de acesso de leitura (RA-GRS) em qualquer região no momento. Compartilhamentos de arquivos na conta de armazenamento RA-GRS funcionam como fariam em contas GRS e são cobrados os preços do GRS.

O GRS replica seus dados para outro datacenter em uma região secundária, mas esses dados estão disponíveis para serem lidos somente se a Microsoft iniciar um failover da região primária para a secundária.

Para uma conta de armazenamento com GRS habilitado, todos os dados são inicialmente replicados com armazenamento localmente redundante (LRS). Uma atualização primeiro é confirmada para o local primário e replicados usando o LRS. A atualização, em seguida, é replicada assincronamente para a região secundária usando GRS. Quando dados são gravados para o local secundário, ela também é replicada dentro desse local usando o LRS.

Ambas as regiões primárias e secundárias gerenciam réplicas entre domínios de falha separados e atualizar domínios dentro de uma unidade de escala de armazenamento. A unidade de escala de armazenamento é a unidade de replicação básica dentro do datacenter. A replicação com esse nível é fornecida por LRS; Para obter mais informações, consulte [armazenamento localmente redundante (LRS): Redundância de dados de baixo custo para o Armazenamento do Azure](../common/storage-redundancy-lrs.md).

Lembre-se esses pontos ao decidir qual opção de replicação para usar:

* Armazenamento com redundância de zona (ZRS) fornece alta disponibilidade com replicação síncrona e pode ser uma escolha melhor em alguns cenários que GRS. Para obter mais informações sobre o ZRS, consulte [ZRS](../common/storage-redundancy-zrs.md).
* A replicação assíncrona envolve um atraso entre a hora em que dados são gravados na região primária e quando são replicados na região secundária. Caso ocorra um desastre na região, as alterações que ainda não foram replicadas para a região secundária poderão ser pedidas se os dados não puderem ser recuperados na região primária.
* Com o GRS, a réplica não está disponível para acesso de leitura ou gravação, a menos que a Microsoft inicie um failover na região secundária. Em caso de failover, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de recuperação de desastre](../common/storage-disaster-recovery-guidance.md).

## <a name="data-growth-pattern"></a>Padrão de crescimento de dados

Hoje, o tamanho máximo para um compartilhamento de arquivos do Azure é 5 TiB (visualização pública limitada, compartilham 100 TiB para arquivo premium). Devido a essa limitação atual, você deve considerar o crescimento esperado dos dados durante a implantação de um compartilhamento de Arquivos do Azure.

É possível sincronizar vários compartilhamentos de arquivos do Azure em um único Servidor de Arquivos do Windows com a Sincronização de Arquivos do Azure. Isso lhe assegura que os compartilhamentos de arquivos mais antigos e maiores, que talvez você tenha localmente, poderão ser transferidos para a Sincronização de Arquivos do Azure. Para obter mais informações, veja [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-files-planning.md).

## <a name="data-transfer-method"></a>Método de transferência de dados

Há muitas opções fáceis para transferência de dados em massa de um arquivo de compartilhamento existente, tal como um compartilhamento de arquivos local, para o Arquivos do Azure. Alguns populares incluem (lista não exaustiva):

* **Sincronização de Arquivos do Azure**: Como parte de uma primeira sincronização entre um compartilhamento de arquivo do Azure (um "Ponto de Extremidade da Nuvem") e um namespace de diretório do Windows (um "Ponto de Extremidade de Servidor"), a Sincronização de Arquivos do Azure replicará todos os dados do compartilhamento de arquivo existente para o Arquivos do Azure.
* **[Importação/Exportação do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: O serviço de importação/exportação do Azure permite a transferência de grandes quantidades de dados com segurança em um compartilhamento de arquivos do Azure pelo envio de discos rígidos para um datacenter do Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy é uma ferramenta de cópia bem conhecida que é fornecida com o Windows e o Windows Server. Robocopy pode ser usado para transferir dados para arquivos do Azure montando o compartilhamento de arquivos localmente e, em seguida, usando a localização montada como o destino no comando Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: O AzCopy é um utilitário de linha de comando projetado para copiar dados de e para os Arquivos do Azure e o Armazenamento de Blobs do Azure, usando comandos simples com o desempenho ideal. O AzCopy está disponível para Windows, Mac e Linux.

## <a name="next-steps"></a>Próximas etapas
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
* [Implantando Arquivos do Azure](storage-files-deployment-guide.md)
* [Implantando a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
