---
title: 'Início Rápido: Exportar dados para o Microsoft Azure Data Box'
description: Saiba como exportar rapidamente seus dados do Azure Data Box no portal do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 789bd2f62673e7faf562d3a407bf2f0a4fd861bf
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125485"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>Início Rápido: Introdução ao Azure Data Box para exportar dados do Azure

Este guia de início rápido descreve como exportar dados do Azure para seu local usando o portal do Azure. As etapas incluem como conectar, configurar e copiar dados do Azure. O início rápido é executado no portal do Azure e na interface do usuário da Web local do dispositivo.

Para obter a implantação detalhada passo a passo e instruções de acompanhamento, acesse [Tutorial: Criar ordem de exportação para o Azure Data Box](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Verifique se a assinatura usada para o serviço Data Box é de um dos seguintes tipos:
  * Microsoft EA (Contrato Enterprise). Leia mais sobre [assinaturas de EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
  * CSP (Provedor de Soluções na Nuvem). Saiba mais sobre o [programa do Azure CSP](/azure/cloud-solution-provider/overview/azure-csp-overview).
  * Microsoft Azure Sponsorship. Saiba mais sobre o [programa Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

* Verifique se você tem acesso de proprietário ou colaborador na assinatura para criar um pedido do Data Box.
* Examine as [diretrizes de segurança do Data Box](data-box-safety.md).
* Você tem um dispositivo Azure Data Box para mover dados para o local. O computador host deve
  * Executar um [Sistema operacional com suporte](data-box-system-requirements.md).
  * Estar conectado a uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, um link de dados de 1 GbE poderá ser usado, mas as velocidades de cópia serão afetadas.
* Você deve ter acesso a uma superfície plana na qual possa colocar o Data Box. Caso deseje colocar o dispositivo em uma prateleira de rack padrão, você precisará de um slot 7U no rack de seu datacenter. Você pode colocar o dispositivo no rack na posição horizontal ou vertical.
* Você obteve os cabos a seguir para conectar o Data Box ao computador host.
  * Dois cabos de cobre SFP+ Twinax de 10 GbE (usar com adaptadores de rede DATA 1 ou DATA 2)
  * Um cabo de rede RJ-45 CAT 6 (usar com adaptador de rede MGMT [gerenciamento])
  * Um cabo de rede RJ-45 CAT 6A OU RJ-45 CAT 6 (usar com adaptador de rede DATA 3 configurado como 10 Gbps ou 1 Gbps respectivamente)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Order

Essa etapa leva aproximadamente 10 minutos.

1. Crie um recurso do Azure Data Box no portal do Azure.
2. Selecione uma assinatura existente habilitada para esse serviço e escolha o tipo de transferência **Exportar para o Azure**. Forneça a **Região do Azure de origem** onde os dados residem e o **País de destino** para a transferência de dados.
3. Selecione **Data Box**. A capacidade utilizável máxima é de 80 TB, e você pode criar vários pedidos para tamanhos maiores de dados.
4. Selecione **Adicionar conta de armazenamento e tipo de exportação** e escolha **Selecionar opção de exportação**.
5. Insira os detalhes do pedido e as informações de envio. Se o serviço estiver disponível em sua região, forneça endereços de email de notificação, examine o resumo e, em seguida, crie o pedido.

Depois que o pedido for criado, o dispositivo estará preparado para remessa.

## <a name="cable"></a>Cabo

Essa etapa leva aproximadamente 10 minutos.

Quando receber o Data Box, execute as etapas a seguir para cabear, conectar e ativar o dispositivo. Essa etapa leva aproximadamente 10 minutos.

1. Se houver alguma evidência de que o dispositivo foi adulterado ou danificado, não continue. Contate o Suporte da Microsoft para receber um dispositivo substituto.
2. Antes de cabear o dispositivo, verifique se você tem os seguintes cabos:

   * Cabo de alimentação com aterramento (incluso) com potência igual ou superior a 10 A e um conector IEC60320 C-13 em uma extremidade para a conexão ao dispositivo.
   * Um cabo de rede RJ-45 CAT 6 (usar com adaptador de rede MGMT [gerenciamento])
   * Dois cabos de cobre SFP+ Twinax de 10 GbE (usar com adaptadores de rede DATA 1 ou DATA 2 de 10 Gbps)
   * Um cabo de rede RJ-45 CAT 6A OU RJ-45 CAT 6 (usar com adaptador de rede DATA 3 configurado como 10 Gbps ou 1 Gbps respectivamente)

3. Remova e coloque o dispositivo em uma superfície plana.

4. Cabeie o dispositivo, conforme mostrado abaixo.  

    ![Backplane do dispositivo Data Box cabeado](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Conecte o cabo de alimentação ao dispositivo.
    2. Use o cabo de rede RJ-45 CAT 6 para conectar o computador host à porta MGMT do dispositivo.
    3. Use o cabo de cobre SFP+ Twinax para conectar, pelo menos, um adaptador de rede de 10 Gbps (preferível a 1 Gbps), DATA 1 ou DATA 2, aos dados.
    4. Ligar o dispositivo. O botão de energia está localizado no painel frontal do dispositivo.

## <a name="connect"></a>Conectar

Essa etapa demora cerca de 5 a 7 minutos para ser concluída.

1. Para obter a senha do dispositivo, acesse **Geral** > **Detalhes do dispositivo** no [portal do Azure](https://portal.azure.com).
2. Atribua um endereço IP estático igual a 192.168.100.5 e a sub-rede 255.255.255.0 ao adaptador Ethernet no computador que você está usando para se conectar ao Data Box. Acesse a interface do usuário da Web local do dispositivo em `https://192.168.100.10`. A conexão poderá levar até 5 minutos após a ativação do dispositivo.
3. Entre usando a senha do portal do Azure. Você verá um erro indicando um problema com o certificado de segurança do site. Siga as instruções específicas do navegador para continuar para a página da Web.
4. Por padrão, as configurações de rede para o adaptador de dados de 10 Gbps (ou 1 Gbps) são definidas como DHCP. Se necessário, configure essa interface como estática e forneça um endereço IP.

## <a name="copy-data"></a>Copiar dados

O tempo necessário para concluir essa operação depende do tamanho dos dados e da velocidade da rede.

1. Se estiver usando um cliente Windows, use uma ferramenta de cópia de arquivos compatível com SMB, como o Robocopy. Para o host NFS, use o comando `cp` ou `rsync` para copiar os dados. Para obter mais informações sobre como usar o Robocopy para copiar dados, acesse [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
2. Conecte-se aos compartilhamentos do dispositivo e comece a copiar dados para o computador host.
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>Enviar para o Azure

Essa operação leva cerca de 10 a 15 minutos para ser concluída.

1. Acesse a página **Preparação para o envio** na interface do usuário da Web local e inicie a preparação da remessa.
2. Desligue o dispositivo por meio da interface do usuário da Web local. Remova os cabos do dispositivo.
3. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte posterior do dispositivo.
4. A etiqueta de remessa de devolução deve estar visível na tela de E-ink. Se a tela E-Ink não estiver exibindo o rótulo, vá para **Visão geral** > **Baixar etiqueta de remessa** no portal do Azure. Baixe a etiqueta de remessa e afixe-a ao dispositivo.
5. Agende uma retirada com sua transportadora regional se estiver devolvendo o dispositivo.
6. Após a coleta e registro do Data Box por sua operadora, o status do pedido no portal será atualizado para **Recolhido**. Uma ID de rastreamento também é exibida.

## <a name="clean-up-resources"></a>Limpar os recursos

Esta etapa leva de 2 a 3 minutos para ser concluída.

* Você pode cancelar o pedido do Data Box no portal do Azure antes de o pedido ser processado. Após ter sido processado, o pedido não pode ser cancelado. O pedido progride até atingir o estágio concluído. Para cancelar o pedido, vá para **Visão geral** e selecione **Cancelar** na barra de comandos.

* Você pode excluir o pedido depois que o status é mostrado como **Completo** ou **Cancelado** no portal do Azure. Para excluir o pedido, vá para **Visão geral** e selecione **Excluir** na barra de comandos.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um pedido de exportação do Azure para um Azure Data Box. Para saber mais sobre o gerenciamento do Azure Data Box, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Usar o portal do Azure para administrar o Data Box](data-box-portal-admin.md)