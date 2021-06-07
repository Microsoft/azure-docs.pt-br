---
title: Monitoramento com testes na Web de várias etapas – Azure Application Insights
description: Configuração de testes na Web de várias etapas para monitorar aplicativos Web com o Azure Application Insights
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 1d3597eaf54c40fb1f986d822af0dd6b8c8a7b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719841"
---
# <a name="multi-step-web-tests"></a>Testes na Web com diversas etapas

Monitore uma sequência registrada de URLs e interações com um site através de testes na Web de várias etapas. Este artigo explicará o processo de criação de um teste na Web de várias etapas com o Visual Studio Enterprise.

> [!NOTE]
> Testes na Web de várias etapas dependem de arquivos de webtest do Visual Studio. Foi [anunciado](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) que o Visual Studio 2019 será a última versão com a funcionalidade webtest. É importante entender que, embora nenhum novo recurso seja adicionado, a funcionalidade webtest no Visual Studio 2019 ainda é suportada e continuará a ter suporte durante o ciclo de vida de suporte do produto. A equipe de produto do Azure Monitor respondeu perguntas sobre o futuro dos testes de disponibilidade de várias etapas [aqui](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101).  
> </br>
> **Não há suporte** para testes na Web de várias etapas na nuvem do [Azure Governamental](../../azure-government/index.yml).


## <a name="pre-requisites"></a>Pré-requisitos

* Visual Studio 2017 Enterprise ou superior.
* Ferramentas de teste de carga e desempenho Web do Visual Studio.

Localizar o pré-requisito das ferramentas de teste. Inicie o **Instalador do Visual Studio** > **Componentes individuais** > **Depuração e teste** > **Ferramentas de teste de carga e de desempenho Web**.

![Captura de tela da interface do usuário do instalador do Visual Studio com componentes individuais selecionados com uma caixa de seleção ao lado do item para ferramentas de teste de carga e desempenho Web](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Testes na Web de várias etapas têm custos adicionais associados a eles. Para saber mais, consulte o [guia de preços oficial](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Gravação de um teste na Web de várias etapas 

> [!WARNING]
> Não é mais recomendável usar o gravador de várias etapas. O gravador foi desenvolvido para páginas HTML estáticas com interações básicas e não fornece uma experiência funcional para páginas da Web modernas.

Para obter orientação sobre como criar testes na Web do Visual Studio, consulte a [documentação oficial do Visual Studio 2019](/visualstudio/test/how-to-create-a-web-service-test).

## <a name="upload-the-web-test"></a>Carregamento do teste na Web

1. No portal do Application Insights no painel Disponibilidade, selecione **Criar teste** > **Tipo de teste** > **Teste na Web de várias etapas**.

2. Defina os locais, a frequência e os parâmetros de alerta do teste.

### <a name="frequency--location"></a>Frequência e local

|Configuração| Explicação
|----|----|----|
|**Frequência de teste**| define a frequência com que o teste é executado em cada localização de teste. Com uma frequência padrão de cinco minutos e cinco locais de teste, seu site é testado em média a cada minuto.|
|**Locais de teste**| São os locais de onde nossos servidores enviam solicitações da Web para a sua URL. **O número mínimo de locais de teste recomendado é cinco**, para garantir que você possa diferenciar problemas no seu site de problemas na rede. Você pode selecionar até 16 locais.

### <a name="success-criteria"></a>Critérios de êxito

|Configuração| Explicação
|----|----|----|
| **Tempo limite de teste** |diminua esse valor para ser alertado sobre respostas lentas. O teste é considerado uma falha se as respostas de seu site não são recebidas dentro desse período. Se você tiver selecionado **Analisar solicitações dependentes**, todas as imagens, arquivos de estilo, scripts e outros recursos dependentes devem ter sido recebidos dentro desse período.|
| **Resposta HTTP** | o código de status retornado que é contado como êxito. 200 é o código que indica que uma página da Web normal foi retornada.|
| **Correspondência de conteúdo** | Uma cadeia de caracteres como, por exemplo, “Bem-vindo!” Faremos o teste que uma correspondência exata de maiúsculas e minúsculas ocorre em todas as respostas. É necessário que seja uma cadeia de caracteres simples, sem curingas. Lembre-se de que se o conteúdo de sua página for alterado, talvez seja necessário atualizá-lo. **Somente caracteres da língua inglesa têm suporte na correspondência de conteúdo** |

### <a name="alerts"></a>Alertas

|Configuração| Explicação
|----|----|----|
|**Quase em tempo real (Versão prévia)** | É recomendável usar alertas quase em tempo real. A configuração desse tipo de alerta é feita após a criação do teste de disponibilidade.  |
|**Limite de locais de alerta**|é recomendável um mínimo de 3/5 locais. A relação ideal entre o limite de alertas locais e o número de locais de teste é o **limite de alertas locais** = **número de locais de teste - 2, com um mínimo de cinco locais de teste.**|

## <a name="configuration"></a>Configuração

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Tempo de conexão e números aleatórios no teste

Suponha que você está testando uma ferramenta que obtém dados dependentes de tempo, como estoques de um feed externo. Quando grava seu teste na Web você deve usar horários específicos, definindo-os, todavia, como parâmetros do teste, StartTime e EndTime.

![Captura de tela do meu aplicativo incrível de ações](./media/availability-multistep/app-insights-72webtest-parameters.png)

Quando você executa o teste, o ideal é que EndTime seja sempre a hora atual e StartTime seja o horário de 15 minutos atrás.

O plug-in de data e hora do teste na Web fornece a maneira de lidar com os tempos de parametrização.

1. Adicione um plug-in de teste na Web para cada valor de parâmetro variável desejado. Na barra de ferramentas de teste da Web, escolha **Adicionar Plug-in de Teste na Web**.
    
    ![Adicionar plug-in de teste na Web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    Neste exemplo, usamos duas instâncias do plug-in de Data e Hora. É uma instância de "15 minutos atrás" e outra de "agora".

2. Abra as propriedades de cada plug-in. Atribua um nome e configure-o para usar a hora atual. Para um deles, defina Add Minutes = -15.

    ![Parâmetros de contexto](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Nos parâmetros de teste na Web, use {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Agora, carregue seu teste no portal. Ele usará os valores dinâmicos em todas as execuções do teste.

### <a name="dealing-with-sign-in"></a>Lidando com a entrada

Se os usuários entrarem em seu aplicativo, você terá várias opções para simular entradas para poder testar as páginas por trás da entrada. A abordagem usada dependerá do tipo de segurança fornecida pelo aplicativo.

Em todos os casos, você deve criar uma conta no aplicativo apenas para fins de teste. Se possível, restrinja as permissões da conta de teste para que não haja possibilidade de que os testes na Web afetem usuários reais.

**Nome de usuário e a senha simples** Registre um teste na Web da maneira habitual. Exclua os cookies primeiro.

**Autenticação SAML**

|Nome da propriedade| Descrição|
|----|-----|
| URI de audiência | O URI de audiência do token SAML.  Esse é o URI para o Serviço de Controle de Acesso (ACS) – incluindo o nome do host e o namespace do ACS. |
| Senha do Certificado | A senha do certificado do cliente que concederá acesso à chave privada inserida. |
| Certificado do Cliente  | O valor do certificado do cliente com a chave privada no formato codificado em Base64. |
| Identificador do Nome | O identificador do nome exclusivo para o token |
| Não Após | O intervalo de tempo durante o qual o token será válido.  O padrão é 5 minutos. |
| Não Antes De | O intervalo de tempo durante o qual um token criado anteriormente será válido (para compensar distorções de tempo).  O padrão é de 5 minutos (negativos). |
| Nome do Parâmetro do Contexto de Destino | O parâmetro do contexto que receberá a declaração gerada. |


**Segredo do cliente** Se o aplicativo tiver uma rota de entrada que envolva um segredo do cliente, use-a. O AAD (Azure Active Directory) é um exemplo de um serviço que fornece uma entrada de segredo do cliente. No AAD, o segredo do cliente é a Chave do Aplicativo.

Aqui está um teste na Web de exemplo de um aplicativo Web que usa uma chave de aplicativo:

![Captura de tela de exemplo](./media/availability-multistep/client-secret.png)

Obtenha o token do AAD usando o segredo do cliente (AppKey).
Extraia o token de portador da resposta.
Chame a API usando o token de portador no cabeçalho de autorização.
Verifique se o teste na Web é um cliente real, ou seja, se ele tem seu próprio aplicativo no AAD, e use seus respectivos ID de cliente e chave do aplicativo. O serviço que está sendo testado também tem seu próprio aplicativo no AAD: o URI appID desse aplicativo é refletido no teste na Web no campo "recurso".

### <a name="open-authentication"></a>Autenticação Aberta
Um exemplo de autenticação aberta é entrar com sua conta da Microsoft ou do Google. Muitos aplicativos que usam OAuth fornecem a alternativa de segredo do cliente. Portanto, sua primeira tática deve ser investigar essa possibilidade.

Se o teste tiver de entrar usando OAuth, a abordagem geral será:

Use uma ferramenta como o Fiddler para examinar o tráfego entre o navegador da web, o site de autenticação e seu aplicativo.
Executar duas ou mais entradas usando computadores ou navegadores diferentes ou em longos intervalos (para permitir que os tokens expirem).
Ao comparar sessões diferentes, identifique o token passado de volta ao site de autenticação, que será então passado para o servidor de aplicativos após a entrada.
Registre um teste na Web usando o Visual Studio.
Parametrize os tokens, definindo o parâmetro quando o token for retornado do autenticador e usando-o na consulta ao site. (O Visual Studio tenta parametrizar o teste, mas não parametriza os tokens corretamente.)

## <a name="troubleshooting"></a>Solução de problemas

[Artigo exclusivo para solução de problemas](troubleshoot-availability.md).

## <a name="next-steps"></a>Próximas etapas

* [Alertas de disponibilidade](availability-alerts.md)
* [Testes na Web de ping de URL](monitor-web-app-availability.md)
