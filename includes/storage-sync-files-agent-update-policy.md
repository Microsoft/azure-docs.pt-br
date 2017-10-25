As atualizações para o agente de Sincronização de Arquivo do Azure serão lançadas regularmente para adicionar novos recursos e resolver quaisquer problemas encontrados. É recomendável habilitar o Microsoft Update para obter todas as atualizações para o agente de Sincronização de Arquivo do Azure conforme as lançarmos. Dito isso, entendemos que algumas organizações gostam de controlar rigidamente e testar as atualizações. Para implantações usando versões mais antigas do agente de Sincronização de Arquivo do Azure:

- O Serviço de Sincronização de Armazenamento manterá a versão principal anterior por três meses após o lançamento inicial de uma nova versão principal. Por exemplo, a versão 1.\* deve ter o suporte pelo Serviço de Sincronização de Armazenamento até três meses após o lançamento da versão 2.\*
- Depois de três meses, o Serviço de Sincronização de Armazenamento começará a bloquear a sincronização dos servidores registrados usando a versão expirada com seus Grupos de Sincronização.
- Dentro de três meses para uma versão principal anterior, todas as correções de bug irão somente para a versão principal atual.

> [!Note]  
> Você será notificado por meio de uma notificação do sistema no portal do Azure se você estiver usando uma versão do Azure File Sync que irá expirar nos próximos três meses.