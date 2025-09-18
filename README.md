## 💰 Sistema de Controle Financeiro Empresarial

API REST para gerenciamento de receitas e despesas, categorização de transações e geração de relatórios básicos. Conectada ao SQL Server e pronta para Docker.

### 📌 Sumário

  - [Status do Projeto](https://www.google.com/search?q=%23-status-do-projeto)
  - [Tecnologias](https://www.google.com/search?q=%23-tecnologias)
  - [Estrutura do Banco de Dados](https://www.google.com/search?q=%23-estrutura-do-banco-de-dados)
  - [Endpoints da API](https://www.google.com/search?q=%23-endpoints-da-api)
  - [Validações de Negócio](https://www.google.com/search?q=%23-valida%C3%A7%C3%B5es-de-neg%C3%B3cio)
  - [Executando o Projeto](https://www.google.com/search?q=%23-executando-o-projeto)
  - [Docker](https://www.google.com/search?q=%23-docker-opcional)
  - [Observações](https://www.google.com/search?q=%23-observa%C3%A7%C3%B5es)
  - [Licença](https://www.google.com/search?q=%23-licen%C3%A7a)

-----

### 🟢 Status do Projeto

Em desenvolvimento / Funcional ✔

  - Todos os endpoints testados no **Postman** ✔
  - Documentação disponível via **Swagger** ✔

### 🛠 Tecnologias

  - **Backend**: .NET 8 Minimal API + Controllers
  - **Banco de Dados**: SQL Server 2022
  - **Documentação**: Swagger / OpenAPI
  - **Containerização**: Docker (opcional)

-----

### 🗄 Estrutura do Banco de Dados

#### 📂 Tabela `Categorias`

| Coluna | Tipo | Obrigatório | Observações |
| --- | --- | :---: | --- |
| `Id` | `int` | Sim | PK, Identity |
| `Nome` | `nvarchar(100)`| Sim | Nome da categoria |
| `Tipo` | `nvarchar(10)` | Sim | Receita ou Despesa |
| `Ativo` | `bit` | Sim | Status da categoria |

#### 📂 Tabela `Transações`

| Coluna | Tipo | Obrigatório | Observações |
| --- | --- | :---: | --- |
| `Id` | `int` | Sim | PK, Identity |
| `Descricao` | `nvarchar(200)`| Sim | Descrição da transação |
| `Valor` | `decimal(18,2)`| Sim | Valor da transação (\>0) |
| `Data` | `datetime`| Sim | Data da transação (não futura) |
| `CategoriaId`| `int` | Sim | FK para Categorias |
| `Observacoes`| `nvarchar(500)`| Não | Observações adicionais |
| `DataCriacao`| `datetime`| Sim | Data de criação da transação |

#### 🔗 Relacionamento

`Transacoes.CategoriaId` -\> `Categorias.Id`

-----

### 📡 Endpoints da API

#### 📌 Categorias

| Método | Endpoint | Descrição |
| --- | --- | --- |
| `GET` | `/categorias` | Lista todas as categorias |
| `GET` | `/categorias/{id}`| Busca categoria por ID |
| `POST` | `/categorias` | Cria nova categoria |
| `PUT` | `/categorias/{id}`| Atualiza categoria existente |
| `DELETE`| `/categorias/{id}`| Inativa categoria (não pode excluir se houver transações) |

**Exemplo `POST` Categoria**

```json
{
  "nome": "Vendas",
  "tipo": "Receita",
  "ativo": true
}
```

#### 📌 Transações

| Método | Endpoint | Descrição |
| --- | --- | --- |
| `GET` | `/transacoes`| Lista todas transações (filtros: `dataInicio`, `dataFim`, `categoriaId`, `tipo`) |
| `GET` | `/transacoes/{id}`| Busca transação por ID |
| `POST` | `/transacoes`| Cria nova transação |
| `PUT` | `/transacoes/{id}`| Atualiza transação existente |
| `DELETE`| `/transacoes/{id}`| Remove transação |

**Exemplo `POST` Transação**

```json
{
  "descricao": "Venda de produto X",
  "valor": 1500.50,
  "data": "2025-09-17T10:00:00",
  "categoriaId": 1,
  "observacoes": "Venda via site",
  "dataCriacao": "2025-09-17T11:00:00"
}
```

#### 📌 Relatórios

| Método | Endpoint | Descrição |
| --- | --- | --- |
| `GET` | `/relatorios/resumo`| Retorna resumo financeiro (receitas, despesas e saldo). Filtros: `dataInicio`, `dataFim` |
| `GET` | `/relatorios/por-categoria`| Agrupa transações por categoria |
| `GET` | `/relatorios/mensal`| Agrupa transações por mês e ano |

**Exemplo `/relatorios/resumo`**

```json
{
  "totalReceitas": 5000.00,
  "totalDespesas": 3200.00,
  "saldo": 1800.00
}
```

**Exemplo `/relatorios/por-categoria`**

```json
[
  {
    "categoria": "Vendas",
    "total": 3500.00
  },
  {
    "categoria": "Serviços",
    "total": 1500.00
  }
]
```

**Exemplo `/relatorios/mensal`**

```json
[
  {
    "mes": 9,
    "ano": 2025,
    "totalReceitas": 5000.00,
    "totalDespesas": 3200.00,
    "saldo": 1800.00
  }
]
```

-----

### ✅ Validações de Negócio

  - O valor da transação deve ser maior que zero.
  - A data da transação não pode ser futura.
  - A categoria deve existir e estar ativa.
  - A descrição da transação é obrigatória.
  - Não é permitido excluir ou inativar uma categoria que tenha transações vinculadas.

-----

### 🚀 Executando o Projeto

#### Pré-requisitos

  - **.NET 8 SDK**
  - **SQL Server 2022**
  - **Docker** (opcional)

#### Executar Localmente

```bash
git clone https://github.com/seu-usuario/controle-financeiro.git
cd controle-financeiro
dotnet run
```

Acesse a documentação do Swagger em: 👉 `http://localhost:5134/swagger`

-----

### 🐳 Docker (opcional)

Você pode usar o Docker para rodar a aplicação e o banco de dados em contêineres.

#### `Dockerfile`

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["FinanceiroApi/FinanceiroApi.csproj", "FinanceiroApi/"]
RUN dotnet restore "FinanceiroApi/FinanceiroApi.csproj"
COPY . .
WORKDIR "/src/FinanceiroApi"
RUN dotnet build "FinanceiroApi.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "FinanceiroApi.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "FinanceiroApi.dll"]
```

#### `docker-compose.yml`

```yaml
version: '3.8'

services:
  sqlserver:
    image: mcr.microsoft.com/mssql/server:2022-latest
    container_name: sqlserver
    environment:
      SA_PASSWORD: "SuaSenha123"
      ACCEPT_EULA: "Y"
    ports:
      - "1433:1433"
  
  financeiroapi:
    build: .
    depends_on:
      - sqlserver
    ports:
      - "5134:80"
    environment:
      ConnectionStrings__DefaultConnection: "Server=sqlserver;Database=FinanceiroDb;User Id=sa;Password=SuaSenha123;"
```

-----

### 📝 Observações

  - A API é totalmente funcional com **CRUD** de transações e categorias.
  - Possui relatórios resumidos, por categoria e mensais.
  - Todos os endpoints foram testados no Postman.
  - A documentação interativa está disponível no Swagger.
  - As validações de negócio estão aplicadas conforme as regras estabelecidas.

-----

### 📜 Licença

[MIT](https://www.google.com/search?q=https://github.com/seu-usuario/seu-repositorio/blob/main/LICENSE)
