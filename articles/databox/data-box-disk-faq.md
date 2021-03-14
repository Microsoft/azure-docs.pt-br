---
title: Perguntas Frequentes do Microsoft Azure Data Box Disk | Microsoft Docs nos dados
description: Contém perguntas frequentes e respostas sobre o Azure Data Box Disk, uma solução de nuvem que permite transferir grandes quantidades de dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: 88aedb7daa375ae6b4a9107dceed1d25ed72ed92
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039097"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: Perguntas frequentes

A solução de nuvem do Microsoft Azure Data Box Disk permite que você envie terabytes de dados do Azure de maneira rápida, barata e confiável. Estas Perguntas frequentes contêm respostas para perguntas que podem surgir durante o uso dos discos do Data Box no portal do Azure. 

As perguntas e respostas são organizadas nas seguintes categorias:

- Sobre o serviço
- Configuração e conexão 
- Rastreamento de status
- Migrar dados 
- Verificação e carregamento de dados 


## <a name="about-the-service"></a>Sobre o serviço

### <a name="q-what-is-azure-data-box-service"></a>Q. O que é o serviço Azure Data Box? 
a.  O serviço Azure Data Box foi criado para a ingestão de dados offline. Esse serviço gerencia uma variedade de produtos e é personalizado para o transporte de dados em diferentes capacidades de armazenamento. 

### <a name="q-what-are-azure-data-box-disks"></a>Q. O que são os discos do Azure Data Box?
a. Os discos do Azure Data Box permitem uma transferência de terabytes de dados rápida, barata e segura entre a plataforma do Azure. A Microsoft fornece de um a cinco discos, com capacidade de armazenamento máxima de 35 TB. Você pode configurar, conectar e desbloquear facilmente esses discos por meio do serviço Data Box no Portal do Azure.  

Os discos são criptografados usando a Criptografia de Unidade de Disco BitLocker da Microsoft e suas chaves de criptografia são gerenciadas no portal do Azure. Em seguida, você copia os dados dos servidores do cliente. No datacenter, a Microsoft migra os dados da unidade para a nuvem usando um link de carregamento rápido de rede privada e os carrega para o Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>Q. Quando devo usar discos do Data Box?
a. Se você tiver 40 TB de dados (ou menos) que deseja transferir para o Azure, os discos do Data Box serão uma boa opção.

### <a name="q-what-is-the-price-of-data-box-disks"></a>Q. Qual é o preço dos discos do Data Box?
a. Para saber mais sobre o preço do Data Box Disk, acesse a [página Preços](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>Q. Como fazer para obter discos do Data Box? 
a.  Para obter Azure Data Box Disks, faça logon no portal do Azure e crie um pedido de discos do Data Box. Forneça detalhes de notificação e informações de contato. Depois de fazer um pedido, com base na disponibilidade, os discos serão enviados para você no prazo de 10 dias.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Q. Qual é a quantidade máxima de dados que posso transferir com discos do Data Box em uma instância?
a. Para cinco discos, cada um com capacidade de 8 TB (7 TB de capacidade utilizável), a capacidade utilizável máxima é de 35 TB. Portanto, você pode transferir 35 TB de dados em uma instância. Para transferir mais dados, você precisa solicitar mais discos.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Q. Como verificar se os discos do Data Box estão disponíveis na minha região? 
a.  Para ver onde os Data Box Disks estão disponíveis no momento, acesse [Disponibilidade de região](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Q. Em que regiões posso armazenar dados com discos do Data Box?
a. Disco do Data Box tem suporte para todas as regiões dentro dos EUA, Canadá, EU, Reino Unido, Austrália, Cingapura, Índia, China, Hong Kong, Japão, Coreia e África do Sul. Somente as regiões de nuvem pública do Azure têm suporte. Não há suporte para o Azure Governamental ou outras nuvens soberanas.

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>Q. Como importar dados de origem presentes em meu local em um país/região para uma região do Azure em um país diferente?
a. Disco do Data Box dá suporte à ingestão de dados somente dentro do mesmo país/região que seu destino e não cruzará nenhuma borda internacional. A única exceção é para pedidos na União Europeia (UE), em que Data Box discos podem enviar de e para qualquer país/região da UE.

Por exemplo, se você quisesse mover dados em seu local no Canadá para uma conta de armazenamento do oeste dos EUA do Azure, você pode obtê-lo da seguinte maneira:

#### <a name="option-1"></a>Opção 1: 

Envie um [disco com suporte](../import-export/storage-import-export-requirements.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#supported-disks) contendo dados usando o [serviço de importação/exportação do Azure](../import-export/storage-import-export-service.md) do local de origem no Canadá para o datacenter do oeste dos EUA do Azure.

#### <a name="option-2"></a>Opção 2:

1. Solicite Disco do Data Box no Canadá escolhendo uma conta de armazenamento dizer na central do Canadá. Os discos SSD são enviados do datacenter do Azure no Canadá central para o endereço de envio (no Canadá) fornecido durante a criação do pedido.

2. Depois que os dados do seu servidor local são copiados para os discos, devolva-os para o datacenter do Azure no Canadá usando os rótulos de retorno fornecidos pela Microsoft. Os dados presentes nas Disco do Data Box serão carregados na conta de armazenamento de destino na região do Azure do Canadá escolhida durante a criação do pedido.

3. Você pode usar uma ferramenta como AzCopy para copiar os dados para uma conta de armazenamento no oeste dos EUA. Essa etapa incorre em custos de [largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) e [armazenamento padrão](https://azure.microsoft.com/pricing/details/storage/) que não estão incluídos na cobrança de disco do data box.

#### <a name="q-does-data-box-disk-store-any-customer-data-outside-of-the-service-region"></a>Q. Disco do Data Box armazenar dados de clientes fora da região de serviço?

a. Não. Disco do Data Box não armazena nenhum dado de cliente fora da região de serviço. O cliente tem Propriedade total de seus dados e pode salvar os dados em um local especificado com base na conta de armazenamento que eles selecionam durante a criação do pedido.  

Além dos dados do cliente, há Disco do Data Box dados que incluem metadados e logs de monitoramento. Em todas as regiões (exceto sul do Brasil e Sudeste Asiático), Disco do Data Box dados são armazenados e replicados em uma [região emparelhada](../best-practices-availability-paired-regions.md) por meio de uma conta de armazenamento com redundância geográfica para proteger contra perda de dados.  

Devido aos [requisitos de residência de dados](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) no sul do Brasil e no sudeste asiático, disco do data Box dados são armazenados em uma conta de ZRS (armazenamento com redundância de zona) para que ele esteja contido em uma única região. Para o Sudeste Asiático, todos os Disco do Data Box dados são armazenados em Cingapura e no sul do Brasil, os dados são armazenados no Brasil. 

Se houver uma interrupção de serviço no sul do Brasil e no sudeste asiático, os clientes poderão criar novos pedidos de outra região. Os novos pedidos serão servidos a partir da região em que são criados e os clientes são responsáveis pelo envio para e partir do Disco do Data Box.



### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>Q. Como posso recuperar meus dados se uma região inteira falhar?

a. Em circunstâncias extremas em que uma região é perdida devido a um desastre significativo, a Microsoft pode iniciar um failover regional. Nenhuma ação de sua parte é necessária nesse caso. Seu pedido será atendido pela região de failover se estiver dentro do mesmo limite de país ou de comércio. No entanto, algumas regiões do Azure não têm uma região emparelhada no mesmo limite geográfico ou de comércio. Se houver um desastre em qualquer uma dessas regiões, você precisará criar a ordem de Data Box novamente de uma região diferente que esteja disponível e copiar os dados para o Azure na nova região. Para saber mais, confira [Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Combinadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Q. Quem devo contatar se eu encontrar problemas com discos do Data Box?
a. Se você encontrar problemas com Data Box discos, [entre em contato com suporte da Microsoft](./data-box-disk-contact-microsoft-support.md).

## <a name="order-device"></a>Solicitar dispositivo

### <a name="q-how-do-i-get-data-box-disk"></a>Q. Como fazer obter Disco do Data Box? 
a.  Para obter Disco do Azure Data Box, entre no portal do Azure e crie um Disco do Data Box pedido. Forneça detalhes de notificação e informações de contato. Depois de fazer um pedido, com base na disponibilidade, Disco do Data Box é enviado para você dentro de 10 dias. Para obter mais informações, acesse [Solicitar um Data Box](data-box-disk-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-disk-order-in-the-azure-portal-why"></a>Q. Não foi possível criar uma ordem de Disco do Data Box no portal do Azure. Por quê?
a. Se você não puder criar uma ordem de Disco do Data Box, há um problema com o tipo de assinatura ou o acesso.

Verifique sua assinatura. Disco do Data Box só está disponível para ofertas de assinatura de Contrato Enterprise (EA) e de provedor de soluções na nuvem (CSP). Se você não tiver nenhum desses tipos de assinatura, entre em contato com Suporte da Microsoft para atualizar sua assinatura.

Se você tiver um tipo de oferta compatível com a assinatura, verifique o nível de acesso da assinatura. Você precisa ser um colaborador ou proprietário em sua assinatura para criar um pedido.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>Q. Quanto tempo o meu pedido levará da criação do pedido para os dados carregados no Azure?

a. Os seguintes tempos de entrega estimados para cada fase do processamento do pedido darão a você uma boa ideia do que esperar.  

Esses prazos de entrega são *estimativas*. O tempo para cada estágio do processamento do pedido é afetado pela carga no datacenter, em ordens simultâneas e em outras condições ambientais.

**Prazos de entrega estimados para um pedido de Disco do Data Box:**

1. Ordem Disco do Data Box: alguns minutos, no portal
2. Preparação e alocação de disco: até 5 dias úteis, dependendo da disponibilidade do estoque e do número de pedidos pendentes a serem processados
3. Remessa: 2 a 3 dias úteis
4. Cópia de dados no site do cliente: depende da natureza dos dados, do tamanho e do número de arquivos.
5. Remessa de devolução: 2 a 3 dias úteis
6. Processamento no datacenter e carregue no Azure: o carregamento de dados começa no datacenter assim que o processamento operacional é concluído e o disco está conectado. O tempo de carregamento depende da natureza dos dados, do tamanho e do número de arquivos.

## <a name="configure-and-connect"></a>Configuração e conexão
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Q. Posso especificar o número de discos do Data Box no pedido?
a.  Não. Você obtém discos de 8 TB (um máximo de cinco discos), dependendo do tamanho dos dados e da disponibilidade dos discos.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Q. Como faço para desbloquear os discos do Data Box? 
a.  No portal do Azure, vá para o pedido do Data Box Disk e navegue até **Detalhes do dispositivo**. Copie a chave de acesso. Baixe e extraia a ferramenta de desbloqueio do Data Box Disk do portal do Azure para seu sistema operacional. Execute a ferramenta no computador que contém os dados que você deseja copiar para os discos. Forneça a chave de acesso para desbloquear os discos. A mesma chave de acesso desbloqueia todos os discos. 

Para obter instruções passo a passo, acesse [Desbloquear discos em um cliente do Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) ou [Desbloquear discos em um cliente do Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Q. Posso usar um computador de host do Linux para conectar e copiar os dados para discos do Data Box?
a.  Sim. Os clientes do Linux e do Windows podem ser usados para conectar e copiar dados em discos do Data Box. Para obter mais informações, vá para a lista de [sistemas operacionais com suporte](data-box-disk-system-requirements.md) de seu computador host.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. Meus discos já foram enviados, mas agora quero cancelar o pedido. Por que o botão Cancelar não está disponível?
a.  Pedidos só podem ser cancelados após a realização do pedido de discos e antes do envio. Depois que os discos são expedidos, você não pode cancelar o pedido. No entanto, você pode devolver seus discos pagando um encargo. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Q. Posso conectar vários discos do Data Box ao mesmo tempo ao computador host para transferir dados?
a. Sim. Vários discos do Data Box podem ser conectados ao mesmo computador host para transferir dados, e vários trabalhos de cópia podem ser executados paralelamente.

## <a name="track-status"></a>Rastreamento de status

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Q. Como rastrear os discos desde a realização do pedido até a devolução? 
a.  Você pode acompanhar o status do pedido de Data Box Disk no portal do Azure. Ao criar o pedido, é necessário também fornecer um email para notificações. Se você tiver fornecido um, você será notificado por email sobre todas as alterações de status do pedido. Para obter mais informações, consulte como [configurar emails de notificação](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>Q. Como fazer para retornar os discos? 
a.  A Microsoft fornece uma etiqueta de remessa com os discos do Data Box no pacote de envio. Cole a etiqueta na embalagem para envio e leve o pacote lacrado à transportadora responsável. Se o rótulo estiver danificado ou for perdido, vá para **Visão geral > Baixar etiqueta de remessa** e baixe um novo rótulo de remessa de devolução.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Posso coletar meu pedido de Data Box Disk? Posso devolver os discos pela transportadora que eu escolher?
a. Sim. A Microsoft também oferece entregas autogerenciadas somente na região US Gov. Ao fazer o pedido do Data Box Disk, você pode escolher a opção de envio autogerenciado. Para coletar seu pedido do Data Box Disk, execute as seguintes etapas:
    
1. Depois de fazer o pedido, o pedido é processado e os discos são preparados. Você será notificado por um email de que seu pedido está pronto para o recebimento. 
2. Quando o pedido estiver pronto para o recebimento, vá para seu pedido na portal do Azure e navegue até a folha **Visão geral**. 
3. Você verá uma notificação com um código no portal do Azure. Envie à [equipe de Operações do Azure Data Box](mailto:adbops@microsoft.com) um email e forneça o código. A equipe informará a localização e agendará uma data e hora de retirada. Você deve chamar a equipe dentro de cinco dias úteis depois de receber a notificação por email.

Depois que a cópia e a validação de dados forem concluídas, execute as seguintes etapas para devolver seu disco:

1. Após a conclusão da validação de dados, desconecte os discos. Remova os cabos de conexão.
2. Empacote todos os discos e os cabos de conexão com um conencapsulamento de bolha e coloque-os na caixa de envio. Poderá haver encargos se os acessórios estiverem ausentes.

    - Reutilize a embalagem da remessa inicial. É recomendável que empacotar os discos usando um plástico bolha bem ajustado.
    - Verifique se que o ajuste é firme para reduzir qualquer movimentos dentro da caixa.
3. Vá para a **folha Visão geral** do seu pedido no portal do Azure. Você deverá ver uma notificação com um código.
4. Use esse código e envie à [equipe de Operações do Azure Data Box](mailto:adbops@microsoft.com) um email e forneça o código. Eles fornecerão informações sobre onde e quando entregar os discos.


## <a name="migrate-data"></a>Migrar dados

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Q. Qual é o tamanho máximo de dados que pode ser usado com discos do Data Box?  
a.  A solução Data Box Disk pode ter até cinco discos com capacidade máxima utilizável de 35 TB. Os discos em si têm 8 TB (7 TB utilizáveis).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Q. Quais são os tamanhos máximos de blob de página e de bloco compatíveis com os discos do Data Box? 
a.  Os tamanhos máximos são regidos pelos limites do Armazenamento do Azure. O blob de blocos máximo é de aproximadamente 4,768 TiB e o tamanho do blob de página máximo é 8 TiB. Para obter mais informações, confira [Escalabilidade e metas de desempenho do Armazenamento de Blobs](../storage/blobs/scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Q. Qual é a velocidade de transferência de dados para os discos do Data Box?
a. Quando testado com discos conectados via USB 3.0, o desempenho do disco foi de até 430 MB/s. Os números reais variam dependendo do tamanho do arquivo usado. Para arquivos menores, o desempenho poderá ser mais baixo.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. Como fazer para saber se meus dados estão protegidos durante o trânsito? 
a.  Os discos do Data Box são criptografados usando a criptografia BitLocker AES de 128 bits e a chave de acesso só está disponível no portal do Azure. Faça logon no portal do Azure usando suas credenciais de conta para obter a chave de acesso. Forneça essa chave de acesso quando executar a ferramenta de desbloqueio do Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Q. Como fazer para copiar os dados em discos do Data Box? 
a.  Use uma ferramenta de cópia SMB como `Robocopy` , `Diskboss` ou até mesmo o Windows File Explorer, arraste e solte para copiar dados em discos.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. Existem dicas para acelerar a cópia dos dados?
a.  Para acelerar o processo de cópia:

- Use diversos fluxos de cópia de dados. Por exemplo, com `Robocopy` , use a opção multi-threaded. Para obter mais informações sobre o comando exato usado, acesse [Tutorial: copiar dados para o Azure Data Box Disk e verificar](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Use várias sessões.
- Em vez de copiar arquivos por compartilhamento de rede (onde você poderia ser limitado pelas velocidades da rede), tenha os dados no mesmo computador em que os discos estão conectados.
- Verifique se você está usando USB 3,0 ou posterior em todo o processo de cópia. Baixe e use a [ `USBView` ferramenta](/windows-hardware/drivers/debugger/usbview) para identificar os controladores USB e os dispositivos USB conectados ao computador.
- Avalie o desempenho do computador usado para copiar os dados. Baixe e use a [ `Bluestop` `FIO` ferramenta](https://ci.appveyor.com/project/axboe/fio) para avaliar o desempenho do hardware do servidor. Selecione o build x86 ou x64 mais recente, selecione a guia **Artefatos** e baixe a MSI.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Q. Como acelerar os dados se a fonte de dados tiver arquivos pequeno (KBs ou alguns MBs)?
a.  Para acelerar o processo de cópia:

- Crie um VHDx local em armazenamento rápido ou crie um VHD vazio em HDD/SSD (mais lento).
- Monte-o em uma VM.
- Copie arquivos para o disco da VM.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Q. Posso usar várias contas de armazenamento com discos do Data Box?
a.  Não. Atualmente, apenas uma conta de armazenamento, geral ou clássica, tem suporte para discos do Data Box. Há suporte para o blob frequente e esporádico.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>Q. O que é o conjunto de ferramentas disponível para meus dados com Data Box Disks?
a. O conjunto de ferramentas disponível com o Data Box Disk contém três ferramentas:
 - **Ferramenta de desbloqueio do Data Box Disk**: Use essa ferramenta para desbloquear os discos criptografados que são enviados pela Microsoft. Ao desbloquear os discos usando a ferramenta, você precisa fornecer uma chave de acesso disponível no pedido do Data Box Disk no portal do Azure. 
 - **Ferramenta de validação do Data Box Disk**: Use essa ferramenta para validar tamanho, formato e nomes de blob de acordo com o Azure convenções de nomenclatura. Ele também gera somas de verificação para os dados copiados, que são usados para verificar os dados carregados no Azure.
 - **Ferramenta de cópia de divisão do Data Box Disk**: Use essa ferramenta quando você usar vários discos e tiver um grande conjunto de dados que precisa ser dividido e copiado entre todos os discos. Essa ferramenta já está disponível para Windows. Esta ferramenta não é compatível com discos gerenciados. Essa ferramenta valida os dados à medida que os copia, para que você possa ignorar a etapa de validação ao usar essa ferramenta.

O conjunto de ferramentas está disponível para Windows e Linux. Você pode baixar o conjunto de ferramentas aqui:
- [Baixe o conjunto de ferramentas do Data Box Disk para Windows](https://aka.ms/databoxdisktoolswin) 
- [Baixe o conjunto de ferramentas do Data Box Disk para Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>Q. Posso usar o Data Box Disk para transferir dados para os Arquivos do Azure e, em seguida, usar os dados com a Sincronização de Arquivos do Azure? 
a. Os arquivos do Azure são compatíveis com o Data Box Disk, mas não funcionarão bem com a Sincronização de Arquivos do Azure. Os metadados não serão mantidos se os dados de arquivo forem usados com a Sincronização de Arquivos do Azure.


## <a name="verify-and-upload"></a>Verificar e carregar

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Q. Quanto tempo demora até eu poder acessar meus dados no Azure depois que os discos são devolvidos? 
a.  Depois que o status do pedido de cópia de dados aparece como concluído, você deve conseguir acessar os dados imediatamente.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. Onde meus dados estão localizados no Azure após o carregamento?
a.  Quando você copia os dados nas pastas *BlockBlob* e *PageBlob* no disco, um contêiner é criado na conta de armazenamento do Azure para cada subpasta nas pastas *BlockBlob* e *PageBlob*. Se você copiou os arquivos nas pastas *BlockBlob* e *PageBlob* diretamente, os arquivos estarão em um contêiner padrão *$root* na conta de armazenamento do Azure. Quando você copia os dados em uma pasta na pasta *AzureFile*, um compartilhamento de arquivos é criado.

### <a name="q-i-just-noticed-that-i-didnt-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. Acabei de observar que não segui os requisitos de nomenclatura do Azure para meus contêineres. Meus dados serão carregados no Azure?
a. Todas as letras maiúsculas em seus nomes de contêiner são automaticamente convertidas em minúsculas. Se os nomes não forem compatíveis de outras maneiras, por exemplo, eles contêm caracteres especiais ou outras linguagens, o carregamento falhará. Para obter mais informações, acesse [Convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>Q. Como fazer para verificar os dados que copiei em vários discos do Data Box?
a.  Depois que a cópia de dados for concluída, você poderá executar `DataBoxDiskValidation.cmd` fornecido na pasta *DataBoxDiskImport* para gerar somas de verificação para validação. Se você tiver vários discos, precisará abrir uma janela de comando por disco e executar esse comando. Tenha em mente que a operação pode levar muito tempo (~ horas) dependendo do tamanho dos dados.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>Q. O que acontece com meus dados após eu devolver os discos?
a.  Depois que a cópia de dados para o Azure for concluída, os dados dos discos serão apagados com segurança de acordo com as diretrizes NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. Como meus dados são protegidos durante o trânsito? 
a.  Os discos Data Box são criptografados com a criptografia BitLocker do Microsoft AES-128 e uma única chave de acesso é necessária para desbloquear todos os discos e acessar dados.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>Q. Preciso executar novamente a validação da soma de verificação se eu adicionar mais dados aos discos do Data Box?
a. Sim. Se você decidir validar seus dados (é recomendável que você o faça!), precisará executar a validação novamente caso tenha adicionado mais dados aos discos.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>Q. Eu usei todos os meus discos para transferir dados e preciso solicitar mais discos. Há uma maneira de fazer o pedido rapidamente?
a. Você pode clonar o pedido anterior. A clonagem cria o mesmo pedido de antes e permite que você edite somente os detalhes do pedido, sem a necessidade de digitar detalhes de endereço, contato e notificação.

### <a name="q-i-copied-data-to-the-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-how-can-i-locate-it"></a>Q. Copiei dados para a pasta ManagedDisk. Não vejo nenhum disco gerenciado com o grupo de recursos especificado para discos gerenciados. Meus dados foram carregados no Azure? Como posso localizá-lo?
a. Sim. Seus dados foram carregados no Azure, mas se você não vir discos gerenciados com os grupos de recursos especificados, é provável que os dados não sejam válidos. Se os blobs de páginas, blobs de blocos, arquivos do Azure ou discos gerenciados não forem válidos, eles irão para as seguintes pastas:
 - Os blobs de páginas vão para um contêiner de blobs de blocos que começa com *databoxdisk-Invalid-PB-*.
 - Os arquivos do Azure vão para um contêiner de blob de blocos que começa com *databoxdisk-Invalid-AF-*.
 - Os discos gerenciados vão para um contêiner de blob de blocos começando com *databoxdisk-Invalid-MD-*.

## <a name="next-steps"></a>Próximas etapas

- Examine os [Requisitos de sistema do Data Box Disk](data-box-disk-system-requirements.md).
- Entenda os [limites do Data Box Disk](data-box-disk-limits.md).
- Implante rapidamente o [Azure Data Box Disk](data-box-disk-quickstart-portal.md) no portal do Azure.