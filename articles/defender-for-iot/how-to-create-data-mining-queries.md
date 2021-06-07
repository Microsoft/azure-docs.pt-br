---
title: Criar relatórios de mineração de dados
description: gere informações abrangentes e granulares sobre seus dispositivos de rede em várias camadas, como protocolos, versões de firmware ou comandos de programação.
ms.date: 01/20/2021
ms.topic: how-to
ms.openlocfilehash: 99754959e7a3a08b4d763b85b0b9315476969774
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779062"
---
# <a name="sensor-data-mining-queries"></a>Consultas de Data Mining de sensor

## <a name="about-sensor-data-mining-queries"></a>Sobre consultas de Data Mining de sensor

As ferramentas de mineração de dados permitem que você gere informações abrangentes e granulares sobre seus dispositivos de rede em várias camadas. Por exemplo, você pode criar uma consulta com base em:

- Períodos de tempo

- Conexões com a Internet

- Portas

- Protocolos

- Versões de firmware

- Comandos de programação

- Inatividade do dispositivo

Você pode ajustar o relatório com base em filtros. Por exemplo, você pode consultar uma sub-rede específica na qual o firmware foi atualizado.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Lista de dispositivos ativos.":::

Várias ferramentas estão disponíveis para gerenciar consultas. Por exemplo, você pode exportar e salvar.

> [!NOTE]
> Os administradores e analistas de segurança têm acesso às opções de mineração de dados.

### <a name="dynamic-updates"></a>Atualizações dinâmicas

As consultas de mineração de dados criadas são atualizadas dinamicamente toda vez que são abertas. Por exemplo:

- Se você criar um relatório para versões de firmware em dispositivos em 1º de junho e abrir o relatório novamente em 10 de junho, esse relatório será atualizado com informações precisas para 10 de junho.

- Se você criar um relatório para detectar novos dispositivos descobertos nos últimos 30 dias em 1º de junho e abrir o relatório em 30 de junho, os resultados serão exibidos nos últimos 30 dias.

### <a name="data-mining-use-cases"></a>Casos de uso de mineração de dados

Você pode usar consultas para lidar com uma ampla gama de necessidades de segurança para várias equipes de segurança:

- **Resposta a incidentes SOC**: gere um relatório em tempo real para ajudar a lidar com resposta imediata a incidentes. Por exemplo, a mineração de dados pode gerar um relatório para uma lista de dispositivos que podem exigir aplicação de patch.

- **Análise forense**: gere um relatório com base em dados históricos para relatórios investigativos.

- **Integridade da rede de ti**: gere um relatório que ajude a melhorar a segurança geral da rede. Por exemplo, gerar um relatório pode ser gerado para listar dispositivos com credenciais de autenticação fracas.

- **Visibilidade**: gere um relatório que cubra todos os itens de consulta para exibir todos os parâmetros de linha de base da sua rede.

## <a name="data-mining-storage"></a>Armazenamento de mineração de dados

As informações de mineração de dados são salvas e armazenadas continuamente, exceto quando um dispositivo é excluído. Os resultados de mineração de dados podem ser exportados e armazenados externamente em um servidor seguro. Além disso, o sensor executa backups diários automáticos para garantir a continuidade do sistema e a preservação de dados.


## <a name="predefined-data-mining-queries"></a>Consultas de Data Mining predefinidas

As consultas predefinidas a seguir estão disponíveis. Essas consultas são geradas em tempo real.

- **CVEs**: uma lista de dispositivos detectados com vulnerabilidades conhecidas nas últimas 24 horas.

- **CVEs excluído**: uma lista de todos os CVEs que foram excluídos manualmente. Para obter resultados mais precisos em relatórios e vetores de ataque do VA, você pode personalizar a lista de CVE manualmente, incluindo e excluindo CVEs.

- **Atividade de Internet**: dispositivos que estão conectados à Internet.

- **Dispositivos não ativos**: dispositivos que não se comunicaram nos últimos sete dias.

- **Dispositivos ativos**: dispositivos de rede ativos nas últimas 24 horas.

- **Acesso remoto**: dispositivos que se comunicam por meio de protocolos de sessão remota.

- **Comandos de programação**: dispositivos que enviam programação industrial.

Esses relatórios são acessíveis automaticamente na tela de **relatórios** , onde os usuários e outros usuários podem exibi-los. Os usuários de RO não podem acessar os relatórios de mineração de dados.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="A tela de Data Mining.":::

## <a name="create-a-data-mining-query"></a>Criar uma consulta Data Mining

Para criar um relatório de mineração de dados:

1. Selecione **mineração de dados** no menu lateral. Relatórios sugeridos predefinidos são exibidos automaticamente.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Selecione Data Mining no painel lateral.":::

2. Selecione :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::.

3. Selecione **novo relatório** e defina o relatório.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Crie um novo relatório preenchendo esta tela.":::

   Os seguintes parâmetros estão disponíveis:

   - Forneça um nome de relatório e uma descrição.

   - Para categorias, selecione:

      - **Categorias (tudo)** para exibir todos os resultados do relatório sobre todos os dispositivos em sua rede.

      - **Genérico** para escolher categorias padrão.

   - Selecione os parâmetros de relatório específicos de seu interesse.

   - Escolha uma ordem de classificação (**ordenar por**). Classificar resultados com base na atividade ou categoria.

   - Selecione **salvar em páginas do relatório** para salvar os resultados do relatório como um relatório que é acessível na página do **relatório** . Isso permitirá que os usuários do RO executem o relatório que você criou.

   - Selecione **filtros (Adicionar)** para adicionar mais filtros. Há suporte para solicitações curinga.

   - Especifique um grupo de dispositivos (definido no mapa do dispositivo).

   - Especifique um endereço IP.

   - Especifique uma porta.

   - Especifique um endereço MAC.

4. Selecione **Salvar**. Os resultados do relatório são abertos na página de **mineração de dados** .

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Relatar resultados como visto na página de mineração de dados.":::

### <a name="manage-data-mining-reports"></a>Gerenciar relatórios de mineração de dados

A tabela a seguir descreve as opções de gerenciamento para Data Mining:

| Imagem do ícone | Descrição |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Edite os parâmetros do relatório. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | Exportar como PDF. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |Exportar como CSV. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | Mostrar informações adicionais, como a data da última modificação. Use esse recurso para criar um instantâneo de resultado da consulta. Talvez seja necessário fazer isso para uma investigação mais detalhada com os líderes da equipe ou os analistas do SOC, por exemplo. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | Exiba na página **relatórios** ou oculte na página **relatórios** . :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="Oculte ou revele seus relatórios."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Exclua o relatório. |

#### <a name="create-customized-directories"></a>Criar diretórios personalizados 

Você pode organizar as informações extensivas para consultas de mineração de dados Criando diretórios para categorias. Por exemplo, você pode criar diretórios para protocolos ou locais.

Para criar um novo diretório:

1. Selecione :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: para adicionar um novo diretório.

2. Selecione **novo diretório** para exibir o novo formulário de diretório.

3. Nomeie o novo diretório.

4. Arraste os relatórios necessários para o novo diretório. A qualquer momento, você pode arrastar o relatório de volta para o modo de exibição principal.

5. Clique com o botão direito do mouse no novo diretório para abri-lo, editá-lo ou excluí-lo.

#### <a name="create-snapshots-of-report-results"></a>Criar instantâneos dos resultados do relatório

Talvez seja necessário salvar determinados resultados da consulta para uma investigação mais detalhada. Por exemplo, talvez seja necessário compartilhar resultados com várias equipes de segurança.

Os instantâneos são salvos nos resultados do relatório e não geram consultas dinâmicas.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Instantâneos.":::

Para criar um instantâneo:

1. Abra o relatório necessário.

2. Selecione o ícone de informações :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: .

3. Selecione **obter novo**.

4. Insira um nome para o instantâneo e selecione **salvar**.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Faça um instantâneo.":::

#### <a name="customize-the-cve-list"></a>Personalizar a lista de CVEs

Você pode personalizar manualmente a lista de CVE da seguinte maneira:

  - Incluir/excluir CVEs

  - Alterar a pontuação de CVE

Para executar alterações manuais no relatório de CVE:

1.  No menu lateral, selecione **mineração de dados**.

2. Selecione :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: no canto superior esquerdo da janela de **mineração de dados** . Em seguida, selecione **novo relatório**.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Crie um novo relatório.":::

3. No painel esquerdo, selecione uma das seguintes opções:

   - **Vulnerabilidades conhecidas**: seleciona ambas as opções e apresenta resultados nas duas tabelas do relatório, uma com CVEs e outra com CVEs excluído.

   - **CVEs**: Selecione esta opção para apresentar uma lista de todos os CVEs.

   - **CVEs excluído**: Selecione esta opção para apresentar uma lista de todos os CVEs excluídos.

4. Preencha as informações de **nome** e **Descrição** e selecione **salvar**. O novo relatório é exibido na janela de **mineração de dados** .

5. Para excluir CVEs, abra o relatório de mineração de dados para CVEs. A lista de todos os CVEs é exibida.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="Relatório C V E.":::

6. Para habilitar a seleção de itens na lista, selecione :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: e selecione o CVEs que você deseja personalizar. A barra de **operações** aparece na parte inferior.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Captura de tela da barra de operações de mineração de dados.":::

7. Selecione o CVEs que você deseja excluir e, em seguida, selecione **excluir registros**. O CVEs que você selecionou não aparece na lista de CVEs e aparecerá na lista de CVEs excluídos quando você gerar um.

8. Para incluir o CVEs excluído na lista de CVEs, gere o relatório para CVEs excluídos e exclua dessa lista os itens que você deseja incluir de volta na lista de CVEs.

9. Selecione o CVEs no qual você deseja alterar a pontuação e, em seguida, selecione **Atualizar Pontuação de CVE**.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="Atualize a pontuação de CVE.":::

10. Insira a nova pontuação e selecione **OK**. A pontuação atualizada é exibida no CVEs que você selecionou.



## <a name="sensor-reports-based-on-data-mining"></a>Relatórios de sensor com base em Data Mining

Relatórios regulares, acessados a partir da opção de **relatórios** , são predefinidos Data Mining relatórios. Elas não são consultas dinâmicas, como estão disponíveis em Data Mining, mas uma representação estática dos resultados da consulta Data Mining.

Os resultados da consulta de mineração de dados não estão disponíveis para usuários somente leitura. Os administradores e analistas de segurança que desejam que usuários somente leitura tenham acesso às informações geradas por Data Mining consultas devem salvar as informações como relatório.

Os relatórios refletem informações geradas por Data Mining resultados da consulta. Isso inclui relatórios de Data Mining padrão, que estão disponíveis no modo de exibição relatórios. Os analistas de administrador e segurança também podem gerar consultas de Data Mining personalizadas e salvá-las como relatórios. Esses relatórios também estão disponíveis para usuários do RO.

Para gerar um relatório:

1. Selecione **relatórios** no menu lateral.

2. Escolha o relatório necessário a ser exibido. A escolha pode ser relatórios **personalizados** ou **gerados automaticamente** , como comandos de **programação** e **acesso remoto**.

3. Você pode exportar o relatório selecionando um dos ícones no canto superior direito da tela:

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: Exportar para um arquivo PDF.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Exportar para um arquivo CSV.

> [!NOTE] 
> O usuário RO pode ver apenas os relatórios criados para eles.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Selecione o relatório a ser gerado.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="Relatório de acesso remoto gerado.":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>Relatórios do console de gerenciamento local com base em relatórios de Data Mining

O console de gerenciamento local permite gerar relatórios para cada sensor conectado a ele. Os relatórios são baseados em consultas de mineração de dados de sensor que são executadas.

Você pode gerar os seguintes relatórios:

- **Dispositivos ativos (últimas 24 horas)**: apresenta uma lista de dispositivos que mostram a atividade de rede em um período de 24 horas.

- **Dispositivos não ativos (últimos 7 dias)**: apresenta uma lista de dispositivos que não mostram nenhuma atividade de rede nos últimos sete dias.

- **Comandos de programação**: apresenta uma lista de dispositivos que enviaram comandos de programação nas últimas 24 horas.

- **Acesso remoto**: apresenta uma lista de dispositivos que as fontes remotas acessadas nas últimas 24 horas.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Captura de tela da exibição de relatórios.":::

Quando você escolhe o sensor no console de gerenciamento local, todos os relatórios personalizados configurados nesse sensor aparecem na lista de relatórios. Para cada sensor, você pode gerar um relatório padrão ou um relatório personalizado configurado nesse sensor.

Para gerar um relatório:

1. No painel esquerdo, selecione **relatórios**. A janela **relatórios** é exibida.

2. Na lista suspensa **sensores** , selecione o sensor para o qual você deseja gerar o relatório.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Captura de tela da exibição de sensores.":::

3. Na lista suspensa à direita, selecione o relatório que deseja gerar.

4. Para criar um PDF dos resultados do relatório, selecione :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: .
