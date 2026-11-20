# ScreenSound

API REST e aplicação de console para gerenciamento de artistas, músicas e gêneros musicais, desenvolvidas em C# com .NET 8 e Entity Framework Core.

> **Observação sobre o conteúdo do repositório:** apesar do nome do arquivo compactado mencionar “BlazorWeb”, o código-fonte incluído contém uma API ASP.NET Core Minimal APIs e uma aplicação de console. Não foi identificado um projeto de interface Blazor neste ZIP.

## Funcionalidades

- Cadastro, consulta, atualização e exclusão de artistas.
- Cadastro, consulta, atualização e exclusão de músicas.
- Cadastro, consulta e exclusão de gêneros musicais.
- Associação entre músicas, artistas e gêneros.
- Persistência de dados em SQL Server usando Entity Framework Core.
- Documentação interativa da API por meio do Swagger.

## Tecnologias

- C#
- .NET 8
- ASP.NET Core Minimal APIs
- Entity Framework Core 7
- SQL Server
- Swagger / Swashbuckle

## Estrutura da solução

```text
ScreenSound/
├── ScreenSound.sln
├── ScreenSound.API/
│   ├── Endpoints/             # Rotas de artistas, músicas e gêneros
│   ├── Requests/              # Modelos de entrada da API
│   ├── Response/              # Modelos de resposta da API
│   └── Program.cs
├── ScreenSound.Shared.Dados/
│   ├── Banco/                 # DbContext e acesso a dados (DAL)
│   └── Migrations/            # Migrações do Entity Framework Core
├── ScreenSound.Shared.Modelos/
│   └── Modelos/               # Entidades Artista, Música e Gênero
└── ScreenSound/
    ├── Menus/                 # Menus da aplicação de console
    └── Program.cs
```

### Projetos

| Projeto | Responsabilidade |
|---|---|
| `ScreenSound.API` | Disponibiliza os endpoints HTTP para gerenciamento do catálogo musical. |
| `ScreenSound.Shared.Dados` | Configura o contexto do banco de dados, as operações de persistência e as migrações. |
| `ScreenSound.Shared.Modelos` | Contém as entidades de domínio compartilhadas. |
| `ScreenSound` | Aplicação de console para registrar e consultar artistas e músicas. |

## Pré-requisitos

Antes de executar o projeto, tenha instalado:

- [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- SQL Server ou SQL Server LocalDB
- Visual Studio 2022, Visual Studio Code ou outra IDE compatível com .NET

## Configuração

### 1. Clone o repositório

```bash
git clone https://github.com/SEU-USUARIO/SEU-REPOSITORIO.git
cd SEU-REPOSITORIO
```

Substitua a URL pelo endereço do seu repositório.

### 2. Configure a conexão com o banco

A API lê a conexão pela chave `ConnectionStrings:ScreenSoundDB`. Configure-a no ambiente de execução, sem publicar credenciais no Git.

No PowerShell:

```powershell
$env:ConnectionStrings__ScreenSoundDB="Server=SEU_SERVIDOR;Database=ScreenSoundV0;Trusted_Connection=True;TrustServerCertificate=True;"
```

Ajuste o valor conforme sua instalação do SQL Server. Para desenvolvimento local, também é possível utilizar SQL Server LocalDB.

> **Segurança:** não coloque usuário, senha ou outras credenciais reais em `appsettings.json`, no código-fonte ou em commits. Se uma credencial real já foi versionada, remova-a do arquivo e do histórico conforme necessário e troque/rotacione essa credencial.

### 3. Restaure as dependências

Na pasta que contém `ScreenSound.sln`, execute:

```bash
dotnet restore ScreenSound.sln
```

### 4. Crie ou atualize o banco de dados

O projeto inclui migrações do Entity Framework Core. Com a conexão configurada, execute:

```bash
dotnet tool restore
dotnet ef database update --project ScreenSound.Shared.Dados --startup-project ScreenSound.API
```

Se houver incompatibilidade entre a versão da ferramenta `dotnet-ef` e os pacotes do Entity Framework instalados no projeto, alinhe as versões antes de aplicar as migrações.

## Executando a API

Na raiz da solução:

```bash
dotnet run --project ScreenSound.API
```

O endereço local será informado no terminal. Com a aplicação em execução, acesse `/swagger` no endereço HTTP/HTTPS exibido para consultar e testar os endpoints.

## Executando a aplicação de console

Em outro terminal, execute:

```bash
dotnet run --project ScreenSound
```

A aplicação apresenta opções para registrar artistas, registrar músicas, listar artistas e consultar a discografia de um artista.

## Endpoints disponíveis

Os endpoints abaixo são definidos pela API. A URL-base depende da porta apresentada quando a aplicação é iniciada.

### Artistas

| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/Artistas` | Lista os artistas. |
| `GET` | `/Artistas/{nome}` | Busca um artista pelo nome. |
| `POST` | `/Artistas` | Cadastra um artista. |
| `PUT` | `/Artistas` | Atualiza os dados de um artista. |
| `DELETE` | `/Artistas/{id}` | Exclui um artista pelo ID. |

Exemplo de corpo para cadastro:

```json
{
  "nome": "Nome do artista",
  "bio": "Biografia do artista"
}
```

### Músicas

| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/Musicas` | Lista as músicas. |
| `GET` | `/Musicas/{nome}` | Busca uma música pelo nome. |
| `POST` | `/Musicas` | Cadastra uma música. |
| `PUT` | `/Musicas` | Atualiza os dados de uma música. |
| `DELETE` | `/Musicas/{id}` | Exclui uma música pelo ID. |

Exemplo de corpo para cadastro:

```json
{
  "nome": "Nome da música",
  "ArtistaId": 1,
  "anoLancamento": 2024,
  "Generos": [
    {
      "Nome": "Rock",
      "Descricao": "Música do gênero rock"
    }
  ]
}
```

### Gêneros

| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/Generos` | Lista os gêneros. |
| `GET` | `/Generos/{nome}` | Busca um gênero pelo nome. |
| `POST` | `/Generos` | Cadastra um gênero. |
| `DELETE` | `/Generos/{id}` | Exclui um gênero pelo ID. |

Exemplo de corpo para cadastro:

```json
{
  "Nome": "Jazz",
  "Descricao": "Gênero musical"
}
```

## Modelo de domínio

O projeto trabalha com três entidades principais:

- **Artista:** armazena nome, biografia, foto de perfil e coleção de músicas.
- **Música:** armazena nome, ano de lançamento e relacionamento com artista e gêneros.
- **Gênero:** armazena nome e descrição, podendo estar associado a várias músicas.

## Observações

- A API utiliza ASP.NET Core Minimal APIs.
- O acesso aos dados é centralizado no componente genérico `DAL<T>`.
- O contexto `ScreenSoundContext` utiliza SQL Server e configura o relacionamento muitos-para-muitos entre músicas e gêneros.
- A configuração de CORS presente no projeto permite qualquer origem, método e cabeçalho. Para produção, restrinja as origens conforme a aplicação cliente.
- O ZIP analisado não inclui testes automatizados nem um projeto de frontend Blazor.

## Contribuição

Contribuições são bem-vindas!

1. Faça um fork do projeto.
2. Crie uma branch para sua alteração (`git checkout -b feature/minha-alteracao`).
3. Faça commit das alterações (`git commit -m "feat: minha alteração"`).
4. Envie a branch (`git push origin feature/minha-alteracao`).
5. Abra um Pull Request.

## Licença

Nenhuma licença foi identificada nos arquivos fornecidos. Antes de redistribuir ou reutilizar o projeto, defina e inclua uma licença apropriada neste repositório.
