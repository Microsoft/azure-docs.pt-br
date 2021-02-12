---
title: Migração da série StorSimple 8000 para Sincronização de Arquivos do Azure
description: Saiba como migrar um dispositivo StorSimple 8100 ou 8600 para Sincronização de Arquivos do Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 76a244810042adf3cec64b15fe847c5b684527c2
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631177"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migração do StorSimple 8100 e 8600 para Sincronização de Arquivos do Azure

A série StorSimple 8000 é representada pelo 8100 ou pelo 8600 de dispositivos locais e físicos, e seus componentes de serviço de nuvem. É possível migrar os dados de qualquer um desses dispositivos para um ambiente Sincronização de Arquivos do Azure. Sincronização de Arquivos do Azure é o serviço do Azure de longo prazo padrão e estratégico para o qual os dispositivos StorSimple podem ser migrados.

A série StorSimple 8000 atingirá o [fim da vida útil](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) em 2022 de dezembro. É importante começar a planejar sua migração assim que possível. Este artigo fornece o conhecimento em segundo plano necessário e as etapas de migração para uma migração bem-sucedida para Sincronização de Arquivos do Azure.

## <a name="phase-1-prepare-for-migration"></a>Fase 1: preparar para a migração

Esta seção contém as etapas que você deve seguir no início da migração de volumes do StorSimple para compartilhamentos de arquivos do Azure.

### <a name="inventory"></a>Inventário

Ao começar a planejar a migração, primeiro identifique todos os dispositivos e volumes do StorSimple que você precisa migrar. Depois de fazer isso, você pode decidir o melhor caminho de migração para você.

* Os dispositivos físicos do StorSimple (série 8000) usam este guia de migração.
* Dispositivos virtuais, [série StorSimple 1200, usam um guia de migração diferente](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Resumo do custo de migração

As migrações para compartilhamentos de arquivos do Azure de volumes do StorSimple por meio de trabalhos do serviço de transformação de dados em um recurso Gerenciador de Dados do StorSimple são gratuitas. Outros custos podem ser incorridos durante e após uma migração:

* **Saída de rede:** Os arquivos do StorSimple residem em uma conta de armazenamento dentro de uma região específica do Azure. Se você provisionar os compartilhamentos de arquivos do Azure que você migra para uma conta de armazenamento que está localizada na mesma região do Azure, nenhum custo de egresso ocorrerá. Você pode mover seus arquivos para uma conta de armazenamento em uma região diferente como parte dessa migração. Nesse caso, os custos de egresso serão aplicados a você.
* **Transações de compartilhamento de arquivos do Azure:** Quando os arquivos são copiados para um compartilhamento de arquivos do Azure (como parte de uma migração ou fora de um), os custos de transações se aplicam à medida que os arquivos e os metadados estão sendo gravados. Como prática recomendada, inicie o compartilhamento de arquivos do Azure na camada de transação otimizada durante a migração. Alterne para a camada desejada após a conclusão da migração. As fases a seguir irão chamá-las no ponto apropriado.
* **Alterar uma camada de compartilhamento de arquivos do Azure:** Alterar a camada de um compartilhamento de arquivos do Azure custa transações. Na maioria dos casos, será mais econômico seguir o Conselho do ponto anterior.
* **Custo de armazenamento:** Quando essa migração começa a copiar arquivos em um compartilhamento de arquivos do Azure, o armazenamento de arquivos do Azure é consumido e cobrado.
* **StorSimple:** Até que você tenha a chance de desprovisionar os dispositivos e as contas de armazenamento do StorSimple, o custo do StorSimple para armazenamento, backups e dispositivos continuará ocorrendo.

### <a name="direct-share-access-vs-azure-file-sync"></a>Compartilhamento direto-acesso versus Sincronização de Arquivos do Azure

Os compartilhamentos de arquivos do Azure abrem um mundo totalmente novo de oportunidades para estruturar sua implantação de serviços de arquivos. Um compartilhamento de arquivos do Azure é apenas um compartilhamento SMB na nuvem que você pode configurar para que os usuários acessem diretamente o protocolo SMB com a autenticação Kerberos familiar e as permissões NTFS existentes (ACLs de arquivo e pasta) funcionando nativamente. Saiba mais sobre o [acesso baseado em identidade aos compartilhamentos de arquivos do Azure](storage-files-active-directory-overview.md).

Uma alternativa ao acesso direto é [sincronização de arquivos do Azure](./storage-sync-files-planning.md). Sincronização de Arquivos do Azure é uma analogia direta para a capacidade do StorSimple de armazenar em cache arquivos usados com frequência no local.

Sincronização de Arquivos do Azure é um serviço de nuvem da Microsoft, com base em dois componentes principais:

* Sincronização de arquivos e camadas de nuvem.
* Compartilhamentos de arquivos como armazenamento nativo no Azure que podem ser acessados por meio de vários protocolos, como SMB e REST de arquivo.

Os compartilhamentos de arquivos do Azure mantêm aspectos importantes de fidelidade de arquivo em arquivos armazenados, como atributos, permissões e carimbos de data/hora. Com compartilhamentos de arquivos do Azure, não há mais necessidade de um aplicativo ou serviço para interpretar os arquivos e as pastas armazenados na nuvem. Você pode acessá-los nativamente por meio de protocolos e clientes familiares, como o explorador de arquivos do Windows. Os compartilhamentos de arquivos do Azure permitem armazenar dados do servidor de arquivos de uso geral e dados de aplicativo na nuvem. O backup de um compartilhamento de arquivos do Azure é uma funcionalidade interna e pode ser aprimorado pelo backup do Azure.

Este artigo se concentra nas etapas de migração. Se você quiser saber mais sobre Sincronização de Arquivos do Azure antes de migrar, consulte os seguintes artigos:

* [Visão geral de Sincronização de Arquivos do Azure](./storage-sync-files-planning.md "Visão geral")
* [Guia de implantação do Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Chave de criptografia de dados do serviço StorSimple

Quando você configura o dispositivo StorSimple pela primeira vez, ele gerou uma chave de criptografia de dados de serviço e o instruiu a armazenar a chave com segurança. Essa chave é usada para criptografar todos os dados na conta de armazenamento do Azure associada em que o dispositivo StorSimple armazena seus arquivos.

A chave de criptografia de dados de serviço é necessária para uma migração bem-sucedida. Agora é um bom momento para recuperar essa chave de seus registros, para cada um dos dispositivos em seu inventário.

Se você não encontrar as chaves em seus registros, poderá recuperar a chave do dispositivo. Cada dispositivo tem uma chave de criptografia exclusiva. Para recuperar a chave:

* Arquivar uma solicitação de suporte com Microsoft Azure por meio do portal do Azure. O conteúdo da solicitação deve ter os números de série do dispositivo StorSimple e a solicitação para recuperar a "chave de criptografia de dados de serviço".
* Um engenheiro de suporte do StorSimple entrará em contato com a solicitação de uma reunião de compartilhamento de tela.
* Certifique-se de que antes da reunião começar, você se conecte ao seu dispositivo StorSimple [por meio de um console serial](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) ou por meio de uma [sessão remota do PowerShell](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple).

> [!CAUTION]
> Quando você estiver decidindo como se conectar ao seu dispositivo StorSimple, considere o seguinte:
>
> * A conexão por meio de uma sessão HTTPS é a opção mais segura e recomendada.
> * Conectar-se diretamente ao console serial do dispositivo é seguro, mas se conectar ao console serial em comutadores de rede não é seguro.
> * Conexões de sessão HTTP são uma opção, mas *não são criptografadas*. Eles não são recomendados a menos que sejam usados em uma rede de confiança fechada.

### <a name="storsimple-volume-backups"></a>Backups de volume do StorSimple

O StorSimple oferece backups diferenciais no nível de volume. Os compartilhamentos de arquivos do Azure também têm essa capacidade, chamada instantâneos de compartilhamento.

Decida se como parte de sua migração, você também tem a obrigação de mover todos os backups.

> [!CAUTION]
> Pare aqui se você precisar migrar backups de volumes do StorSimple.
>
> No momento, você pode migrar apenas o backup de volume mais recente. O suporte para migração de backup chegará ao final de 2020. Se você começar agora, não poderá "desligar" seus backups mais tarde. Na próxima versão, os backups devem ser "reproduzidos" para os compartilhamentos de arquivos do Azure do mais antigo para o mais recente, com instantâneos de compartilhamento de arquivos do Azure obtidos no.

Se você quiser migrar apenas os dados dinâmicos e não tiver requisitos de backups, poderá continuar seguindo este guia. Se você tiver um requisito de retenção de backup de curto prazo de, digamos, um mês ou dois, você poderá optar por continuar sua migração agora e desprovisionar os recursos do StorSimple após esse período. Essa abordagem permite criar tanto histórico de backup no lado do compartilhamento de arquivos do Azure quanto necessário. Para o momento em que os dois sistemas estão sendo executados, o custo adicional se aplica, o que torna essa abordagem uma que você não deve considerar se precisar de mais do que a retenção de backup de curto prazo.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Mapear os volumes do StorSimple existentes para compartilhamentos de arquivos do Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Número de contas de armazenamento

Sua migração provavelmente se beneficiará de uma implantação de várias contas de armazenamento, cada uma mantendo um número menor de compartilhamentos de arquivos do Azure.

Se os compartilhamentos de arquivos estiverem altamente ativos (utilizados por muitos usuários ou aplicativos), dois compartilhamentos de arquivos do Azure poderão alcançar o limite de desempenho da sua conta de armazenamento. Por isso, a prática recomendada é migrar para várias contas de armazenamento, cada uma com seus próprios compartilhamentos de arquivos individuais e, normalmente, não mais do que dois ou três compartilhamentos por conta de armazenamento.

Uma prática recomendada é implantar contas de armazenamento com um compartilhamento de arquivos cada. Você pode agrupar vários compartilhamentos de arquivos do Azure na mesma conta de armazenamento, se tiver compartilhamentos de arquivamento neles.

Essas considerações se aplicam mais ao [acesso direto à nuvem](#direct-share-access-vs-azure-file-sync) (por meio de uma VM ou serviço do Azure) do que a sincronização de arquivos do Azure. Se você planeja usar Sincronização de Arquivos do Azure apenas nesses compartilhamentos, o agrupamento de vários em uma única conta de armazenamento do Azure é bom. Considere também que você talvez queira aumentar e deslocar um aplicativo para a nuvem que acessaria diretamente um compartilhamento de arquivos. Ou você pode começar a usar um serviço no Azure que também se beneficiaria de ter mais de IOPS e números de taxa de transferência disponíveis.

Se você tiver feito uma lista de seus compartilhamentos, mapeie cada compartilhamento para a conta de armazenamento na qual ele residirá.

> [!IMPORTANT]
> Decida em uma região do Azure e verifique se cada conta de armazenamento e Sincronização de Arquivos do Azure recurso corresponde à região selecionada.

### <a name="phase-1-summary"></a>Resumo da fase 1

No final da fase 1:

* Você tem uma boa visão geral de seus dispositivos e volumes do StorSimple.
* O serviço de transformação de dados está pronto para acessar os volumes do StorSimple na nuvem porque você recuperou sua chave de criptografia de dados de serviço para cada dispositivo StorSimple.
* Você tem um plano para o qual os volumes precisam ser migrados e também como mapear seus volumes para o número apropriado de compartilhamentos de arquivos do Azure e contas de armazenamento.

> [!CAUTION]
> Se você precisar migrar backups de volumes do StorSimple, **Pare aqui**.
>
> Essa abordagem de migração depende de novos recursos de serviço de transformação de dados que atualmente não podem migrar backups. O suporte para migração de backup chegará ao final de 2020. No momento, você pode migrar apenas seus dados dinâmicos. Se você começar agora, não poderá "desligar" seus backups mais tarde. Os backups devem ser "reproduzidos" para os compartilhamentos de arquivos do Azure de dados mais antigos para os mais recentes, com instantâneos de compartilhamento de arquivos do Azure no.

Se você quiser migrar apenas os dados dinâmicos e não tiver requisitos de backups, poderá continuar seguindo este guia.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Fase 2: implantar recursos de migração e armazenamento do Azure

Esta seção discute considerações sobre a implantação de diferentes tipos de recursos que são necessários no Azure. Alguns manterão seus dados após a migração e alguns são necessários apenas para a migração. Não inicie a implantação de recursos até que você tenha finalizado seu plano de implantação. É difícil, às vezes impossível, de alterar determinados aspectos dos recursos do Azure depois que eles foram implantados.

### <a name="deploy-storage-accounts"></a>Implantar contas de armazenamento

Provavelmente, você precisará implantar várias contas de armazenamento do Azure. Cada um deles manterá um número menor de compartilhamentos de arquivos do Azure, de acordo com seu plano de implantação, concluído na seção anterior deste artigo. Vá para o portal do Azure para [implantar suas contas de armazenamento planejadas](../common/storage-account-create.md#create-a-storage-account). Considere obedecer às configurações básicas a seguir para qualquer nova conta de armazenamento.

#### <a name="subscription"></a>Assinatura

Você pode usar a mesma assinatura usada para sua implantação do StorSimple ou outra. A única limitação é que sua assinatura deve estar no mesmo locatário Azure Active Directory que a assinatura do StorSimple. Considere mover a assinatura do StorSimple para o locatário correto antes de iniciar uma migração. Você só pode mover a assinatura inteira. Os recursos individuais do StorSimple não podem ser movidos para um locatário ou assinatura diferente.

#### <a name="resource-group"></a>Grupo de recursos

Os grupos de recursos estão ajudando com a organização de recursos e permissões de gerenciamento de administração. Saiba mais sobre [grupos de recursos no Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

#### <a name="storage-account-name"></a>Nome da conta de armazenamento

O nome da sua conta de armazenamento se tornará parte de uma URL e terá determinadas limitações de caracteres. Em sua Convenção de nomenclatura, considere que os nomes de conta de armazenamento devem ser exclusivos no mundo, permitir apenas letras minúsculas e números, exigir entre 3 e 24 caracteres e não permitir caracteres especiais como hifens ou sublinhados. Para obter mais informações, consulte [regras de nomenclatura de recursos de armazenamento do Azure](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

#### <a name="location"></a>Local

O local ou a região do Azure de uma conta de armazenamento é muito importante. Se você usar Sincronização de Arquivos do Azure, todas as suas contas de armazenamento deverão estar na mesma região que o recurso do serviço de sincronização de armazenamento. A região do Azure que você escolher deve ser próxima ou central para seus servidores e usuários locais. Depois que o recurso tiver sido implantado, você não poderá alterar sua região.

Você pode escolher uma região diferente de onde os dados do StorSimple (conta de armazenamento) residem atualmente.

> [!IMPORTANT]
> Se você escolher uma região diferente do local atual da conta de armazenamento do StorSimple, os [encargos de egresso serão aplicados](https://azure.microsoft.com/pricing/details/bandwidth) durante a migração. Os dados deixarão a região do StorSimple e entrarão em sua nova região de conta de armazenamento. Nenhum encargo de largura de banda se aplica se você permanecer dentro da mesma região do Azure.

#### <a name="performance"></a>Desempenho

Você tem a opção de escolher o armazenamento Premium (SSD) para compartilhamentos de arquivos do Azure ou o armazenamento Standard. O armazenamento Standard inclui [várias camadas para um compartilhamento de arquivos](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). O armazenamento Standard é a opção certa para a maioria dos clientes que estão migrando do StorSimple.

Ainda não tem certeza?

* Escolha armazenamento Premium se você precisar do [desempenho de um compartilhamento de arquivos premium do Azure](understanding-billing.md#provisioned-model).
* Escolha o armazenamento padrão para cargas de trabalho de servidor de arquivos de uso geral, que inclui dados dinâmicos e dados de arquivo. Escolha também armazenamento padrão se a única carga de trabalho no compartilhamento na nuvem for Sincronização de Arquivos do Azure.

#### <a name="account-kind"></a>Tipo de conta

* Para armazenamento padrão, escolha *StorageV2 (uso geral v2)*.
* Para compartilhamentos de arquivos premium, escolha *FileStorage*.

#### <a name="replication"></a>Replicação

Há várias configurações de replicação disponíveis. Saiba mais sobre os diferentes tipos de replicação.

Escolha uma das duas opções a seguir:

* *LRS (armazenamento com redundância local)*.
* *ZRS (armazenamento com redundância de zona)*, que não está disponível em todas as regiões do Azure.

> [!NOTE]
> Somente os tipos de redundância LRS e ZRS são compatíveis com os grandes compartilhamentos de arquivos do Azure 100-TiB-Capacity.

O GRS (armazenamento com redundância geográfica) em todas as variações não tem suporte no momento. Você pode mudar seu tipo de redundância mais tarde e alternar para GRS quando o suporte para ele chegar no Azure.

#### <a name="enable-100-tib-capacity-file-shares"></a>Habilitar compartilhamentos de arquivos de 100-TiB-Capacity

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Uma imagem que mostra a guia Avançado no portal do Azure para a criação de uma conta de armazenamento.":::
    :::column-end:::
    :::column:::
        Na seção **avançado** do assistente de nova conta de armazenamento na portal do Azure, você pode habilitar o suporte a **compartilhamentos de arquivos grandes** nesta conta de armazenamento. Se essa opção não estiver disponível para você, provavelmente você selecionou o tipo de redundância incorreto. Certifique-se de selecionar somente LRS ou ZRS para que essa opção fique disponível.
    :::column-end:::
:::row-end:::

Optar por grandes compartilhamentos de arquivos de 100-TiB-capacidade tem vários benefícios:

* O desempenho é aumentado em comparação com os compartilhamentos de arquivos menores de 5 TiB (por exemplo, 10 vezes o IOPS).
* Sua migração será concluída significativamente mais rapidamente.
* Você garante que um compartilhamento de arquivos terá capacidade suficiente para manter todos os dados que serão migrados para ele.
* O crescimento futuro é abordado.

### <a name="azure-file-shares"></a>Compartilhamentos de arquivos do Azure

Depois que as contas de armazenamento forem criadas, vá para a seção **compartilhamento de arquivos** da conta de armazenamento e implante o número apropriado de compartilhamentos de arquivos do Azure de acordo com seu plano de migração da fase 1. Considere cumprir as seguintes configurações básicas para seus novos compartilhamentos de arquivos no Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Uma captura de tela portal do Azure mostrando a nova interface do usuário do compartilhamento de arquivos.":::
    :::column-end:::
    :::column:::
        </br>**Nome**</br>Há suporte para letras minúsculas, números e hifens.</br></br>**Cota**</br>A cota aqui é comparável a uma cota rígida de SMB em uma instância do Windows Server. A prática recomendada é não definir uma cota aqui, pois a migração e outros serviços falharão quando a cota for atingida.</br></br>**Camadas**</br>Selecione a **transação otimizada** para seu novo compartilhamento de arquivos. Durante a migração, muitas transações ocorrerão. Seu mais econômico é alterar sua camada posteriormente para a camada mais adequada para sua carga de trabalho.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

O recurso do Azure que irá armazenar seus trabalhos de migração é chamado de **Gerenciador de dados do StorSimple**. Selecione **novo recurso** e pesquise por ele. Em seguida, selecione **Criar**.

Esse recurso temporário é usado para orquestração. Você o desprovisionará após a conclusão da migração. Ele deve ser implantado na mesma assinatura, grupo de recursos e região que sua conta de armazenamento do StorSimple.

### <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure

Com Sincronização de Arquivos do Azure, você pode adicionar o cache local dos arquivos acessados com mais frequência. Semelhante aos recursos de cache do StorSimple, o Sincronização de Arquivos do Azure recurso de camadas de nuvem oferece latência de acesso local em combinação com controle aprimorado sobre a capacidade de cache disponível na instância do Windows Server e na sincronização de vários sites. Se ter um cache local for seu objetivo, em sua rede local, prepare uma VM do Windows Server (também há suporte para servidores físicos e clusters de failover) com capacidade de armazenamento suficiente de conexão direta.

> [!IMPORTANT]
> Não configure Sincronização de Arquivos do Azure ainda. É melhor configurar Sincronização de Arquivos do Azure depois que a migração do seu compartilhamento for concluída. A implantação de Sincronização de Arquivos do Azure não deve iniciar antes da fase 4 de uma migração.

### <a name="phase-2-summary"></a>Resumo da fase 2

No final da fase 2, você implantará suas contas de armazenamento e todos os compartilhamentos de arquivos do Azure entre elas. Você também terá um recurso Gerenciador de Dados do StorSimple. Você usará o último na fase 3 ao configurar seus trabalhos de migração.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fase 3: criar e executar um trabalho de migração

Esta seção descreve como configurar um trabalho de migração e mapear cuidadosamente os diretórios em um volume do StorSimple que devem ser copiados para o compartilhamento de arquivos de destino do Azure selecionado. Para começar, vá para o Gerenciador de Dados do StorSimple, localize **definições de trabalho** no menu e selecione **+ definição de trabalho**. O tipo de armazenamento de destino é o **compartilhamento de arquivos padrão do Azure**.

![Tipos de trabalho de migração da série StorSimple 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Uma captura de tela das definições de trabalho portal do Azure com uma nova caixa de diálogo Definições de trabalho aberta que solicita o tipo de trabalho: copiar para um compartilhamento de arquivos ou um contêiner de BLOBs.")

> [!IMPORTANT]
> Antes de executar qualquer trabalho de migração, interrompa todos os backups agendados automaticamente dos volumes do StorSimple.

:::row:::
    :::column:::
        ![Trabalho de migração da série StorSimple 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Uma captura de tela do novo formulário de criação de trabalho para um trabalho do serviço de transformação de dados.")
    :::column-end:::
    :::column:::
        **Nome da definição do trabalho**</br>Esse nome deve indicar o conjunto de arquivos que você está movendo. Dar um nome semelhante ao compartilhamento de arquivos do Azure é uma boa prática. </br></br>**Local onde o trabalho é executado**</br>Ao selecionar uma região, você deve selecionar a mesma região da sua conta de armazenamento do StorSimple ou, se isso não estiver disponível, depois uma região próxima a ela. </br></br><h3>Fonte</h3>**Assinatura de origem**</br>Selecione a assinatura na qual você armazena o recurso de Gerenciador de Dispositivos do StorSimple. </br></br>**Recurso do StorSimple**</br>Selecione seu StorSimple Gerenciador de Dispositivos seu dispositivo está registrado. </br></br>**Chave de criptografia de dados de serviço**</br>Marque esta [seção anterior neste artigo](#storsimple-service-data-encryption-key) caso não seja possível localizar a chave em seus registros. </br></br>**Dispositivo**</br>Selecione o dispositivo StorSimple que contém o volume onde você deseja migrar. </br></br>**Volume**</br>Selecione o volume de origem. Posteriormente, você decidirá se deseja migrar todo o volume ou subdiretórios para o compartilhamento de arquivos de destino do Azure. </br></br><h3>Destino</h3>Selecione a assinatura, a conta de armazenamento e o compartilhamento de arquivos do Azure como o destino deste trabalho de migração.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> O backup de volume mais recente será usado para executar a migração. Verifique se pelo menos um volume de backup está presente ou se o trabalho falhará. Além disso, certifique-se de que o backup mais recente que você tem é bastante recente para manter o Delta para o compartilhamento ao vivo o menor possível. Pode valer a pena disparar e concluir outro backup de volume *antes* de executar o trabalho que você acabou de criar.

### <a name="directory-mapping"></a>Mapeamento de diretório

O mapeamento de diretório é opcional para seu trabalho de migração. Se você deixar a seção vazia, *todos* os arquivos e pastas na raiz do seu volume do StorSimple serão movidos para a raiz do seu compartilhamento de arquivos de destino do Azure. Na maioria dos casos, armazenar o conteúdo de um volume inteiro em um compartilhamento de arquivos do Azure não é a melhor abordagem. Muitas vezes, é melhor dividir o conteúdo de um volume em vários compartilhamentos de arquivos no Azure. Se você ainda não fez um plano, consulte [mapear seu volume do StorSimple para compartilhamentos de arquivos do Azure](#map-your-existing-storsimple-volumes-to-azure-file-shares) primeiro.

Como parte do plano de migração, você pode ter decidido que as pastas em um volume do StorSimple precisam ser divididas em vários compartilhamentos de arquivos do Azure. Se esse for o caso, você poderá fazer essa divisão por:

1. Definir vários trabalhos para migrar as pastas em um volume. Cada uma terá a mesma fonte de volume do StorSimple, mas um compartilhamento de arquivos do Azure diferente como o destino.
1. Especificar precisamente quais pastas do volume StorSimple precisam ser migradas para o compartilhamento de arquivos especificado usando a seção **mapeamento de diretório** do formulário de criação de trabalho e seguindo a [semântica de mapeamento](#semantic-elements)específica.

> [!IMPORTANT]
> Os caminhos e as expressões de mapeamento neste formulário não podem ser validados quando o formulário é enviado. Se os mapeamentos forem especificados incorretamente, um trabalho poderá falhar completamente ou produzir um resultado indesejável. Nesse caso, geralmente é melhor excluir o compartilhamento de arquivos do Azure, recriá-lo e, em seguida, corrigir as instruções de mapeamento em um novo trabalho de migração para o compartilhamento. A execução de um novo trabalho com instruções de mapeamento fixo pode corrigir as pastas omitidas e colocá-las no compartilhamento existente. No entanto, somente as pastas que foram omitidas devido a erros de ortografia de caminho podem ser abordadas dessa forma.

#### <a name="semantic-elements"></a>Elementos semânticos

Um mapeamento é expresso da esquerda para a direita: [caminho do \Source] \> [caminho do \target].

|Caractere semântico          | Significado  |
|:---------------------------|:---------|
| **\\**                     | Indicador de nível raiz.       |
| **\>**                     | [Origem] e [destino-mapeamento] operador.     |
|**\|** ou RETURN (nova linha) | Separador de duas instruções de mapeamento de pasta. </br>Como alternativa, você pode omitir esse caractere e selecionar **Enter** para obter a próxima expressão de mapeamento em sua própria linha.        |

### <a name="examples"></a>Exemplos
Move o conteúdo dos *dados de usuário* da pasta para a raiz do compartilhamento de arquivos de destino:
``` console
\User data > \
```
Move todo o conteúdo do volume para um novo caminho no compartilhamento de arquivos de destino:
``` console
\ > \Apps\HR tracker
```
Move o conteúdo da pasta de origem para um novo caminho no compartilhamento de arquivos de destino:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Classifica vários locais de origem em uma nova estrutura de diretório:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Regras semânticas

* Sempre especifique caminhos de pasta relativos ao nível raiz.
* Inicie cada caminho de pasta com um indicador de nível raiz " \\ ".
* Não inclua letras de unidade.
* Ao especificar vários caminhos, os caminhos de origem ou de destino não podem se sobrepor:</br>
   Exemplo de sobreposição de caminho de origem inválido:</br>
    *\\pasta de > folder\1 \\*</br>
    *\\pasta \\ 1 \\ 2 > \\ Pasta2*</br>
   Exemplo de sobreposição de caminho de destino inválido:</br>
   *\\> de pasta \\*</br>
   *\\Pasta2 > \\*</br>
* As pastas de origem que não existem serão ignoradas.
* As estruturas de pasta que não existem no destino serão criadas.
* Como o Windows, os nomes de pastas não diferenciam maiúsculas de minúsculas, mas preservam o caso.

> [!NOTE]
> O conteúdo da pasta *\System Volume Information* e o *$recycle. bin* no seu volume StorSimple não serão copiados pelo trabalho de transformação.

### <a name="phase-3-summary"></a>Resumo da fase 3

No final da fase 3, você terá executado os trabalhos do serviço de transformação de dados de volumes do StorSimple em compartilhamentos de arquivos do Azure. Agora você pode se concentrar em Configurar Sincronização de Arquivos do Azure para o compartilhamento (depois que os trabalhos de migração de um compartilhamento forem concluídos) ou direcionar o acesso de compartilhamento para seus operadores de informações e aplicativos para o compartilhamento de arquivos do Azure.

## <a name="phase-4-access-your-azure-file-shares"></a>Fase 4: acessar seus compartilhamentos de arquivos do Azure

Há duas estratégias principais para acessar seus compartilhamentos de arquivos do Azure:

* **Sincronização de arquivos do Azure**: [implante sincronização de arquivos do Azure](#deploy-azure-file-sync) em uma instância local do Windows Server. Sincronização de Arquivos do Azure tem todas as vantagens de um cache local, assim como o StorSimple.
* **Acesso direto ao compartilhamento**: [implantar acesso direto ao compartilhamento](#deploy-direct-share-access). Use essa estratégia se o cenário de acesso para um determinado compartilhamento de arquivos do Azure não se beneficiar do cache local ou se você não tiver mais a capacidade de hospedar uma instância do Windows Server local. Aqui, seus usuários e aplicativos continuarão a acessar compartilhamentos SMB pelo protocolo SMB. Esses compartilhamentos não estão mais em um servidor local, mas diretamente na nuvem.

Você já deve ter decidido qual é a melhor opção para você na [fase 1](#phase-1-prepare-for-migration) deste guia.

O restante desta seção se concentra nas instruções de implantação.

### <a name="deploy-azure-file-sync"></a>Implantar a Sincronização de Arquivos do Azure

É hora de implantar uma parte do Sincronização de Arquivos do Azure.

1. Crie o Sincronização de Arquivos do Azure recurso de nuvem.
1. Implante o agente de Sincronização de Arquivos do Azure no servidor local.
1. Registre o servidor com o recurso de nuvem.

Não crie nenhum grupo de sincronização ainda. A configuração da sincronização com um compartilhamento de arquivos do Azure só deve ocorrer depois que os trabalhos de migração para um compartilhamento de arquivos do Azure forem concluídos. Se você começou a usar Sincronização de Arquivos do Azure antes de concluir a migração, ela tornaria sua migração desnecessariamente difícil, já que você não poderia dizer com facilidade quando era hora de iniciar um corte.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Implantar o Sincronização de Arquivos do Azure recurso de nuvem

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se você quiser alterar a região do Azure em que os dados residem após a conclusão da migração, implante o serviço de sincronização de armazenamento na mesma região que as contas de armazenamento de destino para essa migração.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Implantar uma instância local do Windows Server

* Crie o Windows Server 2019 (no mínimo 2012R2) como uma máquina virtual ou um servidor físico. Também há suporte para um cluster de failover do Windows Server. Não reutilize o servidor ao lado do StorSimple 8100 ou 8600.
* Provisione ou adicione armazenamento com conexão direta. Não há suporte para o armazenamento anexado à rede.

É uma prática recomendada dar à sua nova instância do Windows Server uma quantidade de armazenamento igual ou maior do que o dispositivo StorSimple 8100 ou 8600 está disponível localmente para cache. Você usará a instância do Windows Server da mesma maneira que usou o dispositivo StorSimple. Se ele tiver a mesma quantidade de armazenamento que o dispositivo, a experiência de cache deverá ser semelhante, se não for a mesma. Você pode adicionar ou remover o armazenamento de sua instância do Windows Server no. Essa funcionalidade permite que você dimensione o tamanho do volume local e a quantidade de armazenamento local disponível para cache.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Preparar a instância do Windows Server para sincronização de arquivos

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Configurar Sincronização de Arquivos do Azure na instância do Windows Server

Sua instância do Windows Server local registrada deve estar pronta e conectada à Internet para esse processo.

> [!IMPORTANT]
> A migração de arquivos e pastas do StorSimple para o compartilhamento de arquivos do Azure deve ser concluída antes de prosseguir. Certifique-se de que não haja mais alterações feitas no compartilhamento de arquivos.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Certifique-se de ativar a disposição em camadas de nuvem. A camada de nuvem é o recurso Sincronização de Arquivos do Azure que permite que o servidor local tenha menos capacidade de armazenamento do que o armazenado na nuvem, mas que tenha o namespace completo disponível. Os dados interessantes localmente também são armazenados em cache localmente para um desempenho rápido e local de acesso. Outro motivo para ativar a camada de nuvem nesta etapa é que não queremos sincronizar o conteúdo do arquivo neste estágio. Somente o namespace deve ser movido neste momento.

### <a name="deploy-direct-share-access"></a>Implantar acesso direto ao compartilhamento

:::row:::
    :::column:::
        [![Guia passo a passo e demonstração sobre como expor com segurança compartilhamentos de arquivos do Azure diretamente para os operadores de informações e aplicativos-clique para jogar!](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Este vídeo é um guia e uma demonstração sobre como expor com segurança compartilhamentos de arquivos do Azure diretamente para os operadores de informações e aplicativos em cinco etapas simples.</br>
        O vídeo faz referência à documentação dedicada para alguns tópicos:

* [Visão geral de identidade](storage-files-active-directory-overview.md)
* [Como ingressar no domínio em uma conta de armazenamento](storage-files-identity-auth-active-directory-enable.md)
* [Visão geral de rede para compartilhamentos de arquivos do Azure](storage-files-networking-overview.md)
* [Como configurar pontos de extremidade públicos e privados](storage-files-networking-endpoints.md)
* [Como configurar uma VPN S2S](storage-files-configure-s2s-vpn.md)
* [Como configurar uma VPN P2S do Windows](storage-files-configure-p2s-vpn-windows.md)
* [Como configurar uma VPN P2S do Linux](storage-files-configure-p2s-vpn-linux.md)
* [Como configurar o encaminhamento de DNS](storage-files-networking-dns.md)
* [Configurar o DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Resumo da fase 4

Nesta fase, você criou e executou vários trabalhos do serviço de transformação de dados em seu Gerenciador de Dados do StorSimple. Esses trabalhos migraram seus arquivos e pastas para compartilhamentos de arquivos do Azure. Você também implantou Sincronização de Arquivos do Azure ou preparou suas contas de rede e de armazenamento para acesso direto ao compartilhamento.

## <a name="phase-5-user-cut-over"></a>Fase 5: recortar o usuário

Esta fase está prestes a resumir sua migração:

* Planeje seu tempo de inatividade.
* Acompanhe todas as alterações que os usuários e aplicativos produzidos no lado do StorSimple enquanto os trabalhos de transformação de dados na fase 3 estiverem em execução.
* Faça o failover de seus usuários para a nova instância do Windows Server com Sincronização de Arquivos do Azure ou os compartilhamentos de arquivos do Azure por meio do acesso direto ao compartilhamento.

### <a name="plan-your-downtime"></a>Planeje seu tempo de inatividade

Essa abordagem de migração requer algum tempo de inatividade para seus usuários e aplicativos. O objetivo é manter o tempo de inatividade mínimo. As seguintes considerações podem ajudar:

* Mantenha seus volumes do StorSimple disponíveis ao executar seus trabalhos de transformação de dados.
* Quando você terminar de executar seus trabalhos de migração de dados para um compartilhamento, é hora de remover o acesso do usuário (pelo menos acesso de gravação) dos volumes ou compartilhamentos do StorSimple. Um RoboCopy final irá acompanhar o compartilhamento de arquivos do Azure. Em seguida, você pode cortar seus usuários. O local em que você executa o RoboCopy depende se você optou por usar Sincronização de Arquivos do Azure ou acesso direto ao compartilhamento. A próxima seção sobre o RoboCopy aborda esse assunto.
* Depois de concluir a atualização do RoboCopy, você estará pronto para expor o novo local para os usuários pelo compartilhamento de arquivos do Azure diretamente ou por um compartilhamento SMB em uma instância do Windows Server com Sincronização de Arquivos do Azure. Muitas vezes, uma implantação DFS-N ajudará a fazer um rápido e eficiente. Ele manterá os endereços de compartilhamento existentes consistentes e reapontará para um novo local que contém seus arquivos e pastas migrados.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Determinar quando seu namespace foi totalmente sincronizado com o servidor

Quando você usa Sincronização de Arquivos do Azure para um compartilhamento de arquivos do Azure, é importante que você determine que todo o seu namespace terminou o download para o servidor *antes* de iniciar qualquer Robocopy local. O tempo necessário para baixar seu namespace depende do número de itens em seu compartilhamento de arquivos do Azure. Há dois métodos para determinar se o namespace foi totalmente recebido no servidor.

#### <a name="azure-portal"></a>Portal do Azure

Você pode usar o portal do Azure para ver quando o namespace foi totalmente entregue.

* Entre no portal do Azure e vá para o grupo de sincronização. Verifique o status de sincronização do seu grupo de sincronização e do ponto de extremidade do servidor.
* A direção interessante é baixar. Se o ponto de extremidade do servidor for provisionado recentemente, ele mostrará a **sincronização inicial**, o que indica que o namespace ainda está sendo encerrado.
Após essa alteração para qualquer coisa, exceto a **sincronização inicial**, o namespace será totalmente preenchido no servidor. Agora você pode continuar com um RoboCopy local.

#### <a name="windows-server-event-viewer"></a>Visualizador de Eventos do Windows Server

Você também pode usar o Visualizador de Eventos em sua instância do Windows Server para saber quando o namespace foi totalmente entregue.

1. Abra o **Visualizador de eventos** e vá para **aplicativos e serviços**.
1. Vá para e abra **Microsoft\FileSync\Agent\Telemetry**.
1. Procure o **evento 9102** mais recente, que corresponde a uma sessão de sincronização concluída.
1. Selecione **detalhes** e confirme que você está observando um evento em que o valor de **SyncDirection** é **baixado**.
1. Para o momento em que o namespace concluiu o download para o servidor, haverá um único evento com **cenário**, o valor **FullGhostedSync** e **HRESULT**  =  **0**.
1. Se você perder esse evento, também poderá procurar outros **eventos 9102** com o **SyncDirection**  =  **Download** e o **cenário**  =  **"RegularSync"**. A localização de um desses eventos também indica que o namespace concluiu o download e a sincronização progrediu para sessões de sincronização regulares, se há alguma coisa a ser sincronizada ou não no momento.

### <a name="a-final-robocopy"></a>Um RoboCopy final

Neste ponto, há diferenças entre sua instância local do Windows Server e o dispositivo StorSimple 8100 ou 8600.

1. Você precisa acompanhar as alterações que os usuários ou aplicativos produziram no lado do StorSimple enquanto a migração estava em andamento.
1. Para casos em que você usa Sincronização de Arquivos do Azure: o dispositivo StorSimple tem um cache populado versus a instância do Windows Server com apenas um namespace sem conteúdo de arquivo armazenado localmente neste momento. O RoboCopy final pode ajudar a iniciar o cache de Sincronização de Arquivos do Azure local ao obter o conteúdo do arquivo armazenado em cache localmente, assim como está disponível e pode caber no servidor Sincronização de Arquivos do Azure.
1. Alguns arquivos podem ter sido deixados para trás pelo trabalho de transformação de dados devido a caracteres inválidos. Nesse caso, copie-os para a instância do Windows Server habilitada para Sincronização de Arquivos do Azure. Posteriormente, você pode ajustá-los para que eles sejam sincronizados. Se você não usar Sincronização de Arquivos do Azure para um compartilhamento específico, será melhor renomear os arquivos com caracteres inválidos no volume do StorSimple. Em seguida, execute o RoboCopy diretamente no compartilhamento de arquivos do Azure.

> [!WARNING]
> Atualmente, o Robocopy no Windows Server 2019 apresenta um problema que fará com que os arquivos em camadas por Sincronização de Arquivos do Azure no servidor de destino sejam copiados novamente da origem e recarregados no Azure ao usar a função/MIR do Robocopy. É imperativo que você use o Robocopy em um servidor Windows diferente de 2019. Uma escolha preferida é o Windows Server 2016. Esta observação será atualizada se o problema for resolvido por meio de Windows Update.

> [!WARNING]
> Você *não deve* iniciar o Robocopy antes que o servidor tenha o namespace para um compartilhamento de arquivos do Azure baixado totalmente. Para obter mais informações, consulte [determinar quando seu namespace foi totalmente baixado em seu servidor](#determine-when-your-namespace-has-fully-synced-to-your-server).

 Você só deseja copiar os arquivos que foram alterados após a última execução do trabalho de migração e os arquivos que não foram movidos por meio desses trabalhos antes. Você pode resolver o problema para o motivo pelo qual eles não se moveram mais tarde no servidor, após a conclusão da migração. Para obter mais informações, consulte [sincronização de arquivos do Azure solução de problemas](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

O RoboCopy tem vários parâmetros. O exemplo a seguir demonstra um comando concluído e uma lista de motivos para escolher esses parâmetros.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Plano de fundo:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Permite que o RoboCopy seja executado em vários threads. O padrão é 8 e o máximo é 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Gera o status do arquivo de LOG como UNICODE (Substitui o log existente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Saídas para a janela do console. Usado em conjunto com a saída para um arquivo de log.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      Omite o log de progresso para manter o log legível.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Executa o RoboCopy no mesmo modo que um aplicativo de backup usaria. Ele permite que o RoboCopy mova arquivos aos quais o usuário atual não tem permissões.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Permite que o RoboCopy considere apenas deltas entre origem (dispositivo StorSimple) e destino (diretório do Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Garante que a fidelidade seja preservada em determinados cenários de espelhamento.</br>Exemplo: entre dois Robocopy é executado um arquivo experimenta uma alteração de ACL e uma atualização de atributo, por exemplo, ele também está marcado como *oculto*. Sem/IT, a alteração de ACL pode ser perdida pelo Robocopy e, portanto, não é transferida para o local de destino.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Fidelidade da cópia de arquivo (o padrão é/COPY: DAT), sinalizadores de cópia: D = dados, A = atributos, T = carimbos de data/hora, S = segurança = ACLs de NTFS, O = informações de proprietário, U = informações de auditoria.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Copie todas as informações do arquivo (equivalente a/COPY: DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Fidelidade para a cópia de diretórios (o padrão é/DCOPY: DA), sinalizadores de cópia: D = data, A = Attributes, T = carimbos de data/hora.
   :::column-end:::
:::row-end:::

Quando você configura os locais de origem e de destino do comando RoboCopy, certifique-se de examinar a estrutura da origem e do destino para garantir que elas correspondam. Se você usou o recurso de mapeamento de diretório do trabalho de migração, sua estrutura de diretório raiz poderá ser diferente da estrutura do seu volume StorSimple. Se esse for o caso, talvez seja necessário vários trabalhos do RoboCopy, um para cada subdiretório. Se você não tiver certeza se o comando será executado conforme o esperado, você poderá usar o parâmetro */l* , que simulará o comando sem realmente fazer nenhuma alteração.

Esse comando RoboCopy usa/MIR, portanto, não moverá arquivos que são os mesmos (arquivos em camadas, por exemplo). Mas se você receber a origem e o caminho de destino errado, o/MIR também limpará as estruturas de diretório na instância do Windows Server ou no compartilhamento de arquivos do Azure que não estão presentes no caminho de origem do StorSimple. Eles devem corresponder exatamente ao trabalho RoboCopy para alcançar seu objetivo pretendido de atualizar o conteúdo migrado com as últimas alterações feitas enquanto a migração está em andamento.

Consulte o arquivo de log RoboCopy para ver se os arquivos foram deixados para trás. Se houver problemas, corrija-os e execute novamente o comando RoboCopy. Não Desprovisione nenhum recurso do StorSimple antes de corrigir problemas pendentes para arquivos ou pastas dos quais você se preocupa.

Se você não usar Sincronização de Arquivos do Azure para armazenar em cache o compartilhamento de arquivos do Azure específico em questão, mas, em vez disso, optou pelo acesso direto ao compartilhamento:

1. [Monte o compartilhamento de arquivos do Azure](storage-how-to-use-files-windows.md#mount-the-azure-file-share) como uma unidade de rede em uma máquina local do Windows.
1. Execute o RoboCopy entre o StorSimple e o compartilhamento de arquivos do Azure montado. Se os arquivos não copiarem, corrija seus nomes no lado do StorSimple para remover caracteres inválidos. Em seguida, tente o RoboCopy novamente. O comando RoboCopy listado anteriormente pode ser executado várias vezes sem causar uma recall desnecessária ao StorSimple.

### <a name="user-cut-over"></a>Recorte do usuário

Se você usar Sincronização de Arquivos do Azure, provavelmente precisará criar os compartilhamentos SMB nessa instância do Windows Server habilitada para Sincronização de Arquivos do Azure que correspondam aos compartilhamentos que você tinha nos volumes do StorSimple. Você pode carregar esta etapa e fazer isso mais cedo para não perder tempo aqui. Mas você deve garantir que antes desse ponto, ninguém tenha acesso para causar alterações na instância do Windows Server.

Se você tiver uma implantação DFS-N, poderá apontar o DFN-Namespaces para os locais da nova pasta do servidor. Se você não tiver uma implantação DFS-N e colocar o dispositivo 8100 ou 8600 em um local com uma instância do Windows Server, poderá retirar esse servidor do domínio. Em seguida, ingresse no domínio sua nova instância do Windows Server habilitada para Sincronização de Arquivos do Azure. Durante esse processo, dê ao servidor o mesmo nome de servidor e compartilhe nomes como o servidor antigo para que o recorte permaneça transparente para os usuários, a diretiva de grupo e os scripts.

Saiba mais sobre o [DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview).

## <a name="deprovision"></a>Desprovisionar

Ao desprovisionar um recurso, você perde o acesso à configuração desse recurso e a seus dados. O desprovisionamento não pode ser desfeito. Não continue até confirmar que:

* A migração foi concluída.
* Não há nenhuma dependência em relação aos arquivos, pastas ou backups de volume do StorSimple que você está prestes a desprovisionar.

Antes de começar, é uma prática recomendada observar sua nova implantação de Sincronização de Arquivos do Azure em produção por algum tempo. Esse tempo oferece a oportunidade de corrigir quaisquer problemas que você possa encontrar. Depois de observar sua implantação de Sincronização de Arquivos do Azure por pelo menos alguns dias, você pode começar a desprovisionar recursos nesta ordem:

1. Desprovisione seu recurso de Gerenciador de Dados do StorSimple por meio do portal do Azure. Todos os trabalhos do DTS serão excluídos com ele. Você não poderá recuperar facilmente os logs de cópia. Se eles forem importantes para seus registros, recupere-os antes de desprovisionar.
1. Verifique se os dispositivos físicos do StorSimple foram migrados e cancele seu registro. Se você não tiver certeza de que eles foram migrados, não continue. Se você desprovisionar esses recursos enquanto eles ainda são necessários, não será possível recuperar os dados ou sua configuração.<br>Opcionalmente, você pode primeiro desprovisionar o recurso de volume do StorSimple, que limpará os dados no dispositivo. Isso pode levar vários dias e **, de forma forense,** zera os dados no dispositivo. Se isso for importante para você, manipule a zero do disco separadamente do desprovisionamento de recursos e de acordo com suas políticas.
1. Se não houver mais dispositivos registrados deixados em um Gerenciador de Dispositivos StorSimple, você poderá continuar a remover esse recurso Gerenciador de Dispositivos em si.
1. Agora é hora de excluir a conta de armazenamento do StorSimple no Azure. Novamente, pare e confirme se a migração foi concluída e se nada e não depende desses dados antes de continuar.
1. Desconecte o dispositivo físico StorSimple do seu data center.
1. Se você possui o dispositivo StorSimple, você está livre para o PC reciclá-lo. Se o dispositivo for concedido, informe o menor e retorne o dispositivo conforme apropriado.

A migração foi concluída.

> [!NOTE]
> Ainda tem dúvidas ou encontrou algum problema?</br>
> Estamos aqui para ajudar em AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>Próximas etapas

* Familiarize-se com o [sincronização de arquivos do Azure: aka.ms/AFS](./storage-sync-files-planning.md).
* Entenda a flexibilidade das políticas de [camadas de nuvem](storage-sync-cloud-tiering.md) .
* [Habilite o backup do Azure](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) nos compartilhamentos de arquivos do Azure para agendar instantâneos e definir agendamentos de retenção de backup.
* Se você vir na portal do Azure de que alguns arquivos estão permanentemente sem sincronização, examine o [Guia de solução de problemas](storage-sync-files-troubleshoot.md) para obter as etapas para resolver esses problemas.