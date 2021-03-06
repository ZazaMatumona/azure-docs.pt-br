---
title: Manipular linhas de erro com fluxos de dados de mapeamento em Azure Data Factory
description: Saiba como lidar com erros de truncamento do SQL em Azure Data Factory usando fluxos de dados de mapeamento.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 3f8ac2d1434019548b01d8468015a543d89d0fba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85254405"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Manipular linhas de erro de truncamento do SQL no mapeamento de Data Factory fluxos de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Um cenário comum no Data Factory ao usar o mapeamento de fluxos de dados é gravar seus dados transformados em um banco de dado no banco de dados SQL do Azure. Nesse cenário, uma condição de erro comum que deve ser evitada é o truncamento de coluna possível. Siga estas etapas para fornecer o log de colunas que não se ajustarão a uma coluna de cadeia de caracteres de destino, permitindo que o fluxo de dados continue nesses cenários.

## <a name="scenario"></a>Cenário

1. Temos uma tabela de banco de dados de destino que tem uma ```nvarchar(5)``` coluna chamada "Name".

2. Dentro de nosso fluxo de dados, queremos mapear títulos de filmes de nosso coletor para a coluna "nome" de destino.

    ![Fluxo de dados de filme 1](media/data-flow/error4.png)
    
3. O problema é que o título do filme não se encaixa em uma coluna do coletor que pode conter apenas 5 caracteres. Ao executar esse fluxo de dados, você receberá um erro como este:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Este vídeo percorre um exemplo de como configurar a lógica de tratamento de linha de erro em seu fluxo de dados:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Como criar com base nessa condição

1. Nesse cenário, o comprimento máximo da coluna "nome" é de cinco caracteres. Portanto, vamos adicionar uma transformação de divisão condicional que nos permitirá registrar linhas com "títulos" com mais de cinco caracteres e, ao mesmo tempo, permitir o restante das linhas que podem caber nesse espaço para gravar no banco de dados.

    ![divisão condicional](media/data-flow/error1.png)

2. Essa transformação de divisão condicional define o comprimento máximo de "título" como cinco. Qualquer linha menor ou igual a cinco será inserida no ```GoodRows``` fluxo. Qualquer linha que tenha mais de cinco será inserida no ```BadRows``` fluxo.

3. Agora, precisamos registrar as linhas que falharam. Adicione uma transformação de coletor ao ```BadRows``` fluxo para registro em log. Aqui, vamos "mapear automaticamente" todos os campos para que tenhamos registro em log do registro de transação completo. Essa é uma saída de arquivo CSV delimitada por texto para um único arquivo no armazenamento de BLOBs. Vamos chamar o arquivo de log "badrows.csv".

    ![Linhas inadequadas](media/data-flow/error3.png)
    
4. O fluxo de dados concluído é mostrado abaixo. Agora, podemos dividir as linhas de erro para evitar os erros de truncamento do SQL e colocar essas entradas em um arquivo de log. Enquanto isso, as linhas bem-sucedidas podem continuar a gravar em nosso banco de dados de destino.

    ![fluxo de dados completo](media/data-flow/error2.png)

## <a name="next-steps"></a>Próximas etapas

* Compile o restante da lógica de fluxo de dados usando as [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
