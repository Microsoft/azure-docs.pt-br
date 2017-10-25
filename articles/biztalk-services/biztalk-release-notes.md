---
title: "Notas de versão dos Serviços BizTalk do Azure | Microsoft Docs"
description: "Lista os problemas conhecidos dos Serviços BizTalk do Azure"
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 18ed891a9bba2b4011d3492722a2366d96fb3c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-azure-biztalk-services"></a>Notas de versão dos Serviços BizTalk do Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

As notas de versão dos Serviços BizTalk do Microsoft Azure contêm os problemas conhecidos nesta versão.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Novidades na atualização de novembro dos Serviços BizTalk
* A Criptografia em Repouso pode ser habilitada no Portal dos Serviços BizTalk. Confira [Habilitar a Criptografia em Repouso no Portal dos Serviços BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Histórico de atualizações
### <a name="october-update"></a>Atualização de outubro
* Suporte para contas organizacionais:  
  * **Cenário**: você registrou uma implantação do Serviço BizTalk usando uma conta da Microsoft (como user@live.com). Nesse cenário, somente os usuários de contas da Microsoft podem gerenciar o Serviço BizTalk usando o portal dos Serviços BizTalk. Não é possível usar uma conta organizacional.  
  * **Cenário**: você registrou uma implantação do Serviço BizTalk usando uma conta organizacional no Azure Active Directory (como user@fabrikam.com ou user@contoso.com). Nesse cenário, somente usuários do Azure Active Directory dentro da mesma organização podem gerenciar o Serviço BizTalk usando o portal de Serviços BizTalk. Não é possível usar uma conta da Microsoft.  
* Ao criar um Serviço BizTalk no portal clássico do Azure, você é registrado automaticamente no Portal dos Serviços BizTalk .
  * **Cenário**: você entra no portal clássico do Azure, cria um Serviço BizTalk e seleciona **Gerenciar** pela primeira vez. Quando abre o portal dos Serviços BizTalk, o Serviço BizTalk é registrado automaticamente e está pronto para as suas implantações.  
    Confira [Registrar e atualizar uma implantação do Serviço BizTalk no portal dos Serviços BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Atualização de 14 de agosto
* Separação de contratos e pontes: as pontes e os contratos entre parceiros comerciais agora estão separados no Portal dos Serviços BizTalk. Agora você cria contratos e pontes separadamente, e no tempo de execução as pontes resolvem para um contrato com base nos valores da mensagem EDI. Confira [Criar contratos nos Serviços BizTalk do Azure](https://msdn.microsoft.com/library/azure/hh689908.aspx), [Criar uma ponte EDI usando o Portal dos Serviços BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [Criar uma ponte AS2 usando o Portal dos Serviços BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx) e [Como as pontes resolvem contratos em tempo de execução?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* A opção de criar modelos para contratos foi descontinuada.  
* No contrato do lado de envio, agora você pode especificar diferentes conjuntos de delimitadores para cada esquema. Essa configuração é especificada nas configurações de protocolo do contrato do lado de envio. Para saber mais, confira [Criar um contrato X12 nos Serviços BizTalk do Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx) e [Criar um contrato EDIFACT nos Serviços BizTalk do Azure](https://msdn.microsoft.com/library/azure/dn606267.aspx). Também foram adicionadas duas novas entidades à API TPM OM para a mesma finalidade. Confira [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) e [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Agora há suporte para construtores XSD padrão, incluindo tipos derivados. Confira [Usar construtores XSD padrão em seus mapas](https://msdn.microsoft.com/library/azure/dn793987.aspx) e [Usar tipos derivados nos exemplos e cenários de mapeamento](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* O AS2 dá suporte a novos algoritmos MIC para assinatura de mensagens e a novos algoritmos de criptografia. Veja [Criar um contrato AS2 nos Serviços BizTalk do Azure](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Problemas conhecidos

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemas de conectividade após a atualização do Portal dos Serviços BizTalk
  Se você tiver o Portal dos Serviços BizTalk aberto enquanto os Serviços BizTalk são atualizados para implementar as alterações ao serviço, você poderá enfrentar problemas de conectividade com o Portal dos Serviços BizTalk.  
  Como alternativa, você pode reiniciar o navegador, excluir o cache do navegador ou iniciar o portal no modo privado.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>O IDE do Visual Studio não pode localizar o artefato se você clicar em um erro ou aviso em um projeto dos Serviços BizTalk
Instale o Visual Studio 2012 Update 3 RC 1 para corrigir o problema.  

### <a name="custom-binding-project-reference"></a>Referência de projeto de associação personalizada
Considere as seguintes situações com um projeto dos Serviços BizTalk em uma solução do Visual Studio:  

* Na mesma solução do Visual Studio, há um projeto dos Serviços BizTalk e um projeto de associação personalizada. O projeto do Serviço BizTalk tem uma referência a esse arquivo do projeto de associação personalizada.
* O projeto do Serviço BizTalk tem uma referência a uma DLL de associação/comportamento personalizado.

Você 'Compilou' a solução com êxito no Visual Studio. Em seguida, você 'Recompila' ou 'Limpa' a solução. Depois disso, quando você recompila ou limpa novamente, ocorre o seguinte erro:  
  Não é possível copiar o arquivo <Path to DLL> para "bin\Debug\FileName.dll". O processo não pode acessar o arquivo 'bin\Debug\FileName.dll' porque ele está sendo usado por outro processo.  

#### <a name="workaround"></a>Solução alternativa
* Se o [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) for instalado, você terá as duas opções a seguir:
  
  * Reiniciar o Visual Studio ou
  * Reiniciar a solução. Em seguida, execute apenas uma Compilação na solução.  
* Se o [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) não estiver instalado, abra o Gerenciador de Tarefas, clique na guia Processos, clique no processo MSBuild.exe e clique no botão Encerrar Processo.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>O roteamento de pontes e do Portal dos Serviços BizTalk para os pontos de extremidade BasicHttpRelay não tem suporte se caracteres não imprimíveis forem promovidos como cabeçalhos HTTP
Se você usar caracteres não imprimíveis como parte das propriedades promovidas para mensagens, essas mensagens não poderão ser roteadas para destinos de retransmissão que usam a associação BasicHttpRelay. Além disso, as propriedades promovidas que estão disponíveis como parte do rastreamento serão codificadas na URL para blobs e não codificadas para destinos.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>A MDN é enviada de forma assíncrona mesmo se a opção Enviar MDN assíncrona estiver desmarcada
Considere este cenário: se você marcar a caixa de seleção **Enviar MDN assíncrona**, especificar uma URL para enviar a MDN assíncrona e desmarcar a caixa de seleção **Enviar MDN assíncrona** novamente, a MDN ainda será enviada para a URL especificada mesmo que a opção Enviar MDN assíncrona não esteja selecionada.  
Como alternativa, você deve limpar a URL especificada antes de desmarcar a caixa de seleção **Enviar MDN assíncrona** e só depois implantar o contrato AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Os caracteres de espaço em branco além de um intercâmbio válido provocam o envio de uma mensagem vazia ao ponto de extremidade de suspensão
Se houver espaços em branco fora de um segmento IEA, o desmontador tratará disso como o fim do intercâmbio atual e examinará o próximo conjunto de espaços em branco como uma próxima mensagem. Como este não é um intercâmbio válido, você pode observar que uma mensagem bem-sucedida é enviada para o destino da rota e uma mensagem vazia é enviada para o ponto de extremidade de suspensão.  

### <a name="tracking-in-biztalk-services-portal"></a>Rastreamento no Portal dos Serviços BizTalk
Os eventos de rastreamento são capturados até o processamento de mensagens EDI e eventuais correlações. Se uma mensagem falhar fora do estágio de Protocolo, o Rastreamento será mostrado como bem-sucedido. Nessa situação, consulte a seção LOG sob a coluna **Detalhes** em **Controle** para obter os detalhes do erro.
As configurações de Recebimento e Envio do X12 ([Criar um Contrato X12 nos Serviços BizTalk do Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fornecem informações sobre o estágio de Protocolo.  

### <a name="update-agreement"></a>Atualização do contrato
O Portal dos Serviços BizTalk permite que você modifique o Qualificador de uma Identidade quando um contrato é configurado. Isso pode resultar em propriedades inconsistentes. Por exemplo, há um contrato usando ZZ:1234567 e ZZ:7654321 como Qualificador. Nas configurações de perfil do Portal dos Serviços BizTalk, você altera ZZ:1234567 para ser 01:ChangedValue. Você abre o contrato e 01:ChangedValue é exibido em vez de ZZ:1234567.
Para modificar o Qualificador de uma identidade, exclua o contrato, atualize **Identidades** no perfil do parceiro e recrie o contrato.  

> AZURE.WARNING Esse comportamento afeta o X12 e o AS2.  
> 
> 

### <a name="as2-attachments"></a>Anexos AS2
Não há suporte para anexos de mensagens AS2 nos envios ou recebimentos. Especificamente, os anexos são silenciosamente ignorados e o corpo da mensagem é processado como uma mensagem AS2 normal.  

### <a name="resources-remembering-path"></a>Recursos: lembrar o caminho
Ao adicionar **Recursos**, a janela de diálogo pode não lembrar o caminho usado anteriormente para adicionar um recurso. Para lembrar o caminho usado anteriormente, experimente adicionar o site do Portal dos Serviços BizTalk aos **Sites Confiáveis** no Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Se você renomear o nome da entidade de uma ponte e fechar o projeto sem salvar as alterações, abrir a entidade novamente resultará em um erro
Considere um cenário na seguinte ordem:  

* Adicionar uma ponte (por exemplo, uma ponte unidirecional XML) a um projeto do Serviço BizTalk  
* Renomeie a ponte especificando um valor para a propriedade Nome da Entidade. Isso renomeia o arquivo .bridgeconfig associado ao nome especificado.  
* Feche o arquivo .bcs (fechando a guia no Visual Studio) sem salvar as alterações.  
* Abra o arquivo .bcs novamente no Gerenciador de Soluções.  
  Você observará que enquanto o arquivo .bridgeconfig associado tem o novo nome especificado, o nome da entidade na superfície de design ainda é o nome antigo. Se você tentar abrir a configuração da ponte clicando duas vezes no componente de ponte, obterá o seguinte erro:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` Para evitar esse cenário, não deixe de salvar as alterações depois de renomear as entidades em um projeto do Serviço BizTalk.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>O projeto do Serviço BizTalk é compilado com êxito mesmo que um artefato tenha sido excluído de um projeto do Visual Studio
Considere um cenário onde você adiciona um artefato (por exemplo, um arquivo XSD) a um projeto do Serviço BizTalk, inclui esse artefato na Configuração da Ponte (por exemplo, especificando-o como um tipo de mensagem de Solicitação) e exclui-o do projeto do Visual Studio. Nesse caso, a compilação do projeto não fornecerá nenhum erro desde que o artefato excluído esteja disponível no disco no mesmo local de onde foi incluído no projeto do Visual Studio.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>O projeto do Serviço BizTalk não verifica a disponibilidade do esquema ao configurar as pontes
Em um projeto do Serviço BizTalk, se um esquema adicionado ao projeto importar outro esquema, o projeto do Serviço BizTalk não verificará se o esquema importado é adicionado ao projeto. Se você tentar compilar esse projeto, não obterá erros de compilação.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>A mensagem de resposta de uma Ponte de Solicitação-Resposta XML é enviada sempre com o conjunto de caracteres UTF-8
Para esta versão, o conjunto de caracteres da mensagem de resposta de uma Ponte de Solicitação-Resposta XML é sempre definido como UTF-8.
  
### <a name="user-defined-datatypes"></a>Tipos de dados definidos pelo usuário
Os adaptadores do BizTalk Adapter Pack dentro do recurso do Serviço do Adaptador do BizTalk podem utilizar tipos de dados definidos pelo usuário para operações de adaptador.
Ao usar tipos de dados definidos pelo usuário, copie os arquivos (.dll) para drive:\Program Files\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\ ou para o Cache de Assembly Global (GAC) no servidor que hospeda o Serviço do Adaptador do BizTalk. Caso contrário, o seguinte erro pode ocorrer no cliente:  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> É recomendável usar o GACUtil.exe para instalar um arquivo no Cache de Assembly Global. O GACUtil.exe mostra como usar essa ferramenta e as opções de linha de comando do Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Reiniciar o Site do Serviço do Adaptador do BizTalk
Instalar o **Tempo de Execução do Serviço do Adaptador do BizTalk*** cria o site da Web **Serviço do Adaptador do BizTalk** no IIS que contém o aplicativo **BAService**. O aplicativo **BAService** usa associação de retransmissão internamente para estender o alcance do ponto de extremidade de serviço local para a nuvem. Para um serviço hospedado localmente, o ponto de extremidade de retransmissão correspondente será registrado no Barramento de Serviço somente quando o serviço local for iniciado.  

Se você interromper e iniciar um aplicativo, a configuração para iniciar automaticamente um aplicativo não será respeitada. Portanto, quando o **BAService** é interrompido, você sempre deve reiniciar o site do **Serviço do Adaptador do BizTalk**. Não inicie ou interrompa o aplicativo **BAService** .

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Caracteres especiais não devem ser usados para nomes de endereços e entidades de componentes LOB
Você não deve usar caracteres especiais para nomes de endereços e entidades de componentes LOB. Se você fizer isso, obterá um erro ao implantar o projeto do Serviço BizTalk. Para determinados caracteres como '%', o site do Serviço do Adaptador do BizTalk pode entrar em um estado parado e você terá que iniciá-lo manualmente.

### <a name="test-map-with-get-context-property"></a>Mapa de teste com a propriedade Get Context
Se uma Transformação contiver uma **Operação de Mapeamento da Propriedade Get Context**, o **Mapa de Teste** falhará. Como solução temporária, substitua a Operação de Mapeamento da **Propriedade Get Context** com uma Operação de Mapeamento de Cadeia de Caracteres contendo dados fictícios. Isso populará o esquema alvo e permitirá testar outra funcionalidade de Transformação.

### <a name="test-map-property-does-not-display"></a>A propriedade do mapa de teste não é exibida
As propriedades do **Mapa de Teste** não são exibidas no Visual Studio. Isso poderá ocorrer se a janela **Propriedades** e a janela **Gerenciador de Soluções** não forem encaixadas simultaneamente. Para resolver isso, encaixe as janelas **Propriedades** e **Gerenciador de Soluções**.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>O menu suspenso de Reformatação de Data/Hora está esmaecido
Quando uma Operação de Mapeamento de Reformatação de Data/Hora é adicionada à superfície de design e configurada, a lista suspensa de Formato pode ser esmaecida. Isso pode acontecer se a Exibição do computador estiver definida como **Médio – 125%** ou **Maior – 150%**. Para resolver, defina a tela como **Menor – 100% (padrão)** usando as etapas abaixo:  

1. Abra o **Painel de Controle** e clique em **Aparência e Personalização**.
2. Clique em **Tela**.
3. Clique em **Menor – 100% (padrão**) e clique em **Aplicar**.

A lista suspensa de **Formato** agora deve funcionar conforme esperado.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Contratos duplicados no Portal dos Serviços BizTalk
Considere este cenário:

1. Crie um contrato usando a API do Modelo de Objeto de Gerenciamento de Parceiros Comerciais.
2. Abra o contrato no Portal dos Serviços BizTalk em duas guias diferentes.
3. Implante o contrato em ambas as guias.
4. Como resultado, ambos os contratos são implantados, resultando em entradas duplicadas no Portal dos Serviços BizTalk

**Solução alternativa**. Abra um dos contratos duplicados no Portal dos Serviços BizTalk e remova a implantação.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>As pontes não usam certificados atualizados mesmo depois de um certificado ter sido atualizado no repositório de artefatos
Considere os seguintes cenários:  

**Cenário 1: usar certificados baseados em impressão digital para transferência segura de mensagens de uma ponte para um ponto de extremidade de serviço**  
Considere um cenário em que você usa certificados baseados em impressão digital no seu projeto do Serviço BizTalk. Você atualiza o certificado no Portal dos Serviços BizTalk com o mesmo nome e com uma impressão digital diferente, mas não atualiza o projeto do Serviço BizTalk de forma correspondente. Nesse cenário, a ponte pode continuar a processar as mensagens porque os dados do certificado antigo ainda podem estar no cache do canal. Após isso, o processamento de mensagens falha.  

**Solução**: atualizar o certificado no projeto do Serviço BizTalk e reimplantar o projeto.  

**Cenário 2: usar comportamentos baseados em nome para identificar certificados para transferência segura de mensagens de uma ponte para um ponto de extremidade de serviço**

Considere um cenário onde você usa comportamentos baseados em nome para identificar certificados no seu projeto do Serviço BizTalk. Você atualiza o certificado no Portal dos Serviços BizTalk mas não atualiza o projeto do Serviço BizTalk de forma correspondente. Nesse cenário, a ponte pode continuar a processar as mensagens porque os dados do certificado antigo ainda podem estar no cache do canal. Após isso, o processamento de mensagens falha.  

**Solução**: atualizar o certificado no projeto do Serviço BizTalk e reimplantar o projeto.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>As pontes continuam a processar mensagens mesmo quando o banco de dados SQL está offline
As pontes de Serviços BizTalk continuam a processar mensagens por algum tempo, mesmo se o Banco de Dados SQL do Microsoft Azure (que armazena as informações em execução como pipelines e artefatos implantados) estiver offline. Isso ocorre porque os Serviços BizTalk usam os artefatos de cache e a configuração da ponte.
Se não quiser que as pontes processem mensagens quando o Banco de Dados SQL estiver offline, você pode usar os cmdlets do PowerShell dos Serviços BizTalk para interromper ou suspender o Serviço BizTalk. Confira [Exemplo de Gerenciamento do Serviço BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=329019) para ver os cmdlets do Windows PowerShell para gerenciar operações.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>A leitura da mensagem XML no componente de código personalizado de uma ponte inclui um caractere BOM adicional
Considere um cenário onde você deseja ler uma mensagem XML no código personalizado de uma ponte. Se você usar a API .NET System.Text.Encoding.UTF8.GetString(bytes), um caractere BOM extra será incluído na saída no início da mensagem. Portanto, se não desejar que a saída inclua o caractere BOM adicional, você deve usar ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>As mensagens enviadas para uma ponte usando WCF não são dimensionadas
As mensagens enviadas para uma ponte usando WCF não são dimensionadas. Em vez disso, você deve usar HttpWebRequest se desejar ter um cliente escalonável.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>ATUALIZAÇÃO: erro no Provedor de Token após a atualização da Visualização dos Serviços BizTalk para Disponibilidade Geral (GA)
Há um Contrato AS2 ou EDI com lotes ativos. Quando o Serviço BizTalk for atualizado do Preview para o GA, poderá ocorrer o seguinte:

* Erro: o provedor de token não pôde fornecer um token de segurança. O provedor de token retornou a mensagem: o nome remoto não pôde ser resolvido.
* As tarefas em lote são canceladas.

**Solução**: após o Serviço BizTalk ser atualizado para a Disponibilidade Geral (GA), reimplante o contrato.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>ATUALIZAÇÃO: a caixa de ferramentas mostra os ícones de ponte antigos depois de atualizar o SDK dos Serviços BizTalk
Depois de atualizar uma versão anterior do SDK dos Serviços BizTalk, que tinha ícones antigos representando as pontes, a caixa de ferramentas continua mostrando os ícones antigos das pontes. No entanto, se você adicionar uma ponte à superfície de designer de projeto do Serviço BizTalk, a superfície mostrará o novo ícone.  

**Solução alternativa**. Você pode contornar esse problema excluindo os arquivos .tbd em<system drive> :\Users\<usuário>\AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>ATUALIZAÇÃO: a atualização do Portal do BizTalk da Visualização para a GA pode mostrar um erro indicando que a capacidade do EDI não está disponível
Se você estiver conectado ao Portal dos Serviços BizTalk quando os Serviços BizTalk forem atualizados da Visualização para a GA, poderá obter o seguinte erro no portal:  

Este recurso não está disponível como parte desta edição dos Serviços BizTalk do Microsoft Azure. Para usar esses recursos, mude para uma edição apropriada.  

**Resolução**: saia do portal, feche e abra o navegador e faça logon no portal.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>ATUALIZAÇÃO: os novos dados de rastreamento não aparecem depois que os Serviços BizTalk são atualizados para a GA
Imagine um cenário no qual você tem uma ponte XML implantada na assinatura de Visualização dos Serviços BizTalk. Você envia mensagens à ponte e o controle de dados correspondente está disponível no Portal dos Serviços BizTalk. Agora, se os bits de tempo de execução do Portal dos Serviços BizTalk e os Serviços BizTalk forem atualizados para a GA e você enviar uma mensagem para o mesmo ponto de extremidade de ponte implantado anteriormente, os dados de rastreamento não aparecerão para mensagens enviadas depois da atualização.  

### <a name="pipelines-versus-bridges"></a>Pipelines versus pontes
Ao longo deste documento, os termos 'pipelines' e 'pontes' são usados de forma intercambiável. Ambos significam essencialmente a mesma coisa, isto é, uma unidade de processamento de mensagens implantada nos Serviços BizTalk.  

### <a name="concepts"></a>Conceitos
[Serviços BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

