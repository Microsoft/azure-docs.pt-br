---
title: Tutorial sobre como configurar o Azure Data Box Heavy | Microsoft Docs
description: Saiba como cabear e conectar o Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 3e6bfe4a93ab8c97bcffb84bda08977f8d811fa8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592365"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Tutorial: Cabear e conectar-se ao Azure Data Box Heavy


Este tutorial descreve como cabear, conectar e ligar o Azure Data Box Heavy.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Cabear o Data Box Heavy
> * Conectar-se ao Data Box Heavy

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Solicitar o Azure Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Você recebeu o Data Box Heavy e o status do pedido no portal está como **Entregue**.
3. Você examinou as [diretrizes de segurança do Data Box Heavy](data-box-safety.md).
4. Você precisa ter acesso a um site simples no datacenter com proximidade a uma conexão de rede disponível que possa acomodar um dispositivo com esse volume. Esse dispositivo não pode ser montado em um rack.
5. Você recebeu quatro cabos de alimentação aterrados para uso com o dispositivo de armazenamento.
6. Você deve ter um computador host conectado à rede do datacenter. O Data Box Heavy copiará os dados deste computador. O computador host precisa executar um [sistema operacional compatível](data-box-heavy-system-requirements.md).
7. Seu datacenter deve ter uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. 
8. Você precisa ter um laptop com um cabo RJ-45 para se conectar à IU local e configurar o dispositivo. Use o laptop para configurar cada nó do dispositivo uma vez.
9. Você precisa de um cabo de 10 ou 40 Gbps por nó do dispositivo.
    - Escolha cabos que sejam compatíveis com o adaptador de rede [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html).
    - Para o cabo de 40 Gbps, a extremidade do dispositivo do cabo precisa ser QSFP+.
    - Para o cabo de 10 Gbps, você precisará de um cabo SFP+ que se conecta a um comutador de 10 Gbps em uma extremidade, com um adaptador QSFP+ para SFP+ (ou o adaptador QSA) para a extremidade que se conecta ao dispositivo.

## <a name="cable-your-device-for-power"></a>Cabear o dispositivo para alimentação

Execute as etapas a seguir para cabear o dispositivo.

1. Inspecione o dispositivo em busca de alguma evidência de adulteração ou qualquer outro dano visível. Se o dispositivo estiver adulterado ou gravemente danificado, não continue. [Contate o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md) imediatamente para ajudá-lo a avaliar se o dispositivo está em boas condições de funcionamento e se é necessário enviar um substituto.
2. Mova o dispositivo para o site de instalação.

    ![Site de instalação do dispositivo Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Trave os rodízios traseiros no dispositivo, conforme mostrado abaixo.

    ![Rodízios traseiros do dispositivo Data Box Heavy travados](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Localize os botões que destravam as portas dianteira e traseira do dispositivo. Destrave-as e mova a porta dianteira até que ela seja liberada com a lateral do dispositivo. Repita esta etapa com porta traseira.
    Ambas as portas precisarão permanecer abertas quando o dispositivo estiver funcionando para permitir um fluxo de ar ideal da parte dianteira à traseira por meio do dispositivo.

    ![Portas abertas do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. A bandeja na parte traseira do dispositivo deve ter quatro cabos de alimentação. Remova todos os cabos da bandeja e separe-os.

    ![Cabos de alimentação do Data Box Heavy na bandeja](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. A próxima etapa é identificar as várias portas na parte traseira do dispositivo. Há dois nós de dispositivo: **NODE1** e **NODE2**. Cada nó tem quatro adaptadores de rede: **MGMT**, **DATA1**, **DATA2** e **DATA3**. **MGMT** é usado para configurar o gerenciamento durante a configuração inicial do dispositivo. **DATA1**-**DATA3** são portas de dados. As portas de **MGMT** e **DATA3** são de 1 Gbps, enquanto **DATA1** e **DATA2** podem funcionar como portas de 10 ou 40 Gbps. Na parte inferior dos dois nós do dispositivo estão quatro PSUs (unidades de alimentação) que são compartilhadas entre os dois nós do dispositivo. Olhando para a frente desse dispositivo, as **PSUs** são **PSU1**, **PSU2**, **PSU3** e **PSU4**, da esquerda para a direita.

    ![Portas do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Conecte todos os quatro cabos de alimentação à alimentação do dispositivo. Os LEDs verdes ficarão acesos e piscarão.
8. Use os botões de energia no plano frontal para ligar os nós do dispositivo. Mantenha o botão de energia pressionado por alguns segundos até a luz azul acender. Todos os LEDs verdes da alimentação na parte traseira do dispositivo agora deverão estar acesos. O painel de operações frontal do dispositivo também contém LEDs de falha. Quando um LED de falha estiver aceso, isso indicará uma PSU ou um ventilador com defeito ou um problema com as unidades de disco.  

    ![Painel de operações frontal do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Cabear o primeiro nó para a rede

Em um dos nós do dispositivo, execute as etapas a seguir para fazer o cabeamento para a rede.

1. Use um cabo de rede CAT 6 RJ-45 (cabo azul na imagem) para conectar o computador host à porta de gerenciamento de 1 Gbps.
2. Use um cabo QSFP+ (fibra ou cobre) para conectar, pelo menos, uma interface de rede de 40 Gbps (preferível a 1 Gbps) para os dados. Se estiver usando um comutador de 10 Gbps, use um cabo SFP+ com um adaptador QSFP+ para SFP+ (o adaptador QSA) para conectar a rede de 40 Gbps para os dados.

    ![Portas do Data Box Heavy cabeadas](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA1 e DATA2 são alternados e não correspondem ao que é exibido na IU da Web local.
    > O adaptador de cabo de 40 Gbps se conecta quando inserido da maneira mostrada abaixo.

    ![Adaptador de cabo de 40 Gbps do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Configurar o primeiro nó

Execute as etapas a seguir para configurar o dispositivo usando a configuração local e o portal do Azure.

1. Baixe as credenciais do dispositivo no portal. Vá para **Geral > Detalhes do dispositivo**. Copie a **Senha do dispositivo**. Essas senhas são vinculadas a uma ordem específica no portal. Correspondentes aos dois nós do Data Box Heavy, os dois números de série serão exibidos. A senha de administrador do dispositivo para ambos os nós é a mesma.

    ![Credenciais de dispositivo do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Conecte a estação de trabalho do cliente ao dispositivo por meio de um cabo de rede CAT6 RJ-45.
3. Configure o adaptador Ethernet no computador que você está usando para se conectar ao dispositivo com o endereço IP estático `192.168.100.5` e a sub-rede `255.255.255.0`.

    ![O Data Box Heavy conecta-se à IU da Web local](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Conecte-se à IU da Web local do dispositivo na seguinte URL: `http://192.168.100.10`. Clique em **Avançado** e, em seguida, em **Prosseguir para 192.168.100.10 (não seguro)** .
5. Você verá uma página **Entrar** da interface do usuário da Web local.
    
    - Um dos números de série do nó nesta página é correspondente na interface do usuário do portal e na IU da Web local. Anote o número do nó para o mapeamento do número de série. Há dois nós e dois números de série do dispositivo no portal. Esse mapeamento ajuda você a entender qual nó corresponde a qual número de série.
    - Nesse momento, o dispositivo está bloqueado.
    - Forneça a senha de administrador do dispositivo que você obteve na etapa anterior para entrar no dispositivo. Clique em **Entrar**.

    ![Entrar na IU da Web local do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. No painel, garanta que os adaptadores de rede estejam configurados. Há quatro adaptadores de rede no nó do dispositivo: dois de 1 Gbps e dois de 40 Gbps. Um dos adaptadores de 1 Gbps é uma interface de gerenciamento e, portanto, não é configurável pelo usuário. Os três adaptadores de rede restantes são dedicados a dados e podem ser configurados pelo usuário.

- Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente.
- Se o DHCP não estiver habilitado, acesse Definir adaptadores de rede e atribua IPs estáticos, se necessário.

    ![Nó 1 no painel do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Configurar o segundo nó

Siga as etapas detalhadas em [Configurar o primeiro nó](#configure-first-node) para o segundo nó do dispositivo.

![Nó 2 no painel do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Após a configuração do dispositivo ser concluída, é possível se conectar aos compartilhamentos de dispositivo e copiar os dados do seu computador para o dispositivo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Heavy, como:

> [!div class="checklist"]
> * Cabear o Data Box Heavy
> * Conectar-se ao Data Box Heavy

Avance para o próximo tutorial para saber como copiar dados para o Data Box Heavy.

> [!div class="nextstepaction"]
> [Copiar seus dados para o Disco do Azure Data Box](./data-box-heavy-deploy-copy-data.md)
