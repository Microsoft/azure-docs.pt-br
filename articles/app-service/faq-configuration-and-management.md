---
title: Perguntas frequentes de configuração - Serviço de Aplicativo do Azure | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre problemas de configuração e gerenciamento para a funcionalidade do Aplicativos Web do Serviço de Aplicativo do Azure.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 6f86a8465bcbd3d88ffb7909cac53c3fd38c3af6
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489445"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Perguntas frequentes sobre configuração e gerenciamento de aplicativos Web no Azure

Este artigo apresenta perguntas frequentes (FAQs) sobre problemas de configuração e gerenciamento para a [funcionalidade do Aplicativos Web do Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Há limitações que devo estar atento se eu quiser mover os recursos do Serviço de Aplicativo?

Se você planeja mover os recursos do Serviço de Aplicativo para um novo grupo de recursos ou assinatura, há algumas limitações a serem consideradas. Para saber mais informações, consulte [Limitações do Serviço de Aplicativo](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Como usar um nome de domínio personalizado para meu aplicativo Web?

Para respostas para perguntas comuns sobre como usar um nome de domínio personalizado com seu aplicativo web do Azure, consulte nosso vídeo de sete minutos [Adicionar um nome de domínio personalizado](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). O vídeo oferece uma explicação passo a passo de como adicionar um nome de domínio personalizado. Descreve como usar sua própria URL em vez da URL *. azurewebsites.net com seu aplicativo web do Serviço de Aplicativo. Você também pode ver uma explicação detalhada de [como mapear um nome de domínio personalizado](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Como comprar um novo domínio personalizado para meu aplicativo web?

Para saber como adquirir e configurar um domínio personalizado para seu aplicativo web do Serviço de Aplicativo, consulte [Comprar e configurar um nome de domínio personalizado no Serviço de Aplicativo](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Como carregar e configurar um certificado SSL existente para meu aplicativo web?

Para saber como carregar e configurar um certificado SSL personalizado existente, consulte [Associar um certificado SSL personalizado existente para um aplicativo web do Azure](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Como comprar e configurar um novo certificado SSL no Azure para meu aplicativo web?

Para saber como adquirir e configurar um certificado SSL para seu aplicativo web do Serviço de Aplicativo, consulte [Adicionar um certificado SSL ao seu aplicativo do Serviço de Aplicativo](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Como mover recursos do Application Insights?

No momento, o Azure Application Insights não oferece suporte para a operação de movimentação. Se o grupo de recursos original inclui um recurso do Application Insights, você não pode mover esse recurso. Se você incluir o recurso Application Insights ao tentar mover um aplicativo do Serviço de Aplicativo, toda a operação de movimentação falhará. No entanto, o Application Insights e o plano do Serviço de Aplicativo não precisam residir no mesmo grupo de recursos que o aplicativo para que o aplicativo funcione corretamente.

Para saber mais informações, consulte [Limitações do Serviço de Aplicativo](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Onde posso encontrar uma lista de verificação de orientação e saber mais sobre o recurso para mover operações?

[Limitações do Serviço de Aplicativo](../azure-resource-manager/move-limitations/app-service-move-limitations.md) mostra como mover recursos para uma nova assinatura ou para um novo grupo de recursos na mesma assinatura. Você pode obter informações sobre a lista de verificação de movimentação de recursos, saber quais serviços de suporte para a operação de movimentação e saber mais sobre as limitações do Serviço de Aplicativo e outros tópicos.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Como definir o fuso horário do servidor para o meu aplicativo web?

Para definir o fuso horário do servidor para o meu aplicativo web:

1. No portal do Azure, na sua assinatura do Serviço de Aplicativo, vá até o menu **Configurações de aplicativo**.
2. Em **Configurações do aplicativo**, adicionar essa configuração:
    * Chave = WEBSITE_TIME_ZONE
    * Valor = *o fuso horário que você deseja*
3. Clique em **Salvar**.

Consulte a coluna **Fuso horário** no artigo [Fusos Horários Padrão](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) para ver os valores aceitos.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Por que meus WebJobs contínuos às vezes falham?

Por padrão, os aplicativos Web serão descarregados se estiverem ociosos por um período de tempo definido. Isso permite ao sistema conservar recursos. Em planos básico e padrão, você pode ativar a configuração **Sempre Ativo** para manter o aplicativo web carregado o tempo todo. Se seu aplicativo web executar continuamente WebJobs, você deve habilitar a opção **Sempre Ativo**; ou é possível que os WebJobs não sejam executados com segurança. Para obter mais informações, consulte [Criar um WebJob de execução contínua](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Como obter o endereço IP de saída para o meu aplicativo web?

Para obter a lista de endereços IP de saída para seu aplicativo web:

1. No portal do Azure, na folha seu aplicativo web, vá até o menu **Propriedades**.
2. Pesquisar **endereços IP de saída**.

A lista de endereços IP de saída é exibida.

Para saber como obter seu endereço IP de saída se seu site estiver hospedado em um ambiente de Serviço do Aplicativo, consulte [Endereços de rede de saída](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Como obter um endereço IP de entrada dedicado ou reservado para meu aplicativo Web?

Para configurar um endereço IP dedicado ou reservado para chamadas de entrada feitas para o site de aplicativo do Azure, instale e configure um certificado SSL com base em IP.

Observe que, para usar um endereço IP dedicado ou reservado para chamadas de entrada, o plano do Serviço de Aplicativo deve ser em um plano de serviço básico ou superior.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Posso exportar meu certificado do Serviço de Aplicativo para uso fora do Azure, como um site hospedado em outro lugar? 

Certificados de Serviço de Aplicativo são considerados recursos do Azure. Eles não estão previstos para ser usados fora de seus serviços do Azure. Você não pode exportá-los para usar fora do Azure. Para obter mais informações, consulte [Perguntas frequentes para certificados de Serviço de Aplicativo e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Posso exportar o certificado do Serviço de Aplicativo para usar com outros serviços de nuvem do Azure?

O portal fornece uma experiência de primeira classe para implantar um certificado de Serviço de Aplicativo por meio do Azure Key Valut para aplicativos de Serviço de Aplicativo. No entanto, podemos ter recebido solicitações de clientes para usar esses certificados fora da plataforma do Serviço de Aplicativo, por exemplo, com as máquinas virtuais do Azure. Para saber como criar uma cópia local do PFX do seu certificado do Serviço de Aplicativo, você pode usar o certificado com outros recursos do Azure, consulte [Criar uma cópia local do PFX de um certificado de Serviço de Aplicativo](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Para obter mais informações, consulte [Perguntas frequentes para certificados de Serviço de Aplicativo e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Por que vejo a mensagem "Foi parcialmente bem-sucedida" ao tentar fazer backup de meu aplicativo web?

Uma causa comum de falha de backup é que alguns arquivos estão em uso pelo aplicativo. Arquivos que estão em uso estão bloqueados durante a realização do backup. Isso impede a realização de backup desses arquivos e pode resultar em um status "Foi parcialmente bem-sucedida". Você pode potencialmente evitar que isso ocorra, excluindo arquivos do processo de backup. Você pode optar por fazer backup somente do que for necessário. Para obter mais informações, consulte [Fazer backup apenas das partes importantes do seu site com aplicativos web do Azure](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Como remover um cabeçalho de resposta HTTP?

Para remover os cabeçalhos de resposta HTTP, atualize o arquivo web.config do seu site. Para obter mais informações, consulte [Remover cabeçalhos de servidor padrão em seus sites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>O Serviço de Aplicativo é compatível com o PCI Standard 3.0 e 3.1?

No momento, a funcionalidade de aplicativos Web do Serviço de Aplicativo do Azure está em conformidade com os dados segurança padrão (DSS) PCI versão 3.0 nível 1. PCI DSS versão 3.1 está em nosso roteiro. Planejamento já está em andamento para adoção do padrão mais recente.

A certificação de versão 3.1 PCI DSS requer a desabilitação do protocolo TLS (TLS) 1.0. Atualmente, desabilitar o TLS 1.0 não é uma opção para a maioria dos planos de Serviço de Aplicativo. No entanto, se você usar o ambiente de Serviço de Aplicativo ou está disposto a migrar sua carga de trabalho para o ambiente de Serviço de Aplicativo, você pode obter maior controle do seu ambiente. Isso envolve desabilitar o TLS 1.0 entrando em contato com o suporte do Azure. Em breve, planejamos disponibilizar essas configurações para os usuários.

Para obter mais informações, consulte [Conformidade do aplicativo web do Serviço de Aplicativo do Microsoft Azure com o PCI Standard 3.0 e 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Como usar os slots de implantação e o ambiente de preparo?

Nos planos Standard e Premium do Serviço de Aplicativo, ao implantar seu aplicativo web a Serviço de Aplicativo, você pode implantar um slot de implantação separado em vez de no slot de produção padrão. Os slots de implantação são aplicativos Web dinâmicos com seus próprios nomes de host. Os elementos de configuração e conteúdo de aplicativo Web podem ser permutados entre dois slots de implantação, incluindo o slot de produção.

Para obter mais informações sobre como usar os slots de implantação, consulte [Configurar um ambiente de preparo no Serviço de Aplicativo](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Como acessar e examine os logs de WebJob?

Para examinar os logs do WebJob:

1. Entre no seu [site Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Selecione o WebJob.
3. Selecione o botão **Alternar saída**.
4. Para baixar o arquivo de saída, selecione o link **Baixar**.
5. Para execuções individuais, selecione **Invocar individuais**.
6. Selecione o botão **Alternar saída**.
7. Selecione o link Baixar.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Estou tentando usar conexões híbridas com o SQL Server. Por que vejo a mensagem "System. "System.OverflowException: A operação aritmética resultou em uma condição de estouro”?

Se você usar conexões híbridas para acessar o SQL Server, uma atualização do Microsoft .NET em 10 de maio de 2016 pode causar falha nas conexões. Você verá esta mensagem:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Resolução

A exceção foi causada por um problema com o Gerenciador de Conexões Híbridas que desde então foi corrigido. Certifique-se de [atualizar o Gerenciador de Conexões Híbridas](https://go.microsoft.com/fwlink/?LinkID=841308) para resolver esse problema.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Como fazer adicionar uma regra de regravação de URL?

Para adicionar uma regra de reescrita de URL, crie um arquivo Web. config com as entradas de configuração relevantes na pasta **wwwroot** . Para obter mais informações, [consulte serviços de Azure App: Entendendo a regravação](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/)de URL.

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Como controlar o tráfego de entrada para o Serviço de Aplicativo?

No nível do site, você tem duas opções para controlar o tráfego de entrada para o Serviço de Aplicativo:

* Ative restrições de IP dinâmico. Para saber como ativar restrições de IP dinâmico, consulte [restrições de domínio e IP para sites do Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Ative a Segurança de Módulo. Para saber como ativar a Segurança de Módulo, consulte [ModSecurity firewall do aplicativo web em sites do Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Se você usar o ambiente de Serviço de Aplicativo, você pode usar o [firewall Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Como bloquear portas em um aplicativo web do Serviço de Aplicativo?

No ambiente do locatário compartilhado do Serviço de Aplicativo, não é possível bloquear portas específicas devido à natureza da infraestrutura. As portas TCP 4016, 4018 e 4020 também podem ser abertas para depuração remota do Visual Studio.

No ambiente de Serviço de Aplicativo, você tem controle total sobre o tráfego de entrada e saído. Você pode usar Grupos de segurança de rede para restringir ou bloquear portas específicas. Para obter mais informações sobre o ambiente de Serviço de Aplicativo, consulte [apresentando o ambiente de Serviço de Aplicativo](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Como capturar um rastreamento F12?

Você tem duas opções para capturar um rastreamento F12:

* Rastreamento de HTTP F12
* Saída do console F12

### <a name="f12-http-trace"></a>Rastreamento de HTTP F12

1. No Internet Explorer, vá para o seu site. É importante entrar antes de fazer as próximas etapas. Caso contrário, o rastreamento F12 captura dados confidenciais de entrada.
2. Pressione F12.
3. Verifique se a guia **Rede** está selecionada e, em seguida, selecione o botão verde **Reproduzir**.
4. Siga as etapas que reproduza o problema.
5. Selecione o botão vermelho **Parar**.
6. Selecione o botão **Salvar** (ícone de disco) e, em seguida, salve o arquivo HAR (no Internet Explorer e Microsoft Edge) *ou* clique com o botão direito do mouse no arquivo HAR e, em seguida, selecione **Salvar como HAR com conteúdo** (no Chrome).

### <a name="f12-console-output"></a>Saída do console F12

1. Selecione a guia **Console**.
2. Para cada guia que contém itens de maior que zero, selecione a guia (**Erro**, **Aviso**, ou **Informações**). Se a guia não estiver selecionada, o ícone de guia é cinza ou preto quando você move o cursor para longe dela.
3. Clique com o botão direito na área de mensagem do painel e, em seguida, selecione **Copiar tudo**.
4. Cole o texto copiado em um arquivo e, em seguida, salve o arquivo.

Para exibir um arquivo HAR, você pode usar o [Visualizador HAR](https://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Por que recebo um erro ao tentar se conectar a um aplicativo web do Serviço de Aplicativo em uma rede virtual que está conectado ao ExpressRoute?

Se você tentar conectar um aplicativo web do Azure a uma rede virtual que está conectada ao ExpressRoute do Azure, ele falhará. A seguinte mensagem é exibida: “O gateway não é um gateway de VPN”.

No momento, você não pode ter conexões VPN de ponto para site a uma rede virtual que está conectado ao ExpressRoute. A VPN de ponto a site e o ExpressRoute não podem coexistir para a mesma rede virtual. Para obter mais informações, consulte [Limites e limitações de conexões VPN site a site e ExpressRoute](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Como conectar um aplicativo web do Serviço de Aplicativo a uma rede virtual que tem um gateway de roteamento estático (baseado em políticas)?

No momento, não há suporte para conectar um aplicativo web do Serviço de Aplicativo a uma rede virtual que tem um gateway de roteamento estático (baseado em políticas). Se a sua rede virtual de destino já existe, ela deve ter a VPN ponto a site habilitada com um gateway de roteamento dinâmico antes que possa ser conectada a um aplicativo. Se o gateway é definido como o roteamento estático, não é possível ativar uma VPN ponto a site. 

Para obter mais informações, consulte [Integrar um aplicativo a uma rede virtual do Azure](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Em meu Ambiente do Serviço de Aplicativo, por que só posso criar um Plano do Serviço de Aplicativo, embora tenha dois processadores disponíveis?

Para proporcionar tolerância a falhas, um Ambiente do Serviço de Aplicativo exige pelo menos um recurso de computação adicional alocado para cada pool de trabalho. O recurso de computação adicional não pode ser atribuído a uma carga de trabalho.

Para obter mais informações, consulte [Como criar um Ambiente do Serviço de Aplicativo](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Por que vejo tempos limite ao tentar criar um Ambiente de Serviço de Aplicativo?

Às vezes, a criação de um Ambiente do Serviço de Aplicativo falhará. Nesse caso, você pode ver o seguinte erro nos logs de atividade:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Para resolver esse problema, certifique-se de que nenhuma das seguintes condições é verdadeira:
* A sub-rede é muito pequena.
* A sub-rede não está vazia.
* O ExpressRoute impede os requisitos de conectividade de rede de um Ambiente de Serviço de Aplicativo.
* Um Grupo de segurança de rede incorreto impede os requisitos de conectividade de rede de um Ambiente de Serviço de Aplicativo.
* O túnel forçado é ativado.

Para obter mais informações, consulte [Problemas frequentes ao implantar (criar) um novo Ambiente do Serviço de Aplicativo do Azure](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Por que não consigo excluir meu plano do Serviço de Aplicativo?

Você não pode excluir um plano do Serviço de Aplicativo se todos os aplicativos do Serviço de Aplicativo estão associados ao plano do Serviço de Aplicativo. Antes de excluir um plano do Serviço de Aplicativo, remova todos os aplicativos do Serviço de Aplicativo associados do plano do Serviço de Aplicativo.

## <a name="how-do-i-schedule-a-webjob"></a>Como agendar um WebJob?

Você pode criar um WebJob agendado usando expressões CRON:

1. Crie um arquivo settings.job.
2. Neste arquivo JSON, inclua uma propriedade de agendamento usando uma expressão Cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Para obter mais informações sobre WebJobs agendados, consulte [Criar um WebJob agendado usando uma expressão Cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Como executar testes de penetração para meu aplicativo de Serviço de Aplicativo?

Para executar o teste de penetração, [enviar uma solicitação](https://portal.msrc.microsoft.com/en-us/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Como configurar um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo usando o Gerenciador de Tráfego?

Para saber como usar um nome de domínio personalizado com um aplicativo de Serviço de Aplicativo que usa o Azure Traffic Manager para balanceamento de carga, consulte [Configurar um nome de domínio personalizado para um aplicativo web do Azure com o Gerenciador de Tráfego](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Meu certificado de Serviço de Aplicativo está sinalizado para fraude. Como resolver isso?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Durante a verificação de domínio de uma compra de certificado de Serviço de Aplicativo, você poderá ver a seguinte mensagem:

"Seu certificado foi sinalizado para uma possível fraude. A solicitação está sendo examinada. Se o certificado não se tornar utilizável dentro de 24 horas, entre em contato com o suporte do Azure.”

Como a mensagem indica, esse processo de verificação de fraude pode levar até 24 horas para ser concluído. Durante esse tempo, você continuará vendo a mensagem.

Se seu certificado de Serviço de Aplicativo continua mostrando esta mensagem após 24 horas, execute o seguinte script do PowerShell. O script contata o [provedor de certificado](https://www.godaddy.com/) diretamente para resolver o problema.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Como funcionam a autenticação e autorização no Serviço de Aplicativo?

Para obter documentação detalhada para autenticação e autorização no Serviço de Aplicativo, consulte os documentos sobre várias entradas de provedor de identidade:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Conta da Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Como redirecionar o domínio padrão *. azurewebsites.net para o domínio personalizado do meu aplicativo web do Azure?

Quando você cria um novo site por meio de aplicativos Web no Azure, um domínio padrão *sitename*.azurewebsites.net é atribuído ao seu site. Se você adicionar um nome do host personalizado para seu site e não deseja que os usuários possam acessar seu padrão domínio *. azurewebsites.net, você pode redirecionar a URL padrão. Para saber como redirecionar todo o tráfego do domínio de padrão do seu site para seu domínio personalizado, consulte [Redirecionar o domínio padrão para seu domínio personalizado em aplicativos web do Azure](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Como determinar qual versão do .NET versão está instalada no Serviço de Aplicativo?

É a maneira mais rápida de localizar a versão do Microsoft .NET que está instalada no Serviço de Aplicativo usando o console do Kudu. Você pode acessar o console do Kudu no portal ou usando a URL do seu aplicativo de Serviço de Aplicativo. Para obter instruções detalhadas, consulte [Determinar a versão instalada do .NET no Serviço de Aplicativo](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Por que o dimensionamento automático não está funcionando conforme o esperado?

Se o dimensionamento automático do Azure não aumentou ou diminuiu a instância de aplicativo Web conforme o esperado, você pode estar executando em um cenário no qual escolhemos intencionalmente por não dimensionar para evitar um loop infinito devido a "oscilações." Isso geralmente acontece quando não há uma margem suficiente entre os limites de escalabilidade horizontal e escala. Para saber como evitar "oscilações" e ler sobre outras práticas recomendadas de dimensionamento automático, consulte [Práticas recomendadas de dimensionamento automático](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Por que o dimensionamento automático, às vezes, dimensiona apenas parcialmente?

Dimensionamento automático é acionado quando métricas excedem os limites pré-configurados. Às vezes, você pode notar que a capacidade é apenas parcialmente preenchida em comparação com o esperado. Isso pode ocorrer quando o número de instâncias que você deseja não está disponível. Nesse cenário, o dimensionamento automático preenche parcialmente com o número de instâncias disponíveis. Dimensionamento automático, em seguida, executa a lógica de rebalanceamento para obter maior capacidade. Aloca as instâncias restantes. Observe que isso pode levar alguns minutos.

Se você não vir o número de instâncias esperado após alguns minutos, pode ser porque a recarga parcial foi suficiente para trazer as métricas dentro dos limites. Ou, o dimensionamento automático pode ter reduzido porque atingiu o limite inferior de métricas.

Se nenhuma dessas condições se aplicam, e o problema persistir, envie uma solicitação de suporte.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Como ativar a compactação HTTP para o conteúdo?

Para ativar a compactação para tipos de conteúdo estáticos e dinâmicos, adicione o seguinte código ao arquivo web.config do nível do aplicativo:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Você também pode especificar os tipos MIME específicos para dinâmico e estáticos que você deseja compactar. Para obter mais informações, consulte nossa resposta a uma pergunta de Fórum no [Configurações de compactação http em um site do Azure simples](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Como migrar de um ambiente local para o ambiente do Serviço de Aplicativo?

Para migrar sites de servidores de web do Windows e Linux para o Serviço de Aplicativo, você pode usar o Assistente de Migração do Serviço de Aplicativo do Azure. A ferramenta de migração cria bancos de dados e aplicativos web no Azure, conforme necessário e, em seguida, publica o conteúdo. Para saber mais, consulte [Assistente de Migração do Serviço de Aplicativo do Azure](https://www.migratetoazure.net/).
