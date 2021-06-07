---
title: 'Tutorial: Mapear um nome DNS personalizado existente'
description: Saiba como adicionar um nome de domínio DNS personalizado existente (domínio intuitivo) a um aplicativo Web, um back-end de aplicativo móvel ou um aplicativo de API no Serviço de Aplicativo do Azure.
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname, vanity domain
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32ad6fa122083b40a948345e360bf5b9b0f09e96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954846"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutorial: Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure

Este tutorial mostra como mapear qualquer <abbr title="Um nome de domínio que você comprou de um registrador de domínios, como o GoDaddy, ou um subdomínio de seu domínio comprado.">nome de domínio DNS personalizado existente</abbr> como <abbr title="Um serviço baseado em HTTP para hospedagem de aplicativos Web, APIs REST e aplicativos de back-end móveis.">Serviço de aplicativo do Azure</abbr>.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Mapear um subdomínio usando um <abbr title="Um registro de nome canônico DNS mapeia um nome de domínio para outro.">Registro CNAME</abbr>.
> * Mapear um domínio raiz usando um <abbr title="Um registro de endereço no DNS mapeia um nome do host para um endereço IP.">Registro A</abbr>.
> * Mapear um domínio curinga usando um registro CNAME.
> * Redirecionar a URL padrão para um diretório personalizado.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

* [Crie um aplicativo do Serviço de Aplicativo](./index.yml) ou use um aplicativo que você criou para outro tutorial.
* Verifique se você pode editar os registros DNS de seu domínio personalizado. Se você ainda não tem um domínio personalizado, pode [comprar um domínio do Serviço de Aplicativo](manage-custom-dns-buy-domain.md).

    <details>
        <summary>Do que eu preciso para editar registros DNS?</summary>
        É necessário acesso ao registro DNS do provedor do domínio, como o GoDaddy. Por exemplo, para adicionar entradas DNS a <code>contoso.com</code> e <code>www.contoso.com</code>, você deve poder definir as configurações de DNS do domínio raiz de <code>contoso.com</code>.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. Preparar o aplicativo

Para mapear um nome DNS personalizado para um aplicativo, seu <abbr title="Especifica o local, o tamanho e os recursos do farm de servidores Web que hospeda o aplicativo.">Plano do Serviço de Aplicativo</abbr> deve ser uma camada paga (não <abbr title="Uma camada do Serviço de Aplicativo do Azure na qual seu aplicativo é executado nas mesmas VMs que outros aplicativos, incluindo os aplicativos de outros clientes. Essa camada é voltada para desenvolvimento e teste.">**Gratuito (F1)**</abbr>). Para saber mais, confira [Visão geral do planos do Serviço de Aplicativo do Azure](overview-hosting-plans.md).

#### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o [portal do Azure](https://portal.azure.com) e entre com a sua conta do Azure.

#### <a name="select-the-app-in-the-azure-portal"></a>Selecionar o aplicativo no Portal do Microsoft Azure

1. Pesquise e selecione **Serviços de Aplicativos**.

   ![Captura de tela que mostra a seleção de Serviços de Aplicativos.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo do Azure.

   ![Captura de tela mostrando a navegação do portal para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    A página de gerenciamento do aplicativo do Serviço de Aplicativo é exibida.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

1. No painel esquerdo da página do aplicativo, role a página até a seção **Configurações** e selecione **Aumentar (Plano do Serviço de Aplicativo)** .

   ![Captura de tela que mostra o menu Aumentar (Plano do Serviço de Aplicativo).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo não está na camada **F1**. Não há suporte para o DNS personalizado na camada **F1**.

   ![Captura de tela que mostra Tipos de preço recomendados.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Se o plano do Serviço de Aplicativo não estiver na camada **F1**, feche a página **Escalar verticalmente** e vá diretamente para [3. Obter a ID de verificação de domínio](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

1. Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter mais opções, selecione **Ver opções adicionais**.

1. Selecione **Aplicar**.

   ![Captura de tela que mostra como verificar o tipo de preço.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

   ![Captura de tela que mostra a confirmação da operação de escala.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. Obter a ID de verificação de domínio

Para adicionar um domínio personalizado ao seu aplicativo, você precisa verificar sua propriedade do domínio adicionando uma ID de verificação como um registro TXT com seu provedor de domínio. 

1. No painel esquerdo da página do aplicativo, selecione **Domínios personalizados**. 
1. Copie a ID da caixa **ID de Verificação de Domínio Personalizado** na página **Domínios Personalizados** para a próxima etapa.

    ![Captura de tela que mostra a ID na caixa ID de Verificação de Domínio Personalizado.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Por que preciso disso?</summary>
        A adição de IDs de verificação de domínio ao domínio personalizado pode impedir entradas DNS pendentes e ajudar a evitar a tomada de controle de subdomínio. Para os domínios personalizados configurados anteriormente sem essa ID de verificação, você deverá protegê-los do mesmo risco adicionando a ID de verificação ao seu registro DNS. Para obter mais informações sobre essa ameaça comum de alta severidade, confira <a href="/azure/security/fundamentals/subdomain-takeover">Tomada de controle de subdomínio</a>.
    </details>
    
<a name="info"></a>

3. **(Apenas um registro)** Para mapear um <abbr title="Um registro de endereço no DNS mapeia um nome do host para um endereço IP.">Registro A</abbr>, você precisa do endereço IP externo do aplicativo. Na página **Domínios personalizados**, copie o valor do **endereço IP**.

   ![Captura de tela que mostra a navegação do portal para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. Criar os registros DNS

1. Entre no site de seu provedor de domínio.

    <details>
        <summary>Posso gerenciar o DNS do meu provedor de domínio usando o Azure?</summary>
        Se desejar, você pode usar o DNS do Azure para gerenciar registros DNS para seu domínio e configurar um nome DNS personalizado para o Serviço de Aplicativo do Azure. Para saber mais, confira <a href="/azure/dns/dns-delegate-domain-azure-dns">Tutorial: Hospedar seu domínio no DNS do Azure></a>.
    </details>

1. Localize a página para gerenciamento de registros DNS. 

    <details>
        <summary>Como fazer para localizar a página?</summary>
        <p>Cada provedor de domínio tem sua própria interface de registros DNS; portanto, consulte a documentação do provedor. Procure áreas do site rotuladas como <strong>Nome de Domínio</strong>, <strong>DNS</strong> ou <strong>Gerenciamento de Servidor de Nomes</strong>.</p>
        <p>Normalmente, você pode encontrar a página de registros DNS exibindo as informações da conta e procurando um link como <strong>Meus domínios</strong>. Acesse essa página e, em seguida, procure um link com um nome semelhante a <strong>Arquivo de zona</strong>, <strong>Registros DNS</strong> ou <strong>Configuração avançada</strong>.</p>
    </details>

   A captura de tela a seguir é um exemplo de uma página de registros DNS:

   ![Captura de tela que mostra um exemplo de página de registros DNS.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Selecione **Adicionar** ou o widget adequado para criar um registro. 

1. Selecione o tipo de registro a ser criado e siga as instruções. Você pode usar um <abbr title="Um registro de nome canônico no DNS mapeia um nome de domínio (um alias) para outro (o nome canônico).">Registro CNAME</abbr> ou um <abbr title="Um registro de endereço no DNS mapeia um nome do host para um endereço IP.">Registro A</abbr> para mapear um nome DNS personalizado para o Serviço de Aplicativo. 

    <details>
        <summary>Qual registro devo escolher?</summary>
        <div>
            <ul>
            <li>Para mapear o domínio raiz (por exemplo, <code>contoso.com</code>), use um registro A. Não use o registro CNAME para o registro raiz (para saber mais, confira a <a href="https://en.wikipedia.org/wiki/CNAME_record">entrada da Wikipédia</a>).</li>
            <li>Para mapear um subdomínio (por exemplo, <code>www.contoso.com</code>), use um registro CNAME.</li>
            <li>Você pode mapear um subdomínio para o endereço IP do aplicativo diretamente com um registro A, mas é possível que <a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">o endereço IP seja alterado</a>. O CNAME é mapeado para o nome de host do aplicativo, que é menos suscetível a alterações.</li>
            <li>Para mapear um <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">domínio curinga</a> (por exemplo, <code>*.contoso.com</code>), use um registro CNAME.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

Para um subdomínio como `www` em `www.contoso.com`, crie dois registros de acordo com a seguinte tabela:

| Tipo de registro | Host | Valor | Comentários |
| - | - | - |
| CNAME | `<subdomain>` (por exemplo, `www`) | `<app-name>.azurewebsites.net` | O mapeamento de domínio em si. |
| TXT | `asuid.<subdomain>` (por exemplo, `asuid.www`) | [A ID de verificação que você obteve anteriormente](#3-get-a-domain-verification-id) | O Serviço de Aplicativo acessa o registro TXT `asuid.<subdomain>` para verificar sua propriedade do domínio personalizado. |

![Captura de tela que mostra a navegação no portal para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Para um domínio raiz como `contoso.com`, crie dois registros de acordo com a seguinte tabela:

| Tipo de registro | Host | Valor | Comentários |
| - | - | - |
| Um | `@` | Endereço IP de [Copiar o endereço IP do aplicativo](#3-get-a-domain-verification-id) | O mapeamento de domínio em si (`@` normalmente representa o domínio raiz). |
| TXT | `asuid` | [A ID de verificação que você obteve anteriormente](#3-get-a-domain-verification-id) | O Serviço de Aplicativo acessa o registro TXT `asuid.<subdomain>` para verificar sua propriedade do domínio personalizado. Para o domínio raiz, use `asuid`. |

![Captura de tela que mostra uma página de registros DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>E se eu quiser mapear um subdomínio com um registro A?</summary>
Para mapear um subdomínio como `www.contoso.com` com um registro A em vez de um registro CNAME recomendado, o seu registro A e o registro TXT devem se parecer com a seguinte tabela:

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">Tabela 3</caption>
<thead>
<tr>
<th>Tipo de registro</th>
<th>Host</th>
<th>Valor</th>
</tr>
</thead>
<tbody>
<tr>
<td>Um</td>
<td><code>&lt;subdomain&gt;</code> (por exemplo, <code>www</code>)</td>
<td>Endereço IP de <a href="#info" data-linktype="self-bookmark">Copiar o endereço IP do aplicativo</a></td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (por exemplo, <code>asuid.www</code>)</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">A ID de verificação que você obteve anteriormente</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Caractere curinga (CNAME)](#tab/wildcard)

Para um nome de caractere curinga como `*` em `*.contoso.com`, crie dois registros de acordo com a seguinte tabela:

| Tipo de registro | Host | Valor | Comentários |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | O mapeamento de domínio em si. |
| TXT | `asuid` | [A ID de verificação que você obteve anteriormente](#3-get-a-domain-verification-id) | O Serviço de Aplicativo acessa o registro TXT `asuid` para verificar sua propriedade do domínio personalizado. |

![Captura de tela que mostra a navegação para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Minhas alterações são apagadas depois de sair da página.</summary>
<p>Para alguns provedores, como GoDaddy, as alterações nos registros DNS só entram em vigor quando você seleciona um link separado <strong>Salvar Alterações</strong>.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. Habilitar o mapeamento no aplicativo

1. No painel esquerdo da página do aplicativo no portal do Azure, selecione **Domínios personalizados**.

    ![Captura de tela que mostra o menu Domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra o item Adicionar nome do host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Digite o nome de domínio totalmente qualificado ao qual você adicionou um registro CNAME, como `www.contoso.com`.

1. Selecione **Validar**. A página **Adicionar domínio personalizado** será exibida.

1. Certifique-se de que **Tipo de registro do nome do host** esteja definido como **CNAME (www\.exemplo.com ou qualquer subdomínio)** . Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra o botão Adicionar domínio personalizado.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios Personalizados** do aplicativo. Atualize o navegador para atualizar os dados.

    ![Captura de tela que mostra como adicionar o registro CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Do que se trata o rótulo de aviso <strong>Não Seguro</strong>?</summary>
        Um rótulo de aviso do domínio personalizado significa que ele ainda não está associado a um certificado TLS/SSL. As solicitações HTTPS de um navegador para o seu domínio personalizado receberão um erro ou um aviso, dependendo do navegador. Para adicionar uma associação de TLS, confira <a href="/azure/app-service/configure-ssl-bindings">Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure</a>.
    </details>

    Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, verá um erro de verificação na parte inferior da página.

    ![Captura de tela que mostra um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Digite o nome de domínio totalmente qualificado para o qual você configurou o registro A, como `contoso.com`. 

1. Selecione **Validar**. A página **Adicionar domínio personalizado** é exibida.

1. Verifique se **Tipo de registro de nome de host** está definido como **Registro A (example.com)** . Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra como adicionar um nome DNS ao aplicativo.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios Personalizados** do aplicativo. Atualize o navegador para atualizar os dados.

    ![Captura de tela que mostra como adicionar um registro A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Do que se trata o rótulo de aviso <strong>Não Seguro</strong>?</summary>
        Um rótulo de aviso do domínio personalizado significa que ele ainda não está associado a um certificado TLS/SSL. As solicitações HTTPS de um navegador para o seu domínio personalizado receberão um erro ou um aviso, dependendo do navegador. Para adicionar uma associação de TLS, confira <a href="/azure/app-service/configure-ssl-bindings">Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure</a>.
    </details>
    
    Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, verá um erro de verificação na parte inferior da página.
    
    ![Captura de tela mostrando um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Caractere curinga (CNAME)](#tab/wildcard)

3. Digite um nome de domínio totalmente qualificado que corresponde ao domínio curinga. Para o exemplo `*.contoso.com`, você pode usar `sub1.contoso.com`, `sub2.contoso.com`, `*.contoso.com` ou qualquer outra cadeia de caracteres correspondente ao padrão do curinga. Em seguida, selecione **Validar**.

    O botão **Adicionar domínio personalizado** está ativado.

1. Certifique-se de que **Tipo de registro do nome do host** esteja definido como **registro CNAME (www\.exemplo.com ou qualquer subdomínio)** . Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra como adicionar um nome DNS ao aplicativo.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios Personalizados** do aplicativo. Atualize o navegador para atualizar os dados.

    <details>
        <summary>Do que se trata o rótulo de aviso <strong>Não Seguro</strong>?</summary>
        Um rótulo de aviso do domínio personalizado significa que ele ainda não está associado a um certificado TLS/SSL. As solicitações HTTPS de um navegador para o seu domínio personalizado receberão um erro ou um aviso, dependendo do navegador. Para adicionar uma associação de TLS, confira <a href="/azure/app-service/configure-ssl-bindings">Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure</a>.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. Testar em um navegador

Procure os nomes DNS configurados anteriormente.

![Captura de tela que mostra a navegação para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Recebo um erro de HTTP 404 (Não Encontrado).</summary>
<ul>
<li>O domínio personalizado configurado não tem um registro A ou um registro CNAME.</li>
<li>O cliente do navegador armazenou em cache o endereço IP antigo do seu domínio. Desmarque o cache e teste a resolução DNS novamente. Em um computador Windows, limpe o cache com o <code>ipconfig /flushdns</code>.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Migrar um domínio ativo

Para migrar um site ativo e seu nome de domínio DNS para o Serviço de Aplicativo sem tempo de inatividade, veja [Migrar um nome DNS ativo para o Serviço de Aplicativo do Azure](manage-custom-dns-migrate-domain.md).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Redirecionar para um diretório personalizado

<details>
<summary>Preciso disso?</summary>
<p>Depende de seu aplicativo. Por padrão, o Serviço de Aplicativo direciona solicitações da Web para o diretório raiz do seu código de aplicativo. No entanto, algumas estruturas da Web não são iniciadas no diretório raiz. Por exemplo, <a href="https://laravel.com/">Laravel</a> inicia no subdiretório <code>public</code>. Para continuar o exemplo de DNS <code>contoso.com</code>, este aplicativo poderá estar acessível em <code>http://contoso.com/public</code>, mas o ideal é direcionar <code>http://contoso.com</code> para o diretório <code>public</code>. </p>
</details>

Embora esse cenário seja comum, ele não envolve o mapeamento do domínio personalizado, e sim a personalização do diretório virtual no aplicativo.

1. Selecione **Configurações de aplicativo** no painel esquerdo da página do aplicativo Web.

1. Na parte inferior da página, o diretório virtual raiz `/` aponta para `site\wwwroot` por padrão, que é o diretório raiz do seu código de aplicativo. Altere-o para apontar para o `site\wwwroot\public` em vez disso, por exemplo, e salve as alterações.

    ![Captura de tela que mostra como personalizar um diretório virtual.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Após a conclusão da operação, verifique navegando até o caminho raiz do aplicativo no navegador (por exemplo, `http://contoso.com` ou `http://<app-name>.azurewebsites.net`).

<hr/> 

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar o gerenciamento de domínios personalizados com scripts usando a [CLI do Azure](/cli/azure/install-azure-cli) ou o [Azure PowerShell](/powershell/azure/).

#### <a name="azure-cli"></a>CLI do Azure

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Para obter mais informações, consulte [Mapear um domínio personalizado para um aplicativo Web](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Para obter mais informações, consulte [Atribuir um domínio personalizado para um aplicativo web](scripts/powershell-configure-custom-domain.md).

<hr/> 

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo tutorial e saiba como associar um certificado TLS/SSL personalizado a um aplicativo Web.

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md)