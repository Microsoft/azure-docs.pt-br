---
title: "Proteger dados pessoais com os recursos de segurança de rede do Azure | Microsoft Docs"
description: "Proteger dados pessoais usando os recursos de segurança de rede do Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: d61b29f1327f57bc32b2c53de3fe58e53fcf3cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>Proteger dados pessoais com os recursos de segurança de rede: Gateway de Aplicativo do Azure e Grupos de Segurança de Rede

Este artigo fornece informações e procedimentos que ajudarão você a usar o Gateway de Aplicativo do Azure e os Grupos de Segurança de Rede para proteger dados pessoais.

Um elemento importante em uma estratégia de segurança de várias camadas para proteger a privacidade de dados pessoais é uma proteção contra explorações de vulnerabilidade comuns, como injeção de SQL ou script entre sites. Manter o tráfego de rede indesejado fora da rede virtual do Azure ajuda a proteger contra o comprometimento potencial de dados confidenciais e o Microsoft Azure fornece as ferramentas para ajudar a proteger seus dados contra invasores.

## <a name="scenario"></a>Cenário

Uma empresa de cruzeiro de grande porte, com sede nos Estados Unidos, está expandindo suas operações para oferecer roteiros nos mares Mediterrâneo, Adriático e Báltico, bem como nas Ilhas Britânicas. Em apoio a esses esforços, ela adquiriu várias linhas de cruzeiro menores localizadas na Itália, na Alemanha, na Dinamarca e no Reino Unido.

A empresa usa o Microsoft Azure para armazenar dados corporativos na nuvem e executar aplicativos em máquinas virtuais que processam e acessam esses dados. Esses dados incluem informações de identificação pessoal, como nomes, endereços, números de telefone e informações de cartão de crédito de sua base global de clientes. Também incluem informações tradicionais de Recursos Humanos, como endereços, números de telefone, números de identificação fiscal e outras informações sobre os funcionários da empresa em todas as localizações. A linha de cruzeiro também mantém um banco de dados grande de membros do programa de recompensa e fidelidade que inclui informações pessoais para acompanhamento das relações com os clientes atuais e anteriores.

Os funcionários corporativos acessam a rede nos escritórios remotos da empresa e os agentes de viagem localizados no mundo todo têm acesso a alguns recursos da empresa e usam aplicativos baseados na Web hospedados em VMs do Azure para interagir com eles.

## <a name="problem-statement"></a>Problema declarado

A empresa deve proteger a privacidade dos dados pessoais de clientes e funcionários contra invasores que exploram vulnerabilidades de software para executar um código mal-intencionado que pode expor os dados pessoais armazenados ou usados pelos aplicativos baseados em nuvem da empresa.

## <a name="company-goal"></a>Meta da empresa

A meta da empresa é garantir que pessoas não autorizadas não possam acessar as Redes Virtuais corporativas do Azure e os aplicativos e dados que residem nelas explorando vulnerabilidades comuns. 

## <a name="solutions"></a>Soluções

O Microsoft Azure fornece mecanismos de segurança para ajudar a impedir que o tráfego indesejado entre nas Redes Virtuais do Azure. O controle do tráfego de entrada e saída é feito tradicionalmente por firewalls. No Azure, use o Gateway de Aplicativo com o Firewall do Aplicativo Web e NSGs (Grupos de Segurança da Rede), que atuam como um firewall distribuído simples. Essas ferramentas permitem detectar e bloquear o tráfego de rede indesejado.

### <a name="application-gatewayweb-application-firewall"></a>Gateway de Aplicativo/Firewall do Aplicativo Web

O componente [WAF](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Firewall do Aplicativo Web) do [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) protege os aplicativos Web, que são alvos cada vez mais frequentes de ataques mal-intencionados que exploram vulnerabilidades comuns conhecidas. Um WAF centralizado protege contra ataques da Web e simplifica o gerenciamento de segurança sem a necessidade de nenhuma alteração no aplicativo.

O WAF do Azure lida com várias categorias de ataque, incluindo injeção de SQL, script entre sites, violações de protocolo HTTP e anomalias, bots, rastreadores, scanners, erros de configuração comuns de aplicativo, Negação de Serviço HTTP e outros ataques comuns como injeção de comando, solicitação HTTP indesejada, separação de resposta HTTP e ataques de inclusão de arquivo remoto. 

Crie um gateway de aplicativo com o WAF ou adicione o WAF a um gateway de aplicativo existente. Em ambos os casos, o Gateway de Aplicativo do Azure exige sua própria sub-rede.

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>Como fazer para criar um gateway de aplicativo com o WAF? 

Para criar um novo gateway de aplicativo com o WAF habilitado, faça o seguinte:

1. Faça logon no portal do Azure e, no painel **Favoritos** do portal, clique em **Novo**

2. Na folha **Novo**, clique em **Rede**.

3. Clique em **Gateway de Aplicativo**.

4. Navegue para o portal do Azure, **clique em Novo \> Rede \> Gateway de Aplicativo.**

   ![criando gateways de aplicativo](media/protect-netsec/app-gateway-01.png)

5. Na folha **Informações Básicas** exibida, insira os valores para os seguintes campos: Nome, Camada (Standard ou WAF), Tamanho de SKU (Pequeno, Médio ou Grande), Contagem de instâncias (2 para alta disponibilidade), Assinatura, Grupo de recursos e Local.

6. Na folha **Configurações** exibida em **Rede virtual**, clique em **Escolher uma rede virtual**. Essa etapa abre a folha Escolher rede virtual.

7. Clique em **Criar nova** para abrir a folha **Criar rede virtual**.

8. Insira os seguintes valores: Nome, Espaço de endereço, Nome da sub-rede, Intervalo de endereços da sub-rede. Clique em **OK**.

9. Na folha **Configurações** em **Configuração de IP de front-end**, escolha o tipo de endereço IP.

10. Clique em **Escolher um endereço IP público** e, em seguida, **Criar novo.**

11. Aceite o valor padrão e clique em **OK.**

12. Na folha **Configurações** em **Configuração do ouvinte**, selecione para usar HTTP ou HTTPS em **Protocolo**. Para usar HTTPS, é necessário ter um certificado.

13. Defina as configurações específicas do WAF: **Status do firewall** (**Habilitado**) e **Modo de firewall** (**Prevenção**). Se você escolher **Detecção** como o modo, o tráfego somente será registrado.

14. Examine a página **Resumo** e clique em **OK**. Agora o Gateway de Aplicativo está na fila e será criado.

Depois que o gateway de aplicativo for criado, navegue para ele no portal e continue a configuração do gateway de aplicativo.

![gateway de aplicativo criado](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>Como fazer para adicionar o WAF a um aplicativo existente?

Para atualizar um gateway de aplicativo existente para dar suporte ao WAF no modo de prevenção, faça o seguinte:

1. No painel **Favoritos** do portal do Azure, clique em **Todos os recursos**.

2. Clique no Gateway de Aplicativo existente, na folha **Todos os recursos**. 
>[!NOTE]
Observação: se a assinatura selecionada já contiver vários recursos, insira o nome na caixa Filtrar por nome... para acessar a Zona DNS facilmente.
3. Clique em **Firewall do aplicativo Web** e atualize as configurações do gateway de aplicativo: **Fazer Upgrade para a Camada do WAF** (marcado), **Status do firewall** (habilitado), **Modo de firewall** (Prevenção). Você também precisa configurar o conjunto de regras e configurar as regras desabilitadas.

Para obter informações mais detalhadas sobre como criar um novo gateway de aplicativo com o WAF e como adicionar o WAF a um gateway de aplicativo existente, consulte [Criar um gateway de aplicativo com o firewall do aplicativo Web usando o portal.](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

### <a name="network-security-groups"></a>Grupos de segurança de rede

Um [NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (grupo de segurança de rede) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede aos recursos conectados a [VNet](https://docs.microsoft.com/azure/virtual-network/) (Rede Virtual) do Azure. Os NSGs podem ser associados a sub-redes ou a VMs individuais. Quando um NSG está associado a uma sub-rede, as regras se aplicam a todos os recursos conectados à sub-rede. O tráfego pode ser restritao também associando um NSG a uma VM ou NIC.

Os NSGs contêm quatro propriedades: Nome, Região, Grupo de recursos e Regras.
>[!Note]
Embora um NSG exista em um grupo de recursos, ele pode ser associado a recursos em qualquer grupo de recursos, desde que o recurso faça parte da mesma região do Azure que o NSG.

As regras do NSG contêm nove propriedades: Nome, Protocolo (TCP, UDP ou \*, que inclui o ICMP, bem como o TCP e UDP), Intervalo de portas de origem, Intervalo de portas de destino, Prefixo do endereço de origem, Prefixo do endereço de destino, Direção (entrada ou saída), Prioridade (entre 100 e 4096) e Tipo de acesso (permitir ou negar). Todos os NSGs contêm um conjunto de regras padrão que pode ser excluído ou substituído pelas regras criadas.

#### <a name="how-do-i-implement-nsgs"></a>Como fazer para implementar os NSGs?

A implementação dos NSGs exige planejamento e há várias considerações sobre design que é necessário levar em conta. Elas incluem limites no número de NSGs por assinatura e regras por NSG; design de VNet e sub-rede, regras especiais, tráfego do ICMP, isolamento de camadas com sub-redes, balanceadores de carga e muito mais.

Para obter mais diretrizes de planejamento e implementação dos NSGs e um cenário de implantação de exemplo, consulte [Filtrar o tráfego de rede com grupos de segurança de rede.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)

#### <a name="how-do-i-create-rules-in-an-nsg"></a>Como fazer para criar regras em um NSG?

Para criar regras de entrada em um NSG existente, faça o seguinte:

1. Clique em **Procurar** e, em seguida, em **Grupos de segurança de rede**.

2. Na lista de NSGs, clique em **NSG-FrontEnd** e, em seguida, em **Regras de segurança de entrada.**

3. Na lista de Regras de segurança de entrada, clique em **Adicionar.**

4. Insira os valores nos seguintes campos: Nome, Prioridade, Origem, Protocolo, Intervalo de origem, Destino, Intervalo de portas de destino e Ação.

A nova regra será exibida no NSG após alguns segundos.

![regras de segurança de rede](media/protect-netsec/inbound-security.png)

Para obter mais instruções sobre como criar NSGs em sub-redes, criar regras e associar um NSG a uma sub-rede front-end e back-end, consulte [Criar grupos de segurança de rede usando o portal do Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)

## <a name="next-steps"></a>Próximas etapas

[Segurança de rede do Azure](https://azure.microsoft.com/blog/azure-network-security/)

[Melhores práticas de segurança de rede do Azure](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[Obter informações sobre um grupo de segurança de rede](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[WAF (Firewall do aplicativo Web)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
