---
title: Comprar um nome de domínio personalizado
description: Saiba como comprar um domínio do Serviço de Aplicativo e usá-lo como domínio personalizado para o Serviço de Aplicativo do Azure.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: cdcf22a42375949cc4d6be0b4f3062cee26219d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704847"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Comprar um nome de domínio personalizado para Serviço de Aplicativo do Azure

Os domínios do Serviço de Aplicativo são domínios personalizados gerenciados diretamente no Azure. Eles facilitam o gerenciamento de domínios personalizados para [Serviço de Aplicativo do Azure](overview.md). Este tutorial mostra como comprar um domínio de Serviço de Aplicativo e atribuir nomes DNS ao Serviço de Aplicativo do Azure.

Para a VM do Azure ou Armazenamento do Azure, consulte [Atribuir o domínio do Serviço de Aplicativo para a VM Azure ou o Armazenamento do Azure](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie um aplicativo do Serviço de Aplicativo](./index.yml) ou use um aplicativo que você criou para outro tutorial. O aplicativo deve estar em uma região pública do Azure. Neste momento, não há suporte para as nuvens nacionais do Azure.
* [Remova o limite de gastos em minha assinatura](../cost-management-billing/manage/spending-limit.md#remove). Não é possível comprar domínios do Serviço de Aplicativo com os créditos de assinatura gratuita.

## <a name="buy-an-app-service-domain"></a>Comprar um domínio do Serviço de Aplicativo

Para obter informações sobre preços de domínios do Serviço de Aplicativo, visite a [página Preço do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/) e role para baixo até Domínio do Serviço de Aplicativo.

1. Abra o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

1. Na barra de pesquisa, pesquise por e selecione **Domínios do Serviço de Aplicativo**.

    ![Navegação do portal para os domínios do Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na exibição **Domínios do Serviço de Aplicativo**, clique em **Adicionar**.

    ![Clique em Adicionar em domínios do Serviço de Aplicativo](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Selecione **Clique aqui para experimentar a versão mais recente da experiência de criação de Domínios do Serviço de Aplicativo**.

    ![Criar um domínio do Serviço de Aplicativo com a nova experiência](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Guia Básico

1. Na guia **Básico**, defina as configurações de acordo com a seguinte tabela:  

   | Configuração  | Descrição |
   | -------- | ----------- |
   | **Assinatura** | A assinatura a ser usada para comprar o domínio. |
   | **Grupo de Recursos** | O grupo de recursos no qual colocar o domínio. Por exemplo, o grupo de recursos no qual seu aplicativo está. |
   | **Domínio** | Digite o domínio desejado. Por exemplo, **contoso.com**. Se o domínio desejado não estiver disponível, selecione uma opção na lista de sugestões disponíveis ou use outro domínio. |

    > [!NOTE]
    > Os [domínios de nível superior](https://wikipedia.org/wiki/Top-level_domain) a seguir têm suporte dos domínios do Serviço de Aplicativo: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ e _co.in_.
    >
    >
    
2. Quando terminar, clique em **Próximo: informações de contato**.

### <a name="contact-information-tab"></a>Guia Informações de contato

1. Forneça suas informações conforme exigido pelo [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) para o registro de domínio. 

    É importante preencher todos os campos obrigatórios com a máxima precisão possível. Informações de contato incorretas resultarão em falhas na compra do domínio.

1. Ao concluir, clique em **Próximo: avançado**.

### <a name="advanced-tab"></a>Guia Avançado

1. Na guia **Avançado**, defina as configurações opcionais:  

   | Configuração  | Descrição |
   | -------- | ----------- |
   | **Renovação automática** | Habilitado por padrão. Seu domínio do Serviço de Aplicativo é registrado para você em incrementos de um ano. A renovação automática garante que o registro do domínio não expire e que você retenha a propriedade do domínio. O valor anual de registro de domínio é cobrado automaticamente na sua assinatura do Azure no momento da renovação. Para recusar, selecione **Desabilitar**. Se a renovação automática estiver desabilitada, você poderá [renovar manualmente](#renew-the-domain). |
   | **Proteção de privacidade** | Habilitado por padrão. A proteção de privacidade oculta as informações de contato do registro de domínio do banco de dados WHOIS. A proteção de privacidade já está incluída no valor de registro anual do domínio. Para recusar, selecione **Desabilitar**. |

2. Ao concluir, clique em **Próximo: marcas**.

### <a name="finish"></a>Concluir

1. Na guia **Marcas**, defina as marcas desejadas para o domínio do Serviço de Aplicativo. A marcação não é necessária para usar os domínios do Serviço de Aplicativo, mas é uma [funcionalidade no Azure que ajuda a gerenciar recursos](../azure-resource-manager/management/tag-resources.md).

1. Clique em **Próximo: Examinar + criar**.

1. Na guia **Examinar + criar**, examine a ordem de domínio. Ao terminar, clique em **Criar**.

    > [!NOTE]
    > Os domínios do Serviço de Aplicativo usam o GoDaddy para o registro e o DNS do Azure para a hospedagem. Além do valor de registro de domínio, há a cobrança do preço de uso do DNS do Azure. Para obter informações, consulte [Preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. Quando o registro de domínio é concluído, aparece o botão **Ir para recurso**. Selecione o botão para ver sua página de gerenciamento.

    ![Domínio do Serviço de Aplicativo criado. Ir para o recurso](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

Agora você pode atribuir um aplicativo do Serviço de Aplicativo a esse domínio personalizado.

## <a name="prepare-the-app"></a>Preparar o aplicativo

Para mapear um nome DNS personalizado para um aplicativo Web, o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo Web precisa ser uma camada paga (Compartilhado, Básico, Standard, Premium ou Consumo para o Azure Functions). Nesta etapa, você verifica se o aplicativo do Serviço de Aplicativo está no tipo de preço com suporte.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para o aplicativo no portal do Azure

1. Na barra de pesquisa superior, pesquise por e selecione **Serviços de Aplicativos**.

    ![Pesquise por Serviços de Aplicativos](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Selecione o nome do aplicativo.

    ![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

    A página de gerenciamento do aplicativo do Serviço de Aplicativo é exibida.  

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

1. No painel de navegação à esquerda da página do aplicativo, role até a seção **Configurações** e selecione **Escalar verticalmente (plano do Serviço de Aplicativo)** .

    ![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo não está na camada **F1**. Não há suporte para DNS personalizado na camada **F1**. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Captura de tela do menu de navegação à esquerda da página do aplicativo com Escalar verticalmente (plano do Serviço de Aplicativo) selecionado.":::

1. Se o plano de serviço de aplicativo não está no **F1** camada, feche o **expandir** página e ir para [comprar o domínio](#buy-an-app-service-domain).

### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

1. Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

1. Clique em **Aplicar**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Captura de tela dos tipos de preço de domínio personalizado na categoria de produção com a guia Produção, plano B1 e o botão Aplicar realçados.":::

    Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

    ![Confirmação da operação de escala](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>Mapear o domínio do Serviço de Aplicativo para seu aplicativo

É fácil mapear um nome de host em seu domínio do Serviço de Aplicativo para um aplicativo do Serviço de Aplicativo, desde que ele esteja na mesma assinatura. Você mapeia o domínio do Serviço de Aplicativo ou qualquer um dos subdomínios diretamente para seu aplicativo, e o Azure cria os registros de DNS necessários para você.

> [!NOTE]
> Se o domínio e o aplicativo estão em assinaturas diferentes, você mapeia o domínio do Serviço de Aplicativo para o aplicativo como [mapearia um domínio comprado externamente](app-service-web-tutorial-custom-domain.md). Nesse caso, o DNS do Azure é o provedor de domínio externo, e você precisa [adicionar manualmente os registros de DNS necessários](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Mapa de domínio

1. No painel de navegação à esquerda da página do aplicativo, role até a seção **Configurações** e selecione **Domínios personalizados**.

    ![Captura de tela que mostra o menu Domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra o item Adicionar nome do host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digite o domínio do Serviço de Aplicativo (como **contoso.com**) ou um subdomínio (como **www.contoso.com**) e clique em **Validar**.

    > [!NOTE]
    > Se você cometer erros de digitação no nome do domínio do Serviço de Aplicativo, um erro de verificação aparecerá na parte inferior da página informando que estão faltando alguns registros de DNS. Você não precisa adicionar esses registros manualmente para um domínio do Serviço de Aplicativo. Basta digitar o nome de domínio correto e clicar em **Validar** novamente.
    >
    > ![Captura de tela que mostra um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Aceite o **Tipo de registro do nome do host** e clique em **Adicionar domínio personalizado**.

    ![Captura de tela que mostra o botão Adicionar domínio personalizado.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios Personalizados** do aplicativo. Atualize o navegador para atualizar os dados.

    ![Captura de tela que mostra como adicionar o registro CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Um rótulo **Não Seguro** para seu domínio personalizado significa que ele ainda não está associado a um certificado TLS/SSL. As solicitações HTTPS de um navegador para o seu domínio personalizado receberão um erro ou um aviso, dependendo do navegador. Para adicionar uma associação de TLS, confira [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Teste do domínio personalizado

Para testar o domínio personalizado, abra-o no navegador.

## <a name="renew-the-domain"></a>Renovar o domínio

O domínio Serviço de Aplicativo que você comprou é válido por um ano a partir do momento da compra. Por padrão, o domínio é configurado para ser renovado automaticamente cobrando seu método de pagamento para o próximo ano. Você pode renovar manualmente seu nome de domínio.

Se você deseja desativar a renovação automática ou se quiser renovar manualmente seu domínio, siga as etapas aqui.

1. Na barra de pesquisa, pesquise por e selecione **Domínios do Serviço de Aplicativo**.

    ![Navegação do portal para os domínios do Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na seção **Domínios do Serviço de Aplicativo**, selecione o domínio que deseja configurar.

1. No painel de navegação esquerdo do domínio, selecione **Renovação de domínio**. Para parar a renovação automática do domínio, selecione **Desativar**. A configuração entra em vigor imediatamente.

    ![Captura de tela que mostra a opção para renovar automaticamente o domínio.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Ao navegar para fora da página, clique em **Ok** para ignorar o erro "Suas edições não salvas serão descartadas".
    >

Para renovar manualmente seu domínio, selecione **Renovar domínio**. No entanto, esse botão não estará ativo até [90 dias antes da expiração do domínio](#when-domain-expires).

Se a renovação do domínio for bem-sucedida, você receberá uma notificação por email dentro de 24 horas.

## <a name="when-domain-expires"></a>Quando o domínio expira

O Azure lida com a expiração de domínios do Serviço de Aplicativo da seguinte maneira:

* Quando a renovação automática está desabilitada: 90 dias antes da expiração do domínio, um email de notificação de renovação é enviado a você, e o botão **Renovar domínio** é ativado no portal.
* Quando a renovação automática está habilitada: no dia após a data de expiração do domínio, o Azure tenta cobrar pela renovação do nome de domínio.
* Se ocorrer um erro na renovação automática (por exemplo, o cartão registrado estiver expirado) ou se a renovação automática estiver desabilitada e você permitir que o domínio expire, o Azure informará a você a expiração do domínio e colocará o domínio em reserva. Você pode [renovar manualmente](#renew-the-domain) seu domínio.
* No 4º e 12º dias após a expiração, o Azure envia emails de notificação adicionais. Você pode [renovar manualmente](#renew-the-domain) seu domínio. No 5º dia após a expiração, a resolução de DNS do domínio expirado é interrompida.
* No 19º dia após a expiração, seu domínio permanece em espera, mas fica sujeito a um valor de resgate. Você pode chamar o atendimento ao cliente para renovar o nome de domínio, sujeito a eventual valor de renovação e resgate aplicável.
* No 25º dia após a expiração, o Azure coloca o domínio em leilão com um serviço de leilões do setor de nome de domínio. Você pode chamar o atendimento ao cliente para renovar o nome de domínio, sujeito a eventual valor de renovação e resgate aplicável.
* No 30º dia após a expiração, você não poderá mais resgatar seu domínio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gerenciar registros DNS personalizados

No Azure, os registros DNS para um Domínio do Serviço de Aplicativo são gerenciados usando [DNS do Azure](https://azure.microsoft.com/services/dns/). Você pode adicionar, remover e atualizar registros DNS, assim como para um domínio adquirido externamente. Para gerenciar registros de DNS personalizados:

1. Na barra de pesquisa, pesquise por e selecione **Domínios do Serviço de Aplicativo**.

    ![Navegação do portal para os domínios do Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na seção **Domínios do Serviço de Aplicativo**, selecione o domínio que deseja configurar.

1. Na página **Visão geral**, selecione **Gerenciar registros DNS**.

    ![Captura de tela que mostra onde acessar os registros DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Para obter informações sobre como editar os registros DNS, consulte [Como gerenciar zonas DNS no portal do Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar compra (excluir domínio)

Depois de comprar o Domínio do Serviço de Aplicativo, você tem cinco dias para cancelar sua compra para obter um reembolso integral. Depois de cinco dias, você pode excluir o Domínio do Serviço de Aplicativo, mas não pode receber um reembolso.

1. Na barra de pesquisa, pesquise por e selecione **Domínios do Serviço de Aplicativo**.

    ![Navegação do portal para os domínios do Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na seção **Domínios do Serviço de Aplicativo**, selecione o domínio que deseja configurar.

1. Na navegação da esquerda do domínio, selecione **Associações de nome do host**. As associações de nome do host de todos os serviços do Azure são listadas aqui.

    ![Captura de tela que mostra a página de associações de nome do host.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Exclua cada associação de nome do host selecionando **…**  > **Excluir**. Depois que todas as associações forem excluídas, selecione **Salvar**.

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. Na navegação à esquerda, selecione **Visão geral**. 

1. Se o período de cancelamento do domínio adquirido não tiver se passado, selecione **Cancelar compra**. Caso contrário, você verá um botão **Excluir** em vez disso. Para excluir o domínio sem um reembolso, selecione **Excluir**.

    ![Captura de tela que mostra onde excluir ou cancelar um domínio comprado.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Confirme a operação selecionando **Sim**.

    Depois que a operação estiver concluída, o domínio será liberado da sua assinatura e ficará disponível para qualquer pessoa comprar novamente. 

## <a name="direct-default-url-to-a-custom-directory"></a>Direcionar a URL padrão para um diretório personalizado

Por padrão, o Serviço de Aplicativo direciona solicitações da Web para o diretório raiz do seu código de aplicativo. Para direcioná-los para um subdiretório, como o `public`, veja [Redirecionar para um diretório personalizado](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Próximas etapas

Saiba como associar um certificado TLS/SSL personalizado ao Serviço de Aplicativo.

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com uma associação TLS no Serviço de Aplicativo do Azure](configure-ssl-bindings.md)
