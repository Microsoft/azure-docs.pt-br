---
title: Solucionar problemas de configurações de delegação restrita de Kerberos para o proxy de aplicativo | Microsoft Docs
description: Solucionar problemas configurações de delegação restrita de Kerberos para o Proxy do Aplicativo
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ca50cfb8697fdbb8c71054c5a6b4d5e23792eb5
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381528"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Solucionar problemas configurações de delegação restrita de Kerberos para o Proxy do Aplicativo

Os métodos disponíveis para a obtenção de SSO a aplicativos publicados podem variar de um aplicativo para outro. Uma das opções que o Proxy de Aplicativo do Azure do Azure Active Directory (Azure AD) oferece por padrão é a delegação restrita de Kerberos (KCD). Você pode configurar um conector, para os seus usuários, para executar a autenticação restrita de Kerberos para aplicativos de back-end.

O procedimento para habilitar o KCD é simples. Ele requer não mais do que uma compreensão geral dos vários componentes e fluxo de autenticação que oferecem suporte a SSO. Mas, às vezes, o SSO do KCD não funciona conforme o esperado. Você precisa de boas fontes de informações para solucionar os problemas para esses cenários.

Este artigo tenta fornece um único ponto de referência que ajuda a solucionar problemas e resolver alguns dos problemas mais comuns por conta própria. Ele também aborda o diagnóstico de problemas de implementação mais complexos.

Este artigo pressupõe o seguinte:

- Implantação do Proxy de Aplicativo do Azure AD conforme a [Introdução ao Proxy de Aplicativo](application-proxy-add-on-premises-application.md) e o acesso geral a aplicativos que não são KCD não está funcionando conforme o esperado.
- O aplicativo de destino publicado baseia-se nos Serviços de Informações da Internet (IIS) e na implementação Microsoft de Kerberos.
- Os hosts de servidor e aplicativo residem em um único domínio do Azure Active Directory. Para obter informações detalhadas sobre cenários de floresta e entre domínios, consulte o [white paper do KCD](https://aka.ms/KCDPaper).
- O aplicativo em questãoo é publicado em um locatário do Azure com pré-autenticação habilitada. Os usuários devem autenticar no Azure por meio da autenticação baseada em formulários. Cenários de autenticação de cliente avançados não são cobertos por este artigo. Eles podem ser adicionados em algum momento no futuro.

## <a name="prerequisites"></a>Pré-requisitos

O Proxy de Aplicativo do Azure AD pode ser implantado em vários tipos de infraestruturas ou ambientes. As arquiteturas variam de organização para organização. As causas mais comuns de problemas relacionados ao KCD não são os ambientes. Pequenos erros de configuração ou erros gerais são o que causam a maior parte dos problemas.

Por esse motivo, é melhor certificar-se de que você cumpriu todos os pré-requisitos em [Usando SSO do KCD com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md) antes de iniciar a solução de problemas. Observe a seção sobre como configurar a delegação restrita de Kerberos no 2012R2. Esse processo emprega uma abordagem diferente para configurar o KCD em versões anteriores do Windows. Além disso, você deve estar atento a essas considerações:

- Não é incomum que um servidor membro do domínio abra uma caixa de diálogo de canal seguro com um controlador de domínio (DC) específico. Em seguida, o servidor pode mudar para outra caixa de diálogo a qualquer momento. Então hosts de conector não estão restritos à comunicação com os controladores de domínio do site do local específico.
- Cenários entre domínios dependem de referências para direcionar um host de conector para controladores de domínio que estejam fora do perímetro da rede local. Nesses casos, é igualmente importante também enviar tráfego para controladores de domínio que representam outros domínios. Caso contrário, a delegação falha.
- Sempre que possível, evite colocar quaisquer dispositivos IPS ou IDS ativos entre os hosts do conector e os controladores de domínio. Esses dispositivos são, às vezes, muito invasivos e interferem no tráfego principal de RPC.

Teste a delegação em cenários simples. Quanto mais variáveis você introduzir, com mais você pode ter que lidar. Para economizar tempo, limite o teste a um único conector. Adicione outros conectores depois que o problema for resolvido.

Alguns fatores relacionados ao ambiente também podem estar contribuindo para um problema. Para evitar esses fatores, minimize a arquitetura o máximo possível durante o teste. Por exemplo, ACLs de firewall interno configurado incorretamente é um problema comum. Se possível, envie todo o tráfego de um conector diretamente para os controladores de domínio e para o aplicativo de back-end.

O melhor local para posicionar os conectores é o mais próximo possível de seus destinos. Um firewall que fica embutido durante o teste adiciona complexidade desnecessária e pode prolongar as investigações.

O que mostra um problema do KCD? Há várias indicações comuns de que o SSO do KCD está falhando. Os primeiros sinais de um problema aparecem no navegador.

![Exemplo: Erro de configuração do KCD incorreto](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Exemplo: A autorização falhou devido à falta de permissões](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Ambas essas imagens mostram o mesmo sintoma: Falha SSO. O acesso do usuário ao aplicativo foi negado.

## <a name="troubleshooting"></a>Solução de problemas

Como você soluciona os problemas depende do problema e dos sintomas observados. Antes de continuar, explore os artigos a seguir. Eles fornecem informações úteis de solução de problemas:

- [Solucionar problemas e mensagens de erro do Application Proxy](application-proxy-troubleshoot.md)
- [Sintomas e erros de Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Trabalhando com o SSO quando as identidades local e na nuvem não são idênticas](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Se você chegou até aqui, então o seu principal problema existe. Para começar, separe o fluxo nos três estágios a seguir que você pode solucionar.

### <a name="client-pre-authentication"></a>Pré-autenticação do cliente

O usuário externo realiza a autenticação no Azure por meio de um navegador. A capacidade de autenticar previamente no Azure é obrigatória para o SSO do KCD funcionar. Teste e corrija esse recurso se houver algum problema. A fase de pré-autenticação não tem nenhuma relação com o KCD ou o aplicativo publicado. É fácil corrigir discrepâncias por meio da verificação de integridade da conta, se ela existe no Azure. Verifique também se ela não está desabilitada ou bloqueada. A resposta de erro no navegador é normalmente descritiva o suficiente para explicar a causa. Se não tiver certeza, verifique outros artigos de solução de problemas da Microsoft para verificar.

### <a name="delegation-service"></a>Serviço de delegação

O conector do Proxy do Azure que obtém um tíquete de serviço Kerberos para usuários de um centro de distribuição de chaves (KCD) de Kerberos.

As comunicações externas entre o cliente e o front-end do Azure não tem nenhuma influência sobre o KCD. Essas comunicações apenas garantem que o KCD funciona. O serviço de Proxy do Azure recebe uma ID de usuário válida que é usada para obter um tíquete do Kerberos. Sem essa ID, o KCD não é possível e falha.

Conforme mencionado anteriormente, as mensagens de erro do navegador fornecem boas dicas do por que as coisas estão falhando. Certifique-se de anotar a ID da atividade e o carimbo de data/hora na resposta. Essas informações ajudam a correlacionar o comportamento a eventos reais no log de eventos do Proxy do Azure.

![Exemplo: Erro de configuração do KCD incorreto](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

As entradas correspondentes visualizadas no log de eventos seriam os eventos 13019 ou 12027. Você pode encontrar os logs de eventos do conector em **Logs de Aplicativos e Serviços** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Conector** &gt; **Admin**.

![Evento 13019 do log de eventos do Application Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Evento 12027 do log de eventos do Application Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Usar um registro **A** no seu DNS interno para o endereço do aplicativo, e não um **CName**.
1. Confirme novamente que o host do conector tenha recebido direitos para delegar para o SPN da conta de destino designada. Confirme novamente que **Usar qualquer protocolo de autenticação** está selecionado. Para obter mais informações sobre este tópico, consulte o [artigo de configuração de SSO](application-proxy-configure-single-sign-on-with-kcd.md).
1. Verifique se há apenas uma instância do SPN existente no Azure AD. Problema `setspn -x` em um prompt de comando em qualquer host de membro do domínio.
1. Verifique se uma política de domínio é aplicada que limita o [tamanho máximo de tokens Kerberos emitidos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Essa política impede o conector de obter um token se ele for considerado excessivo.

Um rastreamento de rede que captura as trocas entre o host de conector e um domínio de KDC é a próxima etapa recomendada para obter detalhes de nível mais baixo sobre os problemas. Para obter mais informações, consulte [aprofundamento do papel de solução de problemas](https://aka.ms/proxytshootpaper).

Se o tíquete parece bom, você verá um evento nos logs, indicando que a autenticação falhou devido ao aplicativo que retorna um 401. Esse evento indica que o aplicativo de destino rejeitou o tíquete. Ir para o estágio seguinte.

### <a name="target-application"></a>Aplicativo de destino

O consumidor do tíquete do Kerberos fornecido pelo conector. Neste estágio, é esperado que o conector tenha enviado um tíquete de serviço Kerberos para o back-end. O tíquete é um cabeçalho na primeira solicitação de aplicativo.

1. Usando a URL do aplicativo interno definida no portal, valide que o aplicativo é acessível diretamente do navegador no host do conector. Em seguida, você pode conectar-se com êxito. Os detalhes podem ser encontrados na página de **Solução de problemas** do conector.
1. Ainda no host do conector, confirme se a autenticação entre o navegador e o aplicativo está usando Kerberos. Execute uma das seguintes ações:
1. Execute as ferramentas de desenvolvimento (**F12**) no Internet Explorer, ou use [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) do host de conector. Vá para o aplicativo usando a URL interna. Inspecione os cabeçalhos de autorização WWW oferecidos e retornados na resposta do aplicativo, para garantir que Negotiate ou Kerberos esteja presente.

   - O próximo blob Kerberos que é retornado na resposta do navegador para o aplicativo começa com **YII**. Essas letras indicam que o Kerberos está em execução. O Microsoft NT LAN Manager (NTLM), por outro lado, sempre começa com **TlRMTVNTUAAB**, que lê o Provedor de Suporte de Segurança NTLM (NTLMSSP) quando decodificado da Base64. Se você vê **TlRMTVNTUAAB** no início do blob, Kerberos não está disponível. Se você não vê **TIRMTVNTUAAB**, é provável que o Kerberos esteja disponível.

      > [!NOTE]
      > Se você usa o Fiddler, esse método requer que você desabilite temporariamente a proteção estendida na configuração do aplicativo no IIS.

      ![Janela de inspeção da rede do navegador](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - O blob nesta figura não começa com **TIRMTVNTUAAB**. Portanto, neste exemplo, o Kerberos está disponível e o blob de Kerberos não começa com **YII**.

1. Remova temporariamente o NTLM da lista de provedores no site do IIS. Acesse o aplicativo diretamente do Internet Explorer no host do conector. NTLM não está mais na lista de provedores. Você pode acessar o aplicativo usando somente o Kerberos. Se o acesso falhar, pode haver um problema com a configuração do aplicativo. A autenticação Kerberos não está funcionando.

   - Se o Kerberos não estiver disponível, verifique as configurações de autenticação do aplicativo no IIS. Certifique-se de que **Negotiate** está listado na parte superior, com NTLM logo abaixo. Se você vir **Sem Negotiate**, **Kerberos ou Negotiate**, ou **PKU2U**, continue somente se o Kerberos estiver funcionando.

     ![Fornecedores de autenticação do Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Com o Kerberos e NTLM implementados, desabilite temporariamente a pré-autenticação para o aplicativo no portal. Tente acessar pela Internet usando a URL externa. Você será solicitado a autenticar. Você pode fazer isso com a mesma conta usada na etapa anterior. Caso contrário, há um problema com o aplicativo de back-end, não KCD.
   - Habilite novamente a pré-autenticação no portal. Autentique por meio do Azure, tentando se conectar ao aplicativo por meio de sua URL externa. Se o SSO falhar, você verá uma mensagem de erro de proibido do navegador, além do evento 13022 no log:

     *Conector do Application Proxy do Microsoft AAD não pode autenticar o usuário porque o servidor back-end responde às tentativas de autenticação do Kerberos com um erro HTTP 401.*

      ![Mostra o erro proibido do HTTTP 401](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Verificar o aplicativo IIS. Certifique-se de que o pool de aplicativos configurado e o SPN estão configurados para usar a mesma conta no Azure AD. Navegue no IIS conforme mostrado na ilustração a seguir:

      ![Janela de configuração de aplicativo IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Depois que você sabe a identidade, verifique se essa conta é configurada com o SPN em questão. Um exemplo é `setspn –q http/spn.wacketywack.com`. Insira o seguinte texto em um prompt de comando:

      ![Mostra a janela de comando SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Verifique o SPN definido nas configurações do aplicativo no portal. Certifique-se de que o mesmo SPN configurado em relação à conta de destino do Azure AD é usado pelo pool de aplicativos do aplicativo.

      ![Configuração de SPN no portal do Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Vá para o IIS e selecione a opção **Editor de configuração** para o aplicativo. Navegue até **system.webServer/security/authentication/windowsAuthentication**. Verifique se o valor de **UseAppPoolCredentials** é **True**.

      ![Opção de credencial de pools de aplicativo da configuração do IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Altere este valor para **True**. Remova todos os tíquetes Kerberos armazenados em cache do servidor de back-end executando o seguinte comando:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Para obter mais informações, consulte [Limpar o cache de tíquete do cliente Kerberos de todas as sessões](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Se você sair do modo de Kernel habilitado, ele melhora o desempenho de operações do Kerberos. Mas isso também faz com que o tíquete para o serviço solicitado seja descriptografado usando a conta do computador. Essa conta também é chamada de sistema Local. Defina esse valor como **True** para interromper o KCD quando o aplicativo é hospedado em mais de um servidor em um farm.

- Como uma verificação adicional, desabilite também a proteção **Estendida**. Em alguns cenários, a proteção **Estendida** interrompeu o KCD quando ele foi habilitado em configurações específicas. Nesses casos, um aplicativo foi publicado como uma subpasta do site padrão. Este aplicativo está configurado somente para autenticação anônima. As caixas de diálogo estão esmaecidas, sugerindo que os objetos filho não herdam as configurações ativas. É recomendável que você teste, mas não se esqueça de restaurar esse valor para **habilitado**, sempre que possível.

  Essa verificação adicional coloca você no caminho para usar o aplicativo publicado. Você pode criar conectores adicionais que também são configurados para delegar. Para obter mais informações, leia o passo a passo detalhado mais técnico, [Solução de problemas do Proxy de Aplicativo do Azure AD](https://aka.ms/proxytshootpaper).

Se você ainda não conseguir progredir, o suporte da Microsoft pode ajudá-lo. Crie um tíquete de suporte diretamente dentro do portal. Um engenheiro entrará em contato com você.

## <a name="other-scenarios"></a>Outros cenários

- Application Proxy do Azure solicita um tíquete do Kerberos antes de enviar a solicitação para um aplicativo. Alguns aplicativos de terceiros não gostam desse método de autenticação. Esses aplicativos esperam que as negociações mais convencionais ocorram. A primeira solicitação é anônima, e permite que o aplicativo responda com os tipos de autenticação que ele oferece suporte através de um 401.
- Autenticação com salto duplo é comumente usada em cenários com aplicativos em camadas, com um back-end e front-end, ambos exigindo autenticação, como o SQL Server Reporting Services. Para configurar o cenário de salto múltiplo, consulte o artigo de suporte a [delegação restrita de Kerberos pode exigir a transição de protocolo em cenários de multi-hop](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Próximas etapas

[Criar um KCD em um domínio gerenciado](../../active-directory-domain-services/deploy-kcd.md).
