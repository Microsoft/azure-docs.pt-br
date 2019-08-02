---
title: Planejando uma implantação de Arquivos do Azure | Microsoft Docs
description: Saiba o que considerar ao planejar uma implantação de Arquivos do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6282ce426b08c4ad9c44bead0bd4ec3d259f65fe
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501426"
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
    * Os clientes que dão suporte à criptografia SMB 3,0 enviam e recebem dados por um canal criptografado.
    * Os clientes que não dão suporte a SMB 3,0 com criptografia podem se comunicar dentro do datacenter sobre SMB 2,1 ou SMB 3,0 sem criptografia. Os clientes do SMB não têm permissão para se comunicar no interior do datacenter via SMB 2.1 ou SMB 3.0 sem criptografia.
    * Os clientes podem se comunicar por REST de arquivo via HTTP ou HTTPS.
* Criptografia em repouso ([Criptografia do Serviço de Armazenamento do Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): A SSE (Criptografia do Serviço de Armazenamento) está habilitada para todas as contas de armazenamento. Os dados em repouso são criptografados com chaves totalmente gerenciadas. Criptografia em repouso não aumenta os custos de armazenamento nem reduz o desempenho. 
* Requisito opcional de dados criptografados em trânsito: quando selecionado, o Arquivos do Azure rejeitam o acesso aos dados por canais sem criptografia. Especificamente, são permitidas somente HTTPS e SMB 3.0 com conexões de criptografia.

    > [!Important]  
    > A exigência de transferência segura de dados fará com que clientes SMB mais antigos não sejam capazes de se comunicar com o SMB 3.0 com criptografia. Para obter mais informações, consulte [Montar no Windows](storage-how-to-use-files-windows.md), [Montar no Linux](storage-how-to-use-files-linux.md) e [Montar no macOS](storage-how-to-use-files-mac.md).

Para segurança máxima, recomendamos habilitar ambas a criptografia em repouso e a criptografia de dados em trânsito sempre que você estiver usando clientes modernos para acessar seus dados. Por exemplo, se você precisa montar um compartilhamento em uma VM Windows Server 2008 R2, que só dá suporte a SMB 2.1, você precisa permitir tráfego não criptografado para sua conta de armazenamento, já que o SMB 2.1 não dá suporte a criptografia.

Se você estiver usando a Sincronização de Arquivos do Azure para acessar o compartilhamento de Arquivos do Azure, usaremos sempre HTTPS e SMB 3.0 com criptografia para sincronizar seus dados aos Windows Servers, independentemente de você exigir ou não criptografia de dados em repouso.

## <a name="file-share-performance-tiers"></a>Níveis de desempenho do compartilhamento de arquivos

Os arquivos do Azure oferecem dois níveis de desempenho: Standard e Premium.

### <a name="standard-file-shares"></a>Compartilhamentos de arquivos padrão

Os compartilhamentos de arquivos padrão são apoiados por HDDs (unidades de disco rígido). Os compartilhamentos de arquivos padrão fornecem um desempenho confiável para cargas de trabalho de e/s que são menos sensíveis à variabilidade de desempenho, como compartilhamentos de arquivos de uso geral e ambientes de desenvolvimento/teste. Os compartilhamentos de arquivos padrão estão disponíveis apenas em um modelo de cobrança pago conforme o uso.

Os compartilhamentos de arquivos padrão de até 5 TiB de tamanho estão disponíveis como uma oferta GA. Enquanto os compartilhamentos de arquivos maiores, que são compartilhamentos maiores que 5 TiB, até um máximo de 100 TiB, estão disponíveis atualmente como uma oferta de visualização.

> [!IMPORTANT]
> Consulte a seção [carregar para compartilhamentos de arquivos maiores (camada Standard)](#onboard-to-larger-file-shares-standard-tier) para obter as etapas para carregar, bem como o escopo e as restrições da versão prévia.

### <a name="premium-file-shares"></a>Compartilhamentos de arquivos Premium

Os compartilhamentos de arquivos Premium são apoiados por SSDs (unidades de estado sólido). Os compartilhamentos de arquivos Premium fornecem alto desempenho e baixa latência consistentes em milissegundos de dígito único para a maioria das operações de e/s, para cargas de trabalho com uso intensivo de e/s. Isso os torna adequados para uma ampla variedade de cargas de trabalho, como bancos de dados, Hospedagem de site e ambientes de desenvolvimento. Os compartilhamentos de arquivos Premium estão disponíveis em um modelo de cobrança provisionado. Os compartilhamentos de arquivos Premium usam um modelo de implantação separado dos compartilhamentos de arquivos padrão.

O backup do Azure está disponível para compartilhamentos de arquivos Premium e o serviço kubernetes do Azure dá suporte a compartilhamentos de arquivos Premium na versão 1,13 e superior.

Se você quiser saber como criar um compartilhamento de arquivos premium, consulte nosso artigo sobre o assunto: [Como criar uma conta de armazenamento de arquivos premium do Azure](storage-how-to-create-premium-fileshare.md).

No momento, não é possível converter diretamente entre um compartilhamento de arquivos padrão e um compartilhamento de arquivos premium. Se desejar alternar para qualquer camada, você deverá criar um novo compartilhamento de arquivos nessa camada e copiar manualmente os dados do compartilhamento original para o novo compartilhamento que você criou. Você pode fazer isso usando qualquer uma das ferramentas de cópia com suporte do Azure files, como Robocopy ou AzCopy.

> [!IMPORTANT]
> Os compartilhamentos de arquivos Premium só estão disponíveis com LRS e estão disponíveis na maioria das regiões que oferecem contas de armazenamento. Para descobrir se os compartilhamentos de arquivos premium estão disponíveis atualmente em sua região, confira a página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para o Azure.

### <a name="provisioned-shares"></a>Compartilhamentos provisionados

Os compartilhamentos de arquivos Premium são provisionados com base em uma taxa de GiB/IOPS/transferência fixa. Para cada GiB provisionado, o compartilhamento emitirá um IOPS e uma taxa de transferência de 0,1 MiB/s até os limites máximos por compartilhamento. O provisionamento mínimo permitido é de 100 GiB, com um IOPS e uma taxa de transferência mínimos.

Com base no melhor esforço, todos os compartilhamentos poderão acumular até três IOPS por GiB de armazenamento provisionado por 60 minutos ou mais, dependendo do tamanho do compartilhamento. Os novos compartilhamentos começam com o crédito de intermitência completa com base na capacidade provisionada.

Os compartilhamentos devem ser provisionados em incrementos de 1 GiB. O tamanho mínimo é 100 GiB, o próximo tamanho é 101 GiB e assim por diante.

> [!TIP]
> IOPS de linha de base = 1 * GiB provisionado. (Até um máximo de 100.000 IOPS).
>
> Limite de intermitência = 3 * IOPS de linha de base. (Até um máximo de 100.000 IOPS).
>
> taxa de egresso = 60 MiB/s + 0, 6 * provisionamento GiB
>
> taxa de entrada = 40 MiB/s + 0, 4 * provisionamento GiB

O tamanho do compartilhamento pode ser aumentado a qualquer momento, mas pode ser reduzido somente após 24 horas desde o último aumento. Depois de aguardar 24 horas sem um aumento de tamanho, você pode diminuir o tamanho do compartilhamento quantas vezes desejar, até aumentá-lo novamente. As alterações de escala de taxa de transferência/IOPS entrarão em vigor em alguns minutos após a alteração do tamanho.

É possível diminuir o tamanho do compartilhamento provisionado abaixo do GiB usado. Se você fizer isso, não perderá dados, mas ainda será cobrado pelo tamanho usado e receberá o desempenho (IOPS de linha de base, taxa de transferência e IOPS de intermitência) do compartilhamento provisionado, não o tamanho usado.

A tabela a seguir ilustra alguns exemplos dessas fórmulas para os tamanhos de compartilhamento provisionados:

|Capacidade (GiB) | IOPS de linha de base | IOPS de intermitência | Saída (MiB/s) | Entrada (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Até 300     | 66   | 44   |
|500         | 500     | Até 1.500   | 90   | 60   |
|1\.024       | 1\.024   | Até 3.072   | 122   | 81   |
|5\.120       | 5\.120   | Até 15.360  | 368   | 245   |
|10.240      | 10.240  | Até 30.720  | 675 | 450   |
|33.792      | 33.792  | Até 100.000 | 2\.088 | 1\.392   |
|51.200      | 51.200  | Até 100.000 | 3\.132 | 2\.088   |
|102.400     | 100.000 | Até 100.000 | 6\.204 | 4\.136   |

> [!NOTE]
> O desempenho dos compartilhamentos de arquivos está sujeito aos limites de rede da máquina, largura de banda de rede disponível, tamanhos de e/s, paralelismo, entre muitos outros fatores. Para obter a escala de desempenho máxima, distribua a carga entre várias VMs. Consulte o [Guia de solução de problemas](storage-troubleshooting-files-performance.md) para alguns problemas comuns de desempenho e soluções alternativas.

### <a name="bursting"></a>Bursting

Os compartilhamentos de arquivos Premium podem aumentar seu IOPS até um fator de três. A intermitência é automatizada e opera com base em um sistema de crédito. A intermitência funciona em uma base de melhor esforço e o limite de intermitência não é uma garantia, os compartilhamentos de arquivos podem *aumentar até* o limite.

Os créditos se acumulam em um Bucket de intermitência sempre que o tráfego para o compartilhamento de arquivos está abaixo do IOPS de linha de base. Por exemplo, um compartilhamento de GiB 100 tem 100 IOPS de linha de base. Se o tráfego real no compartilhamento era de 40 IOPS para um intervalo específico de 1 segundo, o IOPS de 60 não utilizado será creditado em um Bucket de intermitência. Esses créditos serão então usados mais tarde, quando as operações excederem o IOPs de linha de base.

> [!TIP]
> Tamanho do Bucket de intermitência = IOPS de linha de base * 2 * 3600.

Sempre que um compartilhamento exceder o IOPS de linha de base e tiver créditos em um Bucket de intermitência, ele será rompido. Os compartilhamentos podem continuar a aumentar, contanto que os créditos permaneçam, embora os compartilhamentos menores que 50 TiB permanecerão apenas no limite de intermitência de até uma hora. Compartilhamentos maiores que 50 TiB podem, tecnicamente, exceder esse limite de hora, até duas horas, mas isso se baseia no número de créditos de intermitência acumulados. Cada e/s além do IOPS de linha de base consome um crédito e quando todos os créditos são consumidos, o compartilhamento retornaria ao IOPS de linha de base

Os créditos de compartilhamento têm três Estados:

- A acumulação, quando o compartilhamento de arquivos está usando menos do que o IOPS de linha de base.
- Recusando, quando o compartilhamento de arquivos estiver intermitente.
- Constante restante, quando não há créditos ou IOPS de linha de base em uso.

Novos compartilhamentos de arquivos começam com o número total de créditos em seu Bucket de intermitência. Os créditos de intermitência não serão acumulados se o IOPS de compartilhamento cair abaixo do IOPS de linha de base devido à limitação pelo servidor.

## <a name="file-share-redundancy"></a>Redundância de compartilhamento de arquivo

Os compartilhamentos padrão do Azure files dão suporte a três opções de redundância de dados: LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona) e GRS (armazenamento com redundância geográfica).

Os compartilhamentos Premium dos arquivos do Azure oferecem suporte apenas ao LRS (armazenamento com redundância local).

As seções a seguir descrevem as diferenças entre as opções de redundância diferentes:

### <a name="locally-redundant-storage"></a>Armazenamento com redundância local

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica

> [!Warning]  
> Se estiver usando o compartilhamento de arquivos do Azure como um ponto de extremidade de nuvem em uma conta de armazenamento GRS, você não deve iniciar o failover da conta de armazenamento. Se isso for feito, a sincronização deixará de funcionar e poderá causar a perda inesperada de dados no caso de arquivos recentes em camadas. No caso de perda de uma região do Azure, a Microsoft disparará o failover da conta de armazenamento de modo que seja compatível com a Sincronização de Arquivos do Azure.

O armazenamento com redundância geográfica (GRS) foi desenvolvido para fornecer pelo menos 99.99999999999999% (16 9’s) durabilidade dos objetos em um determinado ano, replicando dados para uma região secundária situada a centenas de milhas de distância da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

Se você optar pelo armazenamento com redundância geográfica com acesso de leitura (RA-GRS), deverá saber que o arquivo do Azure não oferece suporte ao armazenamento com redundância geográfica com acesso de leitura (RA-GRS) em qualquer região no momento. Os compartilhamentos de arquivos na conta de armazenamento RA-GRS funcionam como no GRS contas e são cobrados preços GRS.

O GRS replica seus dados para outro datacenter em uma região secundária, mas esses dados estão disponíveis para serem lidos somente se a Microsoft iniciar um failover da região primária para a secundária.

Para uma conta de armazenamento com GRS habilitado, todos os dados são replicados primeiro com LRS (armazenamento com redundância local). Uma atualização primeiro é confirmada para o local primário e replicados usando o LRS. A atualização, em seguida, é replicada assincronamente para a região secundária usando GRS. Quando dados são gravados para o local secundário, ela também é replicada dentro desse local usando o LRS.

Ambas as regiões primárias e secundárias gerenciam réplicas entre domínios de falha separados e atualizar domínios dentro de uma unidade de escala de armazenamento. A unidade de escala de armazenamento é a unidade de replicação básica dentro do datacenter. A replicação nesse nível é fornecida por LRS; para obter mais informações, [consulte LRS (armazenamento com redundância local): Redundância de dados de baixo custo para o Armazenamento do Azure](../common/storage-redundancy-lrs.md).

Lembre-se esses pontos ao decidir qual opção de replicação para usar:

* O ZRS (armazenamento com redundância de zona) fornece alta disponibilidade com replicação síncrona e pode ser uma opção melhor para alguns cenários do que GRS. Para obter mais informações sobre o ZRS, consulte [ZRS](../common/storage-redundancy-zrs.md).
* A replicação assíncrona envolve um atraso entre a hora em que dados são gravados na região primária e quando são replicados na região secundária. Caso ocorra um desastre na região, as alterações que ainda não foram replicadas para a região secundária poderão ser pedidas se os dados não puderem ser recuperados na região primária.
* Com o GRS, a réplica não está disponível para acesso de leitura ou gravação, a menos que a Microsoft inicie um failover na região secundária. Em caso de failover, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de recuperação de desastre](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Integração com compartilhamentos de arquivos maiores (camada Standard)

Esta seção se aplica somente aos compartilhamentos de arquivos padrão. Todos os compartilhamentos de arquivos premium estão disponíveis com 100 TiB como uma oferta GA.

### <a name="restrictions"></a>Restrições

- Os [termos](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de visualização do Azure se aplicam a compartilhamentos de arquivos grandes enquanto estão em visualização, incluindo quando usados com sincronização de arquivos do Azure implantações.
- Exige que você crie uma nova conta de armazenamento de uso geral (não é possível expandir as contas de armazenamento existentes).
- A conversão de conta de LRS/ZRS para GRS não será possível em nenhuma nova conta de armazenamento criada depois que a assinatura for aceita para a versão prévia de compartilhamentos de arquivo maior.


### <a name="regional-availability"></a>Disponibilidade regional

Os compartilhamentos de arquivos padrão estão disponíveis em todas as regiões de até 5 TiB. Em determinadas regiões, ele está disponível com um limite de TiB de 100, essas regiões são listadas na tabela a seguir:

|Região  |Redundância com suporte  |Dá suporte a contas de armazenamento existentes  |
|---------|---------|---------|
|Sudeste da Ásia     |LRS|Não         |
|Europa Ocidental     |LRS, ZRS|Não         |
|Oeste dos EUA 2     |LRS, ZRS|Não         |

Para nos ajudar a priorizar novas regiões e recursos, preencha esta [pesquisa](https://aka.ms/azurefilesatscalesurvey).

### <a name="steps-to-onboard"></a>Etapas para carregar

Para registrar sua assinatura na versão prévia de compartilhamentos de arquivos maiores, você precisa usar Azure PowerShell. Você pode usar [Azure cloud Shell](https://shell.azure.com/) ou instalar o [módulo Azure PowerShell localmente](https://docs.microsoft.com/powershell/azure/install-Az-ps?view=azps-2.4.0) para executar os seguintes comandos do PowerShell:

Primeiro, verifique se a assinatura que você deseja registrar na visualização está selecionada:

```powershell
$context = Get-AzSubscription -SubscriptionId ...
Set-AzContext $context
```

Em seguida, registre-se na versão prévia usando os seguintes comandos:

```powershell
Register-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
Sua assinatura será aprovada automaticamente depois que os dois comandos forem executados.

Para verificar o status do registro, você pode executar o seguinte comando:

```powershell
Get-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
```

Pode levar até 15 minutos para que o status seja atualizado para **registrado**. Depois que seu status for **registrado**, você poderá usar o recurso.

### <a name="use-larger-file-shares"></a>Usar compartilhamentos de arquivos maiores

Para começar a usar compartilhamentos de arquivos maiores, crie uma nova conta de armazenamento de uso geral V2 e um novo compartilhamento de arquivos.

## <a name="data-growth-pattern"></a>Padrão de crescimento de dados

Hoje, o tamanho máximo de um compartilhamento de arquivos do Azure é 5 TiB (100 TiB em versão prévia). Devido a essa limitação atual, você deve considerar o crescimento esperado dos dados durante a implantação de um compartilhamento de Arquivos do Azure.

É possível sincronizar vários compartilhamentos de arquivos do Azure em um único Servidor de Arquivos do Windows com a Sincronização de Arquivos do Azure. Isso lhe assegura que os compartilhamentos de arquivos mais antigos e maiores, que talvez você tenha localmente, poderão ser transferidos para a Sincronização de Arquivos do Azure. Para obter mais informações, veja [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-files-planning.md).

## <a name="data-transfer-method"></a>Método de transferência de dados

Há muitas opções fáceis para transferência de dados em massa de um arquivo de compartilhamento existente, tal como um compartilhamento de arquivos local, para o Arquivos do Azure. Alguns populares incluem (lista não exaustiva):

* **Sincronização de Arquivos do Azure**: Como parte de uma primeira sincronização entre um compartilhamento de arquivo do Azure (um "Ponto de Extremidade da Nuvem") e um namespace de diretório do Windows (um "Ponto de Extremidade de Servidor"), a Sincronização de Arquivos do Azure replicará todos os dados do compartilhamento de arquivo existente para o Arquivos do Azure.
* **[Importação/Exportação do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : O serviço de importação/exportação do Azure permite a transferência de grandes quantidades de dados com segurança em um compartilhamento de arquivos do Azure pelo envio de discos rígidos para um datacenter do Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy é uma ferramenta de cópia bem conhecida que é fornecida com o Windows e o Windows Server. Robocopy pode ser usado para transferir dados para arquivos do Azure montando o compartilhamento de arquivos localmente e, em seguida, usando a localização montada como o destino no comando Robocopy.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : O AzCopy é um utilitário de linha de comando projetado para copiar dados de e para os Arquivos do Azure e o Armazenamento de Blobs do Azure, usando comandos simples com o desempenho ideal.

## <a name="next-steps"></a>Próximas etapas
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
* [Implantando Arquivos do Azure](storage-files-deployment-guide.md)
* [Implantando a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
