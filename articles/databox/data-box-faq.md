---
title: Perguntas frequentes sobre o Microsoft Azure Data Box | Microsoft Docs nos dados
description: Contém perguntas frequentes e respostas sobre o Azure Data Box, uma solução de nuvem que permite transferir grandes quantidades de dados para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 8f090a3f07eba529fd39976545eb654083e637eb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561404"
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

### <a name="q-what-is-azure-data-box-service"></a>P. O que é o serviço Azure Data Box? 
a.  O serviço Azure Data Box foi criado para a ingestão de dados offline. Esse serviço gerencia uma variedade de produtos de diferentes capacidades de armazenamento, todas adaptadas para o transporte de dados. 

### <a name="q-what-is-azure-data-box"></a>P. O que é o Azure Data Box?
a. O Azure Data Box permite uma transferência de terabytes de dados rápida, econômica e segura para o Azure. Você solicita o dispositivo Data Box pelo portal do Azure. A Microsoft envia um dispositivo de armazenamento com capacidade utilizável de 80 TB por meio de uma transportadora regional. 

Depois que o dispositivo é recebido, você configura-o rapidamente usando a interface do usuário da Web local. Copie os dados de seus servidores para o dispositivo e envie o dispositivo novamente para o Azure. No datacenter do Azure, seus dados são carregados automaticamente do dispositivo para o Azure. Todo o processo é acompanhado de ponta a ponta pelo serviço Data Box no portal do Azure.

### <a name="q-when-should-i-use-data-box"></a>P. Quando devo usar o Data Box?
a. Se você tiver de 40 a 500 TB de dados que deseja transferir para o Azure, o Data Box será uma boa opção. Para tamanhos de dados < 40 TB, use o Data Box Disk e, para tamanhos de dados > 500 TB, inscreva-se no [Data Box Heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>P. Qual é o preço do Data Box?
a. O Data Box está disponível por um encargo nominal pelo perído de 10 dias. Quando você seleciona o modelo do produto durante a criação de um pedido no portal do Azure, os encargos do dispositivo são exibidos. O envio também é gratuito; no entanto, os encargos do armazenamento do Azure se aplicam. Para obter mais informações, acesse [Preços do Azure Data Box](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>P. Qual é a quantidade máxima de dados que posso transferir com o Data Box em uma instância?
a. O Data Box tem uma capacidade bruta de 100 TB e uma capacidade utilizável de 80 TB. Você pode transferir até 80 TB de dados com o Data Box. Para transferir mais dados, você precisa solicitar mais dispositivos.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>P. Como posso verificar se o Data Box está disponível em minha região? 
a.  Para obter informações sobre em quais países/regiões o Data Box está disponível, acesse [Disponibilidade de regiões](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>P. Em quais regiões posso armazenar dados com o Data Box?
a. O Data Box é compatível com todas as regiões nos EUA, na Europa Ocidental, na Europa Setentrional, na França, no Reino Unido, no Japão, na Austrália e no Canadá. Para saber mais, acesse [Disponibilidade por região](data-box-overview.md#region-availability).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>P. Quem deverei contatar se eu tiver problemas com o Data Box?
a. Caso tenha problemas com o Data Box, [contate o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>P. Perdi meu Data Box. Há um encargo de dispositivo perdido?
a. Sim. Há um encargo de dispositivo perdido ou danificado. Essa cobrança é abordada na [Página de Preço](https://azure.microsoft.com/pricing/details/storage/databox/), bem como nos [Termos de Serviço do Produto](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Solicitar dispositivo

### <a name="q-how-do-i-get-data-box"></a>P. Como fazer para obter o Data Box? 
a.  Para obter o Azure Data Box, entre no portal do Azure e crie um pedido do Data Box. Forneça detalhes de notificação e informações de contato. Depois de fazer um pedido, com base na disponibilidade, o Data Box será enviado para você no prazo de 10 dias. Para obter mais informações, acesse [Solicitar um Data Box](data-box-deploy-ordered.md).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>P. Não consegui criar um pedido do Data Box no portal do Azure. Por que isso ocorre?
a. Se você não conseguiu criar um pedido do Data Box, há um problema com seu tipo de assinatura ou acesso. 

Verifique sua assinatura. O Data Box só está disponível para as ofertas de assinatura EA (Contrato Enterprise) e CSP (Provedor de Soluções na Nuvem). Caso sua assinatura não se enquadre em nenhum dos tipos acima, contate o Suporte da Microsoft para atualizá-la.

Se você tiver um tipo de oferta compatível com a assinatura, verifique o nível de acesso da assinatura. Você precisa ser um colaborador ou proprietário em sua assinatura para criar um pedido.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>P. Solicitei alguns dispositivos Data Box. Não consigo criar nenhum outro pedido. Por que isso ocorre?
a. Permitimos um máximo de cinco pedidos ativos por assinatura e limite de comércio (combinação de país e a região selecionada). Caso precise solicitar um dispositivo adicional, contate o Suporte da Microsoft para aumentar o limite de sua assinatura.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>P. Quando tento criar um pedido, recebo uma notificação de que o serviço Data Box não está disponível. O que isso significa?
a. Isso significa que o serviço Data Box não está disponível para a combinação de país e região selecionada. A alteração dessa combinação provavelmente permitirá que você utilize o serviço Data Box. Para obter uma lista das regiões nas quais o serviço está disponível, acesse [Disponibilidade de regiões para o Data Box](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>P. Fiz meu pedido do Data Box há alguns dias. Quando receberei meu Data Box?
a. Quando você faz um pedido, verificamos se um dispositivo está disponível para seu pedido. Se um dispositivo estiver disponível, nós o enviaremos em 10 dias. É concebível que há períodos de alta demanda. Nessa situação, seu pedido será colocado na fila e você poderá acompanhar a alteração de status no portal do Azure. Caso seu pedido não seja atendido em 90 dias, ele será cancelado automaticamente.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>P. Preenchi meu Data Box com os Dados e preciso solicitar outro. Há uma maneira de fazer o pedido rapidamente?
a. Você pode clonar o pedido anterior. A clonagem cria o mesmo pedido de antes e permite que você edite somente os detalhes do pedido, sem a necessidade de digitar detalhes de endereço, contato e notificação.

## <a name="configure-and-connect"></a>Configuração e conexão

### <a name="q-how-do-i-unlock-the-data-box"></a>P. Como fazer para desbloquear o Data Box? 
a.  No portal do Azure, acesse seu pedido do Data Box e navegue para **Detalhes do dispositivo**. Copie a senha de desbloqueio. Use essa senha para fazer logon na interface do usuário da Web local do Data Box. Para obter mais informações, acesse [Tutorial: Desempacotar, cabear e conectar o Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>P. Posso usar um computador host Linux para me conectar aos dados e copiá-los para o Data Box?
a.  Sim. Você pode usar o Data Box para se conectar aos clientes SMB e NFS. Para obter mais informações, vá para a lista de [sistemas operacionais com suporte](data-box-system-requirements.md) de seu computador host.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>P. Meu Data Box já foi expedido, mas agora desejo cancelar o pedido. Por que o botão Cancelar não está disponível?
a.  Você pode cancelar o pedido somente depois que o Data Box é solicitado e antes que o pedido é processado. Depois que o pedido do Data Box for processado, não será mais possível cancelá-lo. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>P. Posso conectar um Data Box ao mesmo tempo a vários computadores de host para transferir dados?
a. Sim. Vários computadores host podem se conectar ao Data Box para transferir dados e vários trabalhos de cópia podem ser executados em paralelo. Para obter mais informações, acesse [Tutorial: Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>P. Posso me conectar a ambas as interfaces de 10 GbE no Data Box para transferir dados?
a. Sim. Ambas as interfaces de 10 GbE podem ser conectadas no Data Box para copiar dados ao mesmo tempo. Para obter mais informações sobre como copiar dados, vá para [Tutorial: Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>P. O LED indicador de falha do sistema no painel de operação frontal está ativado. O que devo fazer?
a. Se o LED indicador de falha do sistema estiver aceso, ele indicará que o sistema não está íntegro. [Contate o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md) para as próximas etapas.

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>P. Não consigo acessar a senha de desbloqueio do Data Box no portal do Azure. Por que isso ocorre?
a. Se você não puder acessar a senha de desbloqueio no portal do Azure, verifique as permissões em sua assinatura e na conta de armazenamento. Verifique se você tem permissão de colaborador ou proprietário no nível do grupo de recursos. Caso contrário, você precisa ter, pelo menos, a permissão de função Operador do Data Box para ver as credenciais de acesso.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>P. Há suporte para configuração de canal de porta no Data Box? E quanto ao MPIO?
a. Não damos suporte à configuração de canal de porta, à configuração de MPIO (Multipath I/O) nem à configuração de VLAN no Data Box.

## <a name="track-status"></a>Rastreamento de status

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>P. Como fazer para rastrear o Data Box desde a realização do pedido até a devolução do dispositivo? 
a.  Rastreie o status do pedido do Data Box no portal do Azure. Ao criar o pedido, é necessário também fornecer um email para notificações. Através desse email você será notificado sobre todas as alterações de status do pedido. Para obter mais informações, consulte como [configurar emails de notificação](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>P. Como devolver o dispositivo? 
a.  A Microsoft exibe uma etiqueta de remessa na tela de papel eletrônico. Se a etiqueta de remessa não aparecer na tela de papel eletrônico, acesse **Visão geral > Baixar etiqueta de remessa**. Baixe e imprima a etiqueta, insira-a no compartimento de plástico transparente no dispositivo e devolva o dispositivo para a transportadora. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>P. Recebi uma notificação por email informando que meu dispositivo foi recebido no datacenter do Azure. Como fazer para descobrir se o upload de dados está em andamento?
a. Acesse seu pedido do Data Box no portal do Azure e acesse **Visão Geral**. Se o upload dos dados para o Azure tiver sido iniciado, você verá o progresso da cópia no painel direito. 

## <a name="migrate-data"></a>Migrar dados

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>P. Qual é o tamanho máximo de dados que pode ser usado com o Data Box?  
a.  O Data Box tem uma capacidade de armazenamento utilizável de 80 TB. Você pode usar um único dispositivo Data Box para dados com tamanho que varia entre 40 e 80 TB. Para tamanhos de dados maiores de até 500 TB, você pode solicitar vários dispositivos Data Box. Para tamanhos de dados que excedem 500 TB, inscreva-se no Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>P. Quais são os tamanhos máximos de blob de páginas e blob de blocos compatíveis com o Data Box? 
a.  Os tamanhos máximos são regidos pelos limites do Armazenamento do Azure. O blob de blocos máximo é de aproximadamente 4,768 TiB e o tamanho do blob de página máximo é 8 TiB. Para obter mais informações, vá para [Metas de desempenho e escalabilidade do Armazenamento do Azure](../storage/common/storage-scalability-targets.md). 

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>P. Como fazer para saber se meus dados estão protegidos durante o trânsito? 
a. Há vários recursos de segurança implementados para garantir que o Data Box esteja seguro durante o transporte. Alguns deles incluem selos com evidência de adulteração, detecção de adulteração de hardware e software e senha de desbloqueio de dispositivo. Para obter mais informações, acesse [Segurança e proteção de dados do Azure Data Box](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>P. Como fazer para copiar os dados para o Data Box? 
a.  Se estiver usando um cliente SMB, use uma ferramenta de cópia SMB, como o Robocopy, o Diskboss ou até mesmo a operação do tipo "arrastar e soltar" do Explorador de Arquivos do Windows, para copiar os dados para o dispositivo. 

Se estiver usando um cliente NFS, use [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) ou [Ultracopier](https://ultracopier.first-world.info/). 

Para obter mais informações, acesse [Tutorial: Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>P. Existem dicas para acelerar a cópia dos dados?
a.  Para acelerar o processo de cópia:

- Use diversos fluxos de cópia de dados. Por exemplo, com o Robocopy, use a opção de vários threads. Para obter mais informações sobre o comando exato usado, acesse [Tutorial: Copiar dados para o Azure Data Box e verificá-los](data-box-deploy-copy-data.md).
- Use várias sessões.
- Em vez de fazer a cópia pelo compartilhamento de rede (no qual você pode estar limitado pelas velocidades da rede), garanta que os dados residam localmente no computador ao qual o Data Box está conectado.
- Avalie o desempenho do computador usado para copiar os dados. Baixe e use a [ferramenta FIO Bluestop](https://bluestop.org/fio/) para avaliar o desempenho do hardware do servidor.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>P. Posso usar várias contas de armazenamento com o Data Box?
a.  Sim. Um máximo de 10 contas de armazenamento, de uso geral, clássicas ou de armazenamento de blobs é compatível com o Data Box. Há suporte para o blob frequente e esporádico. 


## <a name="ship-device"></a>Enviar dispositivo

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>P. Meu dispositivo foi entregue, mas parece estar danificado. O que devo fazer?
a. Se o dispositivo chegou danificado ou se há evidência de adulteração, não use o dispositivo. [Contate o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md) e devolva o dispositivo assim que possível. Crie também um pedido do Data Box para um dispositivo substituto. Nesse caso, você não será cobrado pelo dispositivo substituto.

### <a name="q-can-i-use-my-own-shipping-carrier-to-ship-data-box"></a>P. Posso usar minha própria transportadora para enviar o Data Box?
a. Para o serviço Data Box, a Microsoft cuida do envio entre o datacenter do Azure. Caso deseje usar sua própria transportadora, use o serviço de Importação/Exportação do Azure. Para obter mais informações, acesse [O que é o serviço de Importação/Exportação do Azure?](../storage/common/storage-import-export-service.md)

### <a name="q-will-my-data-box-devices-cross-country-borders-during-shipping"></a>P. Meus dispositivos Data Box atravessarão fronteiras de países durante o envio?
a. Todos os dispositivos Data Box são enviados do mesmo país de destino e não cruzarão fronteiras internacionais. A única exceção é para pedidos na União Europeia (UE), onde os dispositivos podem ser enviados de e para qualquer país da UE. Isso se aplica aos dispositivos Data Box e Data Box Heavy.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>P. A tela de papel eletrônico não está mostrando a etiqueta de remessa de devolução. O que devo fazer?
a. Se a tela de papel eletrônico não mostrar a etiqueta de remessa de devolução, execute as seguintes etapas:
- Remova a etiqueta de remessa antiga e os adesivos do envio anterior.
- Acesse seu pedido no portal do Azure. Acesse **Visão geral** e **Baixe a etiqueta de remessa**. Para obter mais informações, acesse [Baixar a etiqueta de remessa](data-box-portal-admin.md#download-shipping-label).
- Imprima a etiqueta de remessa e insira-a no compartimento de plástico transparente anexado ao dispositivo. 
- Verifique se a etiqueta de remessa está claramente visível. 

### <a name="q-how-is-my-data-protected-during-transit"></a>P. Como meus dados são protegidos durante o trânsito? 
a.  Durante o transporte, os recursos do Data Box a seguir ajudam a proteger os dados.
 - Os discos do Data Box são criptografados com a criptografia AES de 256 bits. 
 - O dispositivo está bloqueado e precisa de uma senha de desbloqueio para inserir e acessar dados.
Para obter mais informações, acesse [Recursos de segurança do Data Box](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>P. Concluí a etapa Preparar para o envio e desliguei o dispositivo. Ainda posso adicionar mais dados ao Data Box?
a. Sim. Você pode ligar o dispositivo e adicionar mais dados. Você precisará executar a etapa **Preparar para o envio** novamente após a cópia dos dados.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>P. Recebi meu dispositivo e ele não está inicializando? Como posso enviar novamente o dispositivo?
a. Se seu dispositivo não estiver inicializando, acesse seu pedido no portal do Azure. Baixe uma etiqueta de remessa e afixe-a no dispositivo. Para obter mais informações, acesse [Baixar a etiqueta de remessa](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Verificar e carregar

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>P. Quanto tempo demora até eu conseguir acessar meus dados no Azure depois que o Data Box é devolvido? 
a.  Depois que o status do pedido de **Cópia de Dados** for exibido como **Concluído**, você deverá conseguir acessar os dados imediatamente.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>P. Onde meus dados estão localizados no Azure após o carregamento?
a.  Quando você copia os dados para o Data Box, independentemente se os dados são um blob de blocos, um blob de páginas ou arquivos do Azure, eles são carregados em um dos caminhos a seguir em sua conta de Armazenamento do Azure.
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Como alternativa, você pode acessar sua conta de Armazenamento do Azure no portal do Azure e navegar de lá.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>P. Acabei de ver que não segui os requisitos de nomenclatura do Azure para meus contêineres. Meus dados serão carregados no Azure?
a.  Se os nomes de contêiner tiverem uma letra maiúscula, esses nomes serão automaticamente convertidos em minúsculas. Se os nomes não estiverem em conformidade com outros requisitos (caracteres especiais, outros idiomas e assim por diante), o upload falhará. Para obter mais informações sobre as melhores práticas para nomear compartilhamentos, contêineres e arquivos, acesse:
- [Nomenclatura e referência de compartilhamentos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Convenções de blobs de blocos e blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>P. Como fazer para verificar os dados que eu copiei para o Data Box?
a.  Depois que a cópia de dados for concluída, quando você executar **Preparação para o envio**, seus dados serão validados. O Data Box gera uma lista de arquivos e somas de verificação para os dados durante o processo de validação. Você pode baixar a lista de arquivos e verificá-la com relação aos arquivos nos dados de origem. Para obter mais informações, acesse [Preparar para o envio](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>P. O que acontecerá com meus dados depois que eu devolver o Data Box?
a.  Depois que a cópia de dados para o Azure for concluída, os dados do Data Box serão apagados com segurança, de acordo com as diretrizes NIST SP 800-88 Revisão 1. Para obter mais informações, acesse [Apagamento de dados do Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Relatório de auditoria

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Como o serviço Azure Data Box ajuda a dar suporte ao procedimento de cadeia de custódia dos clientes?
a.  O serviço Azure Data Box fornece nativamente relatórios que você pode usar para sua cadeia de documentação de custódia. Os logs de auditoria e cópia estão disponíveis em sua conta de armazenamento no Azure, e você pode [baixar o histórico de pedidos](data-box-portal-admin.md#download-order-history) no portal do Azure após a conclusão.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Que tipo de relatório está disponível para dar suporte à cadeia de custódia?
a.  Os seguintes relatórios estão disponíveis para dar suporte à cadeia de custódia:

- Logística de transporte da UPS.
- Log da ativação e do acesso de compartilhamento do usuário.
- Arquivo de manifesto com uma CRC-64 (verificação de redundância cíclica de 64 bits) ou uma soma de verificação para cada arquivo ingerido com êxito no Data Box.
- Relatório de arquivos que não conseguiram ser carregados na conta de armazenamento do Azure.
- Limpeza do dispositivo Data Box (de acordo com os padrões NIST 800 88R1) depois que os dados são copiados para sua conta de armazenamento do Azure.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Os logs de acompanhamento da transportadora (da UPS) estão disponíveis? 
a.  Os logs de acompanhamento da transportadora são capturados no histórico de pedidos do Data Box. Esse relatório estará disponível para você depois que o dispositivo retornar ao datacenter do Azure e os dados nos discos do dispositivo forem limpos. Para uma necessidade imediata, acesse também o site da transportadora diretamente com o número de acompanhamento do pedido e obtenha as informações de rastreamento.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Posso transportar o Data Box para o datacenter do Azure? 
a.  Não. No momento, o datacenter do Azure não aceita a entrega do Data Box de clientes ou de transportadoras além da UPS.


## <a name="next-steps"></a>Próximas etapas

- Reveja os [requisitos de sistema do Data Box](data-box-system-requirements.md).
- Entenda os [limites do Data Box](data-box-limits.md).
- Implante rapidamente o [Azure Data Box](data-box-quickstart-portal.md) no portal do Azure.
