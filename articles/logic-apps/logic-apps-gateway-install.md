---
title: Instalar o gateway de dados local
description: Antes de poder acessar dados nas instalações dos Aplicativos Lógicos do Azure, baixe e instale o gateway de dados local
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 4b2559ad20036870c6df5c0662bb973f35155bfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576791"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados no local para os Aplicativos Lógicos do Azure

Antes de poder se [conectar a fontes de dados locais dos Aplicativos Lógicos do Azure](../logic-apps/logic-apps-gateway-connection.md), baixe e instale o [gateway de dados](https://aka.ms/on-premises-data-gateway-installer) local em um computador local. O gateway funciona como uma ponte que fornece transferência de dados rápida e criptografia entre fontes de dados locais e seus aplicativos lógicos. Você pode usar a mesma instalação de gateway com outros serviços de nuvem, como Power Automate, Power BI, Power apps e Azure Analysis Services. Para obter informações sobre como usar o gateway com esses serviços, consulte estes artigos:

* [Gateway de dados local do Microsoft Power Automate](/power-automate/gateway-reference)
* [Gateway de dados local do Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway de dados local do Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway de dados local do Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Este artigo mostra como baixar, instalar e configurar o gateway de dados local para que você possa acessar fontes de dados locais dos Aplicativos Lógicos do Azure. Você também pode saber mais sobre [como o gateway de dados funciona](#gateway-cloud-service) mais adiante neste tópico. Para mais informações sobre o gateway, consulte [O que é um gateway local](/data-integration/gateway/service-gateway-onprem)? Para automatizar tarefas de instalação e de gerenciamento do gateway, visite a galeria do PowerShell para obter os [cmdlets PowerShell do DataGateway](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tiver uma conta do Azure com uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  * Sua conta do Azure precisa ser uma conta corporativa ou de estudante, que se parece com `username@contoso.com` . Você não pode usar contas do B2B (convidado) do Azure ou contas pessoais da Microsoft, como @hotmail.com ou @outlook.com.

    > [!NOTE]
    > Se você se inscreveu para uma oferta de Microsoft 365 e não forneceu seu endereço de email de trabalho, seu endereço pode ser semelhante a `username@domain.onmicrosoft.com` . Sua conta é armazenada em um locatário do Azure AD. Na maioria dos casos, o UPN (nome principal do usuário) da sua conta do Azure é o mesmo que seu endereço de email.

    Para usar uma [assinatura padrão do Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) associada a um conta Microsoft, primeiro [crie um locatário do Azure ad](../active-directory/develop/quickstart-create-new-tenant.md) ou use o diretório padrão. Adicione um usuário com uma senha ao diretório e, em seguida, forneça o acesso a esse usuário à sua assinatura do Azure. Em seguida, você pode entrar durante a instalação do gateway com esse nome de usuário e senha.

  * Sua conta do Azure deve pertencer somente a um único [locatário ou diretório do Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Você precisa usar a mesma conta do Azure para instalar e administrar o gateway no computador local.

  * Ao instalar o gateway, você entra com sua conta do Azure, que vincula a instalação do seu gateway à sua conta do Azure e apenas essa conta. Não é possível vincular a mesma instalação de gateway em várias contas do Azure ou locatários do Azure AD.

  * Posteriormente no portal do Azure, você precisa usar a mesma conta do Azure para criar um recurso de gateway do Azure que esteja vinculado à instalação do seu gateway. Você pode vincular apenas uma instalação de gateway a um recurso de gateway do Azure. No entanto, sua conta do Azure pode ser vinculada a diferentes instalações de gateway que estão associadas a um recurso de gateway do Azure. Seus aplicativos lógicos podem usar esse recurso de gateway em gatilhos e ações que podem acessar fontes de dados locais.

* Aqui estão os requisitos para o computador local:

  **Requisitos mínimos**

  * .NET Framework 4.7.2
  * Versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

  **Requisitos recomendados**

  * CPU com oito núcleos
  * Memória de 8 GB
  * Versão de 64 bits do Windows Server 2012 R2 ou posterior
  * Armazenamento de unidade de estado sólido (SSD) para spooling

  > [!NOTE]
  > O gateway não dá suporte ao Windows Server Core.

* **Considerações relacionadas**

  * Instale o gateway de dados local apenas em um computador local, não em um controlador de domínio. Você não precisa instalar o gateway no mesmo computador que a fonte de dados. Você precisa apenas de um gateway para todas as fontes de dados, portanto, não há necessidade de instalar o gateway para cada fonte de dados.

    > [!TIP]
    > Para minimizar a latência, você pode instalar o gateway o mais próximo possível da sua fonte de dados ou no mesmo computador, presumindo que você tenha permissões.

  * Instale o gateway em um computador local que esteja em uma rede cabeada, conectado à internet, sempre ligado e que não entre em modo de suspensão. Caso contrário, o gateway não poderá ser executado e o desempenho pode ser prejudicado em uma rede sem fio.

  * Se você planeja usar a autenticação do Windows, lembre-se de instalar o gateway em um computador que seja membro do mesmo ambiente do AD DS que suas fontes de dados.

  * Você deve selecionar a mesma região para a instalação do gateway e ao criar mais tarde o recurso de gateway do Azure para seu aplicativo lógico. Por padrão, essa região é o mesmo local que o seu locatário do Azure AD que gerencia sua conta de usuário do Azure. No entanto, você pode alterar o local durante a instalação do gateway ou posterior.

    > [!IMPORTANT]
    > Durante a configuração do gateway, o comando **alterar região** não estará disponível se você tiver entrado com sua conta do Azure governamental, que está associada a um locatário do Azure Active Directory (Azure AD) na [nuvem do Azure governamental](../azure-government/compare-azure-government-global-azure.md). O gateway usa automaticamente a mesma região que o locatário do Azure AD da sua conta de usuário.
    > 
    > Para continuar usando sua conta do Azure governamental, mas configure o gateway para funcionar na nuvem comercial global multilocatário do Azure em vez disso, primeiro entre durante a instalação do gateway com o `prod@microsoft.com` nome de usuário. Essa solução força o gateway a usar a nuvem do Azure multilocatário global, mas ainda permite que você continue usando sua conta do Azure governamental.

  * Se estiver atualizando a instalação do gateway, desinstale primeiro o gateway atual para ter uma experiência mais limpa.

    A melhor prática é usar uma versão com suporte. A Microsoft lança, todo mês, uma nova atualização para o gateway de dados local e oferece suporte atualmente apenas às últimas seis versões do gateway de dados local. Se tiver problemas com a versão que está usando, tente [atualizar para a versão mais recente](https://aka.ms/on-premises-data-gateway-installer) já que seu problema pode ter sido resolvido na versão mais recente.

  * O gateway tem dois modos: modo padrão e modo pessoal, que se aplica somente ao Power BI. Você não pode ter mais de um gateway executando no mesmo modo no mesmo computador.

  * Os Aplicativos Lógicos do Azure têm suporte para operações de leitura e gravação por meio do gateway. No entanto, essas operações têm [limites de tamanho da carga](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalar gateway de dados

1. [Baixe e execute o instalador do gateway em um computador local](https://aka.ms/on-premises-data-gateway-installer).

1. Examine os requisitos mínimos, mantenha o caminho de instalação padrão, aceite os termos de uso e, em seguida, selecione **Instalar**.

   ![Revise os requisitos e aceite os termos de uso](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Depois que o gateway for instalado com êxito, forneça o endereço de email para a conta do Azure e, em seguida, selecione **Conectar**, por exemplo:

   ![Entrar com uma conta corporativa ou de estudante](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   A instalação do gateway pode vincular apenas uma conta do Azure.

1. Selecione **Registrar um novo gateway neste computador** > **Avançar**. Essa etapa registra sua instalação do gateway com o [serviço de nuvem do gateway](#gateway-cloud-service).

   ![Registrar gateway no computador local](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Forneça essas informações para a instalação do gateway:

   * Um nome de gateway que é exclusivo em seu locatário do Azure Active Directory
   * A chave de recuperação, com pelo menos oito caracteres, que você quer usar
   * Confirmação para a chave de recuperação

   ![Forneça as informações para a instalação do gateway](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Salve e mantenha a chave de recuperação em um local seguro. Você precisará dessa chave quando quiser alterar o local, mover, recuperar ou assumir uma instalação de gateway.

   Observe a ação **Adicionar a um cluster existente do gateway** para instalar gateways adicionais para [cenários de alta disponibilidade](#high-availability).

1. Verifique a região do serviço de nuvem do gateway e a [instância de mensagens do barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) que é usada pela instalação do seu gateway. Por padrão, essa região é o mesmo local do locatário do Azure Active Directory da sua conta do Azure.

   ![Confirmar região para serviço de gateway e barramento de serviço](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Para aceitar a região padrão, selecione **Configurar**. No entanto, se a região padrão não for a mais próxima de você, você poderá alterar a região.

   *Por que alterar a região de instalação do gateway?*

   Por exemplo, para reduzir a latência, é possível alterar a região do gateway para a mesma região que o aplicativo lógico. Ou então, é possível selecionar a região mais próxima à fonte de dados local. O *recurso de gateway no Azure* e o aplicativo lógico podem ter locais diferentes.

   1. Ao lado de região atual, selecione **Alterar Região**.

      ![Alterar a região do gateway atual](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na próxima página, abra a lista **Selecionar Região**, escolha a região e selecione **Concluído**.

      ![Selecionar outra região para o serviço de gateway](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Examine as informações na janela de confirmação final. Este exemplo usa a mesma conta para Aplicativos Lógicos, Power BI, Power Apps e Power Automate, para que o gateway esteja disponível para todos esses serviços. Quando estiver pronto, selecione **Fechar**.

   ![Confirmar informações do gateway de dados](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Agora [crie o recurso do Azure para a instalação do gateway](../logic-apps/logic-apps-gateway-connection.md).

<a name="communication-settings"></a>

## <a name="check-or-adjust-communication-settings"></a>Verificar ou ajustar as configurações de comunicação

O gateway de dados local depende do [sistema de mensagens do barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) para conectividade de nuvem e estabelece as conexões de saída correspondentes à região do Azure associada do gateway. Se o seu ambiente de trabalho exigir que o tráfego passe por um proxy ou firewall para acessar a Internet, essa restrição poderá impedir que o gateway de dados local se conecte ao serviço de nuvem do gateway e às mensagens do barramento de serviço do Azure. O gateway tem várias configurações de comunicação que você pode ajustar.

Um cenário de exemplo é o local em que você usa conectores personalizados que acessam recursos locais usando o recurso de gateway de dados local no Azure. Se você também tiver um firewall que limita o tráfego para endereços IP específicos, precisará configurar a instalação do gateway para permitir o acesso aos *[endereços IP de saída](logic-apps-limits-and-config.md#outbound)dos conectores gerenciados* correspondentes. *Todos* os aplicativos lógicos na mesma região usam os mesmos intervalos de endereço IP.

Para saber mais, consulte esses tópicos:

* [Ajustar configurações de comunicação para gateway de dados local](/data-integration/gateway/service-gateway-communication)
* [Definir configurações de proxy para o gateway de dados local](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Suporte de alta disponibilidade

Para evitar pontos únicos de falha para acesso a dados locais, você pode ter várias instalações de gateway (somente no modo padrão), cada uma em um computador diferente e configurá-las como um cluster ou grupo. Dessa forma, se o gateway primário não estiver disponível, as solicitações de dados serão roteadas para o segundo gateway e assim por diante. Como é possível instalar apenas um gateway padrão em um computador, você deve instalar cada gateway adicional que está no cluster em um computador diferente. Todos os conectores que funcionam com o gateway de dados local têm suporte para HA.

* Você precisa ter pelo menos um gateway instalado com a mesma conta do Azure do gateway primário e a chave de recuperação para essa instalação.

* O gateway primário deve estar executando a atualização do gateway de novembro de 2017 ou posterior.

Depois de configurar o gateway primário, quando for instalar outro gateway, selecione **Adicionar a um cluster de gateway existente**, selecione o gateway primário (o primeiro gateway que você instalou) e forneça a chave de recuperação para esse gateway. Para obter mais informações, consulte [Clusters de alta disponibilidade para gateway de dados locais](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Alterar o local, migrar, restaurar ou assumir um gateway existente

Se for necessário alterar o local do gateway, mover a instalação do gateway para um novo computador, recuperar um gateway danificado ou assumir a propriedade de um gateway existente, você precisará da chave de recuperação que foi fornecida durante a instalação do gateway.

> [!NOTE]
> Antes de restaurar o gateway no computador que tem a instalação do gateway original, desinstale primeiro o gateway nesse computador. Essa ação desconecta o gateway original.
> Se você remover ou excluir um cluster de gateway para qualquer serviço de nuvem, não será possível restaurar esse cluster.

1. Execute o instalador do gateway no computador que tem o gateway existente.

1. Espere o instalador abrir e entre com a mesma conta do Azure usada para instalar o gateway.

1. Selecione **Migrar, restaurar ou assumir um gateway existente** > **Avançar**, por exemplo:

   ![Selecionar "Migrar, restaurar ou assumir um gateway existente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selecione entre os clusters e gateways disponíveis e insira a chave de recuperação do gateway selecionado, por exemplo:

   ![Selecionar gateway e fornecer chave de recuperação](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Para alterar a região, selecione **Alterar Região** e selecione a nova região.

1. Quando estiver pronto, selecione **Configurar** para que você possa concluir a tarefa.

## <a name="tenant-level-administration"></a>Administração no nível do locatário

Para obter visibilidade de todos os gateways de dados locais em um locatário do Azure Active Directory, os administradores globais nesse locatário podem entrar no [centro do administrador do Power Platform](https://powerplatform.microsoft.com) como administrador de locatários e selecionar a opção **Gateway de dados**. Para mais informações, consulte [Administração no nível do locatário para gateway de dados local](/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Reiniciar o gateway

Por padrão, a instalação do gateway no computador local é executada como uma conta de serviço do Windows denominada “serviço do gateway de dados local”. No entanto, a instalação do gateway usa o nome do `NT SERVICE\PBIEgwService` como suas credenciais de conta “Fazer logon como” e tem permissões “Fazer logon como um serviço”.

> [!NOTE]
> Sua conta de serviço Windows é diferente da conta usada para conexão a fontes de dados locais e da conta do Azure usada para entrar nos serviços de nuvem.

Como qualquer outro serviço Windows, você pode iniciar e parar o gateway de várias maneiras. Para mais informações, consulte [Reiniciar um gateway de dados local](/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Como funciona o gateway

Os usuários na sua organização podem acessar dados locais para os quais eles já têm acesso autorizado. No entanto, antes que esses usuários possam se conectar à sua fonte de dados local, você precisa instalar e configurar um gateway de dados local. Normalmente, um administrador é a pessoa que instala e configura um gateway. Essas ações podem exigir permissões de administrador do servidor ou conhecimento especial sobre seus servidores locais.

O gateway ajuda a possibilitar uma comunicação em segundo plano mais rápida e mais segura. Essa comunicação flui entre um usuário na nuvem, no serviço de nuvem do gateway e na sua fonte de dados local. O serviço de nuvem do gateway criptografa e armazena suas credenciais da fonte de dados e os detalhes do gateway. O serviço também encaminha consultas e seus resultados entre o usuário, o gateway e sua fonte de dados local.

O gateway funciona com firewalls e usa apenas conexões de saída. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. O gateway envia os dados de fontes locais em canais criptografados por meio de [mensagens do barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md). O barramento de serviço cria um canal entre o gateway e o serviço de chamada, mas não armazena nenhum dado. Todos os dados que trafegam através do gateway são criptografados.

![Arquitetura do gateway de dados local](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Dependendo do serviço de nuvem, talvez seja necessário configurar uma fonte de dados para o gateway.

Estas etapas descrevem o que acontece quando você interage com um elemento que está conectado a uma fonte de dados local:

1. O serviço de nuvem cria uma consulta com as credenciais criptografadas para a fonte de dados. Em seguida, o serviço envia a consulta e as credenciais para a fila do gateway para processamento.

1. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para o sistema de mensagens do barramento de serviço do Azure.

1. As mensagens do barramento de serviço do Azure enviam as solicitações pendentes para o gateway.

1. O gateway obtém a consulta, descriptografa as credenciais e conecta-se a uma ou mais fontes de dados com essas credenciais.

1. O gateway envia a consulta à fonte de dados para execução.

1. Os resultados são enviados da fonte de dados de volta ao gateway e, em seguida, para o serviço de nuvem do gateway. O serviço de nuvem do gateway então usa os resultados.

### <a name="authentication-to-on-premises-data-sources"></a>Autenticação para fontes de dados locais

Uma credencial armazenada é usada para se conectar do gateway em fontes de dados locais. Qualquer que seja o usuário, o gateway usará a credencial armazenada para se conectar. Pode haver exceções de autenticação para serviços específicos, como DirectQuery e LiveConnect para Analysis Services no Power BI.

### <a name="azure-active-directory-azure-ad"></a>Active Directory do Azure (Azure AD)

Os serviços de nuvem da Microsoft usam o [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) para autenticar usuários. Um locatário do Azure Active Directory contém nomes de domínio e grupos de segurança. Normalmente, o endereço de email que você usa para entrar é igual ao UPN da sua conta.

### <a name="what-is-my-upn"></a>Qual é o meu UPN?

Se você não for um administrador de domínio, talvez não saiba seu UPN. Para localizar o UPN da sua conta, execute o comando `whoami /upn` na sua estação de trabalho. Embora ele se pareça com um endereço de email, esse é o UPN da sua conta de domínio local.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Sincronizar um Active Directory local com o Azure Active Directory

O UPN para suas contas locais do Active Directory e contas do Azure Active Directory deve ser o mesmo. Portanto, verifique se cada conta local do Active Directory corresponde à sua conta do Azure Active Directory. Os serviços de nuvem conhecem apenas as contas no Azure Active Directory. Portanto, você não precisa adicionar uma conta ao seu Active Directory local. Se a conta não existir no Azure Active Directory, você não poderá usar essa conta.

Veja como você pode fazer a correspondência das suas contas do Active Directory local com o Azure Active Directory.

* Adicione contas manualmente ao Azure Active Directory.

  Crie uma conta no portal do Azure ou no centro de administração do Microsoft 365. Verifique se o nome da conta corresponde ao UPN para a conta do Active Directory local.

* Sincronize contas locais com seu locatário do Azure Active Directory usando a ferramenta Azure Active Directory Connect.

  A ferramenta Azure Active Directory Connect oferece opções de sincronização de diretório e configuração de autenticação. Essas opções incluem sincronização de hash de senha, autenticação de passagem e federação. Se você não for um administrador de locatários ou um administrador de domínio local, entre em contato com o administrador de TI para obter a configuração do Azure AD Connect. O Azure AD Connect garante a correspondência do UPN do Azure Active Directory com o UPN do AD DS local. Essa correspondência será útil se você estiver usando conexões ativas do Analysis Services com recursos do Power BI ou de SSO.

  > [!NOTE]
  > A sincronização de contas com a ferramenta Azure AD Connect cria novas contas no seu locatário do Azure Active Directory.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Perguntas frequentes e solução de problemas

* [Perguntas frequentes de gateway de dados no local](/data-integration/gateway/service-gateway-onprem-faq)
* [Solucionar problemas do gateway de dados local](/data-integration/gateway/service-gateway-tshoot)
* [Monitorar e otimizar o desempenho do gateway](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md)
* [Recursos de integração corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores de Aplicativos Lógicos do Azure](../connectors/apis-list.md)
