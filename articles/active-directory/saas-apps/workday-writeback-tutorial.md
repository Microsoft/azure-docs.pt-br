---
title: 'Tutorial: Configurar o write-back do Workday no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o write-back de atributo do Azure AD para o Workday
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3a623a487dd31caf8c85b18771d90e3a6306df68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98953997"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Tutorial: Configurar o write-back de atributo do Azure AD para o Workday
O objetivo deste tutorial é mostrar as etapas que você precisará executar para fazer write-back dos atributos do Azure AD para o Workday. O aplicativo de provisionamento de Write-back do Workday dá suporte à atribuição de valores aos seguintes atributos do Workday:
* Email comercial 
* Nome de usuário do Workday
* Número de telefone comercial fixo (incluindo código do país, código de área, número e ramal)
* Sinalizador primário do número de telefone comercial fixo
* Número de celular comercial (incluindo código do país, código de área e número)
* Sinalizador primário de celular comercial

## <a name="overview"></a>Visão geral

Depois de configurar a integração de provisionamento de entrada usando o aplicativo de provisionamento [Workday para o AD local](workday-inbound-tutorial.md) ou [Workday para o Azure AD](workday-inbound-cloud-only-tutorial.md), opcionalmente, você pode configurar o aplicativo de Write-back do Workday para gravar informações de contato, como email de trabalho e número de telefone no Workday. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Essa solução de provisionamento de usuário de Write-back do Workday é ideal para:

* As organizações que usam o Microsoft 365 que desejam fazer write-back de atributos autoritativos gerenciados pela TI (como endereço de email, nome de usuário e número de telefone) no Workday

## <a name="configure-integration-system-user-in-workday"></a>Configurar o usuário do sistema de integração no Workday

Veja a seção [Configurar usuário do sistema de integração](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) para criar uma conta de usuário do sistema de integração do Workday com permissões para recuperar os dados dos trabalhadores. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Configurando o write-back de atributo do Azure AD para o Workday

Siga essas instruções para configurar o write-back de endereços de email do usuário e o nome de usuário do Azure Active Directory para o Workday.

* [Adicionar o aplicativo conector de write-back e criar a conexão com o Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributos de write-back](#part-2-configure-writeback-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar o aplicativo conector de write-back e criar a conexão com o Workday

**Para configurar o conector de Write-back do Workday:**

1. Ir para <https://portal.azure.com>.

2. Na portal do Azure, procure e selecione **Azure Active Directory**.

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e, em seguida, selecione a categoria **Todos**.

5. Pesquise por **Fazer write-back de Workday** e adicione esse aplicativo da galeria.

6. Depois de adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**.

7. Altere o **Modo** de **Provisionamento** para **Automático**.

8. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de Usuário Administrador**  – Digite o nome de usuário da conta do sistema de integração do Workday com o nome de domínio do locatário acrescentado. Ele deve ser semelhante a: *username\@contoso4*

   * **Senha do administrador –** digite a senha da conta do sistema de integração do Workday

   * **URL do locatário –** Digite a URL para o ponto de extremidade de serviços Web do Workday para seu locatário. Esse valor deve ser semelhante a: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources`, onde *contoso4* é substituído pelo seu nome de locatário correto e *wd3-impl* é substituído pela cadeia de caracteres de ambiente correta (se necessário).

   * **Email de notificação –** Digite seu endereço de email e marque a caixa de seleção “enviar email se ocorrer falha”.

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique novamente se o URL do Workday e as credenciais são válidas no Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos de write-back

Nesta seção, você irá configurar como os atributos de write-back fluem do Azure AD para o Workday. 

1. Na guia Provisionamento, em **Mapeamentos**, clique no nome do mapeamento.

2. No campo **Escopo do Objeto de Origem**, você pode, opcionalmente, filtrar, os conjuntos de usuários do Azure Active Directory que devem fazer parte do write-back. O escopo padrão é “todos os usuários no Azure AD”.

3. Na seção **Mapeamentos de atributo**, atualize a ID correspondente para indicar o atributo no Azure Active Directory em que a ID de trabalho do Workday ou a ID de funcionário é armazenada. Um método de correspondência popular é sincronizar o ID de Trabalho do Workday ou o ID de Funcionário para extensionAttribute1-15 no Azure AD e, em seguida, utilizar esse atributo no Azure AD para corresponder os usuários no Workday.

4. Normalmente, você mapeia o atributo do Azure AD *userPrincipalName* para o atributo do Workday *UserID* e mapeia o atributo *mail* do Azure AD para o atributo *EmailAddress* do Workday. 

     >[!div class="mx-imgBorder"]
     >![Azure portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Use as diretrizes compartilhadas abaixo para mapear os valores de atributos de número de telefone do Azure AD para o Workday. 

     | Atributo de telefone do Workday | Valor esperado | Diretrizes de mapeamento |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | true/false | Mapeamento de constante ou de expressão cuja saída é um valor de cadeia de caracteres "true" ou "false". |
     | WorkphoneLandlineCountryCodeName | [Código do país 3166-1 ISO de três letras](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapeamento de constante ou de expressão cuja saída é um código de país de três letras. |
     | WorkphoneLandlineCountryCodeNumber | [Código de chamada internacional do país](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapeamento de constante ou de expressão cuja saída é um código de país válido (sem o sinal +). |
     | WorkphoneLandlineNumber | Número de telefone completo, incluindo o código de área | Mapeie-o para o atributo *telephoneNumber*. Use o regex para remover o espaço em branco, os colchetes e o código do país. Consulte o exemplo a seguir. |
     | WorkphoneLandlineExtension | Número do ramal | Se *telephoneNumber* contiver um ramal, use o regex para extrair o valor. |
     | WorkphoneMobileIsPrimary | true/false | Mapeamento de constante ou de expressão cuja saída é um valor de cadeia de caracteres "true" ou "false" |
     | WorkphoneMobileCountryCodeName | [Código do país 3166-1 ISO de três letras](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapeamento de constante ou de expressão cuja saída é um código de país de três letras. |
     | WorkphoneMobileCountryCodeNumber | [Código de chamada internacional do país](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapeamento de constante ou de expressão cuja saída é um código de país válido (sem o sinal +). |
     | WorkphoneMobileNumber | Número de telefone completo, incluindo o código de área | Mapeie-o para o atributo *mobile*. Use o regex para remover o espaço em branco, os colchetes e o código do país. Consulte o exemplo a seguir. |

     > [!NOTE]
     > Ao invocar o serviço Web Change_Work_Contact do Workday, o Azure AD envia os seguintes valores constantes: <br>
     > * **Communication_Usage_Type_ID** é definida como a cadeia de caracteres constante "WORK" <br>
     > * **Phone_Device_Type_ID** é definida como a cadeia de caracteres constante "Mobile" para números de telefone celular e "Landline" para números de telefone fixo. <br>
     > 
     > Você encontrará falhas de write-back se o seu locatário do Workday usar Type_IDs diferentes. Para evitar essas falhas, use a tarefa **Manter as IDs de Referência** do Workday e atualize as Type_IDs, de modo que correspondam aos valores usados pelo Azure AD. <br>
     >  

     **Expressões regex de referência – Exemplo 1**

     Use a expressão regular abaixo se o número de telefone no Azure AD for definido com o formato exigido para a SSPR (redefinição de senha por autoatendimento). <br>
     Exemplo: se o valor do número de telefone for + 1 1112223333 ->, a expressão regex produzirá 1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Expressões regex de referência – Exemplo 2**

     Use a expressão regular abaixo se o número de telefone no Azure AD for definido com o formato (XXX) XXX-XXXX. <br>
     Exemplo: se o valor do número de telefone for (111) 222-3333 ->, a expressão regex produzirá 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de Atributos.

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do Workday tiverem sido concluídas, você pode ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele iniciará as operações de provisionamento para todos os usuários no escopo. Se houver erros em problemas de dados de mapeamento ou Workday, o trabalho de provisionamento pode falhar e prosseguir para o estado de quarentena. Para evitar isso, como uma prática recomendada, recomendamos configurar o filtro **Escopo do objeto de origem** e testar seus mapeamentos de atributos com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Após ter verificado que os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

1. Na lista suspensa **Escopo**, selecione **Sincronizar todos os usuários e grupos**. Com essa opção, o aplicativo de Write-back fará o write-back dos atributos mapeados de todos os usuários do Azure AD para o Workday, sujeito às regras de escopo definidas em **Mapeamentos** -> **Escopo do Objeto de Origem**. 

   > [!div class="mx-imgBorder"]
   > ![Selecionar escopo de Write-back](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > O aplicativo de provisionamento de Write-back do Workday não dá suporte à opção **Sincronizar apenas usuários e grupos atribuídos**.
 

2. Clique em **Salvar**.

3. Essa operação dará início à sincronização inicial, o que poderá demorar algumas horas dependendo de quantos usuários estiverem no diretório de origem. Verifique a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique a guia **Logs de provisionamento** no portal do Azure para ver as ações executadas pelo serviço de provisionamento. Os logs de auditoria lista todos os eventos de sincronização individuais executados pelo serviço de provisionamento, por exemplo, os usuários importados da origem e exportados para o aplicativo de destino.  

5. Depois que a sincronização inicial for concluída, um relatório de resumo será gravado na guia **Provisionamento**, conforme mostrado abaixo.

     > [!div class="mx-imgBorder"]
     > ![Barra de progresso do provisionamento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

* O aplicativo de Write-back usa um valor predefinido para os parâmetros **Communication_Usage_Type_ID** e **Phone_Device_Type_ID**. Se o seu locatário do Workday estiver usando um valor diferente para esses atributos, a operação de write-back não terá sucesso. Uma solução alternativa sugerida é atualizar as Type_IDs no Workday. 
* Quando o aplicativo de Write-back é configurado para atualizar os números de telefone secundários, ele não substitui o número de telefone secundário existente no Workday. Ele adiciona mais um número de telefone secundário ao registro de trabalho. Não há nenhuma solução alternativa para esse comportamento. 


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os cenários de integração do Azure AD e do Workday e as chamadas de serviço Web](../app-provisioning/workday-integration-reference.md)
* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o Workday e o Azure Active Directory](workday-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como exportar e importar as configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

