---
title: O que é o Azure Data Share?
description: Saiba mais sobre o compartilhamento de dados simples e seguro com vários clientes e parceiros usando o Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: overview
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: 3a7c73e4a5ba00155ab905f28edbcb0eb42c0539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724856"
---
# <a name="what-is-azure-data-share"></a>O que é o Azure Data Share?

No mundo de hoje, os dados são exibidos como um ativo estratégico essencial que muitas organizações precisam compartilhar de modo simples e seguro com seus clientes e parceiros. Há muitas maneiras de que os clientes fazerem isso hoje, incluindo por meio de FTP, email e APIs, para mencionar algumas. As organizações podem facilmente perder o controle de com quem elas o compartilharam seus dados. Compartilhar dados por meio de FTP ou a própria infraestrutura de API costuma ser caro de provisionar e administrar. Há sobrecarga de gerenciamento associada ao uso desses métodos de compartilhamento em grande escala. 

Muitas organizações precisam responsabilizar-se pelos dados compartilhados. Além da responsabilidade, muitas organizações gostariam de poder controlar, gerenciar e monitorar todo o compartilhamento de dados de uma maneira simples. No mundo de hoje, em que os dados devem continuar a crescer em um ritmo exponencial, as organizações precisam de uma maneira simples de compartilhar Big Data. Os clientes exigem os dados mais atualizados para garantir que possam obter insights em tempo hábil.

O Azure Data Share permite que as organizações compartilhem dados de modo simples e seguro com vários clientes e parceiros. Em apenas alguns cliques, você pode provisionar uma nova conta de compartilhamento de dados, adicionar conjuntos de dados e convidar seus clientes e parceiros para o compartilhamento de dados. Provedores de dados estão sempre no controle dos dados que compartilharam. O Azure Data Share facilita o gerenciamento e o monitoramento de quais dados foram compartilhados, quando e por quem. 

Um provedor de dados pode se manter no controle de como seus dados são tratados especificando termos de uso para seu compartilhamento de dados. O consumidor de dados deve aceitar esses termos antes de poder receber os dados. Os provedores de dados podem especificar a frequência com que seus consumidores de dados recebem atualizações. O acesso a novas atualizações pode ser revogado a qualquer momento pelo provedor de dados. 

O Azure Data Share ajuda a aprimorar os insights tornando fácil combinar dados de terceiros para enriquecer os cenários de IA e análise. Use com facilidade o poder das ferramentas de análise do Azure para preparar, processar e analisar os dados compartilhados usando o Azure Data Share. 

O provedor de dados e o consumidor de dados precisam ter uma assinatura do Azure para compartilhar e receber dados. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Cenários para o Azure Data Share

O Azure Data Share pode ser usado em vários setores diferentes. Por exemplo, um varejista talvez queira compartilhar dados recentes de ponto de vendas com seus fornecedores. Usando o Azure Data Share, um varejista pode configurar um compartilhamento de dados contendo dados do ponto de vendas para todos os fornecedores e compartilhar vendas por hora ou por dia. 

O Azure Data Share também pode ser usado para estabelecer um mercado de dados para um setor específico. Por exemplo, um governo ou uma instituição de pesquisa que compartilha regularmente dados anônimos sobre o crescimento da população com terceiros. 

Outro caso de uso do Azure Data Share é estabelecer um consórcio de dados. Por exemplo, várias instituições de pesquisa diferentes podem compartilhar dados com um único órgão confiável. Os dados são analisados, agregados ou processados usando ferramentas de análise do Azure e, em seguida, compartilhados com as partes interessadas. 

## <a name="how-it-works"></a>Como ele funciona

No momento, o Azure Data Share oferece compartilhamento baseado em instantâneo e compartilhamento in-loco. 

No compartilhamento baseada em instantâneo, os dados passam da assinatura do Azure do provedor de dados e chegam à assinatura do Azure do consumidor de dados. Como um provedor de dados, você provisiona um compartilhamento de dados e convida destinatários para ele. Os consumidores de dados recebem um convite para o compartilhamento de dados por email. Depois de aceitar o convite, um consumidor de dados pode disparar um instantâneo completo dos dados compartilhados com ele. Esses dados são recebidos na conta de armazenamento dos consumidores de dados. Os consumidores de dados podem receber atualizações regulares incrementais dos dados compartilhados com eles para que sempre tenham a versão mais recente dos dados. 

Os provedores de dados podem oferecer a seus consumidores de dados atualizações incrementais aos dados compartilhados com eles por meio de um agendamento de instantâneo. Agendas de instantâneo são oferecidas por hora ou por dia. Quando um consumidor de dados aceita e configura o compartilhamento de dados, ele pode assinar uma agenda de instantâneo. Isso é útil em cenários em que os dados compartilhados são atualizados regularmente e o consumidor de dados precisa dos dados mais atualizados. 

![fluxo de compartilhamento de dados](media/data-share-flow.png)

Quando um consumidor de dados aceita um compartilhamento de dados, ele é capaz de receber os dados em um armazenamento de dados de sua escolha. Por exemplo, se o provedor de dados compartilhar dados usando o Armazenamento de Blobs do Azure, o consumidor de dados poderá receber esses dados no Azure Data Lake Storage. Da mesma forma, se o provedor de dados compartilhar dados de um Azure Synapse Analytics (antigo SQL Data Warehouse), o consumidor de dados poderá escolher se deseja receber os dados em um Azure Data Lake Storage, em um Banco de Dados SQL do Azure ou em um Azure Synapse Analytics. No caso do compartilhamento de fontes baseadas em SQL, o consumidor de dados também pode escolher se deseja receber dados em parquet ou csv. 

Com o compartilhamento in-loco, os provedores de dados podem compartilhar dados onde eles residem sem copiar os dados. Depois que a relação de compartilhamento é estabelecida por meio do fluxo de convite, um link simbólico é criado entre o armazenamento de dados de origem do provedor de dados e o armazenamento de dados de destino do consumidor de dados. O consumidor de dados pode ler e consultar os dados em tempo real usando o próprio armazenamento de dados. As alterações no armazenamento de dados de origem estão disponíveis imediatamente para o consumidor de dados. O compartilhamento no local está disponível no momento para o Azure Data Explorer.

## <a name="key-capabilities"></a>Principais recursos

O Azure Data Share permite aos provedores de dados:

* Compartilhar dados da lista de [armazenamentos de dados compatíveis](supported-data-stores.md) com clientes e parceiros fora da sua organização

* Manter o controle de com quem você compartilhou seus dados

* Opção de instantâneo ou compartilhamento in-loco

* Com que frequência os consumidores de dados estão recebendo atualizações para seus dados

* Permitir que seus clientes efetuem pull da versão mais recente dos seus dados conforme necessário ou permitir que recebam automaticamente as alterações incrementais aos seus dados a um intervalo definido por você

O Azure Data Share permite aos consumidores de dados: 

* Exibir uma descrição do tipo de dados que estão sendo compartilhados

* Exibir os termos de uso para os dados

* Aceitar ou rejeitar um convite do Azure Data Share

* Aceite dados compartilhados com você em um [armazenamento de dados compatível](supported-data-stores.md).

* Acessar dados no local ou disparar um instantâneo completo ou incremental de dados compartilhados

Todas as principais funcionalidades listadas acima têm suporte por meio do Azure ou das APIs REST. Para obter mais detalhes sobre como usar o Azure Data Share por meio de APIs REST, confira nossa documentação de referência. 

## <a name="supported-regions"></a>Regiões com suporte

Para obter uma lista de regiões do Azure que disponibilizam o Azure Data Share, confira a página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) e pesquise pelo Azure Data Share. 

Para metadados armazenados pelo Azure Data Share, no Sudeste da Ásia (Singapura), ele é armazenado dentro da região e para todas as outras regiões com suporte, ele é armazenado na área geográfica. O Azure Data Share não armazena, ele próprio, uma cópia dos dados compartilhados. Os dados são armazenados no armazenamento de dados subjacente que está sendo compartilhado. Por exemplo, se um provedor de dados armazena dados em uma conta do Azure Data Lake Storage localizada no Oeste dos EUA, é lá que os dados são armazenados. Se ele estiver compartilhando dados com uma conta de Armazenamento do Azure localizada no Oeste da Europa por meio de instantâneo, normalmente os dados serão transferidos diretamente para a conta de Armazenamento do Azure no Oeste da Europa. 

O serviço do Azure Data Share não precisa estar disponível em sua região para aproveitar o serviço. Por exemplo, se você tiver dados armazenados em uma conta de Armazenamento do Azure localizada em uma região em que o Azure Data Share ainda não está disponível, ainda poderá aproveitar o serviço para compartilhar seus dados. 

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).
