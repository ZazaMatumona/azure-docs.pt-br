---
title: Diagnóstico e monitoramento de atores
description: Este artigo descreve os recursos de diagnóstico e monitoramento de desempenho no runtime de Reliable Actors do Service Fabric, incluindo os eventos e os contadores de desempenho emitidos por ele.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: a38a11d9cf062cd0a45890d43afe9b2530b2b7bb
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258467"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnóstico e monitoramento de desempenho para Reliable Actors
O runtime de Reliable Actors emite eventos [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1) e [contadores de desempenho](/dotnet/api/system.diagnostics.performancecounter?view=dotnet-plat-ext-3.1). Esses recursos fornecem informações sobre como o runtime está operando e ajudam na solução de problemas e no monitoramento de desempenho.

## <a name="eventsource-events"></a>Eventos EventSource
O nome do provedor do EventSource do runtime de Reliable Actors é "Microsoft-ServiceFabric-Actors". Os eventos dessa origem de eventos aparecem na janela [Eventos de Diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o aplicativo de ator está sendo [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam a coletar e/ou exibir eventos EventSource são [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [Log de Semântica](/previous-versions/msp-n-p/dn774980(v=pandp.10)) e [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Palavras-chave
Todos os eventos que pertencem ao EventSource dos Reliable Actors são associados a uma ou mais palavras-chave. Isso permite a filtragem dos eventos que são coletados. Os bits de palavra-chave são definidos a seguir.

| bit | Descrição |
| --- | --- |
| 0x1 |Conjunto de eventos importantes que resumem a operação do runtime dos Atores do Fabric. |
| 0x2 |Conjunto de eventos que descrevem as chamadas de método do ator. Para saber mais, confira o [tópico introdutório sobre atores](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Conjunto de eventos relacionados ao estado do ator. Para saber mais, veja o tópico em [gerenciamento de estados do ator](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Conjunto de eventos relacionados à simultaneidade baseada no turno do ator. Para obter mais informações, confira o tópico sobre [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Contadores de desempenho
O runtime dos Reliable Actors define as categorias de contador de desempenho a seguir.

| Categoria | Descrição |
| --- | --- |
| Ator da Malha do Serviço |Contadores específicos aos atores do Service Fabric do Azure; por exemplo, tempo que se leva para salvar o estado do ator |
| Método de ator da Malha do Serviço |Contadores específicos aos métodos implementados pelos atores do Service Fabric; por exemplo, quantas vezes um método de ator é invocado |

Cada uma das categorias acima tem um ou mais contadores.

O aplicativo [Monitor de Desempenho do Windows](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) está disponível por padrão no sistema operacional Windows e pode ser usado para coletar e exibir dados do contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para coletar dados do contador de desempenho e carregá-los nas tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes da instância do contador de desempenho
Um cluster que tem um grande número de serviços de ator ou partições de serviço de ator terá um grande número de instâncias do contador de desempenho de ator. Os nomes da instância do contador de desempenho podem ajudar a identificar a [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) e o método de ator (se aplicável) aos quais a instância do contador de desempenho está associada.

#### <a name="service-fabric-actor-category"></a>Categoria de ator do Service Fabric
Para a categoria `Service Fabric Actor`, os nomes da instância do contador estão no seguinte formato:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia de caracteres da ID de partição de Service Fabric à qual a instância do contador de desempenho está associada. A ID de partição é um GUID, e sua representação de cadeia de caracteres é gerada por meio do [`Guid.ToString`](/dotnet/api/system.guid.tostring?view=netcore-3.1#System_Guid_ToString_System_String_) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação da cadeia de caracteres de um inteiro de 64 bits que é gerada pelo tempo de execução dos Atores do Fabric para seu uso interno. Isso está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outros nomes da instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir o exemplo de um nome da instância do contador para um contador que pertence à categoria `Service Fabric Actor` :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

No exemplo acima, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação da cadeia de caracteres da ID da partição do Service Fabric e `635650083799324046` é a ID de 64 bits que é gerada para uso interno do runtime.

#### <a name="service-fabric-actor-method-category"></a>Categoria do método de ator do Service Fabric
Para a categoria `Service Fabric Actor Method`, os nomes da instância do contador estão no seguinte formato:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* é o nome do método de ator ao qual a instância do contador de desempenho está associada. O formato do nome do método é determinado com base em alguma lógica no runtime dos Atores da Malha que equilibra a legibilidade do nome com restrições no comprimento máximo dos nomes da instância do contador de desempenho no Windows.

*ActorsRuntimeMethodId* é a representação da cadeia de caracteres de um inteiro de 32 bits que é gerada pelo tempo de execução dos Atores do Fabric para seu uso interno. Isso está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outros nomes da instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia de caracteres da ID de partição de Service Fabric à qual a instância do contador de desempenho está associada. A ID de partição é um GUID, e sua representação de cadeia de caracteres é gerada por meio do [`Guid.ToString`](/dotnet/api/system.guid.tostring?view=netcore-3.1#System_Guid_ToString_System_String_) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação da cadeia de caracteres de um inteiro de 64 bits que é gerada pelo tempo de execução dos Atores do Fabric para seu uso interno. Isso está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outros nomes da instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir o exemplo de um nome da instância do contador para um contador que pertence à categoria `Service Fabric Actor Method` :

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

No exemplo acima, `ivoicemailboxactor.leavemessageasync` é o nome do método, `2` é a ID de 32 bits gerada para uso interno do runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação da cadeia de caracteres da ID da partição do Service Fabric e `635650083804480486` é a ID de 64 bits gerada para uso interno do runtime.

## <a name="list-of-events-and-performance-counters"></a>Lista de eventos e contadores de desempenho
### <a name="actor-method-events-and-performance-counters"></a>Eventos e contadores de desempeno do método de ator
O runtime dos Reliable Actors emite os eventos relacionados aos [métodos de ator](service-fabric-reliable-actors-introduction.md)a seguir.

| Nome do evento | ID do evento | Level | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Detalhado |0x2 |O runtime dos atores está prestes a invocar um método de ator. |
| ActorMethodStop |8 |Detalhado |0x2 |Um método de ator finalizou a execução. Isto é, a chamada assíncrona do runtime para o método de ator foi retornada e a tarefa retornada pelo método de ator foi concluída. |
| ActorMethodThrewException |9 |Aviso |0x3 |Uma exceção foi lançada durante a execução de um método de ator, ou durante a chamada assíncrona do runtime para o método de ator, ou durante a execução da tarefa retornada pelo método de ator. Esse evento indica algum tipo de falha no código do ator que precisa de investigação. |

O runtime dos Reliable Actors publica os contadores de desempenho relacionados à execução dos métodos de ator a seguir.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método de ator da Malha do Serviço |Invocação/s |Número de vezes que o método de serviço do ator é invocado por segundo |
| Método de ator da Malha do Serviço |Média de milissegundos por invocação |Tempo usado para executar o método de serviço do ator em milissegundos |
| Método de ator da Malha do Serviço |Exceções lançadas/s |Número de vezes que o método de serviço do ator lançou uma exceção por segundo |

### <a name="concurrency-events-and-performance-counters"></a>Contadores de desempenho e eventos simultâneos
O runtime dos Atores Confiáveis emite os eventos relacionados à [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency)a seguir.

| Nome do evento | ID do evento | Level | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Detalhado |0x8 |Esse evento é escrito no início de cada novo turno em um ator. Ele contém o número de chamadas pendentes do ator que estão aguardando para adquirir o bloqueio por ator que aplica a simultaneidade baseada em turno. |

O runtime dos Reliable Actors publica os contadores de desempenho a seguir relacionados à simultaneidade.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator da Malha do Serviço |nº de chamadas do ator aguardando o bloqueio |Número de chamadas pendentes do ator aguardando para adquirir o bloqueio por ator que aplica a simultaneidade baseada em turno |
| Ator da Malha do Serviço |Média de milissegundos por espera de bloqueio |Tempo (em milissegundos) para adquirir o bloqueio por ator que impõe a simultaneidade baseada em turno |
| Ator da Malha do Serviço |Média de milissegundos de manutenção de bloqueio do ator |Tempo (em milissegundos) pelo qual o bloqueio por ator é mantido |

### <a name="actor-state-management-events-and-performance-counters"></a>Contadores de desempenho e eventos do gerenciamento de estado do ator
O runtime de Reliable Actors emite os eventos a seguir relacionados ao [gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md).

| Nome do evento | ID do evento | Level | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Detalhado |0x4 |O runtime dos atores está prestes a salvar o estado do ator. |
| ActorSaveStateStop |11 |Detalhado |0x4 |O runtime dos atores acabou de salvar o estado do ator. |

O runtime dos Reliable Actors publica os contadores de desempenho a seguir relacionados ao gerenciamento de estado do ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator da Malha do Serviço |Milissegundos em média por operação de salvamento do estado |Tempo necessário para salvar o estado do ator em milissegundos |
| Ator da Malha do Serviço |Média de milissegundos por operação de carregar estado |Tempo necessário para carregar o estado do ator em milissegundos |

### <a name="events-related-to-actor-replicas"></a>Eventos relacionados às réplicas do ator
O runtime dos Reliable Actors emite os seguintes eventos relacionados às [réplicas do ator](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Nome do evento | ID do evento | Level | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informativo |0x1 |A réplica do ator alterou a função para Primária. Isso significa que os atores dessa partição serão criados dentro dessa réplica. |
| ReplicaChangeRoleFromPrimary |2 |Informativo |0x1 |A réplica do ator alterou a função para não Primária. Isso significa que os atores dessa partição não serão mais criados dentro dessa réplica. Nenhuma nova solicitação será entregue aos atores já criados dentro dessa réplica. Os atores serão destruídos depois que todas as solicitações em andamento forem concluídas. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Eventos de ativação e desativação do ator e contadores de desempenho
O runtime de Reliable Actors emite os eventos a seguir relacionados à [ativação e desativação do ator](service-fabric-reliable-actors-lifecycle.md).

| Nome do evento | ID do evento | Level | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informativo |0x1 |Um ator foi ativado. |
| ActorDeactivated |6 |Informativo |0x1 |Um ator foi desativado. |

O runtime dos Reliable Actors publica os contadores de desempenho a seguir relacionados à ativação e à desativação do ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator da Malha do Serviço |Média de milissegundos para OnActivateAsync |Tempo necessário para executar o método OnActivateAsync em milissegundos |

### <a name="actor-request-processing-performance-counters"></a>Solicitação do ator processando contadores de desempenho
Quando um cliente chama um método por meio de um objeto de proxy do ator, isso resulta no envio de uma mensagem de solicitação pela rede para o serviço de ator. O serviço processa a mensagem de solicitação e envia uma resposta de volta ao cliente. O runtime dos Reliable Actors publica os seguintes contadores de desempenho relacionados ao processamento de solicitação de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator da Malha do Serviço |nº de solicitações pendentes |Número de solicitações sendo processadas no serviço |
| Ator da Malha do Serviço |Média de milissegundos por solicitação |Tempo (em milissegundos) que o serviço leva para processar uma solicitação |
| Ator da Malha do Serviço |Média de milissegundos para desserialização de solicitação |Tempo (em milissegundos) para desserializar a mensagem de solicitação do ator quando ela é recebida no serviço |
| Ator da Malha do Serviço |Média de milissegundos para serialização de resposta |Tempo (em milissegundos) para serializar a mensagem de resposta do ator no serviço antes de a resposta ser enviada ao cliente |

## <a name="next-steps"></a>Próximas etapas
* [Como os Reliable Actors usam a plataforma do Service Fabric](service-fabric-reliable-actors-platform.md)
* [Documentação de referência da API do Ator](/previous-versions/azure/dn971626(v=azure.100))
* [Código de exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Provedores de EventSource no PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
