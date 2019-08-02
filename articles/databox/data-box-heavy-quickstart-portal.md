---
title: Início Rápido do Microsoft Azure Data Box Heavy | Microsoft Docs
description: Saiba como implantar rapidamente o Azure Data Box Heavy no portal do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: alkohli
ms.openlocfilehash: 3467b25c085fb86d4aed3918d5446d118f76ffb8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446727"
---
# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Início Rápido: Implantar o Azure Data Box Heavy usando o portal do Azure

Este início rápido descreve como implantar o Azure Data Box Heavy usando o portal do Azure. As etapas incluem como cabear, configurar e copiar dados para o Data Box Heavy de modo que eles sejam carregados no Azure. O início rápido é executado no portal do Azure e na interface do usuário da Web local do dispositivo.

Para obter a implantação detalhada passo a passo e instruções de acompanhamento, acesse [Tutorial: Solicitar o Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Pré-requisitos

Preencha os seguintes pré-requisitos de configuração para o site de instalação, serviço e dispositivo do Data Box antes de implantar o dispositivo.

### <a name="for-installation-site"></a>Para o site de instalação

Antes de começar, verifique se:

- O dispositivo pode ser ajustado por meio de todas as suas entradas. As dimensões do dispositivo são: largura: 26” comprimento: 48” altura: 28”.
- Se o dispositivo estiver instalado em um andar que não seja o térreo, o acesso ao dispositivo terá que ser feito por um elevador ou uma rampa.
- Você tem duas pessoas para tratar do dispositivo. O dispositivo pesa aproximadamente 500 lbs. e vem com rodas.
- Você tem um site simples no datacenter com proximidade a uma conexão de rede disponível que possa acomodar um dispositivo com esse volume.

### <a name="for-service"></a>Para o serviço

Antes de começar, verifique se:

- Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.
- A assinatura usada no serviço do Data Box é [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Programa do CSP (Provedor de Soluções na Nuvem)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Você tem acesso de proprietário ou colaborador à assinatura para criar um pedido do Data Box Heavy.

### <a name="for-device"></a>Para dispositivo

Antes de começar, verifique se:

- Você examinou as [diretrizes de segurança do Data Box Heavy](data-box-safety.md).
- Você tem um computador host conectado à rede do datacenter. O Data Box Heavy copiará os dados deste computador. O computador host precisa executar um [sistema operacional compatível](data-box-heavy-system-requirements.md).
- Você tem um laptop com um cabo RJ-45 para se conectar à IU local e configurar o dispositivo. Use o laptop para configurar cada nó do dispositivo uma vez.
- Seu datacenter tem rede de alta velocidade e você tem, pelo menos, uma conexão de 10 GbE.
- Você precisa de um cabo de 10 ou 40 Gbps por nó do dispositivo. Escolha cabos que sejam compatíveis com o adaptador de rede Mellanox MCX314A-BCCT:
    - Para o cabo de 40 Gbps, a extremidade do dispositivo do cabo precisa ser QSFP+.
    - Para o cabo de 10 Gbps, você precisará de um cabo SFP+ que se conecta a um comutador de 10 G em uma extremidade, com um adaptador QSFP+ para SFP+ (ou o adaptador QSA) para a extremidade que se conecta ao dispositivo.
- Os cabos de alimentação são incluídos em uma bandeja na parte posterior do dispositivo.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Classificar

Esta etapa leva aproximadamente 5 minutos.

1. Crie um recurso do Azure Data Box no portal do Azure.
2. Selecione uma assinatura existente habilitada para esse serviço e escolha o tipo de transferência como **Importação**. Forneça o **País de origem** onde os dados residem e a **região de destino do Azure** para a transferência de dados.
3. Escolha **Data Box Heavy**. A capacidade utilizável máxima é de 770 TB e você pode criar vários pedidos para tamanhos maiores de dados.
4. Insira os detalhes do pedido e as informações de envio. Se o serviço estiver disponível em sua região, forneça endereços de email de notificação, examine o resumo e, em seguida, crie o pedido.

Depois que o pedido for criado, o dispositivo estará preparado para remessa.

## <a name="cable-for-power"></a>Cabear o recebimento de energia

Essa etapa leva aproximadamente cinco minutos.

Quando você receber o Data Box Heavy, execute as etapas a seguir para cabear o dispositivo para conectar e ativar o dispositivo.

1. Se houver alguma evidência de que o dispositivo foi adulterado ou danificado, não continue. Contate o Suporte da Microsoft para receber um dispositivo substituto.
2. Mova o dispositivo para o site de instalação e bloqueie as rodas traseiras.
3. Conecte todos os quatro cabos à alimentação na parte posterior do dispositivo.
4. Use os botões de energia no plano frontal para ligar os nós do dispositivo.

## <a name="cable-first-node-for-network"></a>Cabear o primeiro nó da rede

Essa etapa demora cerca de 10 a 15 minutos para ser concluída.

1. Use o cabo de rede RJ-45 CAT 6 para conectar o computador host à porta de gerenciamento (MGMT) no dispositivo.
2. Use o cabo de cobre Twinax QSFP+ para conectar, pelo menos, uma interface de rede de 40 Gbps (preferível a 1 Gbps), DATA 1 ou DATA 2, para os dados. Se estiver usando um comutador de 10 Gbps, use um cabo de cobre Twinax SFP+ com um adaptador QSFP+ para SFP+ (o adaptador QSA) para conectar o adaptador de rede de 40 Gbps para os dados.
3. Cabeie o dispositivo, conforme mostrado abaixo.  

    ![Data Box Heavy cabeado](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Configurar o primeiro nó

Essa etapa demora cerca de 5 a 7 minutos para ser concluída.

1. Para obter a senha do dispositivo, acesse **Geral > Detalhes do dispositivo** no [portal do Azure](https://portal.azure.com). A mesma senha é usada para os dois nós do dispositivo.
2. Atribua um endereço IP estático igual a 192.168.100.5 e a sub-rede 255.255.255.0 ao adaptador Ethernet no computador que você está usando para se conectar ao Data Box Heavy. Acesse a interface do usuário da Web local do dispositivo em `https://192.168.100.10`. A conexão poderá levar até 5 minutos após a ativação do dispositivo.
3. Entre usando a senha do portal do Azure. Você verá um erro indicando um problema com o certificado de segurança do site. Siga as instruções específicas do navegador para continuar para a página da Web.
4. Por padrão, as configurações de rede para as interfaces (exceto a MGMT) são definidas como DHCP. Se necessário, configure essas interfaces como estáticas e forneça um endereço IP.

## <a name="cable-and-configure-the-second-node"></a>Cabear e configurar o segundo nó

Essa etapa demora cerca de 15 a 20 minutos para ser concluída.

Siga as etapas usadas no primeiro nó para cabear e configurar o segundo nó do dispositivo.  

## <a name="copy-data"></a>Copiar dados

O tempo necessário para concluir essa operação depende do tamanho dos dados e da velocidade da rede na qual os dados serão copiados.
 
1. Copie dados para os dois nós do dispositivo usando as interfaces de dados de 40 Gbps em paralelo.

    - Se estiver usando um host do Windows, use uma ferramenta de cópia de arquivos compatível com SMB, como o [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - Para o host NFS, use o comando `cp` ou `rsync` para copiar os dados.
2. Conecte-se aos compartilhamentos no dispositivo usando o caminho:`\\<IP address of your device>\ShareName`. Para obter as credenciais de acesso ao compartilhamento, acesse a página **Conectar e copiar** na interface do usuário da Web local do Data Box Heavy.
3. Garanta que os nomes do compartilhamento e das pastas, bem como os dados, sigam as diretrizes descritas nos [Limites do Armazenamento do Azure e do serviço Data Box Heavy](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Preparar para o envio

O tempo para concluir essa operação depende do tamanho dos dados.

1. Depois que a cópia de dados for concluída sem erros, acesse a página **Preparação para o envio** na interface do usuário da Web local e inicie a preparação da remessa.
2. Depois que **Preparar para o envio** for concluído com êxito em ambos os nós, desative o dispositivo na interface da Web local.

## <a name="ship-to-azure"></a>Enviar para o Azure

Essa operação leva cerca de 15 a 20 minutos para ser concluída.

1. Remova os cabos e recoloque-os na bandeja na parte traseira do dispositivo.
2. Agende uma retirada com sua transportadora regional.
3. Contate as [Operações do Data Box](mailto:DataBoxOps@microsoft.com) para informar sobre a retirada e obter a etiqueta de remessa de devolução.
4. A etiqueta de remessa de devolução deve estar visível no painel frontal transparente do dispositivo.

## <a name="verify-data"></a>Verificar os dados

O tempo para concluir essa operação depende do tamanho dos dados.

1. Quando o dispositivo Data Box Heavy estiver conectado à rede de datacenter do Azure, os dados serão automaticamente carregados para o Azure.
2. O serviço Data Box notifica que a cópia de dados foi concluída por meio do portal do Azure.

    1. Verifique se há logs de erros para quaisquer falhas e tome as medidas apropriadas.
    2. Verifique se seus dados estão nas contas de armazenamento antes de excluí-los da fonte.

## <a name="clean-up-resources"></a>Limpar recursos

Esta etapa leva de 2 a 3 minutos para ser concluída.

- Você pode cancelar o pedido do Data Box Heavy no portal do Azure antes de o pedido ser processado. Depois que o pedido tiver sido processado, ele não pode ser cancelado. O pedido progride até atingir o estágio concluído. Para cancelar o pedido, vá para **Visão geral** e clique em **Cancelar** na barra de comandos.

- Você pode excluir o pedido depois que o status é mostrado como **Completo** ou **Cancelado** no portal do Azure. Para excluir o pedido, vá para **Visão geral** e clique em **Excluir** na barra de comandos.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um Data Box Heavy para ajudar a importar seus dados para o Azure. Para saber mais sobre o gerenciamento do Azure Data Box Heavy, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
> [Usar o portal do Azure para administrar o Data Box Heavy](data-box-portal-admin.md)
