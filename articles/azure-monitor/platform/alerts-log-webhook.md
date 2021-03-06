---
title: Ações de webhook para alertas de log nos alertas do Azure
description: Este artigo descreve como criar uma regra de alerta de log usando o espaço de trabalho Log Analytics ou Application Insights, como o alerta envia dados por push como um webhook HTTP e os detalhes das diferentes personalizações que são possíveis.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 3311819f021533a28a41daf2c2f08193218fae96
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075268"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Ações de webhook para regras de alerta do log
Quando um [alerta de log é criado no Azure](alerts-log.md), você tem a opção de [configurá-lo usando grupos de ação](action-groups.md) para executar uma ou mais ações. Este artigo descreve as diferentes ações de webhook que estão disponíveis e mostra como configurar um webhook personalizado baseado em JSON.

> [!NOTE]
> Você também pode usar o [esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs) para suas integrações de webhook. O esquema de alerta comum fornece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Azure Monitor. Observe que o esquema de alerta comum não Honour a opção JSON personalizada para alertas de log. Ele adiará a carga do esquema de alerta comum se ela for selecionada, independentemente da personalização que você tenha feito no nível da regra de alerta. [Saiba mais sobre as definições de esquema de alerta comuns.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Ações de Webhook

Com as ações de webhook, você pode invocar um processo externo por meio de uma única solicitação HTTP POST. O serviço chamado deve dar suporte a WebHooks e determinar como usar qualquer carga que receber.

As ações de webhook exigem as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| **URL de Webhook** |A URL do webhook. |
| **Carga JSON personalizada** |A carga personalizada a ser enviada com o webhook quando essa opção é escolhida durante a criação do alerta. Para obter mais informações, consulte [Manage log Alerts](alerts-log.md).|

> [!NOTE]
> O botão **Exibir webhook** junto com a opção **incluir conteúdo JSON personalizado para o webhook** para o alerta de log exibe o conteúdo do webhook de exemplo para a personalização fornecida. Ele não contém dados reais, mas representa o esquema JSON que é usado para alertas de log. 

Os WebHooks incluem uma URL e uma carga formatada em JSON que os dados enviam para o serviço externo. Por padrão, a carga inclui os valores na tabela a seguir. Você pode optar por substituir essa carga por uma personalizado de sua preferência. Nesse caso, use as variáveis na tabela para cada um dos parâmetros para incluir seus valores em sua carga personalizada.


| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nome da regra de alerta. |
| *Gravidade* |#severity |Severidade definida para o alerta do log disparado. |
| *AlertThresholdOperator* |#thresholdoperator |Operador de limite para a regra de alerta, que usa maior ou menor que. |
| *AlertThresholdValue* |#thresholdvalue |O valor de limite para a regra de alerta. |
| *LinkToSearchResults* |#linktosearchresults |Link para o portal de análise que retorna os registros da consulta que criou o alerta. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Link para a API de análise que retorna os registros da consulta que criou o alerta. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Link para o portal de análise que retorna os registros da consulta filtrada por combinações de valor de dimensões que criaram o alerta. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Link para a API de análise que retorna os registros da consulta filtrada por combinações de valores de dimensões que criaram o alerta. |
| *ResultCount* |#searchresultcount |Número de registros nos resultados da pesquisa. |
| *Hora de término do intervalo de pesquisa* |#searchintervalendtimeutc |Hora de término da consulta em UTC, com o formato mm/dd/aaaa HH: mm: ss AM/PM. |
| *Intervalo de pesquisa* |#searchinterval |Janela de tempo para a regra de alerta, com o formato HH: mm: SS. |
| *Hora de início do intervalo de pesquisa* |#searchintervalstarttimeutc |Hora de início da consulta em UTC, com o formato mm/dd/aaaa HH: mm: ss AM/PM. 
| *SearchQuery* |#searchquery |A consulta da pesquisa de log usada pela regra de alerta. |
| *SearchResults* |"IncludeSearchResults": true|Registros retornados pela consulta como uma tabela JSON, limitados aos primeiros 1.000 registros. "IncludeSearchResults": true é adicionado em uma definição personalizada de webhook JSON como uma propriedade de nível superior. |
| *Dimensões* |"IncludeDimensions": verdadeiro|Combinações de valores de dimensões que dispararam esse alerta como uma seção JSON. "IncludeDimensions": true é adicionado em uma definição personalizada de webhook JSON como uma propriedade de nível superior. |
| *Tipo de alerta*| #alerttype | O tipo de regra de alerta de log configurada como [medida métrica](alerts-unified-log.md#metric-measurement-alert-rules) ou [número de resultados](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID do seu espaço de trabalho do Log Analytics. |
| *ID do Aplicativo* |#applicationid |ID do seu aplicativo Application Insights. |
| *ID da assinatura* |#subscriptionid |ID da sua assinatura do Azure usada. 

> [!NOTE]
> Os links fornecidos passam parâmetros como *SearchQuery*, *StartTime de intervalo de pesquisa*e *hora de término do intervalo de pesquisa* na URL para a Portal do Azure ou API.

Por exemplo, você pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado *text*. O serviço que esse webhook chama espera esse parâmetro.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Esse conteúdo de exemplo é resolvido para algo semelhante ao seguinte quando é enviado para o webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Como todas as variáveis em um webhook personalizado devem ser especificadas dentro de um compartimento JSON, como "#searchinterval", o webhook resultante também tem dados variáveis dentro de compartimentos, como "00:05:00".

Para incluir os resultados da pesquisa em uma carga personalizada, verifique se **IncludeSearchResults** está definido como uma propriedade de nível superior na carga JSON. 

## <a name="sample-payloads"></a>Cargas de exemplo
Esta seção mostra as cargas de exemplo para WebHooks para alertas de log. Os conteúdos de exemplo incluem exemplos quando a carga é padrão e quando é personalizada.

### <a name="standard-webhook-for-log-alerts"></a>Webhook padrão para alertas de log 
Ambos os exemplos têm uma carga fictícia com apenas duas colunas e duas linhas.

#### <a name="log-alert-for-log-analytics"></a>Alerta de log para Log Analytics
A seguinte carga de exemplo é para uma ação de webhook padrão *sem uma opção JSON personalizada* que é usada para alertas com base em log Analytics:

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
 ```

> [!NOTE]
> O valor do campo "Severity" poderá ser alterado se você tiver [alternado sua preferência de API](alerts-log-api-switch.md) para alertas de log em log Analytics.


#### <a name="log-alert-for-application-insights"></a>Alerta de log para Application Insights
O seguinte conteúdo de exemplo é para um webhook padrão *sem uma opção JSON personalizada* quando é usado para alertas de log com base em Application insights:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alerta de log com carga JSON personalizada
Por exemplo, para criar uma carga personalizada que inclua apenas o nome do alerta e os resultados da pesquisa, você pode usar o seguinte: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

A seguinte carga de exemplo é para uma ação personalizada de webhook para qualquer alerta de log:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [alertas de log nos alertas do Azure](alerts-unified-log.md).
- Entenda como [Gerenciar alertas de log no Azure](alerts-log.md).
- Crie e gerencie [grupos de ações no Azure](action-groups.md).
- Saiba mais sobre o [Application Insights](../log-query/log-query-overview.md).
- Saiba mais sobre [consultas de log](../log-query/log-query-overview.md). 
