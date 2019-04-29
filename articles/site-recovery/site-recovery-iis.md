---
title: Configurar a recuperação de desastres para um aplicativo Web baseado em IIS de várias camadas usando o Azure Site Recovery | Microsoft Docs
description: Saiba como replicar máquinas de virtuais de um farm da Web do IIS usando o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: aa42371692cd5d0dc96835db5b66fe0877b90665
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61280347"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Configurar a recuperação de desastre para um aplicativo Web baseado em IIS de várias camadas

Software de aplicativo é o motor da produtividade comercial em uma organização. Vários aplicativos Web podem servir para propósitos diferentes em uma organização. Alguns aplicativos, como aplicativos usados para processamento da folha de pagamento, aplicativos financeiros e sites voltados ao cliente, podem ser críticos para uma organização. Para evitar perda de produtividade, é importante que a organização tenha esses aplicativos continuamente em execução. Mais importante ainda, ter esses aplicativos consistentemente disponíveis pode ajudar a prevenir danos à marca ou imagem da organização.

Aplicativos Web críticos normalmente são configurados como aplicativos de várias camadas: a Web, o banco de dados e o aplicativo estão em diferentes camadas. Além de espalharem-se por várias camadas, os aplicativos também podem usar vários servidores em cada camada para balancear a carga do tráfego. Além disso, os mapeamentos entre várias camadas e no servidor Web podem estar baseados em endereços IP estáticos. Em failover, alguns desses mapeamentos precisam ser atualizados, especialmente se vários sites estiverem configurados no servidor Web. Se os aplicativos Web utilizarem SSL, será necessário atualizar as associações de certificado.

Métodos de recuperação tradicionais que não são baseados na replicação envolvem o backup de vários arquivos de configuração, configurações de registro, associações, componentes personalizados (COM ou .NET), conteúdo e certificados. Os arquivos são recuperados através de um conjunto de etapas manuais. Os métodos de recuperação tradicionais de backup e recuperação manual de arquivos são complicados, propensos a erros e não escaláveis. Por exemplo, é muito fácil esquecer de fazer backup de certificados. Após o failover, não haverá outra escolha senão comprar novos certificados para o servidor.

Uma boa solução de recuperação de desastres tem suporte para modelos de planos de recuperação para arquiteturas de aplicativo complexo. Além disso, é possível adicionar etapas personalizadas ao plano de recuperação para manipular mapeamentos de aplicativos entre camadas. Se houver um desastre, os mapeamentos de aplicativos fornecem uma solução de único clique segura que ajuda a conduzir um RTO inferior.

Este artigo descreve como proteger um aplicativo Web baseado no IIS (Serviços de Informações da Internet) usando o [Azure Site Recovery](site-recovery-overview.md). O artigo aborda as melhores práticas para replicação de um aplicativo Web baseado no IIS de três camadas para Azure, como fazer uma análise da recuperação de desastres e como fazer failover no aplicativo para Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que você sabe fazer as tarefas a seguir:

* [Replicar uma máquina virtual no Azure](vmware-azure-tutorial.md)
* [Criar uma rede de recuperação](site-recovery-network-design.md)
* [Executar um failover de teste para o Azure](site-recovery-test-failover-to-azure.md)
* [Executar um failover para o Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicar o SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implantação
Um aplicativo Web baseado no IIS normalmente segue um destes padrões de implantação:

**Padrão de implantação 1**

Um web farm baseado no IIS com ARR (Application Request Routing), um servidor IIS e SQL Server.

![Diagrama de um web farm baseado no IIS que tem três camadas](./media/site-recovery-iis/deployment-pattern1.png)

**Padrão de implantação 2**

Um web farm baseado no IIS com ARR, um servidor IIS, um servidor de aplicativos e SQL Server.

![Diagrama de um web farm baseado no IIS que tem quatro camadas](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para os exemplos neste artigo, usamos máquinas virtuais VMware com o IIS 7.5 no Windows Server 2012 R2 Enterprise. Como a replicação do Site Recovery não é específica do aplicativo, as recomendações contidas neste artigo devem ser aplicadas nos cenários listados na tabela a seguir e para diferentes versões do IIS.

### <a name="source-and-target"></a>Origem e destino

Cenário | Para um site secundário | Para o Azure
--- | --- | ---
Hyper-V | Sim | Sim
VMware | Sim | Sim
Servidor físico | Não  | Sim
Azure|ND|Sim

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para iniciar a replicação de todas as máquinas virtuais do web farm do IIS para Azure, siga as orientações em [Failover de teste para Azure no Site Recovery](site-recovery-test-failover-to-azure.md).

Se você estiver usando um endereço IP estático, poderá especificar o endereço IP que você deseja que a máquina virtual use. Para configurar o endereço IP, vá para  **Configurações de Computação e Rede** > **TARGET IP**.

![Captura de tela que mostra como configurar o IP de destino no painel Computação e Rede do Site Recovery](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação dá suporte à sequência de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Quando você criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [Criar um plano de recuperação usando o Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover
Um aplicativo Web do IIS de várias camadas típico consiste nos componentes a seguir:
* Uma camada de banco de dados que possui máquinas virtuais do SQL.
* A camada da web, que consiste em um servidor IIS e uma camada de aplicativo. 

Adicione máquinas virtuais a diferentes grupos com base na camada:

1. Crie um plano de recuperação. Adicione as máquinas virtuais de camada de banco de dados no Grupo 1. Isso garante que as máquinas virtuais de camada de banco de dados sejam desligadas por último e acionadas primeiro.
1. Adicione as máquinas virtuais de camada de aplicativo no Grupo 2. Isso garante que as máquinas virtuais de camada de aplicativo sejam acionadas após o acionamento da camada de banco de dados.
1. Adicione as máquinas virtuais de camada Web no Grupo 3. Isso garante que as máquinas virtuais de camada Web sejam acionadas após o acionamento da camada de aplicativo.
1. Adicione máquinas virtuais de balanceamento de carga no Grupo 4. Isso garante que as máquinas virtuais de balanceamento de carga sejam acionadas após o acionamento da camada Web.

Para obter mais informações, consulte [Personalizar o plano de recuperação](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Adicionar um script ao plano de recuperação
Para que o web farm do IIS funcione corretamente, será necessário fazer algumas operações nas máquinas virtuais do Azure pós-failover ou durante um failover de teste. É possível automatizar algumas operações pós-failover. Por exemplo, você pode atualizar a entrada DNS, alterar uma associação do site ou alterar uma cadeia de conexão, adicionando scripts correspondentes ao plano de recuperação. [Adicionar um script do VMM a um plano de recuperação](site-recovery-how-to-add-vmmscript.md) descreve como configurar tarefas automatizadas usando um script.

#### <a name="dns-update"></a>Atualização de DNS
Se o DNS estiver configurado para atualização dinâmica de DNS, as máquinas virtuais normalmente atualizarão o DNS com o novo endereço IP quando forem iniciadas. Se você quiser adicionar uma etapa explícita para atualizar o DNS com os novos endereços IP das máquinas virtuais, adicione um script [para atualizar o IP no DNS ](https://aka.ms/asr-dns-update) como uma ação pós-failover em grupos de planos de recuperação.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Cadeia de conexão no web.config do aplicativo
A cadeia de conexão especifica o banco de dados com o qual o site comunica-se. Se a cadeia de conexão mantiver o nome da máquina virtual do banco de dados, não serão necessárias etapas adicionais pós-failover. O aplicativo poderá comunicar-se automaticamente com o banco de dados. Além disso, se o endereço IP da máquina virtual do banco de dados for mantido, não será necessário atualizar a cadeia de conexão. 

Se a cadeia de conexão referir-se à máquina virtual do banco de dados usando um endereço IP, será necessário atualizá-la pós-failover. Por exemplo, a seguinte cadeia de conexão aponta para o banco de dados com o endereço IP 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Para atualizar a cadeia de conexão na camada Web, adicione um [script de atualização de conexão do IIS](https://aka.ms/asr-update-webtier-script-classic) após o Grupo 3 no plano de recuperação.

#### <a name="site-bindings-for-the-application"></a>Associações de site para o aplicativo
Cada site consiste em informações de associação. As informações de associação incluem o tipo de associação, o endereço IP no qual o servidor IIS escuta as solicitações do site, o número da porta e os nomes do host para o site. Durante o failover, talvez seja necessário atualizar essas associações se houver uma alteração no endereço IP associado a elas.

> [!NOTE]
>
> Se você definir a associação do site para **Não atribuídas**, não será necessário atualizar essa associação pós-failover. Além disso, se o endereço IP associado a um site não for alterado pós-failover, não será necessário atualizar a associação do site. (A retenção do endereço IP depende da arquitetura de rede e sub-redes atribuídas aos sites primário e de recuperação. Atualizá-los pode não ser viável para sua organização.)

![Captura de tela que mostra a configuração da associação SSL](./media/site-recovery-iis/sslbinding.png)

Se você associou o endereço IP a um site, atualize todas as associações do site com o novo endereço IP. Para alterar as associações do site, adicione um [script de atualização da camada Web do IIS](https://aka.ms/asr-web-tier-update-runbook-classic) após o Grupo 3 no plano de recuperação.

#### <a name="update-the-load-balancer-ip-address"></a>Atualizar o endereço IP do balanceador de carga
Se você tiver uma máquina virtual de ARR, para atualizar o endereço IP adicione um [script de failover de ARR do IIS](https://aka.ms/asr-iis-arrtier-failover-script-classic) após o Grupo 4.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>Associação de certificado SSL para uma conexão HTTPS
Um site pode ter um certificado SSL associado que ajuda a garantir uma comunicação segura entre o servidor Web e o navegador do usuário. Se o site tiver uma conexão HTTPS e uma associação do site HTTPS associada ao endereço IP do servidor IIS com uma associação de certificado SSL, será necessário adicionar uma nova associação do site para o certificado com o endereço IP da máquina virtual do IIS pós-failover.

O certificado SSL pode ser emitido em relação a esses componentes:

* O nome de domínio totalmente qualificado do site.
* O nome do servidor.
* Um certificado curinga para o nome de domínio.  
* Um endereço IP. Se o certificado SSL for emitido em relação ao endereço IP do servidor IIS, outro certificado SSL deverá ser emitido em relação ao endereço IP do servidor IIS no site do Azure. É necessário criar uma associação SSL adicional para esse certificado. Por isso, é recomendável não usar um certificado SSL emitido em relação ao endereço IP. Essa opção é menos utilizada, e em breve será preterida de acordo com as novas alterações de fórum do navegador/autoridade de certificação.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Atualizar a dependência entre a camada Web e a camada de aplicativo
Se você tiver uma dependência específica do aplicativo baseada no endereço IP das máquinas virtuais, será necessário atualizar essa dependência pós-failover.

## <a name="run-a-test-failover"></a>Execute um teste de failover

1. No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
2. Selecione o plano de recuperação que você criou para o web farm do IIS.
3. Selecione **Failover de Teste**.
4. Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5. Quando o ambiente secundário estiver ativado, você poderá realizar validações.
6. Quando as validações forem concluídas, para limpar o ambiente de failover de teste, selecione **Validações concluídas**.

Para obter mais informações, consulte [Failover de teste para Azure no Site Recovery ](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar um failover

1. No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
1. Selecione o plano de recuperação que você criou para o web farm do IIS.
1. Selecione **Failover**.
1. Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover no Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como [replicar outros aplicativos](site-recovery-workload.md) usando o Site Recovery.
