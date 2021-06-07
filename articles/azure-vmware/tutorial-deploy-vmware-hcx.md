---
title: Tutorial – Implantar e configurar o VMware HCX
description: Saiba como implantar e configurar uma solução VMware HCX para sua nuvem privada da Solução VMware no Azure.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: ab59b4a29f8ba372c1892613d5e0461bf3fdc512
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869082"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Implantar e configurar o VMware HCX

Este artigo mostra como implantar e configurar o VMware HCX Connector local em sua nuvem privada da Solução VMware no Azure. Com o VMware HCX, você pode migrar suas cargas de trabalho do VMware para a Solução VMware no Azure e para outros sites conectados por meio de vários tipos de migração. Como a Solução VMware no Azure implanta e configura o HCX Cloud Manager, você precisa baixar, ativar e configurar o HCX Connector em seu datacenter do VMware local.

O VMware HCX Advanced Connector é implantado previamente na Solução VMware no Azure. Ele dá suporte a até três conexões de site (local para nuvem ou nuvem para nuvem). Se você precisar de mais de três conexões de site, envie uma [solicitação de suporte](https://portal.azure.com/#create/Microsoft.Support) para habilitar o complemento [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).  

>[!TIP]
>Embora a ferramenta VMware Configuration Maximum descreva os máximos de pares de sites como 25 entre o Conector local e o Cloud Manager, o licenciamento limita isso a três no HCX Edição Advanced e a 10 no HCX Edição Enterprise.

>[!NOTE]
>O VMware HCX Enterprise está disponível com a Solução VMware no Azure como um serviço em versão prévia. Ele é gratuito e está sujeito aos termos e condições de um serviço em versão prévia. Quando o serviço VMware HCX Enterprise estiver em disponibilidade geral, você receberá um aviso de 30 dias de que a cobrança será iniciada. Você também terá a opção de desligar ou recusar o serviço. Não há nenhum caminho de downgrade simples do VMware HCX Enterprise para o VMware HCX Advanced. Se você decidir fazer downgrade, precisará fazer a reimplantação, incorrendo em tempo de inatividade.

Primeiro, examine [Antes de começar](#before-you-begin), [Requisitos de versão do software](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) e os [Pré-requisitos](#prerequisites). 

Em seguida, percorreremos todos os procedimentos necessários para:

> [!div class="checklist"]
> * Baixar o OVA do VMware HCX Connector.
> * Implantar o OVA do VMware HCX (VMware HCX Connector) local.
> * Ativar o VMware HCX Connector.
> * Emparelhar o VMware HCX Connector local com o HCX Cloud Manager da Solução VMware no Azure.
> * Configurar a interconexão (perfil de rede, perfil de computação e malha de serviço).
> * Concluir a instalação verificando o status do dispositivo e validando que a migração é possível.

Após a conclusão, siga as próximas etapas recomendadas no final deste artigo.  

## <a name="before-you-begin"></a>Antes de começar

Quando você preparar sua implantação, recomendamos que você examine a seguinte documentação do VMware:

* [Guia do usuário do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [Como migrar máquinas virtuais com o VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Considerações de Implantação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [Série de blogs do VMware – migração na nuvem](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [Portas de rede necessárias para o VMware HCX](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Pré-requisitos

Se você planeja usar o VMware HCX Enterprise, verifique se solicitou a ativação por meio dos canais de suporte da Solução VMware no Azure.


### <a name="on-premises-vsphere-environment"></a>Ambiente vSphere local

Verifique se seu ambiente vSphere local (ambiente de origem) atende aos [requisitos mínimos](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html). 

### <a name="network-and-ports"></a>Rede e portas

* O [Alcance Global do Azure ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) é configurado entre os circuitos local e do ExpressRoute da nuvem privada na Solução VMware no Azure.

* [Todas as portas necessárias](https://ports.vmware.com/home/VMware-HCX) estão abertas para comunicação entre os componentes locais e a Solução VMware no Azure Privada.

### <a name="ip-addresses"></a>Endereços IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>Baixar o OVA do VMware HCX Connector

Antes de implantar o dispositivo virtual no vCenter local, baixe o OVA do VMware HCX Connector.  

1. Na portal do Azure, selecione a nuvem privada da Solução VMware no Azure. 

1. Selecione **Gerenciar** > **Conectividade** e selecione a guia **HCX** para identificar o endereço IP do HCX Manager da Solução VMware no Azure. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="Captura de tela do endereço IP do VMware HCX." lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. Selecione **Gerenciar** > **Identidade**. 

   As URLs e as credenciais do usuário da nuvem privada do vCenter e do NSX-T Manager são exibidas.

   > [!TIP]
   > A senha do vCenter foi definida quando você configurou a nuvem privada. É a mesma senha que você usará para entrar no HCX Manager da Solução VMware no Azure. Você pode selecionar **Gerar nova senha** para gerar novas senhas do vCenter e do NSX-T.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Exibir URLs e credenciais da nuvem privada do vCenter e do NSX Manager." border="true":::


1. Abra uma janela do navegador, entre no HCX Manager da Solução VMware no Azure na porta `https://x.x.x.9` 443 com as credenciais do usuário **cloudadmin\@vsphere.local**

1. Selecione **Administração** > **Atualizações do Sistema** e, em seguida, selecione **Solicitar Link para Download**.

1. Selecione a opção de sua escolha para baixar o arquivo OVA do VMware HCX Connector.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Implantar o OVA do VMware HCX Connector localmente

1. No vCenter local, selecione um [modelo OVF](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) para implantar o VMware HCX Connector no vCenter local. 

   > [!TIP]
   > Você selecionará o arquivo OVA que baixou na seção anterior.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selecione um nome e uma localização e escolha um recurso ou cluster no qual o VMware HCX Connector será implantado. Em seguida, examine os detalhes e os recursos necessários e selecione **Avançar**.  

1. Examine os termos de licença. Se você concordar com eles, selecione o armazenamento e a rede necessários e clique em **Avançar**.

1. Selecione o armazenamento e **Avançar**.

1. Selecione o segmento de rede de gerenciamento do VMware HCX que você definiu anteriormente na seção [Pré-requisitos de endereços IP](#ip-addresses).  Em seguida, selecione **Avançar**.

1. Em **Personalizar modelo**, insira todas as informações necessárias e selecione **Avançar**. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Captura de tela das caixas para personalizar um modelo." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Verifique a configuração e selecione **Concluir** para implantar o OVA do VMware HCX Connector.
   
   > [!IMPORTANT]
   > Você precisará ativar a solução de virtualização manualmente.  Após a ativação, aguarde de 10 a 15 minutos antes de prosseguir para a próxima etapa.

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Implantação do Dispositivo HCX](https://www.youtube.com/embed/UKmSTYrL6AY). 


## <a name="activate-vmware-hcx"></a>Ativar o VMware HCX

Depois de implantar o OVA do VMware HCX Connector no local e iniciar o dispositivo, você estará pronto para ativar. Primeiro, você precisa obter uma chave de licença do portal da Solução VMware no Azure.

1. No portal da Solução VMware no Azure, acesse **Gerenciar** > **Conectividade**, selecione a guia **HCX** e clique em **Adicionar**.

1. Use as credenciais de **administrador** para entrar no VMware HCX Manager local em `https://HCXManagerIP:9443`. 

   > [!TIP]
   > Você definiu a senha de usuário **admininstrador** durante a implantação do arquivo OVA do VMware HCX Manager.

   > [!IMPORTANT]
   > Inclua o número da porta `9443` com o endereço IP do VMware HCX Manager.

1. Em **Licenciamento**, insira sua chave correspondente à **Chave Avançada do HCX** e selecione **Ativar**.  
   
    > [!NOTE]
    > O VMware HCX Manager precisa ter acesso à Internet aberta ou a um proxy configurado.

1. Em **Localização do Datacenter**, forneça a localização mais próxima para instalação do VMware HCX Manager local está. Depois selecione **Continuar**.

1. Em **Nome do Sistema**, modifique o nome ou aceite o padrão e selecione **Continuar**.
   
1. Selecione **Sim, Continuar**.

1. Em **Conectar o vCenter**, forneça o FQDN ou o endereço IP do vCenter Server e as credenciais apropriadas e clique em **Continuar**.
   
   > [!TIP]
   > O vCenter Server é onde você implantou o VMware HCX Connector em seu datacenter.

1. Em **Configurar o SSO/PSC**, forneça o FQDN ou o endereço IP de seu Platform Services Controller e clique em **Continuar**.
   
   > [!NOTE]
   > Normalmente, é o mesmo que o endereço IP ou o FQDN do vCenter.

1. Verifique se as informações inseridas estão corretas e selecione **Reiniciar**.
    
   > [!NOTE]
   > Haverá um atraso após a reinicialização até o aviso para avançar para a próxima etapa.

Após a reinicialização dos serviços, você verá o vCenter sendo exibido em verde na tela que aparece. O vCenter e o SSO precisam ter os parâmetros de configuração apropriados, que devem ser iguais aos da tela anterior.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Captura de tela do painel com o status verde do vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Ativar HCX](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720).

   > [!IMPORTANT]
   > Quer esteja usando o HCX Advanced ou o HCX Enterprise, talvez seja necessário instalar o patch do [artigo da base de dados 81558](https://kb.vmware.com/s/article/81558) do VMware. 

## <a name="configure-the-vmware-hcx-connector"></a>Configurar o VMware HCX Connector

Agora você está pronto para adicionar um emparelhamento de site, criar um perfil de rede e de computação e habilitar serviços, como migração, extensão de rede ou recuperação de desastre. 

### <a name="add-a-site-pairing"></a>Adicionar um emparelhamento de site

Você pode conectar ou emparelhar o VMware HCX Cloud Manager na Solução VMware no Azure com o VMware HCX Connector no datacenter. 

1. Entre no vCenter local e, na **Página Inicial**, selecione **HCX**.

1. Em **Infraestrutura**, selecione **Emparelhamento de Site** e escolha a opção **Conectar com o Site Remoto** (no meio da tela). 

1. Insira a URL ou o endereço IP do HCX Cloud Manager da Solução VMware no Azure que você anotou anteriormente `https://x.x.x.9`, o nome de usuário cloudadmin\@vsphere.local da Solução VMware no Azure e a senha. Depois, selecione **Conectar**.

   > [!NOTE]
   > Para estabelecer um par de sites com êxito:
   > * O VMware HCX Connector precisa conseguir fazer o roteamento para o seu IP do HCX Cloud Manager na porta 443.
   >
   > * Use a mesma senha usada para entrar no vCenter. Você definiu essa senha na tela inicial de implantação.

   Você verá uma tela mostrando que o VMware HCX Cloud Manager na Solução VMware no Azure e o VMware HCX Connector local estão conectados (emparelhados).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Captura de tela que mostra o emparelhamento do HCX Manager na Solução VMware no Azure e o VMware HCX Connector.":::

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Emparelhamento de Sites do HCX](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720).

### <a name="create-network-profiles"></a>Criar perfis de rede

O VMware HCX Connector implanta um subconjunto de soluções de virtualização (automatizadas) que exigem vários segmentos de IP. Quando você criar seus perfis de rede, use segmentos de IP identificados durante a [fase de preparação e planejamento de pré-implantação de Segmentos de Rede do VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Você criará quatro perfis de rede:

   - Gerenciamento
   - vMotion
   - Replicação
   - Uplink

1. Em **Infraestrutura**, selecione **Interconexão** > **Malha de Serviço Multissite** > **Perfis de Rede** > **Criar Perfil de Rede**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Captura de tela das seleções para começar a criar um perfil de rede." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Para cada perfil de rede, selecione a rede e o grupo de portas, forneça um nome e crie o pool de IPs do segmento. Em seguida, selecione **Criar**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Captura de tela dos detalhes de um novo perfil de rede.":::

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Perfil de Rede do HCX](https://www.youtube.com/embed/O0rU4jtXUxc).


### <a name="create-a-compute-profile"></a>Criar um perfil de computação

1. Em **Infraestrutura**, selecione **Interconexão** > **Perfis de Computação** > **Criar Perfil de Computação**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Captura de tela que mostra as seleções para começar a criar um perfil de computação." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Insira um nome para o perfil e clique em **Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Captura de tela que mostra a entrada de um nome de perfil de computação e o botão Continuar." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selecione os serviços a serem habilitados, como migração, extensão de rede ou recuperação de desastre e clique em **Continuar**.
  
   > [!NOTE]
   > Normalmente, nada é alterado aqui.

1. Em **Selecionar Recursos de Serviço**, selecione um ou mais recursos de serviço (clusters) a serem habilitados para os serviços do VMware HCX selecionados.  

1. Quando os clusters aparecerem no datacenter local, clique em **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Captura de tela que mostra os recursos de serviço selecionados e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Em **Selecionar o Armazenamento de Dados**, escolha o recurso de armazenamento de dados para a implantação dos dispositivos VMware HCX Interconnect. Depois selecione **Continuar**.

   Quando vários recursos são selecionados, o VMware HCX usa o primeiro recurso selecionado até que sua capacidade seja esgotada.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Captura de tela que mostra um recurso de armazenamento de dados selecionado e o botão Continuar." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Em **Selecionar o Perfil de Rede de Gerenciamento**, escolha o perfil de rede de gerenciamento que você criou nas etapas anteriores. Depois selecione **Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Captura de tela que mostra a seleção de um perfil de rede de gerenciamento e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. Em **Selecionar o Perfil de Rede de Uplink**, escolha o perfil de rede de uplink que você criou no procedimento anterior. Depois selecione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Captura de tela que mostra a seleção de um perfil de rede de uplink e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Em **Selecionar o Perfil de Rede do vMotion**, escolha o perfil de rede do vMotion que você criou nas etapas anteriores. Depois selecione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Captura de tela que mostra a seleção de um perfil de rede do vMotion e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Em **Selecionar o Perfil de Rede de Replicação do vSphere**, escolha o perfil de rede de replicação que você criou nas etapas anteriores. Depois selecione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Captura de tela que mostra a seleção de um perfil de rede de replicação e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Em **Selecionar os Comutadores Distribuídos para Extensões de Rede**, selecione os comutadores que contêm as máquinas virtuais a serem migradas para a Solução VMware no Azure em uma rede estendida da camada 2. Depois selecione **Continuar**.

   > [!NOTE]
   > Se você não estiver migrando máquinas virtuais em redes estendidas de camada 2 (L2), ignore esta etapa.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Captura de tela que mostra a seleção de um comutador virtual distribuído e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Examine as regras de conexão e clique em **Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Captura de tela que mostra as regras de conexão e o botão Continuar." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selecione **Concluir** para criar o perfil de computação.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Captura de tela que mostra informações do perfil de computação." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Perfil de Computação](https://www.youtube.com/embed/e02hsChI3b8).

### <a name="create-a-service-mesh"></a>Criar malha de serviço

Este é o momento de configurar a malha de serviço entre o ambiente local e a nuvem privada da Solução VMware no Azure.



> [!NOTE]
> Para estabelecer com êxito uma malha de serviço com a Solução VMware no Azure:
>
> * As portas UDP 500/4500 estão abertas entre seus endereços de perfil de rede 'uplink' definidos pelo VMware HCX Connector local e os endereços de perfil de rede 'uplink' do HCX Cloud da Solução VMware no Azure.
>
> * Lembre-se de examinar as [portas necessárias do VMware HCX](https://ports.vmware.com/home/VMware-HCX).

1. Em **Infraestrutura**, selecione **Interconexão** > **Malha de Serviço** > **Criar Malha de Serviço**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Captura de tela das seleções para começar a criar uma malha de serviço." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Examine os sites que já foram populados e clique em **Continuar**. 

   > [!NOTE]
   > Se esta for sua primeira configuração de malha de serviço, você não precisará modificar esta tela.  

1. Selecione os perfis de computação de origem e remoto nas listas suspensas e clique em **Continuar**.  

   As seleções definem os recursos nos quais as VMs podem consumir os serviços do VMware HCXs.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Captura de tela que mostra a seleção do perfil de computação de origem." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Captura de tela que mostra a seleção do perfil de computação remoto." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Examine os serviços que serão habilitados e selecione **Continuar**.  

1. Em **Configuração Avançada – Substituir Perfis de rede de Uplink** clique em **Continuar**.  

   Os perfis de rede de uplink conectam-se à rede por meio da qual os dispositivos de interconexão do site remoto podem ser alcançados.  
  
1. Em **Configuração Avançada – Escala Horizontal do Dispositivo de Extensão de Rede**, examine a configuração e clique em **Continuar**. 

   Você pode ter até oito VLANs por dispositivo, mas pode implantar outro dispositivo para adicionar outras oito VLANs. Você também precisa ter espaço de IP para considerar os dispositivos adicionais, e ele é um IP por dispositivo.  Para obter mais informações, confira [Limites de configuração do VMware HCX](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Captura de tela que mostra o local em que a contagem de VLAN é aumentada." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. Em **Configuração Avançada – Engenharia de Tráfego**, examine a configuração, faça as modificações que julgar necessárias e clique em **Continuar**.

1. Examine a versão prévia da topologia e selecione **Continuar**.

1. Insira um nome amigável para esta malha de serviço e selecione **Concluir** para finalizar.  

1. Selecione **Exibir Tarefas** para monitorar a implantação. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Captura de tela que mostra o botão usado para exibir tarefas.":::

   Quando a implantação da malha de serviço for concluída com êxito, você verá os serviços como verdes.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Captura de tela que mostra indicadores verdes nos serviços." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verifique a integridade da malha de serviço conferindo o status do dispositivo. 

1. Selecione **Interconexão** > **Dispositivos**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Captura de tela que mostra as seleções para verificar o status do dispositivo." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Malha de Serviço](https://www.youtube.com/embed/COY3oIws108).

### <a name="optional-create-a-network-extension"></a>(Opcional) Criar uma extensão de rede

Se você quiser estender alguma rede do ambiente local para a Solução VMware no Azure, siga estas etapas:

1. Em **Serviços**, selecione **Extensão de Rede** > **Criar uma Extensão de Rede**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Captura de tela que mostra as seleções para começar a criar uma extensão de rede." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selecione cada uma das redes que você deseja estender para a Solução VMware no Azure e clique em **Avançar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Captura de tela que mostra a seleção de uma rede.":::

1. Insira o IP do gateway local de cada uma das redes que você está estendendo e clique em **Enviar**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Captura de tela que mostra a entrada de um endereço IP do gateway.":::

   Demora alguns minutos até que a extensão de rede seja concluída. Após a conclusão, o status será alterado para **Extensão concluída**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Captura de tela que mostra o status da extensão concluída." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Extensão de Rede](https://www.youtube.com/embed/gYR0nftKui0).


## <a name="next-steps"></a>Próximas etapas

Se o status do túnel de interconexão do HCX for **UP** e estiver verde, você poderá migrar e proteger as VMs da Solução VMware no Azure usando o VMware HCX. A Solução VMware no Azure dá suporte a migrações de carga de trabalho (com ou sem uma extensão de rede). Você ainda pode migrar cargas de trabalho no ambiente vSphere, além da criação local de redes e a implantação de VMs nessas redes.  

Para obter mais informações sobre como usar o HCX, acesse a documentação técnica do VMware:

* [Documentação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Como migrar máquinas virtuais com o VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Portas necessárias do HCX](https://ports.vmware.com/home/VMware-HCX)
* [Configurar um servidor proxy do HCX antes de aprovar a chave de licença](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-920242B3-71A3-4B24-9ACF-B20345244AB2.html?hWord=N4IghgNiBcIA4CcD2APAngAgBIGEAaIAvkA)
