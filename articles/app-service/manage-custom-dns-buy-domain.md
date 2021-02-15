---
title: Comprar um nome de domínio personalizado
description: Saiba como comprar um domínio do serviço de aplicativo e usá-lo como um domínio personalizado para o serviço de Azure App de aplicativo.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: 6f0ff7a54c2ad1fa1af649c8082498b442783c7e
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607947"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Comprar um nome de domínio personalizado para Serviço de Aplicativo do Azure

Os domínios do serviço de aplicativo são domínios personalizados que são gerenciados diretamente no Azure. Eles facilitam o gerenciamento de domínios personalizados para [Serviço de Aplicativo do Azure](overview.md). Este tutorial mostra como comprar um domínio de Serviço de Aplicativo e atribuir nomes DNS ao Serviço de Aplicativo do Azure.

Para a VM do Azure ou Armazenamento do Azure, consulte [Atribuir o domínio do Serviço de Aplicativo para a VM Azure ou o Armazenamento do Azure](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie um aplicativo do Serviço de Aplicativo](./index.yml) ou use um aplicativo que você criou para outro tutorial. O aplicativo deve estar em uma região pública do Azure. Neste momento, não há suporte para nuvens nacionais do Azure.
* [Remova o limite de gastos em minha assinatura](../cost-management-billing/manage/spending-limit.md#remove). Não é possível comprar domínios do Serviço de Aplicativo com os créditos de assinatura gratuita.

## <a name="buy-an-app-service-domain"></a>Comprar um domínio do serviço de aplicativo

Para obter informações sobre preços em domínios do serviço de aplicativo, visite a [página de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/) e role para baixo até domínio do serviço de aplicativo.

1. Abra o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

1. Na barra de pesquisa, procure e selecione **domínios do serviço de aplicativo**.

    ![Navegação do portal para Azure App domínios de serviço](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na exibição **domínios do serviço de aplicativo** , clique em **Adicionar**.

    ![Clique em Adicionar em domínios do serviço de aplicativo](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Selecione **clique para experimentar a versão mais recente dos domínios do serviço de aplicativo criar experiência**.

    ![Criar domínio do serviço de aplicativo com nova experiência](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Guia Básico

1. Na guia **noções básicas** , defina as configurações usando a tabela a seguir:  

   | Configuração  | Descrição |
   | -------- | ----------- |
   | **Assinatura** | A assinatura a ser usada para comprar o domínio. |
   | **Grupo de Recursos** | O grupo de recursos no qual colocar o domínio. Por exemplo, o grupo de recursos no qual seu aplicativo está. |
   | **Domínio** | Digite o domínio desejado. Por exemplo, **contoso.com**. Se o domínio desejado não estiver disponível, você poderá selecionar em uma lista de sugestões de domínios disponíveis ou tentar um domínio diferente. |

    > [!NOTE]
    > Os [domínios de nível superior](https://wikipedia.org/wiki/Top-level_domain) a seguir têm suporte dos domínios do Serviço de Aplicativo: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ e _co.in_.
    >
    >
    
2. Quando terminar, clique em **Avançar: informações de contato**.

### <a name="contact-information-tab"></a>Guia informações de contato

1. Forneça suas informações conforme exigido pelo [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) para o registro de domínio. 

    É importante preencher todos os campos obrigatórios com a máxima precisão possível. Dados incorretos para informações de contato podem resultar em falha na compra do domínio.

1. Quando terminar, clique em **Avançar: avançado**.

### <a name="advanced-tab"></a>Guia Avançado

1. Na guia **avançado** , defina as configurações opcionais:  

   | Configuração  | Descrição |
   | -------- | ----------- |
   | **Renovação automática** | Habilitado por padrão. Seu domínio do serviço de aplicativo é registrado para você em incrementos de um ano. A renovação automática garante que o registro de domínio não expire e que você retenha a propriedade do domínio. Sua assinatura do Azure é cobrada automaticamente pela taxa de registro de domínio anual no momento da renovação. Para recusar, selecione **desabilitar**. Se a renovação automática estiver desabilitada, você poderá [renová-la manualmente](#renew-the-domain). |
   | **Proteção de privacidade** | Habilitado por padrão. A proteção de privacidade oculta as informações de contato do registro de domínio do banco de dados WHOIS. A proteção de privacidade já está incluída na taxa de registro anual do domínio. Para recusar, selecione **desabilitar**. |

2. Quando terminar, clique em **Avançar: marcas**.

### <a name="finish"></a>Concluir

1. Na guia **marcas** , defina as marcas desejadas para o domínio do serviço de aplicativo. A marcação não é necessária para usar domínios do serviço de aplicativo, mas é um [recurso no Azure que ajuda você a gerenciar seus recursos](../azure-resource-manager/management/tag-resources.md).

1. Clique em **Avançar: revisar + criar**.

1. Na guia **revisar + criar** , examine sua ordem de domínio. Ao terminar, clique em **Criar**.

    > [!NOTE]
    > Os domínios do serviço de aplicativo usam GoDaddy para o registro de domínio e o DNS do Azure para hospedar os domínios. Além da taxa de registro de domínio anual, os encargos de uso para o DNS do Azure se aplicam. Para obter informações, consulte [Preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. Quando o registro de domínio for concluído, você verá um botão **ir para recurso** . Selecione-o para ver sua página de gerenciamento.

    ![Domínio do serviço de aplicativo criado. Acessar recurso](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

Agora você está pronto para atribuir um aplicativo do serviço de aplicativo a este domínio personalizado.

## <a name="prepare-the-app"></a>Preparar o aplicativo

Para mapear um nome DNS personalizado para um aplicativo Web, o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo Web precisa ser uma camada paga (Compartilhado, Básico, Standard, Premium ou Consumo para o Azure Functions). Nesta etapa, você verifica se o aplicativo do Serviço de Aplicativo está no tipo de preço com suporte.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para o aplicativo no portal do Azure

1. Na barra de pesquisa superior, procure e selecione **serviços de aplicativos**.

    ![Pesquisar serviços de aplicativos](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Selecione o nome do aplicativo.

    ![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

    A página de gerenciamento do aplicativo do Serviço de Aplicativo é exibida.  

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

1. No painel de navegação à esquerda da página do aplicativo, role até a seção **Configurações** e selecione **Escalar verticalmente (plano do Serviço de Aplicativo)** .

    ![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo não está na camada **F1**. Não há suporte para DNS personalizado na camada **F1**. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Captura de tela do menu de navegação à esquerda da página do aplicativo com escalar verticalmente (plano do serviço de aplicativo) selecionado.":::

1. Se o plano de serviço de aplicativo não está no **F1** camada, feche o **expandir** página e ir para [comprar o domínio](#buy-an-app-service-domain).

### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

1. Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

1. Clique em **Aplicar**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Captura de tela dos tipos de preço de domínio personalizado na categoria produção com a guia produção, plano B1 e o botão Aplicar realçado.":::

    Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

    ![Confirmação da operação de escala](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>Mapear o domínio do serviço de aplicativo para seu aplicativo

É fácil mapear um nome de host em seu domínio do serviço de aplicativo para um aplicativo do serviço de aplicativo, desde que ele esteja na mesma assinatura. Você mapeia o domínio do serviço de aplicativo ou qualquer um de seus subdomínios diretamente em seu aplicativo e o Azure cria os registros DNS necessários para você.

> [!NOTE]
> Se o domínio e o aplicativo estiverem em assinaturas diferentes, você mapeará o domínio do serviço de aplicativo para o aplicativo, assim como [mapear um domínio comprado externamente](app-service-web-tutorial-custom-domain.md). Nesse caso, o DNS do Azure é o provedor de domínio externo e você precisa [adicionar manualmente os registros DNS necessários](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Mapa de domínio

1. No painel de navegação à esquerda da página do aplicativo, role até a seção **configurações** e selecione **domínios personalizados**.

    ![Captura de tela que mostra o menu Domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra o item Adicionar nome do host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digite o domínio do serviço de aplicativo (como **contoso.com**) ou um subdomínio (como **www.contoso.com**) e clique em **validar**.

    > [!NOTE]
    > Se você tiver cometido erros de digitação no nome de domínio do serviço de aplicativo, um erro de verificação aparecerá na parte inferior da página para informar que você não tem alguns registros DNS. Você não precisa adicionar esses registros manualmente para um domínio do serviço de aplicativo. Apenas certifique-se de digitar o nome de domínio corretamente e clique em **validar** novamente.
    >
    > ![Captura de tela que mostra um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Aceite o **tipo de registro hostname** e clique em **Adicionar domínio personalizado**.

    ![Captura de tela que mostra o botão Adicionar domínio personalizado.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios Personalizados** do aplicativo. Atualize o navegador para atualizar os dados.

    ![Captura de tela que mostra como adicionar o registro CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Um rótulo **não seguro** para seu domínio personalizado significa que ele ainda não está associado a um certificado TLS/SSL. As solicitações HTTPS de um navegador para o seu domínio personalizado receberão um erro ou um aviso, dependendo do navegador. Para adicionar uma associação de TLS, confira [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Teste do domínio personalizado

Para testar o domínio personalizado, navegue até ele no navegador.

## <a name="renew-the-domain"></a>Renovar o domínio

O domínio Serviço de Aplicativo que você comprou é válido por um ano a partir do momento da compra. Por padrão, o domínio é configurado para ser renovado automaticamente cobrando seu método de pagamento para o próximo ano. Você pode renovar manualmente seu nome de domínio.

Se você deseja desativar a renovação automática ou se quiser renovar manualmente seu domínio, siga as etapas aqui.

1. Na barra de pesquisa, procure e selecione **domínios do serviço de aplicativo**.

    ![Navegação do portal para Azure App domínios de serviço](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na seção **Domínios do Serviço de Aplicativo**, selecione o domínio que deseja configurar.

1. No painel de navegação esquerdo do domínio, selecione **Renovação de domínio**. Para parar de renovar seu domínio automaticamente, selecione **desativado**. A configuração entra em vigor imediatamente.

    ![Captura de tela que mostra a opção para renovar automaticamente seu domínio.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Ao navegar para fora da página, desconsidere o erro "suas edições não salvas serão descartadas" clicando em **OK**.
    >

Para renovar manualmente seu domínio, selecione **Renovar domínio**. No entanto, esse botão não estará ativo até [90 dias antes da expiração do domínio](#when-domain-expires).

Se a renovação do seu domínio for bem-sucedida, você receberá uma notificação por email dentro de 24 horas.

## <a name="when-domain-expires"></a>Quando o domínio expirar

O Azure lida com domínios do serviço de aplicativo expirados ou expirados da seguinte maneira:

* Se a renovação automática estiver desabilitada: 90 dias antes da expiração do domínio, um email de notificação de renovação será enviado a você e o botão **renovar domínio** será ativado no Portal.
* Se a renovação automática estiver habilitada: no dia após a data de expiração do domínio, o Azure tentará cobrar pela renovação do nome de domínio.
* Se ocorrer um erro durante a renovação automática (por exemplo, se o cartão no arquivo estiver expirado) ou se a renovação automática estiver desabilitada e você permitir que o domínio expire, o Azure o notificará sobre a expiração do domínio e os parques do seu nome de domínio. Você pode [renovar manualmente](#renew-the-domain) seu domínio.
* No dia 4 e 12 dias após a expiração, o Azure envia emails de notificação adicionais. Você pode [renovar manualmente](#renew-the-domain) seu domínio.
* No dia 19 após a expiração, seu domínio permanece em espera, mas se torna sujeito a uma taxa de resgate. Você pode chamar o atendimento ao cliente para renovar seu nome de domínio, sujeito a qualquer taxa de renovação e resgate aplicável.
* No 25º dia após a expiração, o Azure coloca seu domínio para o leilão com um serviço de serviços de leilões do setor de nome de domínio. Você pode chamar o atendimento ao cliente para renovar seu nome de domínio, sujeito a qualquer taxa de renovação e resgate aplicável.
* No dia 30 após a expiração, você não poderá mais resgatar seu domínio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gerenciar registros DNS personalizados

No Azure, os registros DNS para um Domínio do Serviço de Aplicativo são gerenciados usando [DNS do Azure](https://azure.microsoft.com/services/dns/). Você pode adicionar, remover e atualizar registros DNS, assim como para um domínio adquirido externamente. Para gerenciar registros DNS personalizados:

1. Na barra de pesquisa, procure e selecione **domínios do serviço de aplicativo**.

    ![Navegação do portal para Azure App domínios de serviço](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na seção **Domínios do Serviço de Aplicativo**, selecione o domínio que deseja configurar.

1. Na página **visão geral** , selecione **gerenciar registros DNS**.

    ![Captura de tela que mostra onde acessar os registros DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Para obter informações sobre como editar os registros DNS, consulte [Como gerenciar zonas DNS no portal do Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar compra (excluir domínio)

Depois de comprar o Domínio do Serviço de Aplicativo, você tem cinco dias para cancelar sua compra para obter um reembolso integral. Depois de cinco dias, você pode excluir o Domínio do Serviço de Aplicativo, mas não pode receber um reembolso.

1. Na barra de pesquisa, procure e selecione **domínios do serviço de aplicativo**.

    ![Navegação do portal para Azure App domínios de serviço](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na seção **Domínios do Serviço de Aplicativo**, selecione o domínio que deseja configurar.

1. Na navegação à esquerda do domínio, selecione **associações de nome de host**. As associações de nome do host de todos os serviços do Azure são listadas aqui.

    ![Captura de tela que mostra a página associações de nome de host.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Exclua cada associação de nome de host selecionando **...**  >  **Excluir**. Depois que todas as associações forem excluídas, selecione **Salvar**.

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. Na navegação à esquerda do domínio, selecione **visão geral**. 

1. Se o período de cancelamento do domínio adquirido não tiver se passado, selecione **Cancelar compra**. Caso contrário, você verá um botão **Excluir** em vez disso. Para excluir o domínio sem um reembolso, selecione **Excluir**.

    ![Captura de tela que mostra onde excluir ou cancelar um domínio comprado.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Confirme a operação selecionando **Sim**.

    Depois que a operação estiver concluída, o domínio será liberado da sua assinatura e ficará disponível para qualquer pessoa comprar novamente. 

## <a name="direct-default-url-to-a-custom-directory"></a>Direcionar a URL padrão para um diretório personalizado

Por padrão, o Serviço de Aplicativo direciona solicitações da Web para o diretório raiz do seu código de aplicativo. Para direcioná-los para um subdiretório, como `public` , consulte [redirecionar para um diretório personalizado](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Próximas etapas

Saiba como associar um certificado TLS/SSL personalizado ao serviço de aplicativo.

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com uma associação TLS no serviço Azure App](configure-ssl-bindings.md)
