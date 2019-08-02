---
title: Visão geral do Microsoft Azure Data Box Heavy | Microsoft Docs nos dados
description: Descreve o Azure Data Box, uma solução híbrida que permite a transferência de grandes quantidades de dados para o Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 0f4657cdd71a104ca111f62a6e9757b5a33b46e8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592312"
---
# <a name="what-is-azure-data-box-heavy"></a>O que é o Azure Data Box Heavy?

O Azure Data Box Heavy permite enviar centenas de terabytes de dados para o Microsoft Azure de forma rápida, econômica e confiável. Os dados são transferidos para o Microsoft Azure enviando um dispositivo Data Box Heavy com capacidade de armazenamento de 1 PB, com o qual você preenche seus dados e envia à Microsoft. O dispositivo tem uma caixa reforçada para proteger os dados durante o transporte.

Quando receber o dispositivo em seu datacenter, use a IU da Web local para configurá-lo. Copie os dados de seus servidores para o dispositivo e envie o dispositivo novamente para o Azure. No datacenter do Azure, os dados são carregados em suas contas de Armazenamento do Microsoft Azure. É possível controlar o processo de ponta a ponta no portal do Azure.


> [!IMPORTANT]
> - Para solicitar um dispositivo, inscreva-se no [Portal do Azure](https://portal.azure.com).


## <a name="use-cases"></a>Casos de uso

O Data Box Heavy é ideal para tamanhos de dados com centenas de terabytes, em que a conectividade de rede é insuficiente para carregar os dados no Microsoft Azure. A movimentação de dados pode ser única, periódica ou uma transferência de dados em massa inicial seguida por transferências periódicas. Veja a seguir os vários cenários em que o Data Box Heavy pode ser usado para a transferência de dados.

 - **Migração única** - quando uma grande quantidade de dados local é transferida para o Azure.
     - Mova uma biblioteca de mídia de fitas offline para o Microsoft Azure para criar uma biblioteca de mídia online.
     - Migre o farm da VM, o SQL Server e os aplicativos para o Microsoft Azure.
     - Mova os dados históricos para o Microsoft Azure para fazer uma análise detalhada e gerar relatórios usando o HDInsight.

 - **Transferência em massa inicial** – quando uma transferência em massa inicial é feita usando o Data Box Heavy (semente) seguida por transferências incrementais pela rede.
     - Por exemplo, os parceiros de soluções de backup e o Data Box Heavy são usados para mover o backup histórico grande inicial para o Microsoft Azure. Após a conclusão, os dados incrementais são transferidos pela rede para o armazenamento do Azure.

 - **Carregamentos periódicos** - quando grandes quantidades de dados são geradas periodicamente e precisam ser movidas para o Azure. Por exemplo, na exploração de energia, em que o conteúdo de vídeo é gerado em plataformas de petróleo e em produções de energia eólica.

## <a name="benefits"></a>Benefícios

O Data Box Heavy foi projetado para mover grandes quantidades de dados para o Microsoft Azure com pouco ou nenhum impacto sobre a rede. A solução oferece as seguintes vantagens:

- **Velocidade** – o Data Box Heavy usa adaptadores de rede de 40 Gbps de alto desempenho.

- **Segurança** – o Data Box Heavy tem proteções de segurança internas para o dispositivo, os dados e o serviço.
    - O dispositivo tem uma caixa reforçada protegida por parafusos resistentes a adulteração e adesivos com evidência de adulteração.
    - Os dados do dispositivo são sempre protegidos por uma criptografia AES de 256 bits.
    - O dispositivo só pode ser desbloqueado com uma senha fornecida no portal do Azure.
    - O serviço é protegido pelos recursos de segurança do Azure.
    - Após carregar os dados no Microsoft Azure, os discos do dispositivo são apagados, de acordo com as normas 800-88r1 do NIST (National Institute of Standards and Technology).


## <a name="features-and-specifications"></a>Recursos e especificações

O dispositivo Data Box Heavy tem os seguintes recursos nesta versão.

| Especificações                                          | DESCRIÇÃO              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | Aproximadamente 500 lbs. <br>Dispositivo nas rodas para transporte|
| Dimensões                                              | Largura: 66,04 cm de altura: 71,12 cm de comprimento: 121,92 cm |
| Espaço em rack                                              | Não pode ser montado em rack|
| Cabos necessários                                         | Inclui 4 cabos de alimentação 120 V/10 A de aterramento (NEMA 5-15) <br> O dispositivo tem suporte para até 240 V de potência e conectores C-13 <br> Usar cabos de rede compatíveis com [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Potência                                                    | 4 unidades de fonte de energia internas compartilhadas nos dois nós do dispositivo <br> Consumo de energia típico de 1.200 watts|
| Capacidade de armazenamento                                        | Aproximadamente 70 discos de 1 PB brutos com 14 TB cada <br> 770 TB de capacidade utilizável|
| Número de nós                                          | 2 nós independentes por dispositivo (500 TB cada) |
| Adaptadores de rede por nó                             | 4 adaptadores de rede por nó <br><br> MGMT, DATA3 <ul><li> 2 interfaces 1 GbE </li><li> MGMT serve para gerenciamento e configuração inicial, não é configurável pelo usuário </li><li> DATA3 é um protocolo DHCP configurável pelo usuário por padrão</li><li>Os adaptadores de rede 1 GbE também podem ser configurados como as interfaces 10 GbE</li></ul>Interfaces de dados DATA1, DATA2 <ul><li>2 interfaces 40 GbE </li><li> Usuário configurável para DHCP (padrão) ou estático</li></ul>|


## <a name="components"></a>Componentes

O Data Box Heavy inclui os seguintes componentes:

* **Dispositivo Data Box Heavy** – um dispositivo físico com uma parte externa reforçada que armazena os dados com segurança. Este dispositivo tem uma capacidade de armazenamento utilizável de 770 TB.
    
* **Serviço Data Box** – uma extensão do portal do Azure que permite gerenciar um dispositivo Data Box Heavy por meio uma interface da Web que você pode acessar em diferentes locais geográficos. Use o serviço do Data Box para administrar o dispositivo Data Box Heavy. As tarefas do serviço incluem como criar e gerenciar pedidos, exibir e gerenciar alertas e gerenciar compartilhamentos.  

* **Interface do usuário da Web local** – uma interface do usuário baseada na Web usada para configurar o dispositivo, de modo que ele se conecte à rede local e, em seguida, registrá-lo no serviço Data Box. Use também a interface do usuário da Web local para desligar e reiniciar o dispositivo, exibir logs de cópia e contatar o Suporte da Microsoft para arquivar uma solicitação de serviço.


## <a name="the-workflow"></a>O fluxo de trabalho

Um fluxo de trabalho típico inclui as seguintes etapas:

1. **Ordem** - Crie um pedido no Portal do Azure, forneça informações de envio e a conta de armazenamento de destino do Azure para seus dados. Se o dispositivo estiver disponível, o Azure preparará e enviará o dispositivo com uma ID de acompanhamento de remessa.

2. **Recebimento** – após a entrega do dispositivo, conecte o dispositivo à rede e à rede elétrica usando os cabos especificados. Ative e conecte-se ao dispositivo. Configure a rede do dispositivo e monte os compartilhamentos no computador host do qual deseja copiar os dados.

3. **Cópia dos dados** – copie os dados para os compartilhamentos do Data Box Heavy.

4. **Devolução** – prepare, desligue e envie o dispositivo novamente para o datacenter do Azure.

5. **Upload** – os dados são copiados automaticamente do dispositivo para o Azure. Os discos do dispositivo são apagados com segurança, de acordo com as diretrizes do NIST (National Institute of Standards and Technology).

Ao longo deste processo, você receberá uma notificação por email sobre todas as alterações de status.

## <a name="region-availability"></a>Disponibilidade de região

O Data Box Heavy pode transferir dados de acordo com a região na qual o serviço foi implantado, o país ou a região aos quais o dispositivo é enviado e a conta de Armazenamento do Microsoft Azure de destino usada para a transferência dos dados.

- **Disponibilidade do serviço** – para esta versão, o Data Box Heavy está disponível nas seguintes regiões:
    - Todas as regiões da nuvem pública nos Estados Unidos – Centro-oeste dos EUA, Oeste dos EUA 2, Oeste dos EUA, Centro-Sul dos EUA, Centro dos EUA, Centro-Norte dos EUA, Leste dos EUA e Leste dos EUA 2.
    - União Europeia – Europa Ocidental e Europa Setentrional.
    - Reino Unido – Sul do Reino Unido e Oeste do Reino Unido.
    - França – França Central e Sul da França.

- **Contas de Armazenamento de Destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure nas quais o serviço está disponível.

Para obter informações mais atualizadas sobre a disponibilidade de região do Data Box Heavy, vá até a página de [Produtos do Microsoft Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Inscrição

Para se inscrever no Data Box Heavy, faça o seguinte:

1. Entre no portal do Azure em: https://portal.azure.com.
2. Clique em **+ Criar um recurso** para criar um novo recurso. Pesquise **Azure Data Box**. Selecione o serviço **Azure Data Box**.
3. Clique em **Criar**.
4. Escolha a assinatura que deseja usar para o Data Box Heavy. Selecione a região na qual você deseja implantar o recurso Data Box Heavy. Na opção **Data Box Heavy**, clique em **Inscrever-se**.
5. Responda às perguntas sobre país/região de residência dos dados, período, serviço de destino do Microsoft Azure para transferência de dados, largura de banda de rede e frequência da transferência de dados. Examine a Privacidade e os termos e marque a caixa de seleção A Microsoft pode usar seu endereço de email para contatá-lo.

Após se inscrever, você poderá solicitar um Data Box Heavy.

    
