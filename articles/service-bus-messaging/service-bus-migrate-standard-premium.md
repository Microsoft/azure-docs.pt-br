---
title: Migrar namespaces do barramento de serviço do Azure-Standard para Premium
description: Guia para permitir a migração de namespaces padrão do barramento de serviço do Azure para Premium
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ed09a077f086390c658e6650171c552b361008d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340752"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrar namespaces padrão do barramento de serviço do Azure para a camada Premium

Anteriormente, o barramento de serviço do Azure ofereceu namespaces apenas na camada Standard. Os namespaces são configurações de vários locatários que são otimizadas para baixo rendimento e ambientes de desenvolvimento. A camada Premium oferece recursos dedicados por namespace para latência previsível e maior taxa de transferência a um preço fixo. A camada Premium é otimizada para ambientes de produção e alta taxa de transferência que exigem recursos empresariais adicionais.

Este artigo descreve como migrar namespaces de camada Standard existentes para a camada Premium.  

>[!WARNING]
> A migração destina-se aos namespaces padrão do barramento de serviço a serem atualizados para a camada Premium. A ferramenta de migração não oferece suporte a downgrade.

Alguns dos pontos a serem observados:

- Essa migração deve ocorrer em vigor, o que significa que os aplicativos de remetente e receptor existentes **não exigem nenhuma alteração no código ou na configuração**. A cadeia de conexão existente apontará automaticamente para o novo namespace Premium.
- O namespace **Premium** não deve ter **nenhuma entidade** para que a migração tenha sucesso.
- Todas as **entidades** no namespace padrão são **copiadas** para o namespace Premium durante o processo de migração.
- A migração dá suporte a **1.000 entidades por unidade de mensagens** na camada Premium. Para identificar quantas unidades de mensagens você precisa, comece com o número de entidades que você tem em seu namespace padrão atual.
- Não é possível migrar diretamente da **camada básica** para a **camada Premium**, mas você pode fazê-lo indiretamente migrando do básico para o Standard primeiro e, em seguida, do Standard para o Premium na próxima etapa.

## <a name="migration-steps"></a>Etapas da migração

Algumas condições estão associadas ao processo de migração. Familiarize-se com as etapas a seguir para reduzir a possibilidade de erros. Essas etapas descrevem o processo de migração e os detalhes passo a passo são listados nas seções a seguir.

1. Crie um novo namespace Premium.
1. Emparelhe os namespaces padrão e Premium entre si.
1. Sincronize (Copie) as entidades do padrão para o namespace Premium.
1. Confirme a migração.
1. Esvaziar entidades no namespace padrão usando o nome de pós-implantação do namespace.
1. Exclua o namespace padrão.

>[!IMPORTANT]
> Depois que a migração tiver sido confirmada, acesse o namespace padrão antigo e dissipe as filas e assinaturas. Depois que as mensagens forem descarregadas, elas poderão ser enviadas para o novo namespace Premium para serem processadas pelos aplicativos receptores. Depois que as filas e as assinaturas tiverem sido descarregadas, recomendamos que você exclua o namespace padrão antigo.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrar usando o CLI do Azure ou o PowerShell

Para migrar seu namespace standard do barramento de serviço para Premium usando a ferramenta CLI do Azure ou PowerShell, siga estas etapas.

1. Crie um novo namespace do barramento de serviço Premium. Você pode fazer referência aos [modelos de Azure Resource Manager](service-bus-resource-manager-namespace.md) ou [usar o portal do Azure](service-bus-create-namespace-portal.md). Certifique-se de selecionar **Premium** para o parâmetro **serviceBusSku** .

1. Defina as seguintes variáveis de ambiente para simplificar os comandos de migração.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > O alias/nome (post_migration_dns_name) pós-migração será usado para acessar o namespace padrão antigo após a migração. Use isso para drenar as filas e as assinaturas e, em seguida, excluir o namespace.

1. Emparelhe os namespaces padrão e Premium e inicie a sincronização usando o seguinte comando:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Verifique o status da migração usando o seguinte comando:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    A migração é considerada completa quando você vê os seguintes valores:

    * Migrationstate = "ativo"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "êxito"

    Esse comando também exibe a configuração de migração. Verifique se os valores estão definidos corretamente. Verifique também o namespace Premium no portal para garantir que todas as filas e tópicos tenham sido criados e que correspondam ao que existia no namespace padrão.

1. Confirme a migração executando o seguinte comando completo:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrar usando o portal do Azure

A migração usando o portal do Azure tem o mesmo fluxo lógico que a migração usando os comandos. Siga estas etapas para migrar usando o portal do Azure.

1. No menu de **navegação** no painel esquerdo, selecione **migrar para Premium**. Clique no **botão introdução** para continuar na próxima página.
    ![Página de aterrissagem de migração][]

1. Conclua **a instalação**.
   ![Namespace de instalação][]
   1. Crie e atribua o namespace Premium para migrar o namespace padrão existente para o.
        ![Configurar namespace-criar namespace Premium][]
   1. Escolha um **nome de migração após**. Você usará esse nome para acessar o namespace padrão depois que a migração for concluída.
        ![Namespace da instalação-escolha o nome da migração após o][]
   1. Selecione **' Avançar '** para continuar.
1. Sincronizar entidades entre os namespaces padrão e Premium.
    ![Configurar o namespace-entidades de sincronização-iniciar][]

   1. Selecione **Iniciar sincronização** para começar a sincronizar as entidades.
   1. Selecione **Sim** na caixa de diálogo para confirmar e iniciar a sincronização.
   1. Aguarde até que a sincronização seja concluída. O status está disponível na barra de status.
        ![Configurar o namespace-entidades de sincronização-progresso][]
        >[!IMPORTANT]
        > Se você precisar anular a migração por qualquer motivo, examine o fluxo de anulação na seção de perguntas frequentes deste documento.
   1. Depois que a sincronização for concluída, selecione **Avançar** na parte inferior da página.

1. Examine as alterações na página Resumo. Selecione **concluir migração** para alternar os namespaces e concluir a migração.
    ![Alternar namespace – menu alternar][]  
    A página confirmação é exibida quando a migração é concluída.
    ![Alternar namespace-êxito][]

## <a name="caveats"></a>Advertências

Alguns dos recursos fornecidos pela camada standard do barramento de serviço do Azure não são suportados pela camada Premium do barramento de serviço do Azure. Eles são por design, uma vez que a camada Premium oferece recursos dedicados para taxa de transferência e latência previsíveis.

Aqui está uma lista de recursos que não têm suporte do Premium e de sua mitigação-

### <a name="express-entities"></a>Entidades expressas

   As entidades expressas que não confirmam nenhum dado de mensagem para o armazenamento não têm suporte no Premium. Os recursos dedicados forneciam uma melhoria significativa na taxa de transferência, garantindo que os dados sejam persistidos, como é esperado de qualquer sistema de mensagens empresariais.

   Durante a migração, qualquer uma de suas entidades expressas em seu namespace padrão será criada no namespace Premium como uma entidade não expressa.

   Se você utilizar modelos de Azure Resource Manager (ARM), certifique-se de remover o sinalizador ' enableExpress ' da configuração de implantação para que os fluxos de trabalho automatizados sejam executados sem erros.

### <a name="partitioned-entities"></a>Entidades particionadas

   As entidades particionadas tinham suporte na camada Standard para fornecer melhor disponibilidade em uma configuração multilocatário. Com o provisionamento de recursos dedicados disponíveis por namespace na camada Premium, isso não é mais necessário.

   Durante a migração, qualquer entidade particionada no namespace Standard é criada no namespace Premium como uma entidade não particionada.

   Se o modelo do ARM definir ' enablePartitioning ' como ' true ' para uma fila ou tópico específico, ele será ignorado pelo agente.

## <a name="faqs"></a>Perguntas frequentes

### <a name="what-happens-when-the-migration-is-committed"></a>O que acontece quando a migração é confirmada?

Depois que a migração for confirmada, a cadeia de conexão que aponta para o namespace padrão apontará para o namespace Premium.

Os aplicativos remetente e destinatário serão desconectados do namespace padrão e reconectam-se ao namespace Premium automaticamente.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>O que devo fazer depois que a migração Standard para Premium for concluída?

A migração padrão para Premium garante que os metadados de entidade, como tópicos, assinaturas e filtros, sejam copiados do namespace padrão para o namespace Premium. Os dados da mensagem que foram confirmados no namespace padrão não são copiados do namespace Standard para o namespace Premium.

O namespace padrão pode ter algumas mensagens que foram enviadas e confirmadas enquanto a migração estava em andamento. Esvaziar manualmente essas mensagens do namespace padrão e enviá-las manualmente para o namespace Premium. Para drenar manualmente as mensagens, use um aplicativo de console ou um script que dissipe as entidades de namespace padrão usando o nome DNS de migração posterior que você especificou nos comandos de migração. Envie essas mensagens para o namespace Premium para que elas possam ser processadas pelos receptores.

Depois que as mensagens tiverem sido descarregadas, exclua o namespace padrão.

>[!IMPORTANT]
> Depois que as mensagens do namespace padrão tiverem sido descarregadas, exclua o namespace padrão. Isso é importante porque a cadeia de conexão que inicialmente fazia referência ao namespace padrão agora se refere ao namespace Premium. Você não precisa mais do namespace padrão. Excluir o namespace padrão que você migrou ajuda a reduzir a confusão mais tarde.

### <a name="how-much-downtime-do-i-expect"></a>Quanto tempo de inatividade eu esperava?

O processo de migração destina-se a reduzir o tempo de inatividade esperado para os aplicativos. O tempo de inatividade é reduzido usando a cadeia de conexão que os aplicativos remetente e destinatário usam para apontar para o novo namespace Premium.

O tempo de inatividade que é experimentado pelo aplicativo é limitado ao período necessário para atualizar a entrada DNS para apontar para o namespace Premium. O tempo de inatividade é de aproximadamente 5 minutos.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>É necessário fazer alterações de configuração ao fazer a migração?

Não, não há nenhuma alteração de código ou configuração necessária para fazer a migração. A cadeia de conexão que os aplicativos remetente e destinatário usam para acessar o namespace padrão é mapeada automaticamente para atuar como um alias para o namespace Premium.

### <a name="what-happens-when-i-abort-the-migration"></a>O que acontece quando eu cancelo a migração?

A migração pode ser anulada usando o `Abort` comando ou usando o portal do Azure.

#### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portal do Azure

![Anular fluxo-anular sincronização de anular ][]
 ![ fluxo-anular conclusão][]

Quando o processo de migração é anulado, ele anula o processo de cópia das entidades (tópicos, assinaturas e filtros) do padrão para o namespace Premium e interrompe o emparelhamento.

A cadeia de conexão não é atualizada para apontar para o namespace Premium. Seus aplicativos existentes continuam funcionando como faziam antes de você iniciar a migração.

No entanto, ele não exclui as entidades no namespace Premium nem exclui o namespace Premium. Exclua as entidades manualmente se você decidiu não avançar com a migração.

>[!IMPORTANT]
> Se você decidir anular a migração, exclua o namespace Premium que você provisionou para a migração para que você não seja cobrado pelos recursos.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Não quero ter que drenar as mensagens. O que devo fazer?

Pode haver mensagens enviadas pelos aplicativos do remetente e confirmadas no armazenamento no namespace padrão enquanto a migração está ocorrendo e logo antes da migração ser confirmada.

Durante a migração, a carga/dados da mensagem real não é copiada do padrão para o namespace Premium. As mensagens precisam ser descarregadas manualmente e enviadas ao namespace Premium.

No entanto, se você puder migrar durante uma janela de manutenção planejada/manutenções e não quiser drenar e enviar as mensagens manualmente, siga estas etapas:

1. Pare os aplicativos do remetente. Os aplicativos receptores processarão as mensagens que estão atualmente no namespace padrão e irão drenar a fila.
1. Depois que as filas e assinaturas no namespace padrão estiverem vazias, siga o procedimento descrito anteriormente para executar a migração do padrão para o namespace Premium.
1. Após a conclusão da migração, você poderá reiniciar os aplicativos do remetente.
1. Os remetentes e receptores agora se conectarão automaticamente com o namespace Premium.

    >[!NOTE]
    > Você não precisa interromper os aplicativos receptores para a migração.
    >
    > Depois que a migração for concluída, os aplicativos receptores serão desconectados do namespace padrão e se conectarão automaticamente ao namespace Premium.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [diferenças entre as mensagens Standard e Premium](./service-bus-premium-messaging.md).
* Saiba mais sobre os [aspectos de alta disponibilidade e de recuperação de Geo-Disaster para o barramento de serviço Premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Página de aterrissagem de migração]: ./media/service-bus-standard-premium-migration/1.png
[Namespace de instalação]: ./media/service-bus-standard-premium-migration/2.png
[Configurar namespace-criar namespace Premium]: ./media/service-bus-standard-premium-migration/3.png
[Namespace da instalação-escolha o nome da migração após o]: ./media/service-bus-standard-premium-migration/4.png
[Configurar o namespace-entidades de sincronização-iniciar]: ./media/service-bus-standard-premium-migration/5.png
[Configurar o namespace-entidades de sincronização-progresso]: ./media/service-bus-standard-premium-migration/8.png
[Alternar namespace – menu alternar]: ./media/service-bus-standard-premium-migration/9.png
[Alternar namespace-êxito]: ./media/service-bus-standard-premium-migration/12.png

[Anular o fluxo-anular sincronização]: ./media/service-bus-standard-premium-migration/abort1.png
[Anular fluxo-anulação concluída]: ./media/service-bus-standard-premium-migration/abort3.png
