---
title: Ajustar as configurações de rede do cluster do Microsoft Azure FXT Edge Filer
description: Como personalizar as configurações de rede depois de criar o cluster do Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 36ed354304cb1c88e48088f4b36c1ad0350af0dc
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543007"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Tutorial: Definir as configurações de rede do cluster 

Antes de usar um cluster do Azure FXT Edge Filer recém-criado, verifique e personalize as várias configurações de rede de seu fluxo de trabalho. 

Este tutorial explica as configurações de rede que podem ser necessárias para ajustar um novo cluster. 

Você aprenderá: 

> [!div class="checklist"]
> * Quais configurações de rede podem precisar de atualização após a criação de um cluster
> * Quais casos de uso do Azure FXT Edge Filer exigem um servidor do AD ou um servidor DNS 
> * Como configurar RRDNS (DNS round robin) para balancear automaticamente a carga de solicitações de cliente para o cluster FXT

O tempo usado para concluir essas etapas depende da quantidade de mudanças na configuração exigidas em seu sistema:

* Se você só precisar ler o tutorial e verificar algumas configurações, deverá demorar de 10 a 15 minutos. 
* Se você precisar configurar o DNS round robin, talvez essa tarefa demore um hora ou mais.

## <a name="adjust-network-settings"></a>Ajustar as configurações de rede

A configuração de um novo cluster do Azure FXT Edge Filer contém várias tarefas relacionadas à rede. Verifique essa lista e decida quais se aplicam ao seu sistema.

Para saber mais sobre as configurações de rede do cluster, leia [Configurar serviços de rede](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) no Guia de configuração do cluster.

* Configurar o DNS round robin para a rede voltada ao cliente (opcional)

  Faça o balanceamento de carga do tráfego do cluster configurando o sistema DNS conforme descrito em [Configurar o DNS para o cluster do FXT Edge Filer](#configure-dns-for-load-balancing).

* Verificar as configurações de NTP

* Configurar o Active Directory e os downloads de nome de usuário/nome do grupo (se for necessário)

  Se os hosts da rede usarem o Active Directory ou outro tipo de serviço de diretório externo, será necessário modificar a configuração dos serviços de diretório do cluster para definir como o cluster baixa informações de nome de usuário e do grupo. Leia **Cluster** > **Serviços de Diretório** no Guia de configuração do cluster para obter detalhes.

  Se você quiser suporte a SMB, precisará de um servidor do AD. Configure o AD antes de começar a configurar o SMB.

* Definir VLANs (opcional)
  
  Configure outras VLANs necessárias antes de definir os vservers e o namespace global do seu cluster. Leia [Trabalhar com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) no Guia de configuração do cluster para saber mais.

* Configurar servidores proxy (se for necessário)

  Se o cluster usar um servidor proxy para acessar endereços externos, execute estas etapas para configurá-lo:

  1. Defina o servidor proxy na página de configurações **Configuração de Proxy**
  1. Aplique a configuração do servidor proxy com a página **Cluster** > **Configuração Geral** ou com a página **Detalhes do Arquivista Central**.
  
  Para saber mais, leia [Usar proxies da Web](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) no Guia de configuração do cluster.

* Carregue os [certificados de criptografia](#encryption-certificates) para uso do cluster (opcional)

### <a name="encryption-certificates"></a>Certificados de criptografia

O cluster do FXT Edge Filer usa certificados X.509 para essas funções:

* Criptografar o tráfego de administração do cluster

* Autenticar em nome de um cliente em servidores KMIP de terceiros

* Verificar os certificados de servidor dos provedores de nuvem

Se você precisar carregar certificados no cluster, use a página de configurações **Cluster** > **Certificados**. Os detalhes estão na página [Cluster > Certificados](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) do Guia de configuração do cluster.

Para criptografar a comunicação de gerenciamento do cluster, use a página de configurações **Cluster** > **Configuração Geral** para selecionar qual certificado usar para SSL administrativo.

> [!Note] 
> As chaves de acesso do serviço de nuvem são armazenadas usando a página de configuração **Credenciais de Nuvem**. A seção [Adicionar um arquivista central](fxt-add-storage.md#add-a-core-filer) acima mostra um exemplo; leia a seção [Credenciais de nuvem](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) do Guia de configuração do cluster para obter detalhes. 

## <a name="configure-dns-for-load-balancing"></a>Configurar o DNS para balanceamento de carga

Esta seção explica as noções básicas de configuração de um sistema RRDNS (DNS round robin) para distribuir a carga de clientes entre todos os endereços IP voltados para o cliente em seu cluster do FXT Edge Filer. 

### <a name="decide-whether-or-not-to-use-dns"></a>Decidir se quer ou não usar o DNS

O balanceamento de carga é sempre recomendável, mas você não precisa usar sempre o DNS. Por exemplo, com alguns tipos de fluxos de trabalho do cliente, talvez seja melhor usar um script para atribuir endereços IP do cluster uniformemente entre os clientes quando eles montarem o cluster. Confira vários métodos em [Montar o cluster](fxt-mount-clients.md). 

Tenha em mente as informações a seguir ao decidir se deseja ou não usar um servidor DNS: 

* Se o seu sistema for acessado apenas por clientes do NFS, o DNS não será necessário. É possível especificar todos os endereços de rede usando endereços IP numéricos. 

* Se o sistema dá suporte ao acesso de SMB (CIFS), o DNS é necessário, pois você precisa especificar um domínio DNS para o servidor do Active Directory.

* O DNS é necessário quando você quer usar autenticação com Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Detalhes de configuração de DNS round robin

Quando os clientes acessam o cluster, o RRDNS equilibra automaticamente suas solicitações entre todas as interfaces disponíveis.

Para atingir o desempenho ideal, configure seu servidor DNS para lidar com endereços de cluster voltados para o cliente da maneira mostrada no diagrama a seguir.

Um vserver do cluster é mostrado à esquerda e os endereços IP aparecem no centro e à direita. Configure cada ponto de acesso do cliente com registros e ponteiros A conforme ilustrado.

![Diagrama de cluster DNS round robin - link de texto Alt detalhado após a imagem](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[descrição de texto detalhada](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

Cada endereço IP voltado ao cliente deve ter um nome exclusivo para uso interno pelo cluster. (Neste diagrama, os IPs do cliente são chamados de vs1-client-IP-* para maior clareza, mas na produção você provavelmente deve usar algo mais conciso, como cliente*.)

Os clientes montam o cluster usando o nome do vserver como argumento do servidor. 

Modifique o arquivo ``named.conf`` do servidor DNS para definir a ordem cíclica para as consultas em seu vserver. Essa opção garante que todos os valores disponíveis sejam percorridos. Adicione uma instrução semelhante à seguinte:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Os comandos ``nsupdate`` a seguir mostram um exemplo de como configurar o DNS corretamente:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

### <a name="enable-dns-in-the-cluster"></a>Habilitar o DNS no cluster 

Especifique o servidor DNS usado pelo cluster na página de configuração **Cluster** > **Rede Administrativa**. As configurações na página incluem:

* Endereço do servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa DNS

Para ver mais detalhes, leia [Configurações de DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no Guia de configuração do cluster.

## <a name="next-steps"></a>Próximas etapas

Esta é a última etapa de configuração básica para o cluster do Azure FXT Edge Filer. 

* Saiba mais sobre os LEDs do sistema e outros indicadores em [Monitorar o status do hardware](fxt-monitor.md).
* Saiba mais sobre como os clientes devem montar o cluster do FXT Edge Filer em [Montar o cluster](fxt-mount-clients.md). 
* Para saber mais sobre como operar e gerenciar um cluster do FXT Edge Filer, confira o [Guia de configuração do cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 