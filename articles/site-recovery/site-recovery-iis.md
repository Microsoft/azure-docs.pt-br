---
title: "Replicar um aplicativo Web baseado em IIS de várias camadas usando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como replicar máquinas de virtuais de um farm da Web do IIS usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: 4ac79df703de00ac009d9845772d8be740e74f29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Replicar um aplicativo Web baseado em IIS de várias camadas usando o Azure Site Recovery

## <a name="overview"></a>Visão geral


Software de aplicativo é o motor da produtividade comercial em uma organização. Vários aplicativos Web podem servir para propósitos diferentes em uma organização. Alguns deles, como processamento de folha de pagamento, aplicativos financeiros e sites voltados para o cliente podem ser essenciais para uma organização. É importante que a organização os tenha sempre em execução para evitar a perda de produtividade e, acima de tudo, evitar danos à imagem da marca da organização.

Os aplicativos Web críticos normalmente são configurados como aplicativos de várias camadas, com a Web, o banco de dados e o aplicativo em camadas diferentes. Além do serem espalhados em várias camadas, os aplicativos também podem usar vários servidores em cada camada a fim de balancear o tráfego. Além disso, os mapeamentos entre várias camadas e no servidor Web podem ter base em endereços IP estáticos. Durante o failover, será necessário atualizar alguns desses mapeamentos, especialmente se houver vários sites configurados no servidor da Web. No caso de aplicativos Web que usam SSL, será necessário atualizar as associações de certificado.

Os métodos tradicionais de recuperação sem base na replicação envolvem o backup de vários arquivos de configuração, configurações do Registro, associações, componentes personalizados (COM ou .NET), conteúdo e também certificados e recuperação dos arquivos por meio de um conjunto de etapas manuais. Está claro que essas técnicas são inconvenientes, propensas a erros e não escalonáveis. É possível, por exemplo, que você se esqueça facilmente de fazer backup de certificados e acabe ficando sem outra opção além de comprar novos certificados para o servidor após o failover.

Uma boa solução de recuperação de desastres deve permitir a modelagem de planos de recuperação em torno das arquiteturas de aplicativo complexas indicadas acima, e também tem a capacidade de adicionar etapas personalizadas para lidar com mapeamentos de aplicativo entre as várias camadas, fornecendo uma solução certeira acionada com um único clique no caso de um desastre resultar em um RTO inferior.


Este artigo descreve como proteger um aplicativo Web baseado em IIS usando o [Azure Site Recovery](site-recovery-overview.md). Este artigo abordará as práticas recomendadas para a replicação de um aplicativo Web baseado em IIS no Azure, como você pode fazer uma análise de recuperação de desastres e como é possível realizar o failover do aplicativo no Azure.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa entender o seguinte:

1. [Replicar uma máquina virtual no Azure](site-recovery-vmware-to-azure.md)
1. Como [criar uma rede de recuperação](site-recovery-network-design.md)
1. [Executar um failover de teste no Azure](./site-recovery-test-failover-to-azure.md)
1. [Executar um failover no Azure](site-recovery-failover.md)
1. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
1. Como [replicar o SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implantação
Um aplicativo Web baseado no IIS normalmente segue um destes padrões de implantação:

**Padrão de implantação 1** Um farm da Web baseado no IIS com ARR (Application Request Routing), o Servidor IIS e o Microsoft SQL Server.

![Padrão de implantação](./media/site-recovery-iis/deployment-pattern1.png)

**Padrão de implantação 2** Um farm da Web baseado no IIS com ARR (Application Request Routing), o Servidor IIS, Servidor de Aplicativos e o Microsoft SQL Server.


![Padrão de implantação](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para a criação deste artigo, usamos as máquinas virtuais VMware com o Servidor IIS versão 7.5 no Windows Server 2012 R2 Enterprise. Como a replicação de recuperação do site é independente do aplicativo, as recomendações fornecidas aqui devem servir para os seguintes cenários, bem como para uma versão diferente do IIS.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Não | Sim

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Siga [este guia](site-recovery-vmware-to-azure.md) para começar a replicar todas as máquinas virtuais do farm da Web do IIS no Azure.

Se você estiver usando um IP estático, especifique o IP que você deseja usar na máquina virtual na configuração [**IP de Destino**](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties), nas configurações de Rede e Computação.

![IP de Destino](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação

Um plano de recuperação permite o sequenciamento do failover de várias camadas em um aplicativo de várias camadas, mantendo assim a consistência do aplicativo. Execute as etapas abaixo ao criar um plano de recuperação para um aplicativo Web de várias camadas.  [Saiba mais sobre a criação de um plano de recuperação](./site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover
Um aplicativo Web comum do IIS de várias camadas será formado por uma camada de banco de dados com máquinas virtuais SQL, pela camada da Web composta por um Servidor IIS, e uma camada de aplicativo. Adicione todas essas máquinas virtuais a outro grupo com base na camada abaixo. [Saiba mais sobre como personalizar o plano de recuperação](site-recovery-runbook-automation.md#customize-the-recovery-plan).

1. Crie um plano de recuperação. Adicione as máquinas virtuais da camada de banco de dados ao Grupo 1 para garantir que elas sejam encerradas por último e ativadas primeiro.

1. Adicione as máquinas virtuais de camada de aplicativo ao Grupo 2, para que elas sejam acionadas após o acionamento da camada de banco de dados.

1. Adicione as máquinas virtuais de camada da Web ao Grupo 3, para que elas sejam acionadas após o acionamento da camada de aplicativo.

1. Adicione as máquinas virtuais de balanceamento de carga ao Grupo 4, para que elas sejam acionadas após o acionamento da camada da Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Talvez seja necessário fazer algumas operações nas máquinas virtuais do Azure após o failover/teste de failover a fim de fazer o farm da Web do IIS funcionar corretamente. Automatize a operação pós-failover, como a atualização da entrada DNS, alteração da associação de site, alteração na cadeia de conexão, adicionando scripts correspondentes no plano de recuperação, conforme mostrado a seguir. [Saiba mais sobre como adicionar script ao plano de recuperação](./site-recovery-create-recovery-plans.md#add-scripts).

#### <a name="dns-update"></a>Atualização de DNS
Se o DNS estiver configurado para atualização dinâmica de DNS, as máquinas virtuais normalmente atualizarão o DNS com o novo IP quando forem iniciadas. Se você quiser adicionar uma etapa explícita para atualizar o DNS com os novos IPs das máquinas virtuais, adicione este [script para atualizar o IP no DNS](https://aka.ms/asr-dns-update) como uma ação posterior nos grupos de plano de recuperação.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Cadeia de conexão no web.config do aplicativo
A cadeia de conexão especifica o banco de dados com o qual o banco de dados se comunica.

Se a cadeia de conexão levar o nome da máquina virtual do banco de dados, nenhuma outra etapa será necessária após o failover, e o aplicativo será capaz de se comunicar automaticamente com o banco de dados. Além disso, se o endereço IP da máquina virtual do banco de dados for mantido, ele não será necessário para atualizar a cadeia de conexão. Se a cadeia de conexão se referir à máquina virtual do banco de dados usando um endereço IP, será necessário atualizá-lo após o failover. Por exemplo a cadeia de conexão abaixo aponta para o banco de dados com IP 127.0.1.2

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Atualize a cadeia de conexão na camada da Web adicionando o [script de atualização de conexão do IIS](https://aka.ms/asr-update-webtier-script-classic) após o Grupo 3 no plano de recuperação.

#### <a name="site-bindings-for-the-application"></a>Associações de site para o aplicativo
Cada site é composto por informações de associação que incluem o tipo de associação, o endereço IP no qual o servidor IIS escuta as solicitações para o site, o número da porta e os nomes de host do site. No momento de um failover, talvez seja necessário atualizar essas associações se houver uma alteração no endereço IP associado a elas.

> [!NOTE]
>
> Se você tiver marcado 'todos não atribuídos' para a associação de site, como no exemplo a seguir, não será necessário atualizar essa associação após o failover. Além disso, se o endereço IP associado a um site não for alterado após o failover, não será necessário atualizar a associação do site (a retenção do endereço IP depende da arquitetura de rede e das sub-redes atribuídas aos sites primário e de recuperação e, assim, pode ou não ser viável para a sua organização.)

![Associação de SSL](./media/site-recovery-iis/sslbinding.png)

Se você tiver associado o endereço IP a um site, será necessário atualizar todas as associações de site com o novo endereço IP. Adicione o [script de atualização da camada da Web do IIS](https://aka.ms/asr-web-tier-update-runbook-classic) após o Grupo 3 no plano de recuperação para alterar as associações de site.


#### <a name="update-load-balancer-ip-address"></a>Atualizar o endereço IP do balanceador de carga
Se você tiver uma máquina virtual com Application Request Routing, adicione o [script de failover de ARR do IIS](https://aka.ms/asr-iis-arrtier-failover-script-classic) após o Grupo 4 para atualizar o endereço IP.

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>A associação de certificados SSL para uma conexão https
Os sites podem ter um certificado SSL associado que ajuda a garantir uma comunicação segura entre o servidor Web e o navegador do usuário. Se o site tiver uma conexão https e uma associação de site https associada ao endereço IP do servidor IIS com uma associação de certificado SSL, será necessário adicionar uma nova associação de site ao certificado com o IP da máquina virtual do IIS após o failover.

O certificado SSL pode ser executado:

a) No nome de domínio totalmente qualificado do site<br>
b) No nome do servidor<br>
c) Em um certificado curinga para o nome de domínio<br>
d) Em um endereço IP – Se o certificado SSL for emitido para o IP do servidor IIS, outro certificado SSL deverá ser emitido para o endereço IP do servidor IIS no site do Azure, e será necessário criar uma associação SSL adicional para esse certificado. Portanto, não aconselhamos o uso de um certificado SSL emitido com base no IP. Essa é uma opção menos usada e em breve será substituída de acordo com as novas alterações na CA/fórum do navegador.

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>Atualizar a dependência entre a camada da Web e de aplicativo
Se você tiver uma dependência específica do aplicativo com base no endereço IP das máquinas virtuais, será necessário atualizar essa dependência após o failover.

## <a name="doing-a-test-failover"></a>Executar um failover de teste
Siga [este guia](site-recovery-test-failover-to-azure.md) fazer um failover de teste.

1.  Acesse o Portal do Azure e selecione seu cofre do Serviço de Recuperação.
1.  Clique no plano de recuperação criado para o farm da Web do IIS.
1.  Clique em 'Failover de Teste'.
1.  Selecione o ponto e recuperação e a rede virtual do Azure para iniciar o processo de failover de teste.
1.  Quando o ambiente secundário estiver funcionando, você poderá executar sua validações.
1.  Após a conclusão das validações, selecione 'Validações concluídas', e o ambiente do failover de teste será limpo.

## <a name="doing-a-failover"></a>Executar um failover
Siga [este guia](site-recovery-failover.md) quando estiver realizando um failover.

1.  Acesse o Portal do Azure e selecione seu cofre do Serviço de Recuperação.
1.  Clique no plano de recuperação criado para o farm da Web do IIS.
1.  Clique em 'Failover'.
1.  Selecione o ponto de recuperação para iniciar o processo de failover.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [replicar outros aplicativos](site-recovery-workload.md) usando o Site Recovery.
