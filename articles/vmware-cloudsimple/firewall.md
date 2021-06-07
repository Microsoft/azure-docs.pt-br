---
title: Solução do Azure VMware por CloudSimple-configure as tabelas e regras de firewall
description: Descreve como configurar tabelas e regras de firewall de nuvem privada para restringir o tráfego em sub-redes e VLANs.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 32dcfb0c6cd26b5f34afad328db8f383fa1c2a6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895624"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Configurar tabelas e regras de firewall para nuvens privadas

As tabelas de firewall e as regras associadas permitem que você especifique as restrições de tráfego a serem aplicadas a sub-redes e VLANs específicas.

* Uma sub-rede pode ser associada a uma tabela de firewall.
* Uma tabela de firewall pode ser associada a várias sub-redes.

## <a name="add-a-new-firewall-table"></a>Adicionar uma nova tabela de firewall

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md) e selecione **rede** no menu lateral.
2. Selecione **tabelas de firewall**.
3. Selecione **criar tabela de firewall**.

    ![Página VLAN/sub-rede](media/firewall-tables-page.png)

4. Insira um nome para a tabela.
5. Uma regra padrão para a tabela é listada. Clique em **criar nova regra** para criar uma regra adicional. Consulte o procedimento a seguir para obter detalhes.
6. Clique em **concluído** para salvar a tabela de firewall.

> [!IMPORTANT]
> Você pode criar até duas tabelas de firewall por nuvem privada.

## <a name="firewall-rules"></a>Regras de firewall

As regras de firewall determinam como o firewall trata tipos específicos de tráfego. A guia **regras** para uma tabela de firewall selecionada lista todas as regras associadas.

![Tabela de regras de firewall](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Criar uma regra de firewall

1. Exiba as configurações para criar uma regra de firewall de uma das seguintes maneiras:
    * Clique em **Adicionar regra** ao criar uma tabela de firewall.
    * Selecione uma tabela de firewall específica na página **tabelas de firewall > rede** e clique em **criar nova regra de firewall**.
2. Configure a regra da seguinte maneira:
    * **Nome**. Dê um nome à regra.
    * **Prioridade**. Atribua uma prioridade à regra. As regras com números inferiores são executadas primeiro.
    * **Tipo de tráfego**. Selecione se a regra é para o tráfego de nuvem privada, Internet ou VPN (sem estado) ou para um endereço IP público (com estado).
    * **Protocolo**. Selecione o protocolo coberto pela regra (TCP, UDP ou qualquer protocolo).
    * **Direção**. Selecione se a regra é para o tráfego de entrada ou de saída. Você deve definir regras separadas para o tráfego de entrada e de saída.
    * **Ação**. Selecione a ação a ser tomada se a regra corresponder (permitir ou negar).
    * **Fonte**. Especifique as fontes cobertas pela regra (bloco CIDR, interno ou qualquer fonte).
    * **Intervalo de portas de origem**. Especifique o intervalo de portas sujeitas à regra.
    * **Direção**. Selecione entrada ou saída.
    * **Destino**. Especifique os destinos cobertos pela regra (bloco CIDR, interno ou qualquer fonte).
    * **Intervalo de portas de origem**. Especifique o intervalo de portas sujeitas à regra.

    ![Tabela de firewall-Adicionar regra](media/firewall-rule-create.png)

3. Clique em **concluído** para salvar a regra e adicioná-la à lista de regras para a tabela de firewall.

> [!IMPORTANT]
> Cada tabela de firewall pode ter até 10 regras de entrada e 20 regras de saída. Esses limites podem ser aumentados [entrando em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>Anexar VLANs/sub-redes

Depois de definir uma tabela de firewall, você pode especificar as sub-redes que estão sujeitas às regras na tabela.

1. Na página   >  **tabelas de firewall** de rede, selecione uma tabela de firewall.
2. Abra a guia **VLANs/sub-rede anexadas** .
3. Clique em **anexar a uma VLAN/sub-rede**.
4. Selecione a nuvem privada e a VLAN. O nome de sub-rede e o bloco CIDR associados são mostrados.
5. Clique em **Enviar**.
