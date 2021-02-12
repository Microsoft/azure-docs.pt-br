---
title: Solução do Azure VMware por CloudSimple-configurar um gateway de VPN
description: Descreve como configurar o gateway de VPN ponto a site e o gateway de VPN site a site e criar conexões entre sua rede local e sua nuvem privada do CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 78f78bfbf885cbae7708a75cb54ce2d41e7b526c
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899092"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Configurar gateways de VPN na rede CloudSimple

Os gateways de VPN permitem que você se conecte à rede CloudSimple a partir de sua rede local e de um computador cliente remotamente. Uma conexão VPN entre sua rede local e sua rede CloudSimple fornece acesso ao vCenter e às cargas de trabalho em sua nuvem privada. O CloudSimple dá suporte a VPN site a site e gateways de VPN ponto a site.

## <a name="vpn-gateway-types"></a>Tipos de gateway de VPN

* A conexão **VPN site a site** permite que você configure suas cargas de trabalho de nuvem privada para acessar os serviços locais. Você também pode usar Active Directory locais como uma fonte de identidade para autenticação em seu vCenter de nuvem privada.  Atualmente, há suporte apenas para o tipo **VPN baseado em política** .
* A conexão **VPN ponto a site** é a maneira mais simples de se conectar à sua nuvem privada do seu computador. Use a conectividade VPN ponto a site para se conectar à nuvem privada remotamente. Para obter informações sobre como instalar um cliente para uma conexão VPN ponto a site, consulte [Configurar uma conexão VPN com sua nuvem privada](set-up-vpn.md).

Em uma região, você pode criar um gateway de VPN ponto a site e um gateway de VPN site a site.

## <a name="automatic-addition-of-vlansubnets"></a>Adição automática de VLAN/sub-redes

Os gateways de VPN CloudSimple fornecem políticas para adicionar VLANs/sub-redes a gateways de VPN.  As políticas permitem que você especifique regras diferentes para gerenciamento de VLAN/sub-redes e VLANs/sub-redes definidas pelo usuário.  As regras de gerenciamento VLAN/sub-redes se aplicam a quaisquer novas nuvens privadas que você criar.  Regras para VLANs/sub-redes definidas pelo usuário permitem adicionar automaticamente quaisquer novas VLANs/sub-redes a nuvens privadas existentes ou novas para um gateway de VPN site a site, você define a política para cada conexão.

As políticas de adição de VLANs/sub-redes a gateways de VPN se aplicam a gateways de VPN de ponto a site e VPN entre sites.

## <a name="automatic-addition-of-users"></a>Adição automática de usuários

Um gateway de VPN ponto a site permite que você defina uma política de adição automática para novos usuários. Por padrão, todos os proprietários e colaboradores da assinatura têm acesso ao portal do CloudSimple.  Os usuários são criados somente quando o portal do CloudSimple é iniciado pela primeira vez.  A seleção de **adicionar regras automaticamente** permite que qualquer usuário novo acesse a rede CloudSimple usando a conexão VPN ponto a site.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Configurar um gateway de VPN site a site

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md) e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Clique em **novo gateway de VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração de gateway**, especifique as seguintes configurações e clique em **Avançar**.

    * Selecione **VPN site a site** como o tipo de gateway.
    * Insira um nome para identificar o gateway.
    * Selecione o local do Azure onde o serviço CloudSimple está implantado.
    * Opcionalmente, habilite a alta disponibilidade.

    ![Criar gateway de VPN site a site](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Habilitar a alta disponibilidade exige que o dispositivo VPN local dê suporte à conexão com dois endereços IP. Esta opção não pode ser desabilitada depois que o gateway de VPN é implantado.

5. Crie a primeira conexão de sua rede local e clique em **Avançar**.

    * Insira um nome para identificar a conexão.
    * Para o IP do par, insira o endereço IP público do gateway de VPN local.
    * Insira o identificador de par do seu gateway de VPN local.  O identificador de par é geralmente o endereço IP público do seu gateway de VPN local.  Se você tiver configurado um identificador específico em seu gateway, insira o identificador.
    * Copie a chave compartilhada a ser usada para conexão do gateway de VPN local.  Para alterar a chave compartilhada padrão e especificar uma nova, clique no ícone Editar.
    * Para **prefixos locais**, insira os prefixos CIDR locais que acessarão a rede CloudSimple.  Você pode adicionar vários prefixos CIDR ao criar a conexão.

    ![Criar conexão de gateway de VPN site a site](media/create-vpn-gateway-s2s-connection.png)

6. Habilite as VLANs/sub-redes em sua rede de nuvem privada que serão acessadas da rede local e clique em **Avançar**.

    * Para adicionar uma VLAN/sub-rede de gerenciamento, habilite **Adicionar VLANs de gerenciamento/sub-redes de nuvens privadas**.  A sub-rede de gerenciamento é necessária para as sub-redes do vMotion e do vSAN.
    * Para adicionar sub-redes do vMotion, habilite **Adicionar rede VMotion de nuvens privadas**.
    * Para adicionar sub-redes vSAN, habilite **Adicionar a sub-rede VSAN de nuvens privadas**.
    * Selecione ou cancele a seleção de VLANs específicas.

    ![Criar conexão](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Examine as configurações e clique em **Enviar**.

    ![Revisão e criação do gateway de VPN site a site](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Criar gateway de VPN ponto a site

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md) e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Clique em **novo gateway de VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração de gateway**, especifique as seguintes configurações e clique em **Avançar**.

    * Selecione **VPN ponto a site** como o tipo de gateway.
    * Insira um nome para identificar o gateway.
    * Selecione o local do Azure onde o serviço CloudSimple está implantado.
    * Especifique a sub-rede do cliente para o gateway ponto a site.  Os endereços DHCP serão fornecidos da sub-rede do cliente quando você se conectar.

5. Para **conexão/usuário**, especifique as seguintes configurações e clique em **Avançar**.

    * Para permitir que todos os usuários atuais e futuros acessem automaticamente a nuvem privada por meio do gateway ponto a site, selecione **adicionar todos os usuários automaticamente**. Quando você seleciona a opção, todos os usuários na lista de usuários são selecionados automaticamente. Você pode substituir a opção automática desmarcando usuários individuais na lista.
    * Para selecionar usuários individuais, clique nas caixas de seleção na lista de usuários.

6. A seção VLANs/sub-redes permite especificar VLANs de gerenciamento e de usuário/sub-redes para o gateway e as conexões.

    * As opções **adicionar automaticamente** definem a política global para o gateway. As configurações se aplicam ao gateway atual. As configurações podem ser substituídas na área de **seleção** .
    * Selecione **Adicionar VLANs de gerenciamento/sub-redes de nuvens privadas**. 
    * Para adicionar todas as VLANs/sub-redes definidas pelo usuário, clique em  **Adicionar VLANs/sub-redes definidas pelo usuário**.
    * As configurações de **selecionar** substituem as configurações globais em **adicionar automaticamente**.

7. Clique em **Avançar** para revisar as configurações. Clique nos ícones de edição para fazer alterações.
8. Clique em **criar** para criar o gateway de VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Sub-rede do cliente e protocolos para gateway de VPN ponto a site

O gateway de VPN ponto a site permite conexões TCP e UDP.  Escolha o protocolo a ser usado quando você se conectar do seu computador selecionando a configuração TCP ou UDP.

A sub-rede do cliente configurada é usada para clientes TCP e UDP.  O prefixo CIDR é dividido em duas sub-redes, uma para TCP e outra para clientes UDP. Escolha a máscara de prefixo com base no número de usuários VPN que se conectarão simultaneamente.  

A tabela a seguir lista o número de conexões de cliente simultâneas para a máscara de prefixo.

| Máscara de prefixo | /24 | / 25 | / 26 | / 27 | / 28 |
|-------------|-----|-----|-----|-----|-----|
| Número de conexões TCP simultâneas | 124 | 60 | 28 | 12 | 4 |
| Número de conexões UDP simultâneas | 124 | 60 | 28 | 12 | 4 |

Para se conectar usando a VPN ponto a site, consulte [conectar-se ao CloudSimple usando a VPN ponto a site](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
