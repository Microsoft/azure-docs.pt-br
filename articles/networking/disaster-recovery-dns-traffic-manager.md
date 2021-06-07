---
title: Recuperação de desastre usando o DNS do Azure e o Gerenciador de Tráfego | Microsoft Docs
description: Visão geral das soluções de recuperação de desastre usando o DNS do Azure e o Gerenciador de Tráfego.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.author: kumud
ms.openlocfilehash: 077e92b67f0cf6dac673cc870b7ff8c86fbe60dd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551281"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Recuperação de desastres usando o DNS do Azure e o Gerenciador de Tráfego

A recuperação de desastre tem como foco a recuperação após uma perda grave de funcionalidade do aplicativo. Para escolher uma solução de recuperação de desastre, os proprietários de negócios e tecnologia devem primeiro determinar o nível de funcionalidade que é necessário durante um desastre, como: não disponível, parcialmente disponível por meio de funcionalidade reduzida ou disponibilidade atrasada, ou totalmente disponível.
A maioria das empresas estão escolhendo uma arquitetura de várias regiões para proporcionar resiliência em relação a um aplicativo ou um failover de nível de infraestrutura. Os clientes podem escolher várias abordagens na busca para alcançar alta disponibilidade por meio de arquitetura de redundância e failover. Aqui estão algumas das abordagens mais populares:

- **Ativo-passivo com espera passiva**: nesta solução de failover, as VMs e outros dispositivos em execução na região de espera não ficam ativos até que haja uma necessidade de failover. No entanto, o ambiente de produção é replicado em forma de backups, imagens VM ou modelos do Resource Manager em uma região diferente. Esse mecanismo de failover é econômico, mas leva mais tempo para realizar um failover completo.
 
    ![Ativo/Passivo com espera passiva](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Figura - Ativo/Passivo com configuração de recuperação de desastre de espera passiva*

- **Ativo/Passivo com luz piloto**: Nesta solução de failover, o ambiente de espera é configurado com uma configuração mínima. A instalação tem os serviços necessários em execução para dar suporte a apenas um conjunto mínimo e crítico de aplicativos. Em seu formato nativo, esse cenário pode apenas executar a funcionalidade mínima, mas pode escalar verticalmente e gerar mais serviços para assumir uma grande quantidade da carga de produção caso ocorra um failover.
    
    ![Ativo/Passivo com luz piloto](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Figura: Ativo/Passivo com configuração de recuperação de desastre de luz piloto*

- **Ativo/Passivo com espera passiva**: nesta solução de failover, a região em espera fica preparada e pronta para assumir a carga base, o dimensionamento automático fica ativado e todas as instâncias começam a ser executadas. Essa solução não é dimensionada para assumir toda a carga de produção, mas é funcional, e todos os serviços ficam em execução. Essa solução é uma versão mais completa da abordagem de luz piloto.
    
    ![Ativo/Passivo com espera ativa](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Figura: Ativo/Passivo com configuração de recuperação de desastre de espera ativa*
    
Para saber mais sobre failover e alta disponibilidade, consulte [Recuperação de desastre para Aplicativos Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planejar a sua arquitetura de recuperação de desastre

Há dois aspectos técnicos para a configuração de sua arquitetura de recuperação de desastre:
-  Usar um mecanismo de implantação para replicar instâncias, dados e configurações entre ambientes primários e de espera. Esse tipo de recuperação de desastre pode ser feito nativamente por meio do Azure Site Recovery através de dispositivos/serviços parceiros do Microsoft Azure como Veritas ou NetApp. 
- Desenvolver uma solução para desviar o tráfego de rede/da Web do site primário para o site em espera. Esse tipo de recuperação de desastre pode ser obtido por meio do DNS do Azure, Gerenciador de Tráfego do Azure (DNS) ou balanceadores de carga global de terceiros.

Este artigo é limitado a abordagens por meio de redirecionamento do tráfego de rede e da Web. Para obter instruções sobre a configuração do Azure Site Recovery, confira a [Documentação do Azure Site Recovery](../site-recovery/index.yml).
O DNS é um dos mecanismos mais eficientes para desviar o tráfego de rede porque o DNS normalmente é global e externo para o data center e é isolado de quaisquer falhas no nível de zona de disponibilidade (AZ) ou regional. É possível usar um mecanismo de failover com base no DNS e no Azure, dois serviços DNS podem fazer o mesmo de certa maneira - o DNS do Azure DNS (DNS autoritativo) e o Gerenciador de Tráfego do Azure (roteamento de tráfego inteligente baseado em DNS). 

É importante compreender alguns conceitos no DNS que são amplamente usados para discutir as soluções fornecidas neste artigo:
- **Registro A de DNS** – Registros A são ponteiros que apontam um domínio para um endereço IPv4. 
- **Nome CNAME ou Canonical** - Este tipo de registro é usado para apontar para outro registro de DNS. CNAME não responde com um endereço IP, mas com o ponteiro para o registro que contém o endereço IP. 
- **Roteamento ponderado** – É possível escolher associar um peso para pontos de extremidade de serviço e, em seguida, distribuir o tráfego com base nos níveis de importância atribuídos. Esse método de roteamento é um dos quatro mecanismos de roteamento de tráfego disponíveis no Gerenciador de Tráfego. Para obter mais informações, consulte [Método de roteamento ponderado](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Roteamento prioritário** – O roteamento prioritário é baseado em verificações de integridade de pontos de extremidade. Por padrão, o Gerenciador de Tráfego do Azure envia todo o tráfego para o ponto de extremidade de prioridade mais alta e, após uma falha ou desastre, o Gerenciador de Tráfego roteia o tráfego para o ponto de extremidade secundário. Para obter mais informações, consulte [Método de roteamento prioritário](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Failover manual usando o DNS do Azure
A solução de failover manual do DNS do Azure para recuperação de desastre usa o mecanismo DNS padrão para fazer o failover no site de backup. A opção manual por meio do DNS do Azure funciona melhor quando usada em conjunto com as abordagens de espera passiva ou de luz piloto. 

![Failover manual usando o DNS do Azure](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Figura - Failover Manual usando o DNS do Azure*

As suposições feitas para a solução são:
- Os pontos de extremidade primários e secundários têm IPs estáticos que não são alterados com frequência. Digamos que para o site primário, o IP é 100.168.124.44 e o IP para o site secundário é 100.168.124.43.
- Existe uma zona DNS do Azure para o site primário e secundário. Digamos que, para o site primário o ponto de extremidade é prod.contoso.com e para o site de backup é dr.contoso.com. Também existe um registro DNS para o aplicativo principal, conhecido como www\.contoso.com.   
- O TTL está no limite ou abaixo do SLA de RTO definido na organização. Por exemplo, se uma empresa define o RTO da resposta de desastre do aplicativo para ser de 60 minutos, então o valor TTL deve ser menor que 60 minutos, preferencialmente quanto menor, melhor. 
  Você pode configurar o DNS do Azure para failover manual da seguinte maneira:
- Criar uma zona DNS
- Criar registros de zona DNS
- Atualizar um registro CNAME

### <a name="step-1-create-a-dns"></a>Etapa 1: Criar um DNS
Crie uma zona DNS (por exemplo, www\.contoso.com), conforme mostrado abaixo:

![Criar uma zona DNS no Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Figura - Criar uma zona DNS no Azure*

### <a name="step-2-create-dns-zone-records"></a>Etapa 2: Criar registros de zona DNS

Dentro dessa zona, crie três registros (por exemplo, www\.contoso.com, prod.contoso.com e dr.consoto.com) conforme mostrado abaixo.

![Criar registros de zona DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Figura - Criar registros de zona DNS no Azure*

Nesse cenário, o site www\.contoso.com tem um TTL de 30 minutos, que é bem abaixo do RTO indicado e está apontando para o site de produção prod.contoso.com. Essa configuração é aplicável durante operações normais de negócios. O TTL de prod.contoso.com e dr.contoso.com foi definido para 300 segundos ou 5 minutos. Você pode usar um serviço de monitoramento do Azure, como o Azure Monitor ou Azure App Insights ou qualquer solução de monitoramento de parceiro, como Dynatrace. Você pode até mesmo usar soluções de internas que possam monitorar ou detectar falhas de nível de infraestrutura virtual ou de aplicativo.

### <a name="step-3-update-the-cname-record"></a>Etapa 3: Atualizar o registro CNAME

Depois da falha ser detectada, altere o valor de registro para apontar para dr.contoso.com, conforme mostrado abaixo:
       
![Atualizar um registro CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Figura - Atualizar o registro CNAME no Azure*

Em até 30 minutos, durante os quais a maioria dos resolvedores atualizará o arquivo de zonas armazenadas em cache, qualquer consulta ao www\.contoso.com será redirecionada para dr.contoso.com.
Você também pode executar o seguinte comando na CLI do Azure para alterar o valor CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Essa etapa pode ser executada manualmente ou por meio de automação. Ela pode ser feito manualmente por meio do console ou pela CLI do Azure. O SDK do Azure e a API podem ser usados para automatizar a atualização CNAME para que nenhuma intervenção manual seja necessária. A automação pode ser criada por meio de funções do Azure ou em um aplicativo de monitoramento de terceiros ou até mesmo localmente.

### <a name="how-manual-failover-works-using-azure-dns"></a>Como o failover manual funciona com o DNS do Azure
Como o servidor DNS está fora da zona de failover ou desastre, ele é isolado contra qualquer tempo de inatividade. Isso permite ao usuário projetar um cenário de failover simples que seja econômico e funcionará o tempo inteiro supondo que o operador tenha conectividade de rede durante um desastre e possa fazer a inversão. Se a solução for inserida no script, então é necessário garantir que o servidor ou o serviço que executa o script seja isolado contra o problema que afeta o ambiente de produção. Além disso, tenha em mente o TTL baixo que foi definido em relação à zona para que nenhum resolvedor ao redor do mundo mantenha o ponto de extremidade armazenado em cache por muito tempo e para que os clientes possam acessar o site dentro do RTO. Para uma espera passiva e luz piloto, já que algumas atividades preparativas e de preparação podem ser necessárias, também deve ser dado tempo suficiente antes de se fazer a inversão.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Failover automático usando o Gerenciador de Tráfego do Azure
Quando você tiver arquiteturas complexas e vários conjuntos de recursos capazes de executar a mesma função, você pode configurar o Gerenciador de Tráfego do Azure (com base no DNS) para verificar a integridade de seus recursos e rotear o tráfego do recurso não íntegro para o recurso íntegro. No exemplo a seguir, a região primária e a região secundária têm uma implantação completa. Essa implantação inclui os serviços de nuvem e um banco de dados sincronizado. 

![Failover automático usando o Gerenciador de Tráfego do Azure](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Figura - Failover automático usando o Gerenciador de Tráfego do Azure*

No entanto, somente a região primária processa ativamente solicitações de rede dos usuários. A região secundária se torna ativa apenas quando a região primária apresentar uma interrupção do serviço. Nesse caso, todas as novas solicitações de rede são encaminhadas para a região secundária. Como o backup do banco de dados é quase instantâneo, ambos os balanceadores de carga possuem IPs que podem ter a integridade verificada, e as instâncias estão sempre em execução, essa topologia oferece uma opção para um RTO baixo e failover sem nenhuma intervenção manual. A região de failover secundária deve estar pronta para entrar em atividade imediatamente após a falha da região primária.
Este cenário é ideal para o uso do Gerenciador de Tráfego do Azure que tenha investigações incorporadas para vários tipos de verificações de integridade, incluindo http / https e TCP. O Gerenciador de Tráfego do Azure também tem um mecanismo de regras que pode ser configurado para failover quando ocorre uma falha, conforme descrito abaixo. Vamos considerar a seguinte solução usando o Gerenciador de Tráfego:
- O cliente tem o ponto de extremidade da Região nº1 conhecido como prod.contoso.com com um endereço IP estático de 100.168.124.44 e um ponto de extremidade da Região nº2 conhecido como dr.contoso.com com um endereço IP estático de 100.168.124.43. 
-   Cada um desses ambientes é apoiado por meio de uma propriedade pública como um balanceador de carga. O balanceador de carga pode ser configurado para ter um ponto de extremidade com base em DNS ou um nome de domínio totalmente qualificado (FQDN), como mostrado acima.
-   Todas as instâncias na Região 2 possuem replicação quase em tempo real em relação à Região 1. Além disso, as imagens de computadores estão atualizadas e todos os dados de configuração/software tem os patches aplicados e estão de acordo com a Região 1.  
-   O dimensionamento automático é pré-configurado antes. 

As etapas para configurar o failover com o Gerenciador de Tráfego do Azure são as seguintes:
1. Criar um novo perfil do Gerenciador de Tráfego do Azure
2. Criar pontos de extremidade no perfil do Gerenciador de Tráfego
3. Definir a configuração de failover e verificação de integridade

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Etapa 1: Criar um novo perfil do Gerenciador de Tráfego do Azure
Criar um novo perfil do Gerenciador de Tráfego do Azure com o nome contoso123 e selecione o Método de roteamento como Prioritário. Se você tiver um grupo de recursos já existente que você deseja associar, então você pode selecionar um grupo de recursos existente, caso contrário, crie um novo grupo de recursos.

![Criar perfil do Gerenciador de Tráfego](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Figura – Criar um perfil do Gerenciador de Tráfego*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Etapa 2: Criar pontos de extremidade no perfil do Gerenciador de Tráfego

Nesta etapa, você cria pontos de extremidade que apontam para os sites de produção e de recuperação de desastre. Aqui, escolha o **Tipo** como um ponto de extremidade externo, mas se o recurso for hospedado no Azure, então você pode escolher o **Ponto de extremidade do Azure** também. Se você escolher **Ponto de extremidade do Azure**, então selecione um **Recurso de destino** que seja um **Serviço de Aplicativo** ou um **IP público** que está alocado por Azure. A prioridade é definida como **1** já que é o principal serviço para a Região 1.
Da mesma forma, crie o ponto de extremidade de recuperação de desastre no Gerenciador de Tráfego.

![Criar pontos de extremidade de recuperação de desastre](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Figura - Criar pontos de extremidade de recuperação de desastre*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Etapa 3: Definir a configuração de failover e verificação de integridade

Nesta etapa, você define o TTL do DNS para 10 segundos, que é cumprido por resolvedores recursivos voltados para a Internet. Essa configuração significa que o resolvedor de DNS não armazenará as informações em cache por mais de 10 segundos. Para as configurações do monitor de ponto de extremidade, o caminho é atualmente definido para / ou raiz, mas você pode personalizar as configurações de ponto de extremidade para avaliar um caminho, por exemplo, prod.contoso.com/index. O exemplo a seguir mostra o **https** como o protocolo de investigação. No entanto, você também pode escolher **http** ou **tcp**. A opção de protocolo depende do aplicativo final. O intervalo de investigação é definido como 10 segundos, que permite uma rápida investigação e a repetição é definida como 3. Como resultado, o Gerenciador de Tráfego fará o failover no segundo ponto de extremidade se três intervalos consecutivos registrarem uma falha. A fórmula a seguir define o tempo total para um failover automático: Tempo de failover = TTL + Repetição * Intervalo de investigação e nesse caso, o valor é 10 + 3 * 10 = 40 segundos (Máx).
Se Repetição estiver definida como 1 e o TTL estiver definido como 10 segundos, então o tempo para failover é de 10 + 1 * 10 = 20 segundos. Defina a Repetição para um valor maior que **1** para eliminar a possibilidade de failovers devido a falsos positivos ou quaisquer perturbarções de rede secundária. 


![Configurar verificação de integridade](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Figura - Definir a configuração de failover e verificação de integridade*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Como funciona o failover automático usando o Gerenciador de Tráfego

Durante um desastre, o ponto de extremidade primário é analisado e o status é alterado para **degradado** e o site recuperação de desastre permanece **Online**. Por padrão, o Gerenciador de Tráfego envia todo o tráfego para o ponto de extremidade primário (prioridade mais alta). Se o ponto de extremidade primário aparece como degradado, o Gerenciador de Tráfego roteia o tráfego para o segundo ponto de extremidade desde que ele permaneça íntegro. É possível configurar mais pontos de extremidade no Gerenciador de Tráfego, que podem servir como pontos de extremidade de failover adicionais ou como balanceadores de carga que compartilham a carga entre os pontos de extremidade.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md).
- Saiba mais sobre [DNS do Azure](../dns/dns-overview.md).