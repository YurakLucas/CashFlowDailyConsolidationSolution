#CashFlowDailyConsolidation
Este repositório contém dois serviços em .NET 8 para gerenciamento de fluxo de caixa (CashFlow) e consolidação diária (DailyConsolidation). O projeto segue uma arquitetura limpa (Clean Architecture), com camadas bem definidas (Domain, Application, Infrastructure) e APIs separadas para cada responsabilidade.

Visão Geral
1 CashFlowService.API
	Responsável pelo controle de lançamentos (débito e crédito). Possui autenticação via Basic Authentication, exigindo credenciais para acessar os endpoints.

2 DailyConsolidationService.API
	Responsável pela consolidação diária dos lançamentos, retornando relatórios com totais de créditos e débitos. Possui autenticação via Basic Authentication, exigindo credenciais para acessar os endpoints.

Ambos compartilham a camada de Application (DTOs e Interfaces), Domain (Entidades) e Infrastructure (serviços, repositórios, etc.). Um ApplicationDbContext InMemory é usado como banco de dados para simplificar a execução local.

Estrutura do Projeto

CashFlowDailyConsolidationSolution/
├── CashFlowDailyConsolidationSolution.sln
├── src/
│   ├── Application/                          
│   │   ├── DTOs/
│   │   │   ├── ConsolidationReportRequest.cs
│   │   │   ├── CreateTransactionRequest.cs
│   │   │   └── TransactionResponse.cs
│   │   └── Interfaces/
│   │       ├── ICashFlowService.cs
│   │       └── IConsolidationService.cs
│   │
│   ├── Domain/                               
│   │   └── Entities/
│   │       ├── DailyBalance.cs
│   │       └── Transaction.cs
│   │
│   ├── Infrastructure/
│   │   ├── Data/
│   │   │   └── ApplicationDbContext.cs
│   │   └── Services/
│   │       ├── CashFlowService.cs
│   │       └── ConsolidationService.cs
│   │
│   ├── CashFlowService.API/
│   │   ├── Authentication/
│   │   │   └── BasicAuthenticationHandler.cs
│   │   ├── Controllers/
│   │   │   └── CashFlowController.cs
│   │   ├── Program.cs
│   │   ├── appsettings.json
│   │   └── launchSettings.json
│   │
│   ├── DailyConsolidationService.API/
│   │   ├── Controllers/
│   │   │   └── ConsolidationController.cs
│   │   ├── Program.cs
│   │   ├── appsettings.json
│   │   └── launchSettings.json
│   │
└── tests/
    ├── CashFlowService.Tests/
    │   └── CashFlowControllerTests.cs
    └── DailyConsolidationService.Tests/
        └── ConsolidationControllerTests.cs

#Tecnologias Utilizadas
.NET 8
Entity Framework Core (InMemory)
Para Log Microsoft.Extensions.Logging
Polly para CircuitBreaker(para o DailyConsolidationService)
Basic Authentication (para o CashFlowServic e DailyConsolidationService)
Swagger para documentação dos endpoints
xUnit e Moq para testes unitários

Como Executar Localmente
Clonar o repositório

bash
git clone <URL-do-repositório>
cd CashFlowDailyConsolidationSolution
Abrir a solução no Visual Studio 2022

Abra o arquivo CashFlowDailyConsolidationSolution.sln no Visual Studio 2022 (ou outra IDE de sua preferência).
Restaurar pacotes NuGet

O Visual Studio fará isso automaticamente ao abrir a solução, ou você pode executar:
bash
dotnet restore
Configurar as credenciais no CashFlowService.API

No arquivo appsettings.json do CashFlowService.API, há uma seção "BasicAuth" com Username e Password. Ajuste conforme necessário:

"BasicAuth": {
  "Username": "admin",
  "Password": "password123"
}
Executar cada projeto

Selecione o CashFlowService.API como projeto de inicialização e rode a aplicação.
Em seguida, selecione o DailyConsolidationService.API como projeto de inicialização (ou abra outra instância do Visual Studio) e rode a aplicação.
Acessar o Swagger

Por padrão, cada serviço expõe o Swagger em /swagger. Por exemplo:
CashFlowService.API: https://localhost:5001/swagger
DailyConsolidationService.API: https://localhost:7096/swagger
Ajuste as portas conforme definido em seu launchSettings.json.
Autenticação 

Ao testar endpoints da API, inclua o header de Authorization no formato Basic. Exemplo:

Authorization: Basic YWRtaW46cGFzc3dvcmQxMjM=
Onde YWRtaW46cGFzc3dvcmQxMjM= é admin:password123 em Base64.
Executar Testes

Dentro da pasta tests/, execute:
bash

dotnet test
Ou use o Test Explorer do Visual Studio para rodar e visualizar os resultados.

#Uso Básico
CashFlowService.API
POST api/cashflow/transactions
Cria um novo lançamento (débito ou crédito).
Exemplo de Payload:

{
  "date": "2025-02-18",
  "amount": 100,
  "type": "Credit",
  "description": "Venda de produto"
}
Requer Basic Auth.

GET api/cashflow/transactions/{date}
Lista todos os lançamentos para uma data específica.
Exemplo: api/cashflow/transactions/2025-02-18
Requer Basic Auth.

DailyConsolidationService.API
GET api/consolidation/report/{date}
Gera um relatório consolidado de créditos, débitos e saldo do dia.
Exemplo: api/consolidation/report/2025-02-18

#Evoluções Futuras
Autenticação unificada (JWT):
Poderíamos migrar ambos os serviços para JWT, facilitando a gestão de tokens e a escalabilidade em ambientes distribuídos.

Mensageria Assíncrona:
Utilizar um broker (RabbitMQ, Kafka, etc.) para desacoplar ainda mais o CashFlowService do DailyConsolidationService, garantindo que o primeiro não seja afetado por eventuais indisponibilidades do segundo.

Persistência em Banco Relacional:
Substituir o InMemory por SQL Server ou PostgreSQL para produção. Isso possibilitaria histórico persistente de lançamentos e consolidações.

Cache Distribuído (Redis):
Implementar cache no DailyConsolidationService para armazenar relatórios recentes e melhorar a performance em cenários de alta carga.

Monitoramento e Observabilidade:
Integrar com ferramentas como Application Insights, Prometheus, Grafana para logs, métricas e alertas em produção.

Containerização e Orquestração:
Utilizar Docker e o uso de Kubernetes (com YAMLs de deployment e service), além de automações CI/CD.

Conclusão
Este projeto demonstra a separação de responsabilidades entre dois serviços (CashFlow e Consolidation), uso de boas práticas de Clean Architecture, testes unitários e uma forma simples de autenticação (Basic Auth). Há espaço para melhorias e adoção de padrões mais avançados (mensageria, caching, etc.), mas esta base fornece um ponto de partida sólido para evolução em um ambiente real.