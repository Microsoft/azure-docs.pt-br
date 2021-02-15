---
title: Tutorial para configurar o Azure Data Box | Microsoft Docs
description: Neste tutorial, saiba como realizar o cabeamento do Azure Data Box, conectá-lo ativá-lo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ac87d5040cd572635d81be51308f48a57ddd38e3
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335461"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Tutorial: Cabear e conectar-se ao Azure Data Box

::: zone-end

::: zone target="chromeless"

## <a name="cable-and-connect-to-your-device"></a>Cabear e conectar ao seu dispositivo

::: zone-end

::: zone target="docs"

Este tutorial descreve como cabear, conectar e ligar seu Azure Data Box.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Cabear seu Data Box
> * Conectar-se ao seu Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você realizou o pedido para o Azure Data Box.
    - Para uma ordem de importação, confira [Tutorial: Solicitar o Azure Data Box](data-box-deploy-ordered.md).
    - Para uma ordem de exportação, confira [Tutorial: Solicitar o Azure Data Box](data-box-deploy-export-ordered.md)
1. Você recebeu seu Data Box e o status do pedido no portal está como **Entregue**. 
    - Há uma etiqueta de remessa na bolsa transparente afixada ao dispositivo sob o rótulo atual. Mantenha essa etiqueta segura, pois você a usará para remessa de devolução.
    - Algumas regiões da Europa podem receber o dispositivo empacotado em uma caixa. Retire o dispositivo do pacote e guarde a caixa para devolução da remessa.
1. Você analisou as [diretrizes de segurança do Data Box](data-box-safety.md).
1. Você recebeu um cabo de alimentação com aterramento para usar com seu dispositivo de armazenamento de 100 TB.
1. Você tem um computador host que é usado para copiar dados para (ordem de importação) ou copiar dados de (ordem de exportação) seu Data Box. O computador host deve
    - Executar um [Sistema operacional com suporte](data-box-system-requirements.md).
    - Estar conectado a uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, um link de dados de 1 GbE poderá ser usado, mas as velocidades de cópia serão afetadas. 
1. Você deve ter acesso a uma superfície plana na qual possa colocar o Data Box. Caso deseje colocar o dispositivo em uma prateleira de rack padrão, você precisará de um slot 7U no rack de seu datacenter. Você pode colocar o dispositivo no rack na posição horizontal ou vertical.
1. Você comprou os cabos a seguir para conectar o Data Box ao computador host.
    - Um ou mais cabos de cobre SFP+ Twinax de 10 GbE ou cabos de fibra ótica SFP+ (usar com adaptadores de rede DATA 1 ou DATA 2). O Data Box tem os Adaptadores Mellanox ConnectX®-3 Pro EN Dual-Port 10GBASE-T com adaptador de rede PCI Express 3.0; portanto, os cabos compatíveis com esse adaptador devem funcionar. Por exemplo, um cabo CISCO SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP +3M foi usado para teste interno. Para obter mais informações, confira a [lista de cabos e comutadores compatíveis da Mellanox](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - Um cabo de rede RJ-45 CAT 6 (usar com adaptador de rede MGMT)
    - Um cabo de rede RJ-45 CAT 6A OU RJ-45 CAT 6 (usar com adaptador de rede DATA 3 configurado como 10 Gbps ou 1 Gbps respectivamente)

## <a name="cable-your-device"></a>Cabear seu dispositivo

Execute as etapas a seguir para cabear seu dispositivo.

1. Inspecione o dispositivo em busca de alguma evidência de adulteração ou qualquer outro dano visível. Se o dispositivo estiver adulterado ou gravemente danificado, não continue. Entre em contato imediatamente com o Suporte da Microsoft para obter ajuda e avaliar se o dispositivo está em boas condições de funcionamento e se é necessário enviar um substituto.
2. Transporte o dispositivo para o local onde você deseja ligá-lo. Coloque o dispositivo em uma superfície plana. O dispositivo também pode ser colocado em uma prateleira de rack padrão.
3. Conecte os cabos de alimentação e de rede. O backplane de um dispositivo conectado para uma configuração comum é mostrado abaixo. Dependendo do seu ambiente, é possível escolher entre outras [opções de cabeamento](data-box-cable-options.md).
    
    ![Backplane do dispositivo Data Box cabeado](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Conecte o cabo de alimentação ao local de entrada de energia identificado. A outra extremidade do cabo de alimentação deve estar conectada a uma unidade de distribuição de energia.
    2. Use o cabo RJ-45 CAT 6 para conectar a porta MGMT em uma extremidade e um laptop na outra extremidade.            
    3. Use o cabo RJ-45 CAT 6 para conectar-se à porta DATA 3 em uma extremidade. A DATA 3 está configurada como 10 GbE, se a conexão for via cabo RJ-45 CAT 6A, e como 1 GbE, se a conexão for via cabo RJ-45 CAT 6.
    4. Dependendo das interfaces de rede, convém se conectar para transferência de dados, use até dois cabos de cobre SFP+ Twinax de 10 GbE ou de fibra ótica SFP+ para conectar as portas DATA 1 e DATA 2, respectivamente. 
    5. As outras extremidades dos cabos das portas de dados estão conectadas ao computador host por um switch de 10 GbE.

4. Localize o botão de energia na parte frontal do painel do dispositivo de operação. Ligar o dispositivo.

    ![Botão de energia do Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

Após receber o dispositivo, você precisará cabear e conectar-se ao seu dispositivo. 

## <a name="cable-your-device"></a>Cabear seu dispositivo

1. Se houver alguma evidência de que o dispositivo foi adulterado ou danificado, não continue. Contate o Suporte da Microsoft para receber um dispositivo substituto.
2. Antes de cabear o dispositivo, verifique se você tem os seguintes cabos:
    
    - Cabo de alimentação com aterramento (incluso) com potência igual ou superior a 10 A e um conector IEC60320 C-13 em uma extremidade para a conexão ao dispositivo.
    - Um cabo de rede RJ-45 CAT 6 (usar com adaptador de rede MGMT)
    - Dois cabos de cobre SFP+ Twinax de 10 GbE (usar com adaptadores de rede DATA 1 ou DATA 2 de 10 Gbps)
    - Um cabo de rede RJ-45 CAT 6A OU RJ-45 CAT 6 (usar com adaptador de rede DATA 3 configurado como 10 Gbps ou 1 Gbps respectivamente)

3. Remova e coloque o dispositivo em uma superfície plana. 
    
4. Cabeie o dispositivo, conforme mostrado abaixo.  

    ![Backplane do dispositivo Data Box cabeado](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Conecte o cabo de alimentação ao dispositivo.
    2. Use o cabo de rede RJ-45 CAT 6 para conectar o computador host à porta de gerenciamento (MGMT) no dispositivo. 
    3. Use o cabo de cobre SFP+ Twinax para conectar, pelo menos, um adaptador de rede de 10 Gbps (preferível a 1 Gbps), DATA 1 ou DATA 2, aos dados. 
    4. Ligar o dispositivo. O botão de energia está localizado no painel frontal do dispositivo.

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>Conectar-se ao seu dispositivo

Execute as etapas a seguir para configurar seu dispositivo usando a interface do usuário da Web local e o portal da interface do usuário.

1. Configure o adaptador Ethernet no laptop que você está usando para se conectar ao dispositivo com o endereço IP estático de 192.168.100.5 e a sub-rede 255.255.255.0. 
2. Conecte-se à porta MGMT do dispositivo e acesse sua interface do usuário da Web local em https\://192.168.100.10. Isso pode levar até 5 minutos após a ativação do dispositivo.
3. Clique em **Detalhes** , depois em **Ir para a página da Web**.

   ![Conectar-se à interface do usuário da Web local](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png)

4. Você verá uma página **Entrar** da interface do usuário da Web local. Verifique se o número de série do dispositivo corresponde entre a interface do usuário do portal e a interface do usuário da Web local. Nesse momento, o dispositivo está bloqueado.
5. Faça logon no [Portal do Azure](https://portal.azure.com).
6. Baixe as credenciais do dispositivo no portal. Vá para **Geral > Detalhes do dispositivo**. Copie a **Senha do dispositivo**. A senha do dispositivo é vinculada a uma ordem específica no portal. 

    ![Obter credenciais do dispositivo](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Forneça a senha do dispositivo que você obteve do portal do Azure na etapa anterior para entrar na interface do usuário da Web local do dispositivo. Clique em **Entrar**.
8. No **Painel** , verifique se as interfaces de rede estão configuradas. 
   - Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente. 
   - Se o DHCP não estiver habilitado, vá até **Definir interfaces de rede** e atribua IPs estáticos se necessário.

     ![Painel do dispositivo](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Depois que os adaptadores de rede de dados forem configurados, você também pode usar o endereço IP de qualquer um dos adaptadores DATA 1 a DATA 3 para acessar a interface do usuário da Web local em `https://<IP address of a data network interface>`. 

Após a configuração do dispositivo ser concluída, é possível se conectar aos compartilhamentos de dispositivo e copiar os dados. 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>Conectar o dispositivo

1. Para obter a senha do dispositivo, acesse **Geral > Detalhes do dispositivo** no [portal do Azure](https://portal.azure.com).
2. Atribua um endereço IP estático igual a 192.168.100.5 e a sub-rede 255.255.255.0 ao adaptador Ethernet no computador que você está usando para se conectar ao Data Box. Acesse a interface do usuário da Web local do dispositivo em `https://192.168.100.10`. A conexão poderá levar até 5 minutos após a ativação do dispositivo. 
3. Entre usando a senha do portal do Azure. Você verá um erro indicando um problema com o certificado de segurança do site. Siga as instruções específicas do navegador para continuar para a página da Web.
4. Por padrão, as configurações de rede para o adaptador de dados de 10 Gbps (ou 1 Gbps) são definidas como DHCP. Se necessário, configure essa interface como estática e forneça um endereço IP. 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Cabear seu Data Box
> * Conectar-se ao seu Data Box

Avance para o próximo tutorial para saber como copiar dados.

> [!div class="nextstepaction"]
> [Copiar seus dados para o Azure Data Box para uma ordem de importação](./data-box-deploy-copy-data.md)

Ou

> [!div class="nextstepaction"]
> [Copiar seus dados do Azure Data Box para uma ordem de exportação](./data-box-deploy-export-copy-data.md)

::: zone-end

