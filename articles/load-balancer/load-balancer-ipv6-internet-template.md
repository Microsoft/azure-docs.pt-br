---
title: Implantar um balanceador de carga voltado para a Internet com o IPv6 – Modelo do Azure
titlesuffix: Azure Load Balancer
description: Como implantar o suporte a IPv6 para o Azure Load Balancer e VMs com balanceamento de carga.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: ipv6, azure load balancer, pilha dual, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4a8c7309a07238ef3410e42c3d631ad525f023cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216710"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Implantar uma solução de balanceamento de carga voltada para a Internet com IPv6 usando um modelo

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [CLI do Azure](load-balancer-ipv6-internet-cli.md)
> * [Modelo](load-balancer-ipv6-internet-template.md)



Um Azure Load Balancer é um balanceador de carga de Camada 4 (TCP, UDP). O balanceador de carga fornece alta disponibilidade, distribuindo o tráfego de entrada entre instâncias do serviço de integridade em serviços de nuvem ou máquinas virtuais em um conjunto de balanceadores de carga. O Azure Load Balancer também pode apresentar esses serviços em várias portas, vários endereços IP ou ambos.

## <a name="example-deployment-scenario"></a>Exemplo de cenário de implantação

O diagrama a seguir ilustra a solução de balanceamento de carga que está sendo implantada usando o exemplo de modelo descrito neste artigo.

![Cenário com o balanceador de carga](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Nesse cenário, você criará os seguintes recursos do Azure:

* uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos
* um balanceador de carga voltado para a Internet com um IPv4 e um endereço IP público IPv6
* duas regras de balanceamento de carga para mapear os VIPs públicos para os pontos de extremidade privados
* um Conjunto de disponibilidade que contém as duas VMs
* duas VMs (máquinas virtuais)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implantar o modelo usando o Portal do Azure

Este artigo faz referência a um modelo publicado na galeria [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) . Você pode baixar o modelo da galeria ou iniciar a implantação no Azure diretamente da Galeria. Este artigo pressupõe que você baixou o modelo em seu computador local.

1. Abra o Portal do Azure e entre com uma conta que tenha permissões para criar VMs e recursos de rede dentro de uma assinatura do Azure. Além disso, a menos que você esteja usando recursos existentes, a conta precisará de permissão para criar um grupo de recursos e uma conta de armazenamento.
2. Clique em "+Novo" partir do menu e digite "modelo" na caixa de pesquisa. Selecione a “Implantação do modelo” nos resultados da pesquisa.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Na folha Tudo, clique em "Implantação de modelo".

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Clique em "Criar".

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Clique em "Editar modelo". Exclua o conteúdo existente e copie/cole todo o conteúdo do arquivo de modelo (para incluir o início e término {}) e clique em "Salvar".

    > [!NOTE]
    > Se você estiver usando o Microsoft Internet Explorer, quando você colar receberá uma caixa de diálogo solicitando sua permissão para acessar a área de transferência do Windows. Clique em "Permitir acesso".

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Clique em "Editar parâmetros". Na folha Parâmetros, especifique os valores de acordo com as diretrizes na seção Parâmetros de modelo, clique em "Salvar" para fechar a folha Parâmetros. Na folha Implantação personalizada, selecione sua assinatura, um grupo de recursos existente ou crie um. Se você estiver criando um grupo de recursos, selecione um local para o grupo de recursos. Em seguida, clique em **Termos legais**, em **Compra** para os termos legais. O Azure começa a implantar os recursos. Demora vários minutos para implantar todos os recursos.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Para saber mais sobre esses parâmetros, consulte a seção [Variáveis e parâmetros de modelo](#template-parameters-and-variables) mais adiante neste artigo.

7. Para ver os recursos criados pelo modelo, clique em Procurar, role para baixo na lista até que você veja "Grupos de recursos", e clique nele.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Na folha Grupos de recursos, clique no nome do grupo de recursos especificado na etapa 6. Consulte uma lista de todos os recursos que foram implantados. Caso tudo tenha corrido bem, deverá aparecer "Êxito" em "Última implantação". Caso contrário, certifique-se de que a conta que você está usando tenha permissões para criar os recursos necessários.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Se você procurar os Grupos de recursos imediatamente após a conclusão da etapa 6, "Última implantação" exibirá o status de "Implantação" durante a implantação dos recursos.

9. Clique em "myIPv6PublicIP" na lista de recursos. Você verá que há um endereço IPv6 no endereço IP, e seu nome DNS é o valor especificado para o parâmetro dnsNameforIPv6LbIP na etapa 6. Este recurso é o endereço IPv6 público e nome do host que está acessível para clientes da Internet.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Validar a conectividade

Quando o modelo tiver sido implantado com êxito, você poderá validar a conectividade concluindo às seguintes tarefas:

1. Entre no Portal do Azure e conecte-se a cada uma das VMs criadas pela implantação do modelo. Se você implantou uma VM do Windows Server, execute ipconfig /all a partir de um prompt de comando. Você vê que as VMs têm endereços IPv4 e IPv6. Se você implantou as VMs do Linux, precisará configurar o sistema operacional Linux para receber endereços IPv6 dinâmicos usando as instruções fornecidas para sua distribuição do Linux.
2. Em um cliente conectado à Internet IPv6, inicie uma conexão com o endereço IPv6 público do balanceador de carga. Para confirmar se o balanceador de carga está balanceando entre as duas VMs, instale um servidor Web como o Microsoft Internet Information Services (IIS) em cada uma das VMs. A página Web padrão em cada servidor pode conter o texto "Server0" ou "Server1" para identificá-lo exclusivamente. Em seguida, abra um navegador da Internet em um cliente conectado à Internet IPv6 e navegue até o nome de host especificado para o parâmetro dnsNameforIPv6LbIP do balanceador de carga para confirmar a conectividade IPv6 de ponta a ponta para cada VM. Se você vir a página Web apenas de apenas um servidor, será necessário limpar o cache do navegador. Abra várias sessões de navegação privadas. Você verá uma resposta de cada servidor.
3. Em um cliente conectado à Internet IPv4, inicie uma conexão com o endereço IPv4 público do balanceador de carga. Para confirmar que o balanceador de carga está balanceando a carga das duas VMs, teste usando o IIS, conforme detalhado na Etapa 2.
4. De cada VM, inicie uma conexão de saída para um dispositivo de Internet conectado a IPv4 ou IPv6. Em ambos os casos, o IP de origem visto pelo dispositivo de destino é o endereço público IPv4 ou IPv6 do balanceador de carga.

> [!NOTE]
> O ICMP para IPv4 e IPv6 está bloqueado na rede do Azure. Como resultado, as ferramentas ICMP, como ping, sempre falham. Para testar a conectividade, use uma alternativa TCP como TCPing ou o cmdlet do PowerShell Test-NetConnection. Observe que os endereços IP mostrados no diagrama são exemplos de valores que você pode ver. Como os endereços IPv6 são atribuídos dinamicamente, os endereços que você receber serão diferentes e podem variar por região. Além disso, é comum que o endereço IPv6 público no balanceador de carga para comece com um prefixo diferente dos endereços IPv6 particulares no pool de back-end.

## <a name="template-parameters-and-variables"></a>Variáveis e parâmetros de modelo

Um modelo do Resource Manager contém diversas variáveis e parâmetros que você pode personalizar de acordo com suas necessidades. Variáveis são usadas para valores fixos os quais você não quer que um usuário altere. Os parâmetros são usados para valores os quais você deseja que um usuário forneça ao implantar o modelo. O modelo de exemplo está configurado para o cenário descrito neste artigo. Você pode personalizá-lo de acordo com as necessidades do seu ambiente.

O modelo de exemplo usado neste artigo inclui as variáveis e parâmetros a seguir:

| Parâmetro/variável | Observações |
| --- | --- |
| adminUsername |Especifique o nome da conta de administrador usada para entrar nas máquinas virtuais. |
| adminPassword |Especifique a senha da conta de administrador usada para entrar nas máquinas virtuais. |
| dnsNameforIPv4LbIP |Especifique o nome de host DNS que você deseja atribuir como o nome público do balanceador de carga. Esse nome resolve para endereço de IPv4 públicos do balanceador de carga. O nome deve ser minúsculo e corresponder ao regex:^[a-z] [a-z0-9 -]{1,61}[a-z0-9] $. |
| dnsNameforIPv6LbIP |Especifique o nome de host DNS que você deseja atribuir como o nome público do balanceador de carga. Esse nome resolve para endereço de IPv6 públicos do balanceador de carga. O nome deve ser minúsculo e corresponder ao regex:^[a-z] [a-z0-9 -]{1,61}[a-z0-9] $. Pode ser o mesmo nome que do endereço IPv4. Quando um cliente envia uma consulta DNS para esse nome, o Azure retorna os registros A e AAAA quando o nome é compartilhado. |
| vmNamePrefix |Especifique o prefixo do nome VM. O modelo inclui um número (0, 1 etc.) ao nome quando as VMs são criadas. |
| nicNamePrefix |Especifique o prefixo de nome da interface de rede. O modelo inclui um número (0, 1 etc.) ao nome quando as interfaces de rede são criadas. |
| storageAccountName |Insira o nome de uma conta de armazenamento existente ou especifique o nome de uma nova que será criada pelo modelo. |
| availabilitySetName |Insira nome do conjunto de disponibilidade a ser usado com as VMs |
| addressPrefix |O prefixo de endereço usado para definir o intervalo de endereços da rede virtual |
| subnetName |O nome da sub-rede criada para a VNet |
| subnetPrefix |O prefixo de endereço usado para definir o intervalo de endereços da sub-rede |
| vnetName |Especifique o nome para a VNet usada pelas VMs. |
| ipv4PrivateIPAddressType |O método de alocação usado para o endereço IP privado (estático ou dinâmico) |
| ipv6PrivateIPAddressType |O método de alocação usado para o endereço IP privado (dinâmico). IPv6 só dá suporte à alocação dinâmica. |
| numberOfInstances |O número de instâncias com balanceamento de carga implantadas pelo modelo |
| ipv4PublicIPAddressName |Especifique o nome DNS que você quer usar para se comunicar com o endereço IPv4 público do balanceador de carga. |
| ipv4PublicIPAddressType |O método de alocação usado para o endereço IP público (estático ou dinâmico) |
| Ipv6PublicIPAddressName |Especifique o nome DNS que você quer usar para se comunicar com o endereço IPv6 público do balanceador de carga. |
| ipv6PublicIPAddressType |O método de alocação usado para o endereço IP público (dinâmico). IPv6 só dá suporte à alocação dinâmica. |
| lbName |Especifique o nome do balanceador de carga. Esse nome é exibido no portal ou usado para se referir a ele com um comando da CLI ou do PowerShell. |

As variáveis restantes no modelo contêm valores derivados atribuídos quando o Azure cria os recursos. Não altere essas variáveis.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a sintaxe e as propriedades JSON de um balanceador de carga em um modelo em [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
