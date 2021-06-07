---
title: Guia de implantação do acesso híbrido seguro do Azure AD com F5 | Microsoft Docs
description: Tutorial para implantar a VM de F5 BIG-IP Virtual Edition (VE) no Azure IaaS para acesso híbrido seguro
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f962bf131b87f17712186145b8c8b8e6090f7002
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730649"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Tutorial para implantar a VM do F5 BIG-IP Virtual Edition na IaaS do Azure para acesso híbrido seguro

Este tutorial orienta você pelo processo de ponta a ponta de implantação de vitural (edição BIG-IP) no Azure IaaS. Ao final deste tutorial, você deve ter:

- Uma VM (máquina virtual) BIG-IP totalmente preparada para modelar uma prova de conceito de SHA (acesso híbrido seguro)

- Uma instância de preparo a ser usada para testar novas atualizações e hotfixes do sistema BIG-IP

## <a name="prerequisites"></a>Pré-requisitos

A experiência de BIG-IP anterior F5 ou o conhecimento não é necessário. no entanto, recomendamos familiarizar-se com a [terminologia de Big-IP F5](https://www.f5.com/services/resources/glossary). A implantação de um BIG IP no Azure para SHA requer:

- Uma assinatura paga do Azure ou uma [assinatura de avaliação](https://azure.microsoft.com/free/)gratuita de 12 meses.

- Qualquer uma das seguintes SKUs de licença do F5 BIG-IP

  - F5 BIG-IP® melhor pacote

  - Licença autônoma do BIG-IP Access Policy Manager™ (APM) da F5

  - A licença de complemento do Gerenciador de políticas de acesso do BIG-IP™ (APM) em um BIG-IP F5 existente® o Gerenciador de tráfego local™ (LTM)
  
  - [licença de avaliação](https://www.f5.com/trial/big-ip-trial.php)de recursos completos de Big-IP de 90 dias.

- Um certificado de curinga ou de SAN (nome alternativo da entidade) para publicar aplicativos Web sobre SSL (Secure Socket Layer). [Vamos criptografar](https://letsencrypt.org/) oferece um certificado gratuito de 90 dias para teste.

- Um certificado SSL para proteger a interface de gerenciamento de BIG-IPs. Um certificado usado para publicar aplicativos Web pode ser usado, se o assunto corresponder ao FQDN (nome de domínio totalmente qualificado) de BIG-IP. Por exemplo, um certificado curinga definido com um assunto *. contoso.com seria adequado para `https://big-ip-vm.contoso.com:8443`

A implantação de VM e as configurações do sistema base levam aproximadamente 30 minutos. nesse ponto, sua plataforma de BIG IP estará pronta para implementar qualquer um dos cenários de SHA listados [aqui](f5-aad-integration.md).

Para testar os cenários, este tutorial pressupõe que o BIG-IP será implantado em um grupo de recursos do Azure que contém um ambiente de Active Directory (AD). O ambiente deve consistir em um DC (controlador de domínio) e em VMs do host Web (IIS). Ter esses servidores em outros locais para a VM BIG-IP também está Ok, fornecendo o BIG-IP tem a linha de visão para cada uma das funções necessárias para dar suporte a um determinado cenário. Também há suporte para cenários em que a VM BIG-IP está conectada a outro ambiente em uma conexão VPN.

Se você não tiver os itens mencionados aqui para teste, poderá implantar um ambiente de domínio do AD inteiro no Azure usando esse [script](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). Uma coleção de aplicativos de teste de exemplo também pode ser implantada programaticamente em um host Web do IIS usando essa [automação com script](https://github.com/jeevanbisht/DemoSuite).

>[!NOTE]
>A [portal do Azure](https://portal.azure.com/#home) está em constante evolução, portanto, algumas das etapas neste tutorial podem ser diferentes do layout real observado no portal do Azure.

## <a name="azure-deployment"></a>Implantação do Azure

Um BIG-IP pode ser implantado em topologias diferentes. Este guia se concentra em uma única implantação de NIC (interface de rede). No entanto, se sua implantação de BIG-IP exigir várias interfaces de rede para alta disponibilidade, segregação de rede ou mais de 1 GB de taxa de transferência, considere o uso de [modelos se BigIP ARM (](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)pré-Azure Resource Manager compilados).

Conclua as tarefas a seguir para implantar o BIG-IP VE do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Faça logon no [portal do Azure](https://portal.azure.com/#home) com uma conta, que tem permissões para criar VMs. Por exemplo, colaborador

2. Na caixa de pesquisa da faixa de tipos superior, digite **Marketplace**, seguido de **Enter**

3. Digite **F5** no filtro do Marketplace, seguido por **Enter**

4. Selecione **+ Adicionar** na faixa de opções superior e digite **F5** no filtro do Marketplace, seguido por **Enter**

5. Selecione **F5 Big-IP Virtual Edition (BYOL)**  >  **Selecione um plano de software**  >  **F5 Big-IP ve-todos (BYOL, 2 locais de inicialização)**

6. Selecione **Criar**.

![A imagem mostra etapas para selecionar um plano de software](./media/f5ve-deployment-plan/software-plan.png)

7. Percorra o menu **básico** e use as seguintes configurações

 |  Detalhes do projeto     |  Valor     |
 |:-------|:--------|
 |Subscription|Assinatura de destino para a implantação de VM BIG-IP|
 |Resource group | Grupo de recursos do Azure existente a VM BIG-IP será implantada ou criará uma. Deve ser o mesmo grupo de recursos do seu DC e VMs do IIS|
 | **Detalhes da instância**|  |
 |Nome da VM| Exemplo de BIG-IP-VM |
 |Região | Direcionar a região geográfica do Azure para BIG-IP-VM |
 |Opções de disponibilidade| Habilitar somente se estiver usando a VM em produção|
 |Imagem| F5 BIG-IP VE-ALL (BYOL, 2 locais de inicialização)|
 |Instância do Azure Spot| Não, mas sinta-se à vontade para habilitar, se apropriado |
 |Tamanho| A especificação mínima deve ser 2 vCPUs e 8 GB de memória|
 |**Conta de administrador**|  |
 |Tipo de autenticação|Selecione a senha por enquanto. Você pode alternar para um par de chaves mais tarde |
 |Nome de Usuário|A identidade que será criada como uma conta local BIG-IP para acessar suas interfaces de gerenciamento. O nome de usuário diferencia maiúsculas de minúsculas.|
 |Senha|Proteger o acesso de administrador com uma senha forte|
 |**Regras de porta de entrada**|  |
 |Porta de entrada públicas|Nenhum|

8. Selecione **Avançar: discos** deixando todos os padrões e selecione **Avançar: rede**.

9. No menu **rede** , conclua essas configurações.

 |Adaptador de rede|      Valor |
 |:--------------|:----------------|
 |Rede virtual|Mesma VNet do Azure usada pelo seu DC e VMs do IIS, ou crie uma|
 |Sub-rede| Mesma sub-rede interna do Azure que o DC e as VMs do IIS, ou crie uma|
 |IP público |  Nenhum|
 |Grupo de segurança de rede NIC| Selecione nenhum se a sub-rede do Azure que você selecionou nas etapas anteriores já estiver associada a um NSG (grupo de segurança de rede); caso contrário, selecione básico|
 |Acelerar a rede| Desativado |
 |**Balanceamento de carga**|     |
 |VM de balanceamento de carga| Não|

10. Selecione **Avançar: gerenciamento** e conclua essas configurações.

 |Monitoramento|    Valor |
 |:---------|:-----|
 |Monitoramento detalhado| Desativado|
 |Diagnóstico de inicialização|Habilite com a conta de armazenamento personalizada. Permite conectar-se à interface de Secure Shell de BIG-IP (SSH) por meio da opção de console serial no portal do Azure. Selecione qualquer conta de armazenamento do Azure disponível|
 |**Identidade**|  |
 |Identidade gerenciada atribuída pelo sistema|Desativado|
 |Azure Active Directory|O BIG-IP não oferece suporte a essa opção no momento|
 |**Desligamento automático**|    |
 |Habilitar desligamento automático| Se estiver testando, considere definir o BIG-IP-VM para desligar diariamente|

11. Selecione **Avançar: avançado** deixando todos os padrões e selecione **Avançar: marcas**.

12. Selecione **Avançar: examinar + criar** para examinar suas configurações de VM Big-IP, antes de selecionar **criar** para iniciar a implantação.

13. O tempo para implantar completamente uma VM BIG-IP normalmente é de 5 minutos. Quando concluir, não selecione **ir para recurso**, em vez disso, expanda o menu à esquerda do portal do Azure e selecione **grupos de recursos** para navegar até sua nova VM Big-IP. Se a criação da VM falhar, selecione **voltar** e **Avançar**.

## <a name="network-configuration"></a>Configuração de rede

Quando a VM BIG-IP for inicializada pela primeira vez, sua NIC será provisionada com um IP privado **primário** emitido pelo serviço DHCP (protocolo de configuração dinâmica de hosts) da sub-rede do Azure à qual ele está conectado. Esse IP será usado pelo TMOS (sistema operacional de gerenciamento de tráfego) BIG-IP para se comunicar com:

- Comunicando-se com outros hosts e serviços

- Acesso de saída à Internet pública

- Acesso de entrada à configuração da Web do BIG-IPs e às interfaces de gerenciamento do SSH

A exposição de qualquer uma dessas interfaces de gerenciamento à Internet aumenta a superfície de ataque de BIG-IPs, portanto, por que o IP primário BIG-IPs não foi provisionado com um IP público durante a implantação. Em vez disso, um IP interno secundário e um IP público associado serão provisionados para serviços de publicação.
O mapeamento de 1 para 1 entre um IP público de VM e o IP privado permite que o tráfego externo alcance uma VM. No entanto, uma regra de NSG do Azure também é necessária para permitir o tráfego, praticamente da mesma forma que um firewall.

O diagrama mostra uma única implantação de NIC de um BIG-IP VE no Azure, configurada com um IP primário para operações e gerenciamento gerais e um IPs de servidor virtual separado para serviços de publicação.
Nesse arranjo, uma regra NSG permite o tráfego remoto destinado para `intranet.contoso.com` rotear para o IP público para o serviço publicado, antes de ser encaminhado para o servidor virtual Big-IP.

![A imagem mostra a implantação de NIC única ](./media/f5ve-deployment-plan/single-nic-deployment.png) por padrão, os IPs públicos e privados emitidos para VMs do Azure são sempre dinâmicos, portanto, provavelmente serão alterados em cada reinicialização de uma VM. Evite problemas de conectividade imprevistos alterando o IP de gerenciamento de BIG-IPs para estático e faça o mesmo para IPs secundários usados para serviços de publicação.

1. No menu da VM Big-IP, acesse **configurações**  >  **rede**

2. Na exibição rede, selecione o link à direita da interface de **rede**

![A imagem mostra a configuração de rede](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>Os nomes de VM são gerados aleatoriamente durante a implantação.

3. No painel esquerdo, selecione **configurações de IP** e, em seguida, selecione linha **ipconfig1**

4. Defina a opção de **atribuição de IP** como estática e, se necessário, altere o endereço IP primário das VMs de Big-IP. Em seguida, selecione **salvar** e fechar o menu ipconfig1

>[!NOTE]
>Você usará esse IP primário para se conectar e gerenciar o BIG-IP-VM.

5. Selecione **+ Adicionar** na faixa de opções superior e forneça um nome para um IP privado secundário, por exemplo, ipconfig2

6. Defina a opção de **alocação** das configurações de endereço IP privado como **estática**. Fornecer o próximo IP consecutivo mais alto ou inferior ajuda a manter as coisas de forma ordenada.

7. Defina o endereço IP público como **associar** e selecione **criar**

8. Forneça um nome para o novo endereço IP público, por exemplo, BIG-IP-VM_ipconfig2_Public

9. Se solicitado, defina a **SKU** como Standard

10. Se solicitado, defina a **camada** como **global** e

11. Defina a opção de **atribuição** como **estática** e, em seguida, selecione **OK** duas vezes

Sua VM BIG-IP agora está pronta para ser configurada com:

- **IP privado primário**: dedicado ao gerenciamento da VM Big-IP por meio de seu utilitário de configuração da Web e SSH. Ele será usado pelo sistema BIG-IP como seu **próprio IP** para se conectar aos serviços de back-end publicados. Além disso, ele se conecta a serviços externos, como NTP, AD e LDAP.

- **IP privado secundário**: usado ao criar um servidor virtual APM Big-IP para escutar a solicitação de entrada para um ou mais serviços publicados.

- **IP público**: associado ao IP privado secundário, permite que o tráfego do cliente da Internet pública alcance o servidor virtual Big-IP para os serviços publicados

O exemplo ilustra a relação de 1 a 1 entre um IPs público e privado de VM. Uma NIC de VM do Azure pode ter apenas um IP primário, e todos os IPs criados também são chamados de secundários.

>[!NOTE]
>Você precisará dos mapeamentos de IP secundários para a publicação de serviços de BIG-IP.

![Esta imagem mostra todos os IPs secundários](./media/f5ve-deployment-plan/secondary-ips.png)

Para implementar o SHA usando a **configuração guiada de acesso** de Big-IP, repita as etapas 5-11 para criar pares IP públicos e privados adicionais para cada serviço adicional que você planeja publicar por meio do APM de Big-IP. A mesma abordagem também pode ser usada se estiver publicando serviços usando BIG-IPs **Configuração avançada**. No entanto, nesse cenário, você tem a opção de evitar sobrecargas de IP público usando uma configuração de [SNI (indicador de nome de servidor)](https://support.f5.com/csp/#/article/K13452) . Nessa configuração, um servidor virtual BIG-IP aceitará todo o tráfego de cliente recebido e o roteará em frente ao seu destino.

## <a name="dns-configuration"></a>Configuração de DNS

É essencial ter o DNS configurado para clientes para resolver seus serviços de SHA publicados para os IP públicos de sua VM BIG-IP.

As etapas a seguir pressupõem que a zona DNS do domínio público usado para seus serviços SHA seja gerenciada no Azure. No entanto, os mesmos princípios de DNS de criação de registro de localizador ainda se aplicam independentemente de onde a zona DNS é gerenciada.

1. Se ainda não estiver aberto, expanda o menu à esquerda do portal e navegue até o BIG-IP-VM por meio da opção **grupos de recursos**

2. No menu da VM Big-IP, acesse **configurações**  >  **rede**

3. Na exibição de rede BIG-IP-VMs, selecione o primeiro IP secundário na lista suspensa configuração de IP e selecione o link para seu **IP público NIC**

![Captura de tela para mostrar o IP público NIC](./media/f5ve-deployment-plan/networking.png)

4. Abaixo da seção **configurações** no painel esquerdo, selecione **configuração** para abrir o menu de propriedades de IP público e de DNS para o IP secundário selecionado

5. Selecione e **crie** um registro de alias e selecione a **zona DNS** na lista suspensa. Se uma zona DNS ainda não existir, ela poderá ser gerenciada fora do Azure ou você poderá criar uma para o sufixo de domínio que você verificou no Azure AD.

6. Use os detalhes a seguir para criar o primeiro registro de alias de DNS:

 | Campo | Valor |
 |:-------|:-----------|
 |Subscription| Mesma assinatura que o BIG-IP-VM|
 |Zona DNS| Zona DNS que é autoritativa para o sufixo de domínio verificado que seus sites publicados usarão, por exemplo, www.contoso.com |
 |Nome | O nome de host que você especificar será resolvido para o IP público que está associado ao IP secundário selecionado. Certifique-se de definir o DNS correto para mapeamentos de IP. Consulte a última imagem na seção Configurações de rede, por exemplo, intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |Unidades de TTL | Horas |

7. Selecione **criar** para o Azure para salvar essas configurações no DNS público.

8. Deixe o **rótulo de nome DNS (opcional)** e selecione **salvar** antes de fechar o menu de IP público.

9. Repita as etapas 1 a 6 para criar registros DNS adicionais para cada serviço que você planeja publicar usando a configuração guiada de BIG IP.

  Com os registros DNS em vigor, você pode usar qualquer uma das ferramentas online, como o [Verificador DNS](https://dnschecker.org/) , para verificar se um registro criado foi propagado com êxito em todos os servidores DNS públicos globais.

  Se você gerenciar seu namespace de domínio DNS usando um provedor externo como [GoDaddy](https://www.godaddy.com/), você precisará criar registros usando sua própria instalação de gerenciamento de DNS.

>[!NOTE]
>Você também pode usar o arquivo de hosts local de um computador se estiver testando e alterando com frequência os registros DNS. O arquivo de localhosts em um computador Windows pode ser acessado pressionando Win + R no teclado e enviando os **drivers** de palavra na caixa Executar. Apenas lembre-se de que um registro de localhost fornecerá apenas a resolução de DNS para o computador local, e não para outros clientes.

## <a name="client-traffic"></a>Tráfego do cliente

Por padrão, as sub-redes do Azure VNets e associadas são redes privadas que não podem receber tráfego de Internet. A NIC do BIG-IP-VM deve ser anexada ao NSG especificado durante a implantação. Para que o tráfego da Web externo alcance a VM BIG-IP, você deve definir uma regra de NSG de entrada para permitir portas 443 (HTTPS) e 80 (HTTP) por meio da Internet pública.

1. No menu principal **geral** da VM Big-IP, selecione **rede**

2. Selecione **Adicionar** regra de entrada para inserir as seguintes propriedades de regra NSG:

 |     Campo   |   Valor        |
 |:------------|:------------|
 |Fonte| Qualquer|
 |Intervalos de portas de origem| *|
 |Endereços IP de destino|Lista separada por vírgulas de todos os IPs privados secundários de IP de VM|
 |Portas de destino| 80.443|
 |Protocolo| TCP |
 |Ação| Allow|
 |Prioridade|Menor valor disponível entre 100-4096|
 |Nome | Um nome descritivo, por exemplo: `BIG-IP-VM_Web_Services_80_443`|

3. Selecione **Adicionar** para confirmar as alterações e feche o menu **rede** .

O tráfego HTTP e HTTPS de qualquer local agora terá permissão para alcançar todas as suas interfaces secundárias de BIG-IP-VMs. A permissão da porta 80 é útil para permitir que o APM de BIG IP redirecione automaticamente os usuários de HTTP para HTTPS. Essa regra pode ser editada para adicionar ou remover IPs de destino, sempre que necessário.

## <a name="manage-big-ip"></a>Gerenciar BIG-IP

Um sistema BIG-IP é administrado por meio de sua interface do usuário de configuração da Web, que pode ser acessada usando qualquer um dos seguintes métodos recomendados:

- De um computador na rede interna de BIG IP

- De um cliente VPN conectado à rede interna da VM BIG-IP

- Publicado por meio [do Azure proxy de aplicativo do AD](./application-proxy-add-on-premises-application.md)

Você precisará decidir sobre o método mais adequado para poder prosseguir com as configurações restantes. Se necessário, você pode se conectar diretamente à configuração da Web pela Internet Configurando o IP primário do BIG-IP com um IP público. Em seguida, adicione uma regra NSG para permitir o tráfego 8443 para esse IP primário. Certifique-se de restringir a origem ao seu próprio IP confiável, caso contrário, qualquer pessoa poderá se conectar.

Quando estiver pronto, confirme se você pode se conectar à configuração da Web da VM BIG-IP e logon com as credenciais especificadas durante a implantação da VM:

- Se você estiver se conectando de uma VM em sua rede interna ou via VPN, conecte-se diretamente ao BIG-IPs IP primário e à porta de configuração da Web. Por exemplo, `https://<BIG-IP-VM_Primary_IP:8443`. Seu navegador solicitará que a conexão seja insegura, mas você poderá ignorar o prompt até que o BIG-IP seja configurado. Se o navegador insistir em bloquear o acesso, limpe seu cache e tente novamente.

- Se você publicou a configuração da Web via proxy de aplicativo, use a URL definida para acessar a configuração da Web externamente, sem acrescentar a porta, por exemplo, `https://big-ip-vm.contoso.com` . A URL interna deve ser definida usando a porta de configuração da Web, por exemplo, `https://big-ip-vm.contoso.com:8443` 

Um sistema de grande IP também pode ser gerenciado por meio de seu ambiente SSH subjacente, que é normalmente usado para tarefas de linha de comando (CLI) e acesso de nível raiz. Existem várias opções para se conectar à CLI, incluindo:

- [Serviço de bastiões do Azure](../../bastion/bastion-overview.md): permite conexões rápidas e seguras com qualquer VM em uma vNET, de qualquer local

- Conecte-se diretamente por meio de um cliente SSH como saída por meio da abordagem JIT

- Console serial: oferecido na parte inferior da seção suporte e solução de problemas do menu VMs no Portal. Ele não dá suporte a transferências de arquivos.

- Assim como acontece com a configuração da Web, você também pode se conectar diretamente à CLI pela Internet Configurando o IP primário de BIG-IP com um IP público e adicionando uma regra NSG para permitir o tráfego SSH. Novamente, certifique-se de restringir a origem para seu próprio IP confiável, se estiver usando esse método.  

## <a name="big-ip-license"></a>Licença BIG-IP

Um sistema BIG-IP deve ser ativado e provisionado com o módulo APM para que possa ser configurado para serviços de publicação e SHA.

1. Faça logon novamente na configuração da Web e, na página **Propriedades gerais** , selecione **Ativar**

2. No campo **chave de registro de base** , insira a chave que diferencia maiúsculas de minúsculas fornecida por F5

3. Deixe o **método de ativação** definido como **automático** e selecione **Avançar**, o Big-IP validará a licença e exibirá o EULA (contrato de licença de usuário final).

4. Selecione **aceitar** e aguarde a conclusão da ativação, antes de selecionar **continuar**.

5. Faça logon novamente e, na parte inferior da página de resumo da licença, selecione **Avançar**. O BIG-IP agora exibirá uma lista de módulos que fornecem os vários recursos necessários para o SHA. Se você não vir isso, na guia principal, vá para   >  **provisionamento de recursos** do sistema.

6. Verificar a coluna de provisionamento para a política de acesso (APM)

![A imagem mostra o provisionamento de acesso](./media/f5ve-deployment-plan/access-provisioning.png)

7. Selecione **Enviar** e aceite o aviso

8. Seja paciente e aguarde o BIG-IP concluir a iluminação dos novos recursos. Ele pode ser reciclado várias vezes antes de ser totalmente inicializado. 

9. Quando estiver pronto, selecione **continuar** e, na guia **sobre** , selecione **executar o utilitário de instalação**

>[!IMPORTANT]
>As licenças F5 são restritas a serem consumidas por uma única instância BIG-IP VE a qualquer momento. Pode haver motivos para você querer migrar uma licença de uma instância para outra e, se estiver fazendo isso, certifique-se de [revogar](https://support.f5.com/csp/article/K41458656) sua licença de avaliação na instância ativa antes de descomissionar, caso contrário, a licença será perdida permanentemente.

## <a name="provision-big-ip"></a>Provisionar BIG-IP

Proteger o tráfego de gerenciamento de e para BIG-IPs configuração da Web é fundamental. Configure um certificado de gerenciamento de dispositivo para ajudar a proteger o canal de configuração da Web contra o comprometimento.

1. Na barra de navegação à esquerda, vá para gerenciamento de certificados do **sistema**  >    >  **tráfego gerenciamento** de certificados  >  **lista de certificados SSL**  >  **importar**

2. Na lista suspensa **tipo de importação** , selecione **PKCS 12 (IIS)** e **escolha Arquivo**. Localize um certificado da Web SSL que tenha um nome de entidade ou SAN que abrangerá o FQDN que você atribuirá à VM de BIG-IP nas próximas etapas

3. Forneça a senha para o certificado e selecione **importar**

4. Na barra de navegação à esquerda, vá para   >  **plataforma** do sistema

5. Configurar a VM BIG-IP com um nome de host totalmente qualificado e o fuso horário para seu ambiente, seguido por **Update**

![A imagem mostra as propriedades gerais](./media/f5ve-deployment-plan/general-properties.png)

6. Na barra de navegação à esquerda, vá para configuração do **sistema**  >    >  **dispositivo**  >  **NTP**

7. Especifique uma fonte de NTP confiável e selecione **Adicionar**, seguido por **atualização**. Por exemplo, `time.windows.com`

Agora você precisa de um registro DNS para resolver o BIG-IPs FQDN especificado nas etapas anteriores, para seu IP privado primário. Um registro deve ser adicionado ao DNS interno do seu ambiente ou ao arquivo localhost de um computador que será usado para se conectar à configuração da Web de BIG-IP. De qualquer forma, o aviso do navegador não deve mais aparecer quando você se conecta diretamente à configuração da Web. Ou seja, não via proxy de aplicativo ou qualquer outro proxy reverso.

## <a name="ssl-profile"></a>Perfil SSL

Como um proxy reverso, um sistema de grande IP pode atuar como um serviço de encaminhamento simples, também conhecido como um proxy transparente, ou um proxy completo que participa ativamente das trocas entre clientes e servidores.
Um proxy completo cria duas conexões distintas: uma conexão de cliente TCP de front-end junto com uma conexão de servidor TCP de backend separada, acoplada por uma lacuna suave no meio. Os clientes se conectam ao ouvinte de proxy em uma extremidade, de outra forma conhecida como um **servidor virtual**, e o proxy estabelece uma conexão separada independente com o servidor de back-end. Esse é o bidirecional em ambos os lados.
Nesse modo de proxy completo, o sistema de BIG-IP F5 é capaz de inspecionar o tráfego e, portanto, interagir com solicitações e respostas também é possível. Determinadas funções, como balanceamento de carga e otimização de desempenho da Web, bem como serviços de gerenciamento de tráfego mais avançados, como segurança da camada de aplicativo, aceleração da Web, roteamento de página e acesso remoto seguro, dependem dessa funcionalidade.
Ao publicar serviços baseados em SSL, o processo de descriptografar e criptografar o tráfego entre clientes e serviços de back-end é tratado por perfis de BIG IP SSL.

Existem dois tipos de perfis:

- **SSL do cliente**: a maneira mais comum de configurar um sistema Big-IP para publicar serviços internos com o SSL é criar um perfil de cliente SSL. Com um perfil de cliente SSL, um sistema BIG-IP pode descriptografar as solicitações de entrada do cliente antes de enviá-las para um serviço downstream. Em seguida, ele criptografa as respostas de back-end de saída antes de enviá-las aos clientes.

- **SSL do servidor**: para serviços de back-end configurados para https, você pode configurar Big-IP para usar um perfil de servidor SSL. Com um perfil de servidor SSL, o BIG-IP criptografa novamente a solicitação do cliente antes de enviá-la para o serviço de back-end de destino. Quando o servidor retorna uma resposta criptografada, o sistema BIG-IP descriptografa e criptografa novamente a resposta, antes de enviá-la para o cliente, por meio do perfil SSL do cliente configurado.

O provisionamento de perfis SSL de cliente e servidor terá o BIG-IP pré-configurado e pronto para todos os cenários de SHA.

1. Na barra de navegação à esquerda, vá para gerenciamento de certificados do **sistema**  >    >  **tráfego gerenciamento** de certificados  >  **lista de certificados SSL**  >  **importar**

2. Na lista suspensa **tipo de importação** , selecione **PKCS 12 (IIS)**

3. Forneça um nome para o certificado importado, por exemplo,  `ContosoWilcardCert`

4. Selecione **escolher arquivo** para navegar até o certificado Web SSL que o nome da entidade corresponde ao sufixo de domínio que você planeja usar para serviços publicados

5. Forneça a **senha** para o certificado importado e selecione **importar**

6. Na barra de navegação à esquerda, vá para perfis de **tráfego local**  >    >    >  **cliente** SSL e, em seguida, selecione **criar**

7. Na página **novo perfil de cliente SSL** , forneça um nome amigável exclusivo para o novo perfil de cliente SSL e verifique se o perfil pai está definido como **clientssl**

![A imagem mostra atualização Big-IP](./media/f5ve-deployment-plan/client-ssl.png)

8. Marque a caixa de seleção da extrema direita na linha da **cadeia de chaves do certificado** e selecione **Adicionar**

9. Nas três listas suspensas, selecione o certificado curinga que você importou sem uma frase secreta e, em seguida, selecione **Adicionar**  >  **concluído**.

![A imagem mostra a atualização do curinga de Big-IP contoso](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Repita as etapas 6-9 para criar um **perfil de certificado de servidor SSL**. Na faixa de opções superior, selecione  >  **servidor** SSL  >  **criar**.

11. Na página **novo perfil de servidor SSL** , forneça um nome amigável exclusivo para o novo perfil de servidor SSL e verifique se o perfil pai está definido como **serverssl**

12. Marque a caixa de seleção da extrema direita para as linhas de certificado e chave e, na lista suspensa, selecione o certificado importado, seguido por **concluído**.

![A imagem mostra a atualização de todo o perfil do servidor Big-IP](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Não se desespere se não for possível adquirir um certificado SSL, você poderá usar o servidor e os certificados SSL de cliente autoassinados integrados. Você verá apenas um erro de certificado no navegador.

Uma etapa final na preparação de um BIG-IP para SHA é garantir que ele seja capaz de localizar os recursos de sua publicação e também do serviço de diretório do qual ele depende para SSO. Um BIG-IP tem duas fontes de resolução de nomes, começando com seu arquivo local/.../hosts, ou se um registro não for encontrado, o sistema de BIG-IP usará qualquer serviço de DNS com o qual foi configurado. O método de arquivo hosts não se aplica a nós e pools do APM que usam um FQDN.

1. Na configuração da Web, vá para configuração do **sistema**  >    >    >  **DNS** do dispositivo

2. Na **lista servidor** DNS de pesquisa, insira o endereço IP do servidor DNS de seus ambientes

3. Selecione **Adicionar**  >  **atualização**

Como uma etapa separada e opcional, você pode considerar uma [configuração de LDAP](https://somoit.net/f5-big-ip/authentication-using-active-directory) para autenticar administradores Big-IP no AD, em vez de gerenciar contas de Big-IP locais.

## <a name="update-big-ip"></a>Atualizar BIG-IP

Sua VM de BIG-IP deve ser atualizada para desbloquear todas as novas funcionalidades abordadas nas [diretrizes baseadas em cenário](f5-aad-integration.md). Você pode verificar a versão dos sistemas TMOS passando o ponteiro do mouse sobre o nome de host do BIG-IPs na parte superior esquerda da página principal. É recomendável executar o v15. x e superior, obtido do download de [F5](https://downloads.f5.com/esd/productlines.jsp). Use a [orientação](https://support.f5.com/csp/article/K34745165) para atualizar o so do sistema principal (tmos).

Se a atualização do TMOS principal não for possível, considere pelo menos atualizar a configuração guiada sozinho, usando estas etapas.

1. Na guia principal da configuração da Web Big-IP, vá para **acessar**  >  **configuração interativa**

2. Na página **configuração guiada** , selecione **Atualizar configuração guiada**

![A imagem mostra como atualizar Big-IP](./media/f5ve-deployment-plan/update-big-ip.png)

3. Na caixa de diálogo **Atualizar configuração guiada** , **escolha Arquivo** para carregar o pacote de caso de uso baixado e selecione **carregar e instalar**

4. Quando a atualização for concluída, selecione **continuar**.

## <a name="backup-big-ip"></a>Backup de BIG-IP

Com o sistema BIG-IP agora totalmente provisionado, recomendamos fazer um backup completo de sua configuração:

1. Ir para a  >  criação de **arquivos mortos**  >   do sistema

2. Forneça um **nome de arquivo** exclusivo e habilite a **criptografia** com uma senha

3. Defina a opção de **chaves privadas** como **incluir** para também fazer backup de certificados de dispositivo e SSL

4. Selecione **concluído** e aguarde a conclusão do processo

5. Um status de operação será exibido fornecendo um status de resultado de backup. Selecione **OK**

6. Salve o arquivo do conjunto de configuração do usuário (UCS) localmente, escolhendo o link do backup e selecione **baixar**.

Como uma etapa opcional, você também pode fazer um backup de todo o disco do sistema usando [instantâneos do Azure](../../virtual-machines/windows/snapshot-copy-managed-disk.md), o que, ao contrário do backup de configuração da Web, forneceria alguma contingência para o teste entre as versões do tmos ou revertendo para um sistema novo.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>Restaurar BIG-IP

A restauração de um BIG-IP segue um procedimento semelhante ao backup e também pode ser usada para migrar configurações entre VMs de BIG IP. Os detalhes sobre os caminhos de atualização com suporte devem ser observados antes da importação de um backup.

1. Ir para   >  **arquivos mortos** do sistema

2. Escolha o link de um backup que você deseja restaurar ou selecione o botão **carregar** para navegar até um arquivo UCS salvo anteriormente que não está na lista

3. Forneça a frase secreta para o backup e selecione **restaurar**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>No momento da gravação, o cmdlet AzVmSnapshot é limitado à restauração do instantâneo mais recente, com base na data. Os instantâneos são armazenados na raiz do grupo de recursos da VM. Lembre-se de que a restauração de instantâneos reinicia uma VM do Azure, portanto, observe isso com cuidado.

## <a name="additional-resources"></a>Recursos adicionais

-   [Redefinir a senha BIG-IP VE no Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Redefinir a senha sem usar o portal](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Alterar a NIC usada para o gerenciamento de BIG-IP VE](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Sobre rotas em uma única configuração de NIC](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Próximas etapas

Selecione um [cenário de implantação](f5-aad-integration.md) e inicie sua implementação.