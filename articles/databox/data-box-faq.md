---
title: Perguntas frequentes sobre o Microsoft Azure Data Box | Microsoft Docs nos dados
description: Contém perguntas frequentes e respostas sobre o Azure Data Box, uma solução de nuvem que permite transferir grandes quantidades de dados para o Azure.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/25/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: a692aeba312b6fcad580eac901f4b7bc65f059fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730568"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Perguntas frequentes

A solução híbrida Microsoft Azure Data Box permite que você envie terabytes de dados para o Azure de maneira rápida, econômica e confiável usando um dispositivo de transferência. Estas perguntas frequentes contêm respostas para perguntas que possam surgir durante o uso do Data Box no portal do Azure.

As perguntas e respostas são organizadas nas seguintes categorias:

- Sobre o serviço
- Solicitar dispositivo
- Configuração e conexão 
- Rastreamento de status
- Copiar dados 
- Enviar dispositivo
- Verificação e carregamento de dados 
- Cadeia de suporte de custódia

## <a name="about-the-service"></a>Sobre o serviço

### <a name="q-what-is-azure-data-box-service"></a>Q. O que é o serviço Azure Data Box? 
a.  O serviço Azure Data Box foi criado para a ingestão de dados offline. Esse serviço gerencia uma variedade de produtos de diferentes capacidades de armazenamento, todas adaptadas para o transporte de dados. 

### <a name="q-what-is-azure-data-box"></a>Q. O que é o Azure Data Box?
a. O Azure Data Box permite uma transferência de terabytes de dados rápida, econômica e segura para o Azure. Você solicita o dispositivo Data Box pelo portal do Azure. A Microsoft envia um dispositivo de armazenamento com capacidade utilizável de 80 TB por meio de uma transportadora regional. 

Depois que o dispositivo é recebido, você configura-o rapidamente usando a interface do usuário da Web local. Copie os dados dos servidores para o dispositivo ou do dispositivo para os servidores e envie o dispositivo de volta para o Azure. Para uma ordem de importação, no datacenter do Azure, seus dados são automaticamente carregados do dispositivo para o Azure. Todo o processo é acompanhado de ponta a ponta pelo serviço Data Box no portal do Azure.

### <a name="q-when-should-i-use-data-box"></a>Q. Quando devo usar o Data Box?
a. Se você tiver 40 a 500 TB de dados que deseja transferir para ou do Azure, você se beneficiará do uso de Data Box. Para tamanhos de dados < 40 TB, use o Data Box Disk e, para tamanhos de dados > 500 TB, inscreva-se no [Data Box Heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>Q. Qual é o preço do Data Box?
a. O Data Box está disponível em um encargo nominal para 10 dias. Quando você seleciona o modelo do produto durante a criação de um pedido no portal do Azure, os encargos do dispositivo são exibidos. Cobranças e encargos de envio padrão para o armazenamento do Azure também se aplicam. As ordens de exportação seguem um modelo de preços semelhante para ordens de importação, embora possam ser aplicadas encargos de egresso adicionais. 

Para obter mais informações, acesse Azure Data Box [cobranças](https://azure.microsoft.com/pricing/details/bandwidth/)de [preços](https://azure.microsoft.com/pricing/details/storage/databox/) e de egresso. 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>Q. Qual é a quantidade máxima de dados que posso transferir com o Data Box em uma instância?
a. O Data Box tem uma capacidade bruta de 100 TB e uma capacidade utilizável de 80 TB. Você pode transferir até 80 TB de dados com o Data Box. Para transferir mais dados, você precisa solicitar mais dispositivos.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>Q. Como posso verificar se o Data Box está disponível em minha região? 
a.  Para obter informações sobre em quais países/regiões o Data Box está disponível, acesse [Disponibilidade de regiões](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>Q. Em quais regiões posso armazenar dados com o Data Box?
a. O Data Box é compatível com todas as regiões nos EUA, na Europa Ocidental, no Norte da Europa, na França, no Reino Unido, no Japão, na Austrália e no Canadá. Para saber mais, acesse [Disponibilidade por região](data-box-overview.md#region-availability).

### <a name="q-how-can-i-import-source-data-at-my-location-in-a-particular-country-to-an-azure-region-in-a-different-countryregion-or-export-data-from-an-azure-region-in-one-country-to-a-different-countryregion"></a>Q. Como importar dados de origem em meu local em um país específico para uma região do Azure em um país/região diferente ou exportar dados de uma região do Azure em um país para um país/região diferente?

Data Box dá suporte à ingestão de dados ou egresso somente dentro do mesmo país/região que o destino e não cruzará nenhuma borda internacional. A única exceção é para pedidos na União Europeia (UE), em que as caixas de dados podem ser enviadas de e para qualquer país/região da UE.

Por exemplo, no cenário de importação, se você tivesse os dados de origem no Canadá que você queria mover para uma conta de armazenamento do oeste dos EUA do Azure, você poderia obtê-lo da seguinte maneira:

1. Solicite Data Box no Canadá escolhendo uma conta de armazenamento no Canadá. O dispositivo é enviado de um datacenter do Azure no Canadá para o endereço de envio (no Canadá) fornecido durante a criação do pedido.

2. Depois que a cópia de dados local para a Data Box for concluída, retorne o dispositivo para o datacenter do Azure no Canadá. Os dados presentes na Data Box são carregados para a conta de armazenamento de destino na região do Azure do Canadá escolhida durante a criação do pedido.

3. Você pode usar uma ferramenta como AzCopy para copiar os dados para uma conta de armazenamento no oeste dos EUA. Essa etapa incorre em custos de [largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) e [armazenamento padrão](https://azure.microsoft.com/pricing/details/storage/) que não estão incluídos na cobrança de data box.

#### <a name="q-does-data-box-store-any-customer-data-outside-of-the-service-region"></a>Q. Data Box armazenar dados de clientes fora da região de serviço?

a. Não. Data Box não armazena nenhum dado de cliente fora da região de serviço. O cliente tem Propriedade total de seus dados e pode salvar os dados em um local especificado com base na conta de armazenamento que eles selecionam durante a criação do pedido.  

Além dos dados do cliente, há Data Box dados que incluem os artefatos de segurança relacionados ao dispositivo, os logs de monitoramento para o dispositivo e o serviço e os metadados relacionados ao serviço. Em todas as regiões (exceto sul do Brasil e Sudeste Asiático), Data Box dados são armazenados e replicados na região emparelhada por meio de uma conta de armazenamento com redundância geográfica para proteger contra perda de dados.  

Devido aos [requisitos de residência de dados](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) no sul do Brasil e no sudeste asiático, data Box dados são armazenados em uma conta de ZRS (armazenamento com redundância de zona) para que ele esteja contido em uma única região. Para o Sudeste Asiático, todos os Data Box dados são armazenados em Cingapura e no sul do Brasil, os dados são armazenados no Brasil. 

Se houver uma interrupção de serviço no sul do Brasil e no sudeste asiático, os clientes poderão criar novos pedidos de outra região. Os novos pedidos serão atendidos a partir da região em que são criados e os clientes são responsáveis pelo envio para e partir do dispositivo de Data Box.

### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>Q. Como posso recuperar meus dados se uma região inteira falhar?

a. Em circunstâncias extremas em que uma região é perdida devido a um desastre significativo, a Microsoft pode iniciar um failover regional. Nenhuma ação de sua parte é necessária nesse caso. Seu pedido será atendido pela região de failover se estiver dentro do mesmo limite de país ou de comércio. No entanto, algumas regiões do Azure não têm uma região emparelhada no mesmo limite geográfico ou de comércio. Se houver um desastre em qualquer uma dessas regiões, você precisará criar a ordem de Data Box novamente de uma região diferente que esteja disponível e copiar os dados para o Azure na nova região. Para saber mais, confira [Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Combinadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="q-who-should-i-contact-if-i-come-across-any-issues-with-data-box"></a>Q. Quem devo entrar em contato se eu entrar em qualquer problema com Data Box?
a. Se você tiver problemas com Data Box, [entre em contato com suporte da Microsoft](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-lost-my-data-box-is-there-a-lost-device-charge"></a>Q. Perdi meu Data Box. Há um encargo de dispositivo perdido?
a. Sim. Há um encargo para um dispositivo perdido ou danificado. Essa cobrança é abordada na [página de preços](https://azure.microsoft.com/pricing/details/storage/databox/) e nos [termos de serviço do produto](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Solicitar dispositivo

### <a name="q-how-do-i-get-data-box"></a>Q. Como fazer para obter o Data Box? 
a.  Para obter o Azure Data Box, entre no portal do Azure e crie um pedido do Data Box. Forneça detalhes de notificação e informações de contato. Depois de fazer um pedido, com base na disponibilidade, o Data Box será enviado para você no prazo de 10 dias. Para obter mais informações, acesse [Solicitar um Data Box](data-box-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-order-in-the-azure-portal-why"></a>Q. Não foi possível criar uma ordem de Data Box no portal do Azure. Por quê?
a. Se você não puder criar uma ordem de Data Box, há um problema com o tipo de assinatura ou o acesso.

Verifique sua assinatura. O Data Box só está disponível para as ofertas de assinatura EA (Contrato Enterprise) e CSP (Provedor de Soluções na Nuvem). Se você não tiver nenhum desses tipos de assinatura, entre em contato com Suporte da Microsoft para atualizar sua assinatura.

Se você tiver um tipo de oferta compatível com a assinatura, verifique o nível de acesso da assinatura. Você precisa ser um colaborador ou proprietário em sua assinatura para criar um pedido.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>Q. Quanto tempo o meu pedido levará da criação do pedido para os dados carregados no Azure?

a. Os seguintes tempos de entrega estimados para cada fase do processamento do pedido darão a você uma boa ideia do que esperar.  

Esses prazos de entrega são *estimativas*. O tempo para cada estágio do processamento do pedido é afetado pela carga no datacenter, em ordens simultâneas e em outras condições ambientais.

**Prazos de entrega estimados para um pedido de Data Box:**

1. Ordem Data Box: alguns minutos, no portal
2. Preparação e alocação de dispositivo: 1-2 dias úteis, sujeito à disponibilidade de estoque e a outros pedidos pendentes de conclusão
3. Remessa: 2 a 3 dias úteis
4. Cópia de dados no site do cliente: depende da natureza dos dados, do tamanho e do número de arquivos
5. Remessa de devolução: 2 a 3 dias úteis
6. Dispositivo de processamento em datacenter: 1-2 dias úteis, sujeito a outros pedidos com processamento pendente
7. Carregar dados no Azure: começa assim que o processamento é concluído e o dispositivo está conectado. O tempo de carregamento depende da natureza dos dados, do tamanho e do número de arquivos.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-cant-create-any-additional-orders-why"></a>Q. Solicitei alguns dispositivos Data Box. Não consigo criar pedidos adicionais. Por quê?
a. Permitimos um máximo de cinco pedidos ativos por assinatura por limite de comércio (combinação de país e região selecionados). Caso precise solicitar um dispositivo adicional, contate o Suporte da Microsoft para aumentar o limite de sua assinatura.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>Q. Quando tento criar um pedido, recebo uma notificação de que o serviço Data Box não está disponível. O que isso significa?
a. O serviço de Data Box não está disponível para a combinação de país e região que você selecionou. A alteração dessa combinação provavelmente permitirá que você utilize o serviço Data Box. Para obter uma lista das regiões nas quais o serviço está disponível, acesse [Disponibilidade de regiões para o Data Box](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>Q. Fiz meu pedido do Data Box há alguns dias. Quando receberei meu Data Box?
a. Quando você faz um pedido, verificamos se um dispositivo está disponível para seu pedido. Se um dispositivo estiver disponível, nós o enviaremos em 10 dias. É concebível que há períodos de alta demanda. Nessa situação, seu pedido será colocado na fila e você poderá acompanhar a alteração de status no portal do Azure. Caso seu pedido não seja atendido em 90 dias, ele será cancelado automaticamente.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>Q. Eu preenche meus Data Box com dados e preciso pedir outro. Há uma maneira de fazer o pedido rapidamente?
a. Você pode clonar o pedido anterior. A clonagem cria o mesmo pedido de antes e permite que você edite somente os detalhes do pedido, sem a necessidade de digitar detalhes de endereço, contato e notificação. A clonagem é permitida somente para ordens de importação.

## <a name="configure-and-connect"></a>Configuração e conexão

### <a name="q-how-do-i-unlock-the-data-box"></a>Q. Como fazer para desbloquear o Data Box? 
a.  No portal do Azure, acesse seu pedido do Data Box e navegue para **Detalhes do dispositivo**. Copie a senha de desbloqueio. Use essa senha para fazer logon na interface do usuário da Web local do Data Box. Para obter mais informações, acesse [Tutorial: Desempacotar, cabear e conectar o Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>Q. Posso usar um computador host Linux para me conectar aos dados e copiá-los para o Data Box?
a.  Sim. Você pode usar o Data Box para se conectar aos clientes SMB e NFS. Para obter mais informações, vá para a lista de [sistemas operacionais com suporte](data-box-system-requirements.md) de seu computador host.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. Meu Data Box já foi expedido, mas agora desejo cancelar o pedido. Por que o botão Cancelar não está disponível?
a.  Você pode cancelar o pedido somente depois que o Data Box é solicitado e antes que o pedido é processado. Depois que o pedido do Data Box for processado, não será mais possível cancelá-lo. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>Q. Posso conectar um Data Box ao mesmo tempo a vários computadores de host para transferir dados?
a. Sim. Vários computadores host podem se conectar ao Data Box para transferir dados e vários trabalhos de cópia podem ser executados em paralelo. Para obter mais informações, acesse [Tutorial: Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>Q. Posso me conectar a ambas as interfaces de 10 GbE no Data Box para transferir dados?
a. Sim. Ambas as interfaces de 10 GbE podem ser conectadas no Data Box para copiar dados ao mesmo tempo. Para obter mais informações sobre como copiar dados, vá para [Tutorial: Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why?
A.

### Q. I could not set up Data Box using a Static IP address. Why?
A.

### Q. I could not set up Data Box on a private network. Why?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>Q. O LED indicador de falha do sistema no painel de operação frontal está ativado. O que devo fazer?
a. Há duas luzes de LED sob o botão de energia na frente de um Data Box. A luz mais baixa é o indicador de falha do sistema, que indica se o sistema está íntegro.

Um LED indicador de falha do sistema que é vermelho pode indicar um dos seguintes problemas:
- Falha do ventilador
- A temperatura da CPU é alta
- A temperatura da placa-mãe está alta
- Erro de conexão de código (ECC) do módulo de memória embutida dupla (DIMM)

Siga estas etapas:
1. Verifique se o ventilador está funcionando.
2. Mova o dispositivo para um local com um fluxo de ar maior.

Se a luz do indicador de falha do sistema ainda estiver ativada, [entre em contato com suporte da Microsoft](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why"></a>Q. Não consigo acessar a senha de desbloqueio do Data Box no portal do Azure. Por quê?
a. Se você não conseguir acessar a senha de desbloqueio no portal do Azure, verifique as permissões em sua assinatura e conta de armazenamento. Verifique se você tem permissão de colaborador ou proprietário no nível do grupo de recursos. Você precisa ter pelo menos Data Box permissão de função de operador para ver as credenciais de acesso.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>Q. Há suporte para configuração de canal de porta no Data Box? E quanto ao MPIO?
a. Não há suporte para configuração de canal de porta, configuração de multipath IO (MPIO) ou configuração de vLAN em Data Box.

## <a name="track-status"></a>Rastreamento de status

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>Q. Como fazer para rastrear o Data Box desde a realização do pedido até a devolução do dispositivo? 
a.  Rastreie o status do pedido do Data Box no portal do Azure. Ao criar o pedido, você será solicitado a fornecer um email de notificação. Se você tiver fornecido um, você será notificado por email sobre todas as alterações de status do pedido. Para obter mais informações, consulte como [configurar emails de notificação](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>Q. Como devolver o dispositivo? 
a.  A Microsoft exibe uma etiqueta de remessa na tela de papel eletrônico. Se o rótulo de remessa não aparecer na tela de E-Ink, acesse **visão geral > baixar o rótulo de envio**. Baixe e imprima o rótulo, insira o rótulo na marca de plástico clara no dispositivo e solte o dispositivo no local da sua operadora de entrega. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>Q. Recebi uma notificação por email informando que meu dispositivo foi recebido no datacenter do Azure. Como fazer para descobrir se o upload de dados está em andamento?
a. Acesse seu pedido do Data Box no portal do Azure e acesse **Visão Geral**. Se o upload dos dados para o Azure tiver sido iniciado, você verá o progresso da cópia no painel direito. 

## <a name="migrate-data"></a>Migrar dados

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>Q. Qual é o tamanho máximo de dados que pode ser usado com o Data Box?  
a.  O Data Box tem uma capacidade de armazenamento utilizável de 80 TB. Você pode usar um único dispositivo Data Box para dados com tamanho que varia entre 40 e 80 TB. Para tamanhos de dados maiores de até 500 TB, você pode solicitar vários dispositivos Data Box. Para tamanhos de dados que excedem 500 TB, inscreva-se no Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>Q. Quais são os tamanhos máximos de blob de páginas e blob de blocos compatíveis com o Data Box? 
a.  Os tamanhos máximos são regidos pelos limites do Armazenamento do Azure. O blob de blocos máximo é de aproximadamente 4,768 TiB e o tamanho do blob de página máximo é 8 TiB. Para obter mais informações, confira [Escalabilidade e metas de desempenho do Armazenamento de Blobs](../storage/blobs/scalability-targets.md).

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. Como fazer para saber se meus dados estão protegidos durante o trânsito? 
a. Há vários recursos de segurança implementados para garantir que o Data Box esteja seguro durante o transporte. Alguns deles incluem selos com evidência de adulteração, detecção de adulteração de hardware e software e senha de desbloqueio de dispositivo. Para obter mais informações, acesse [Segurança e proteção de dados do Azure Data Box](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>Q. Como fazer para copiar os dados para o Data Box? 
a.  Se estiver usando um cliente SMB, você poderá usar uma ferramenta de cópia SMB, como `Robocopy` , `Diskboss` ou até mesmo o Windows File Explorer, arrastar e soltar para copiar dados para o dispositivo. 

Se estiver usando um cliente NFS, use [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) ou [Ultracopier](https://ultracopier.first-world.info/). 

Para obter mais informações, acesse [Tutorial: Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. Existem dicas para acelerar a cópia dos dados?
a.  Para acelerar o processo de cópia:

- Use diversos fluxos de cópia de dados. Por exemplo, com `Robocopy` , use a opção multi-threaded. Para obter mais informações sobre o comando exato usado, acesse [Tutorial: Copiar dados para o Azure Data Box e verificá-los](data-box-deploy-copy-data.md).
- Use várias sessões.
- Em vez de copiar um compartilhamento de rede (em que as velocidades de rede podem limitar a velocidade de cópia), armazene os dados localmente no computador ao qual o Data Box está conectado.
- Avalie o desempenho do computador usado para copiar os dados. Baixe e use a [ `Bluestop` `FIO` ferramenta](https://ci.appveyor.com/project/axboe/fio) para avaliar o desempenho do hardware do servidor. Selecione o build x86 ou x64 mais recente, selecione a guia **Artefatos** e baixe a MSI.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>Q. Posso usar várias contas de armazenamento com o Data Box?
a.  Sim. Um máximo de 10 contas de armazenamento, de uso geral, clássicas ou de armazenamento de blobs é compatível com o Data Box. Há suporte para o blob frequente e esporádico.


## <a name="ship-device"></a>Enviar dispositivo

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>Q. Meu dispositivo foi entregue, mas o dispositivo parece estar danificado. O que devo fazer?
a. Se o dispositivo tiver chegado danificado ou se houver evidências de violação, não use o dispositivo. [Entre em contato com suporte da Microsoft](data-box-disk-contact-microsoft-support.md) e retorne o dispositivo assim que possível. Crie também um pedido do Data Box para um dispositivo substituto. Nesse caso, você não será cobrado pelo dispositivo de substituição.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>Q. Posso coletar meu pedido do Data Box? Posso devolver o Data Box pela transportadora que eu escolher?
a. Sim. A Microsoft também oferece entregas autogerenciadas. Ao fazer o pedido do Data Box, você pode escolher a opção de envio autogerenciado. Para obter mais informações, consulte [entrega gerenciada automaticamente para data Box](data-box-portal-customer-managed-shipping.md).

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>Q. Meus dispositivos Data Box atravessarão fronteiras de países/regiões durante o envio?
a. Todos os dispositivos Data Box são enviados do mesmo país/região de destino e não cruzarão fronteiras internacionais. A única exceção é para pedidos na União Europeia (UE), onde os dispositivos podem ter origem ou destino de envio em qualquer país/região da UE. Isso se aplica aos dispositivos Data Box e Data Box Heavy.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>Q. Eu solicitei um Data Box no Leste dos EUA, mas recebi um dispositivo que foi enviado de um local no Oeste dos EUA. Para onde devo retornar o dispositivo?
a. Tentamos obter um dispositivo Data Box o mais rápido possível. Nós priorizamos a remessa de um datacenter mais próximo ao local da conta de armazenamento, mas enviaremos um dispositivo de qualquer datacenter do Azure que tenha estoque disponível. Seu Data Box deve ser retornado para o mesmo local de que foi enviado, conforme exibido na etiqueta de remessa.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>Q. A tela de papel eletrônico não está mostrando a etiqueta de remessa de devolução. O que devo fazer?
a. Se a exibição de E-Ink não mostrar o rótulo de remessa de retorno, execute as seguintes etapas:
- Remova a etiqueta de remessa antiga e os adesivos do envio anterior.
- Acesse seu pedido no portal do Azure. Acesse **Visão geral** e **Baixe a etiqueta de remessa**. Para obter mais informações, acesse [Baixar a etiqueta de remessa](data-box-portal-admin.md#download-shipping-label).
- Imprima a etiqueta de remessa e insira-a no compartimento de plástico transparente anexado ao dispositivo. 
- Verifique se a etiqueta de remessa está claramente visível. 

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. Como meus dados são protegidos durante o trânsito? 
a.  Durante o transporte, os recursos do Data Box a seguir ajudam a proteger os dados.
 - Os discos do Data Box são criptografados com a criptografia AES de 256 bits. 
 - O dispositivo está bloqueado e precisa de uma senha de desbloqueio para inserir e acessar dados.
Para obter mais informações, acesse [Recursos de segurança do Data Box](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-for-my-import-order-and-shut-down-the-device-can-i-still-add-more-data-to-the-data-box"></a>Q. Terminei de preparar para enviar para minha ordem de importação e desligar o dispositivo. Ainda posso adicionar mais dados à Data Box?
a. Sim. Você pode ligar o dispositivo e adicionar mais dados. Você precisará executar a etapa **Preparar para o envio** novamente após a cópia dos dados.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>Q. Recebi meu dispositivo e ele não está inicializando? Como posso enviar novamente o dispositivo?
a. Se o dispositivo não estiver inicializando, vá para o seu pedido na portal do Azure. Baixe um rótulo de envio e anexe-o ao dispositivo. Para obter mais informações, acesse [Baixar a etiqueta de remessa](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Verificar e carregar

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>Q. Quanto tempo demora até eu conseguir acessar meus dados no Azure depois que o Data Box é devolvido? 
a.  Depois que o status do pedido de **Cópia de Dados** for exibido como **Concluído**, você deverá conseguir acessar os dados imediatamente.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. Onde meus dados estão localizados no Azure após o carregamento?
a.  Quando você copia os dados para Data Box, dependendo se os dados são blob de blocos ou BLOB de páginas ou arquivos do Azure, os dados são carregados em um dos seguintes caminhos na sua conta de armazenamento do Azure:
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Como alternativa, você pode ir para sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. Acabei de ver que não segui os requisitos de nomenclatura do Azure para meus contêineres. Meus dados serão carregados no Azure?
a.  Se os nomes dos contêineres tiverem letras maiúsculas, esses nomes serão automaticamente convertidos em minúsculas. Se os nomes não estiverem em conformidade com outros requisitos (caracteres especiais, outros idiomas e assim por diante), o upload falhará. Para obter mais diretrizes para nomear compartilhamentos, contêineres e arquivos, acesse:
- [Nomenclatura e referência de compartilhamentos](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Convenções de blobs de blocos e blobs de páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>Q. Como fazer para verificar os dados que eu copiei para o Data Box?
a.  Depois que a cópia de dados for concluída, quando você executar **Preparação para o envio**, seus dados serão validados. O Data Box gera uma lista de arquivos e somas de verificação para os dados durante o processo de validação. Você pode baixar a lista de arquivos e verificá-la com relação aos arquivos nos dados de origem. Para obter mais informações, acesse [Preparar para o envio](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-return-the-data-box"></a>Q. O que acontece com meus dados depois de retornar o Data Box?
a.  Depois que a cópia de dados para o Azure for concluída, os dados do Data Box serão apagados com segurança, de acordo com as diretrizes NIST SP 800-88 Revisão 1. Para obter mais informações, acesse [Apagamento de dados do Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Relatório de auditoria

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Como o serviço Azure Data Box ajuda a dar suporte ao procedimento de cadeia de custódia dos clientes?
a.  O serviço Azure Data Box fornece nativamente relatórios que você pode usar para sua cadeia de documentação de custódia. Os logs de auditoria e cópia estão disponíveis em sua conta de armazenamento no Azure, e você pode [baixar o histórico de pedidos](data-box-portal-admin.md#download-order-history) no portal do Azure após a conclusão.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Que tipo de relatório está disponível para dar suporte à cadeia de custódia?
a.  Os seguintes relatórios estão disponíveis para dar suporte à cadeia de custódia:

- Logística de transporte da UPS.
- Log da ativação e do acesso de compartilhamento do usuário.
- O arquivo de manifesto ou BOM com uma verificação de redundância cíclica de 64 bits (CRC-64) ou soma de verificação para cada arquivo ingerido com êxito no Data Box.
- Relatório de arquivos que não conseguiram ser carregados na conta de armazenamento do Azure.
- Limpeza do dispositivo Data Box (de acordo com os padrões NIST 800 88R1) depois que os dados são copiados para sua conta de armazenamento do Azure.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Os logs de acompanhamento da transportadora (da UPS) estão disponíveis? 
a.  Os logs de acompanhamento da transportadora são capturados no histórico de pedidos do Data Box. Esse relatório estará disponível para você depois que o dispositivo retornar ao datacenter do Azure e os dados nos discos do dispositivo forem limpos. Se houver urgência, você pode acessar o site da transportadora com o número de rastreamento do pedido e obter as informações de rastreamento.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Posso transportar o Data Box para o datacenter do Azure? 
a.  Não. Se você tiver escolhido envio gerenciado da Microsoft, não poderá transportar os dados. Atualmente, o datacenter do Azure não aceita a entrega do Data Box de clientes ou de operadoras diferentes de UPS.

Se você escolher entrega autogerenciada, poderá pegar ou retirar sua Data Box do datacenter do Azure.


## <a name="next-steps"></a>Próximas etapas

- Reveja os [requisitos de sistema do Data Box](data-box-system-requirements.md).
- Entenda os [limites do Data Box](data-box-limits.md).
- Implante rapidamente o [Azure Data Box](data-box-quickstart-portal.md) no portal do Azure.