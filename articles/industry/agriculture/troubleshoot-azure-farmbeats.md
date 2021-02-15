---
title: Solução de problemas do Azure FarmBeats
description: Este artigo descreve como solucionar problemas do Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: dd0ed78c56e4d656a2ecee6395d831ed093e85b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001602"
---
# <a name="troubleshoot-azure-farmbeats"></a>Solução de problemas do Azure FarmBeats

Este artigo fornece soluções para problemas comuns do FarmBeats do Azure. Para obter ajuda adicional, entre em contato com nosso [p&um fórum de suporte](/answers/topics/azure-farmbeats.html) ou envie um email para farmbeatssupport@microsoft.com .

> [!NOTE]
  > Se você tiver instalado o FarmBeats em abril e seus trabalhos estiverem falhando com uma mensagem de erro vazia, talvez sua instalação não tenha sido alocada a nenhuma cota de lote para priorizar o suporte para as organizações de saúde e segurança críticas. Consulte [aqui](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) para obter mais informações. Você precisará solicitar que as VMs sejam alocadas para a conta do lote para executar trabalhos com êxito.

## <a name="install-issues"></a>Problemas de instalação

  > [!NOTE]
  > Se você estiver reiniciando a instalação devido a um erro, exclua o **Grupo de Recursos** ou exclua todos os recursos do Grupo de Recursos antes de disparar novamente a instalação.

### <a name="invalid-sentinel-credentials"></a>Credenciais de Sentinel inválidas

As credenciais de Sentinel fornecidas durante a instalação estão incorretas. Reinicie a instalação com as credenciais corretas.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Foi atingida a cota da conta regional das contas do lote para a assinatura especificada

Aumente a cota ou exclua as contas do lote não usadas e reinicie a instalação.

### <a name="invalid-resource-group-location"></a>Localização inválida do grupo de recursos

Verifique se o **Grupo de Recursos** está no mesmo local que a **Região** especificada durante a instalação.

### <a name="other-install-issues"></a>Outros problemas de instalação

Entre em contato com os seguintes detalhes:

- A ID de sua assinatura
- Nome do Grupo de Recursos
- Siga as etapas abaixo para anexar o arquivo de log para a falha de implantação:

    1. Navegue até o **Grupo de Recursos** no portal do Azure.

    2. Selecione **Implantações** na seção **Configurações** no lado esquerdo.

    3. Para cada implantação que mostra **Falha**, selecione até os detalhes e baixe os detalhes da implantação. Anexe este arquivo ao email.

## <a name="sensor-telemetry"></a>Sensor de telemetria

### <a name="cant-view-telemetry-data"></a>Não foi possível exibir dados de telemetria

**Sintoma**: Dispositivos ou sensores são implantados e você vinculou o FarmBeats ao seu parceiro de dispositivo, mas não pode obter ou exibir dados de telemetria no FarmBeats.

**Ação corretiva**

1. Vá para o grupo de recursos do FarmBeats.
2. Selecione o namespace do **Hub de eventos** ("sensor-Partner-eh-namespace-xxxx"), clique em "hubs de eventos" e verifique o número de mensagens de entrada no Hub de eventos atribuído ao parceiro
3. Execute um destes procedimentos:

   - Se não houver *mensagem de entrada*, entre em contato com o parceiro do dispositivo.  
   - Se houver *mensagens de entrada*, entre em contato conosco com os logs do Datahub e do Acelerador e a telemetria capturada.

Para entender como baixar logs, vá até a seção ["Coletar logs manualmente"](#collect-logs-manually).  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível exibir dados de telemetria depois de ingerir dados históricos/de streaming de seus sensores

**Sintoma**: Os dispositivos ou sensores são implantados e você criou os dispositivos/sensores em FarmBeats e a telemetria ingerida para o EventHub, mas não consegue obter ou exibir dados de telemetria no FarmBeats.

**Ação corretiva**

1. Verifique se você fez o registro do parceiro corretamente. Vá até o swagger do datahub, navegue até a API do /Partner, faça uma operação Get e verifique se o parceiro está registrado. Do contrário, siga essas [etapas](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar o parceiro.

2. Verifique se você usou o formato de mensagem de telemetria correto:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Não tem a cadeia de conexão dos Hubs de Eventos do Azure

**Ação corretiva**

1. No Datahub Swagger, vá até a API do parceiro.
2. Selecione **Get** > **Try it out** > **Execute**.

> [!NOTE]
> A ID de parceiro do sensor no qual você está interessado.

3. Volte para a API de parceiro e selecione **obter/ \<ID>**.
4. Especifique a ID de parceiro da etapa 3 e, em seguida, selecione **Executar**.

   A resposta da API deve ter a cadeia de conexão dos Hubs de Eventos.

### <a name="device-appears-offline"></a>O dispositivo aparece offline

**Sintomas**: Os dispositivos estão instalados e você vinculou o FarmBeats ao seu parceiro de dispositivo. Os dispositivos estão online e enviando dados de telemetria, mas aparecem offline.

**Ação corretiva** O intervalo de relatório não está configurado para este dispositivo. Para definir o intervalo de relatórios, contate o fabricante do dispositivo. 

### <a name="error-deleting-a-device"></a>Erro ao excluir um dispositivo

Enquanto você estiver excluindo um dispositivo, poderá encontrar um dos seguintes cenários de erro comuns:  

**Mensagem**: "O dispositivo é referenciado em sensores: Há um ou mais sensores associados ao dispositivo. Exclua os sensores e, em seguida, exclua o dispositivo."  

**Significado**: O dispositivo está associado a vários sensores que são implantados no farm.

**Ação corretiva**  

1. Exclua os sensores associados ao dispositivo por meio do Acelerador.  
2. Se você quiser associar os sensores a um dispositivo diferente, peça ao seu parceiro de dispositivo para fazer o mesmo.  
3. Exclua o dispositivo usando uma chamada `DELETE API` e defina o parâmetro Force como *true*.  

**Mensagem**: "O dispositivo é referenciado em dispositivos como ParentDeviceId: Há um ou mais dispositivos associados a este dispositivo como dispositivos filho. Exclua-os e exclua este dispositivo."  

**Significado**: Seu dispositivo tem outros dispositivos associados a ele.  

**Ação corretiva**

1. Exclua os dispositivos associados a este dispositivo específico.  
2. Exclua o dispositivo específico.  

    > [!NOTE]
    > Você não poderá excluir um dispositivo se os sensores estiverem associados a ele. Para saber mais sobre como excluir sensores associados, confira a seção **Excluir sensor** em [Obter dados de sensor de parceiros de sensor](get-sensor-data-from-sensor-partner.md).
    > Os parceiros não têm permissão para excluir um dispositivo ou sensor. Somente administradores têm permissão para excluir.

## <a name="issues-with-jobs"></a>Problemas com trabalhos

### <a name="farmbeats-internal-error"></a>Erro interno do FarmBeats

**Mensagem**: "Erro interno do FarmBeats, confira o guia de solução de problemas para obter mais detalhes".

**Ação corretiva** Esse problema pode resultar de uma falha temporária no pipeline de dados. Criar o trabalho novamente. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

## <a name="accelerator-troubleshooting"></a>Solução de problemas do Acelerador

### <a name="access-control"></a>Controle de acesso

**Problema**: Você receberá um erro enquanto estiver adicionando uma atribuição de função.

**Mensagem**: “Nenhum usuário correspondente localizado”.

**Ação corretiva** Verifique a ID de email para a qual você está tentando adicionar uma atribuição de função. A ID de email deve ser uma correspondência exata da ID, que é registrada para esse usuário no Active Directory. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

### <a name="unable-to-log-in-to-accelerator"></a>Não é possível fazer logon no Acelerador

**Mensagem**: "Erro: Você não está autorizado a chamar o serviço. Contate o administrador para autorização".

**Ação corretiva** Peça ao administrador para autorizá-lo a acessar a implantação do FarmBeats. Isso pode ser feito fazendo um POST das APIs do RoleAssignment ou por meio do controle de acesso no painel **Configurações** no Acelerador.  

Se você já tiver recebido o acesso e continua encontrando esse erro, tente novamente atualizando a página. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

![Captura de tela que mostra o erro de autorização.](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemas do Acelerador  

**Problema**: Você recebeu um erro de Acelerador de causa indeterminada.

**Mensagem**: "Erro: Ocorreu um erro desconhecido”.

**Ação corretiva** Esse erro ocorrerá se você deixar a página ociosa por muito tempo. Atualize a página. Se o erro persistir, entre em contato conosco com a mensagem/logs de erro.

**Problema**: O Acelerador do FarmBeats não está mostrando a versão mais recente, mesmo depois de ter atualizado o FarmBeatsDeployment.

**Ação corretiva** Este erro ocorre devido à persistência de trabalho do serviço no navegador. Faça o seguinte:

1. Feche todas as guias do navegador que têm o Acelerador aberto e feche a janela do navegador.
2. Inicie uma nova instância do navegador e recarregue o URI do Acelerador. Essa ação carrega a nova versão do Acelerador.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problemas relacionados a imagens

### <a name="wrong-username-or-password"></a>Senha ou nome de usuário incorreto

**Mensagem de falha de trabalho**: "É necessário ter a autenticação completa para acessar este recurso."

**Ação corretiva**: Realize um dos seguintes procedimentos:

- Atualize o FarmBeats com o nome de usuário/senha corretos usando as etapas abaixo e repita o trabalho.

  **Atualizar nome de usuário do Sentinel**

    1. Entre no [Portal do Azure](https://portal.azure.com).
    2. Na caixa **Pesquisar**, procure o grupo de recursos do Datahub do FarmBeats.
    3. Selecione Conta de armazenamento***** > **Contêineres** > **batch-prep-files** > **to_vm** > **config.ini**
    4. Selecionar **Editar**
    5. Atualizar o nome de usuário na seção sentinel_account

  **Atualizar senha do Sentinel**

    1. Entre no [Portal do Azure](https://portal.azure.com).
    2. Na caixa **Pesquisar**, procure o grupo de recursos do Datahub do FarmBeats.
    3. Selecionar keyvault-*****
    4. Selecione as Políticas de Acesso em Configurações
    5. Selecione **Adicionar política de acesso**
    6. Use **Gerenciamento de segredo** para configurar a partir do modelo e adicionar a si mesmo ao principal
    7. Selecione **Adicionar** e, em seguida, selecione **Salvar** na página **Políticas de Acesso**
    8. Selecione **Segredos**, em **Configurações**
    9. Selecione **Sentinel-password**
    10. Crie uma nova versão do valor e habilite-a.

- Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e, em seguida, verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Hub do Sentinel: URL ou site incorreto não acessível

**Mensagem de falha de trabalho**: “Opa! Algo deu errado. A página que você estava tentando acessar está temporariamente indisponível".

**Ação corretiva**:

1. Abra [Sentinel](https://scihub.copernicus.eu/dhus/) em seu navegador para ver se o site está acessível.
2. Se o site não estiver acessível, verifique se algum firewall, rede da empresa ou outro software de bloqueio está impedindo o acesso ao site e execute as etapas necessárias para permitir a URL do Sentinel. 
3. Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e verifique se o trabalho foi bem-sucedido.  

### <a name="sentinel-server-down-for-maintenance"></a>Servidor do Sentinel: Inoperante para manutenção

**Mensagem de falha de trabalho**: "O Copernicus Open Access Hub estará de volta em breve! Lamentamos o inconveniente, estamos realizando manutenção no momento. Voltaremos a ficar online em breve"! 

**Ação corretiva**:

Esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor do Sentinel.

1. Se algum trabalho ou pipeline falhar porque a manutenção está sendo executada, envie o trabalho novamente após algum tempo. 

   Para saber mais sobre as atividades de manutenção de sentinela planejadas ou não planejadas, acesse o site [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/).  

2. Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: Atingido o número máximo de conexões

**Mensagem de falha de trabalho**: "número máximo de dois fluxos simultâneos obtidos pelo usuário ' \<username> '."

**Significado**: Se um trabalho falhar porque o número máximo de conexões foi atingido, isso significará que a mesma conta do Sentinel está sendo usada em vários trabalhos.

**Ação corretiva**: Tente um destes procedimentos:

* Aguarde até que os outros trabalhos sejam concluídos antes de executar novamente o trabalho com falha.
* Crie uma nova conta do Sentinel e, em seguida, atualize o nome de usuário e a senha do Sentinel no FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Servidor do Sentinel: Conexão recusada

**Mensagem de falha de trabalho**: "O servidor recusou a conexão em: http://172.30.175.69:8983/solr/dhus."

**Ação corretiva**: Esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor do Sentinel.

1. Se algum trabalho ou pipeline falhar porque a manutenção está sendo executada, envie o trabalho novamente após algum tempo.

   Para saber mais sobre as atividades de manutenção de sentinela planejadas ou não planejadas, acesse o site [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/).  

2. Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e verifique se o trabalho foi bem-sucedido.

### <a name="soil-moisture-map-has-white-areas"></a>O mapa do Soil Moisture tem áreas brancas

**Problema**: O **mapa do Soil Moisture** foi gerado, mas o mapa tem principalmente áreas brancas.

**Ação corretiva**: Esse problema pode ocorrer se os índices satélite gerados para o momento para o qual o mapa foi solicitado tiverem valores NDVI menores que 0,3. Para saber mais, visite o [Guia técnico do Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Execute novamente o trabalho para um intervalo de datas diferente e verifique se os valores de NDVI nos índices satélite são maiores que 0,3.

## <a name="collect-logs-manually"></a>Coletar logs manualmente

[Instale e implante o Gerenciador de Armazenamento do Azure]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Coletar Azure Data Factory logs de trabalho ou logs do serviço de aplicativo no Datahub

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na caixa **Pesquisar**, procure o grupo de recursos do Datahub do FarmBeats.
3. No painel **Grupo de Recursos**, procure a conta de armazenamento do *datahublogs\** . Por exemplo, *datahublogsmvxmq*.  
4. Na coluna **Nome**, selecione a conta de armazenamento para exibir o painel **Conta de Armazenamento**.
5. No **painel datahubblogs \* *_, selecione _* abrir no Explorer** para exibir o aplicativo **aberto Gerenciador de armazenamento do Azure** .
6. No painel esquerdo, selecione **Contêineres de Blob** e, em seguida, selecione **logs de trabalhos** para logs do Azure Data Factory ou **appinsights-logs** para logs do Serviço de Aplicativo.
7. Selecione **Download** e baixe os logs em uma pasta local no computador.

    ![Captura de tela que mostra os arquivos de log baixados.](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Coletar Azure Data Factory logs de trabalho ou logs do serviço de aplicativo para Acelerador

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na caixa **Pesquisar**, procure o grupo de recursos do Acelerador do FarmBeats.
3. No painel **Grupo de recursos**, procure a conta de *armazenamento\** . Por exemplo, *storagedop4k\** .
4. Selecione a conta de armazenamento na coluna **Nome** para exibir o painel **Conta de armazenamento**.
5. No **painel armazenamento \* *_, selecione _* abrir no Explorer** para abrir o aplicativo Gerenciador de armazenamento do Azure.
6. No painel esquerdo, selecione **Contêineres de Blob** e, em seguida, selecione **logs de trabalhos** para logs do Azure Data Factory ou **appinsights-logs** para logs do Serviço de Aplicativo.
7. Selecione **Download** e baixe os logs em uma pasta local no computador.

## <a name="high-cpu-usage"></a>Alto uso da CPU

**Erro**: Você recebe um alerta de email que se refere a um **Alerta de alto uso da CPU**.

**Ação corretiva**:

1. Vá até o grupo de recursos do Datahub do FarmBeats.
2. Selecione o **Serviço de aplicativo**.  
3. Vá até a [página de preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/) e, em seguida, selecione um tipo de preço apropriado.

## <a name="weather-data-job-failures"></a>Falhas de trabalho de dados meteorológicos

**Erro**: você executa trabalhos para obter dados meteorológicos, mas o trabalho falha

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>Coletar logs para solucionar problemas de falhas do trabalho de dados meteorológicos

1. Vá para o grupo de recursos FarmBeats na portal do Azure.
2. Clique no serviço de Data Factory que faz parte do grupo de recursos. O serviço terá uma marca "SKU: Datahub"

> [!NOTE]
> Para exibir as marcas dos serviços no grupo de recursos, clique em "Editar colunas" e adicione "marcas" à exibição do grupo de recursos

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="Captura de tela que realça a marca SKU: Datahub.":::

3. Na página Visão geral do data Factory, clique em **autor e monitor**. Uma nova guia é aberta no navegador. Clique em **Monitor**

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="Captura de tela que realça a opção de menu monitor.":::

4. Você verá uma lista de execuções de pipeline que fazem parte da execução do trabalho meteorológico. Clique no trabalho para o qual você deseja coletar logs
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="Captura de tela que realça a opção de menu execuções de pipeline e o trabalho selecionado.":::

5. Na página Visão geral do pipeline, você verá a lista de execuções de atividade. Anote as IDs de execução das atividades para as quais você deseja coletar os logs
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="Captura de tela que mostra a lista de execuções de atividade.":::

6. Volte para o grupo de recursos FarmBeats em portal do Azure e clique na conta de armazenamento com o nome **datahublogs-xxxx**
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="Captura de tela que realça a conta de armazenamento com o nome datahublogs-XXXX.":::

7. Clique em **contêineres**  ->  **adfjobs**. Na caixa de pesquisa, insira a ID de execução do trabalho que você anotou na etapa 5 acima.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="Projeto FarmBeats":::

8. O resultado da pesquisa conterá a pasta que tem os logs pertencentes ao trabalho. Baixe os logs e envie-os para farmbeatssupport@microsoft.com para obter assistência na depuração do problema.