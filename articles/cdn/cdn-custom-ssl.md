---
title: "Habilitar ou desabilitar o HTTPS em um domínio personalizado da Rede de Distribuição de Conteúdo do Azure | Microsoft Docs"
description: "Saiba como habilitar ou desabilitar o HTTPS no ponto de extremidade CDN do Azure com um domínio personalizado."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
ms.openlocfilehash: c92f1e20acf55b8bd791fad43f17e162a5cb3847
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-or-disable-https-on-an-azure-content-delivery-network-custom-domain"></a>Habilitar ou desabilitar o HTTPS em um domínio personalizado da Rede de Distribuição de Conteúdo do Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

O suporte a HTTPS para domínios personalizados da CDN (Rede de Distribuição de Conteúdo) do Microsoft Azure permite distribuir conteúdo seguro via SSL usando seu próprio nome de domínio para aumentar a segurança dos dados em trânsito. O fluxo de trabalho de ponta a ponta para habilitar HTTPS para o seu domínio personalizado é simplificado com a habilitação de apenas um clique e o gerenciamento do certificado completo, sem nenhum custo adicional.

É fundamental para garantir a privacidade e a integridade dos dados de todos os seus dados confidenciais de aplicativos Web em trânsito. Usar o protocolo HTTPS garante que os seus dados confidenciais são criptografados quando enviados através da internet. Ele fornece confiabilidade, autenticação e protege seus aplicativos Web contra ataques. Atualmente, o CDN do Azure oferece suporte a HTTPS em um ponto de extremidade CDN. Por exemplo, se você criar um ponto de extremidade CDN por meio da CDN do Azure (como, https://contoso.azureedge.net), o HTTPS será habilitado por padrão. Agora, com o HTTPS de domínio personalizado, você também pode habilitar a distribuição segura para um domínio personalizado (por exemplo, https://www.contoso.com). 

Estes são alguns dos atributos principais do recurso HTTPS:

- Sem custo adicional: não há custos de aquisição ou renovação do certificado e não há custo adicional para tráfego HTTPS. Você paga apenas por GB de saída do CDN.

- Ativação simples: o provisionamento com um clique está disponível no [portal do Azure](https://portal.azure.com). Você também pode usar a API REST ou outras ferramentas de desenvolvedor para habilitar o recurso.

- Gerenciamento de certificado completo: todos os certificados de aquisição e gerenciamento são manipulados para você. Certificados são automaticamente provisionados e renovados antes da expiração. Isso remove completamente os riscos de interrupção do serviço como resultado de uma expiração do certificado.

>[!NOTE] 
>Antes de habilitar o suporte a HTTPS, você deve estabelecer uma [domínio personalizado CDN do Azure ](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>Habilitando o HTTPS

Para habilitar o HTTPS, siga estas etapas:

### <a name="step-1-enable-the-feature"></a>Etapa 1: Habilitar o recurso 

1. No [portal do Azure](https://portal.azure.com), navegue até o seu perfil CDN Verizon padrão ou premium.

2. Na lista de pontos de extremidade, clique no ponto de extremidade que contém seu domínio personalizado.

3. Clique no domínio personalizado que deseja habilitar o HTTPS.

    ![Folha de ponto de extremidade](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Clique em **Ativado** para habilitar o HTTPS e, em seguida, clique em **Aplicar**.

    ![Caixa de diálogo personalizada HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>Etapa 2: Validar o domínio

>[!IMPORTANT] 
>Conclua a validação completa de domínio antes de ativar o HTTPS no seu domínio personalizado. Você tem seis dias úteis para aprovar o domínio. As solicitações que não foram aprovadas em até seis dias úteis serão canceladas automaticamente. 

Depois de habilitar o HTTPS em seu domínio personalizado, nosso provedor de certificado HTTPS DigiCert validará a propriedade de seu domínio entrando em contato com o inscrito do domínio, de acordo com as informações do inscrito [WHOIS](http://whois.domaintools.com/) do domínio. O contato é feito pelo endereço de email (por padrão) ou pelo número de telefone listado no registro WHOIS. 

![Registro WHOIS](./media/cdn-custom-ssl/whois-record.png)

Além disso, o DigiCert enviará o email de verificação para os endereços a seguir. Se as informações do inscrito WHOIS forem particulares, verifique se é possível aprovar diretamente por meio de um destes endereços:

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

Você deverá receber um email em poucos minutos, semelhante ao exemplo a seguir, solicitando que você aprove a solicitação. Se estiver usando um filtro de spam, adicione admin@digicert.com à sua lista de permissões. Se você não receber um email em até 24 horas, contate o suporte da Microsoft.
    
![Caixa de diálogo personalizada HTTPS](./media/cdn-custom-ssl/domain-validation-email.png)

Quando você clicar no link de aprovação, será direcionado para o seguinte formulário de aprovação online: 
    
![Caixa de diálogo personalizada HTTPS](./media/cdn-custom-ssl/domain-validation-form.png)

Siga as instruções do formulário; você tem duas opções de verificação:

- Você pode aprovar todos os pedidos futuros feitos por meio da mesma conta para o mesmo domínio raiz; por exemplo, contoso.com. Essa é a abordagem recomendada se você planeja adicionar mais domínios personalizados no futuro para o mesmo domínio raiz.

- Você pode aprovar apenas o nome do host específico usado nesta solicitação. Uma aprovação adicional será necessária para as solicitações seguintes.

Após a aprovação, o DigiCert adicionará seu nome de domínio personalizado ao certificado SAN (Nomes Alternativo da Entidade). O certificado é válido por um ano e será renovado automaticamente antes de expirar.

### <a name="step-3-wait-for-propagation"></a>Etapa 3: Aguardar a propagação

Depois da validação do nome de domínio, é necessário de 6 a 8 horas para a ativação do recurso HTTPS de domínio personalizado. Quando o processo for concluído, o status “HTTPS Personalizado” no portal do Azure será definido como “Habilitado” e as quatro etapas da operação da folha HTTPS do domínio personalizado serão marcadas como concluídas. Seu domínio personalizado agora está pronto para usar o HTTPS.

![Habilitar a caixa de diálogo do HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você habilita o HTTPS. Depois de habilitar o HTTPS, quatro etapas de operação são exibidas na folha HTTPS do domínio personalizado. À medida que cada etapa fica ativa, detalhes adicionais são exibidos na etapa com o progresso. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Etapa da operação | Detalhes da etapa da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| | Sua solicitação HTTPS está sendo enviada. |
| | Sua solicitação HTTPS foi enviada com êxito. |
| 2 Validação de domínio | Enviamos um email a você solicitando a validação da propriedade do domínio. Aguardando sua confirmação. |
| | Sua propriedade do domínio foi validada com êxito. |
| | A solicitação de validação da propriedade do domínio expirou (provavelmente, o cliente não respondeu dentro de 6 dias). O HTTPS não será habilitado no domínio. * |
| | A solicitação de validação da propriedade do domínio foi rejeitada pelo cliente. O HTTPS não será habilitado no domínio. * |
| 3 Provisionamento de certificado | No momento, a autoridade de certificação está emitindo o certificado necessário para habilitar o HTTPS em seu domínio. |
| | O certificado foi emitido e está sendo implantado na rede CDN. Isso pode levar até 6 horas. |
| | O certificado foi implantado com êxito na rede CDN. |
| 4 Concluído | O HTTPS foi habilitado com êxito em seu domínio. |

\* Esta mensagem não será exibida, a menos que tenha ocorrido um erro. 

Se ocorrer um erro antes que a solicitação seja enviada, você verá a seguinte mensagem de erro:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>Desabilitando o HTTPS

Depois de habilitar o HTTPS, é possível desabilitá-lo posteriormente. Para desabilitar o HTTPS, siga estas etapas:

### <a name="step-1-disable-the-feature"></a>Etapa 1: Desabilitar o recurso 

1. No [portal do Azure](https://portal.azure.com), navegue até o seu perfil CDN Verizon padrão ou premium.

2. Na lista de pontos de extremidade, clique no ponto de extremidade que contém seu domínio personalizado.

3. Clique no domínio personalizado no qual você deseja desabilitar o HTTPS.

    ![Folha de ponto de extremidade](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Clique em **Desativado** para desabilitar o HTTPS e, em seguida, clique em **Aplicar**.

    ![Caixa de diálogo personalizada HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>Etapa 2: Aguardar a propagação

Depois que o recurso HTTPS do domínio personalizado for desabilitado, poderá levar até 6-8 horas para que ele entre em vigor. Quando o processo for concluído, o status “HTTPS Personalizado” no portal do Azure será definido como “desabilitado” e as três etapas da operação da folha HTTPS do domínio personalizado serão marcadas como concluídas. Seu domínio personalizado não pode mais usar o HTTPS.

![Desabilitar a caixa de diálogo do HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você desabilita o HTTPS. Depois de desabilitar o HTTPS, três etapas de operação são exibidas na folha HTTPS do domínio personalizado. À medida que cada etapa fica ativa, detalhes adicionais são exibidos na etapa. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Andamento da operação | Detalhes da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| 2 Desprovisionamento de certificado | Excluindo o certificado |
| 3 Concluído | Certificado excluído |

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. *Quem é o provedor de certificado e que tipo de certificado é usado?*

    Usamos o certificado de nomes de alternativos da entidade (SAN) fornecido pela DigiCert. Um certificado SAN pode proteger vários nomes de domínio totalmente qualificados com um certificado.

2. *Posso usar o meu certificado dedicado?*
    
    Atualmente não, mas está previsto.

3. *E se eu não receber o email de verificação de domínio do DigiCert?*

    Contate o suporte da Microsoft caso você não receba um email em até 24 horas.

4. *Usar um certificado SAN é menos seguro do que um certificado dedicado?*
    
    Um certificado SAN segue os mesmos padrões de criptografia e segurança que um certificado dedicado. Todos os certificados SSL emitidos usam SHA-256 para uma maior segurança do servidor.

5. *Eu posso usar HTTPS do domínio personalizado com o CDN do Azure do Akamai?*

    Atualmente, esse recurso só está disponível com o CDN do Azure da Verizon. Estamos trabalhando para oferecer suporte a esse recurso com o CDN do Azure do Akamai nos próximos meses.


## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar um [domínio personalizado no seu ponto de extremidade do CDN do Azure](./cdn-map-content-to-custom-domain.md)


