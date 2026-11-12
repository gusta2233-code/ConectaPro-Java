# ConectaPro – CRUD em Java + MySQL

Plataforma (projeto acadêmico do SENAI) que conecta **clientes** a **profissionais** de serviços,
da parte elétrica e instalação de internet à construção civil.

## O que o projeto faz
CRUD completo (cadastrar, listar, atualizar, excluir) de:
- **Usuários** (Cliente ou Profissional)
- **Serviços** (oferecidos por um profissional)
- **Solicitações** (pedidos de um cliente, com serviço opcional e status)

## Estrutura
```
ConectaPro_CRUD/
├── banco/conectapro.sql           # cria o banco e as 3 tabelas
├── config.properties.example      # modelo da configuração do MySQL
├── docs/                          # landing page PWA (GitHub Pages)
├── src/br/com/conectapro/
│   ├── Main.java                  # ponto de partida
│   ├── view/        Menu, Janelas        (telas em JOptionPane)
│   ├── controller/  PessoaController, ServicoController, SolicitacaoController
│   ├── services/    Conexao, PessoaService, ServicoService, SolicitacaoService  (JDBC)
│   ├── models/      Pessoa (abstrata) ← Cliente, Profissional; Servico; Solicitacao; enums
│   └── util/        SenhaUtil (hash da senha)
└── test/br/com/conectapro/
    ├── services/    testes de integração dos 3 CRUDs (usam o MySQL)
    ├── controller/  testes das validações dos controllers (sem banco)
    └── models/      testes das regras das entidades (sem banco)
```

## Arquitetura em camadas
`view` → `controller` → `services` → banco (MySQL), usando os objetos de `models`.
- **View:** só pergunta e mostra (JOptionPane). Não tem regra nem SQL.
- **Controller:** recebe o que a View digitou, valida pelos setters dos models e chama o Service.
- **Services:** conversa com o MySQL via JDBC (`PreparedStatement`).
- **Models:** entidades com herança (`Pessoa` → `Cliente`/`Profissional`), `equals`/`hashCode` e validações.

Relacionamentos: `Servico` → `Profissional`; `Solicitacao` → `Cliente` e `Servico` (opcional).

## Como rodar (IntelliJ IDEA, sem Maven)
1. No MySQL Workbench, execute `banco/conectapro.sql`.
2. Copie `config.properties.example` para `config.properties`, na pasta raiz do projeto
   (a que tem `src` e `test`), e coloque a senha do seu MySQL. Esse arquivo não vai para o GitHub.
3. No IntelliJ: **File > Open** e escolha a pasta `ConectaPro_CRUD`.
4. **File > Project Structure > Modules**: marque a pasta `src` como **Sources** e a pasta `test` como **Tests**.
5. **File > Project Structure > Libraries > + > From Maven** e adicione:
   - `com.mysql:mysql-connector-j:9.2.0` (ou use o mesmo `.jar` do MySQL das aulas);
   - `org.junit.jupiter:junit-jupiter:5.11.4` (para os testes).
   Marque a opção de baixar também as dependências.
6. Abra `src/br/com/conectapro/Main.java` e clique no ▶ ao lado de `main`.

Use JDK 17 ou superior. Se aparecer "config.properties não encontrado", em **Run > Edit Configurations**
confira se o **Working directory** é a pasta raiz do projeto.

## Testes automatizados
Botão direito na pasta `test` > **Run 'All Tests'**.
- Os testes de `services/` precisam do MySQL ligado e do script já executado. Eles criam os próprios dados
  (e-mails únicos) e apagam tudo ao final.
- Os testes de `controller/` e `models/` rodam sem banco.

## Git e GitHub
```
git init
git add .
git commit -m "Primeira versão do ConectaPro"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/conectapro.git
git push -u origin main
```
Faça commits pequenos a cada etapa (ex.: "Adiciona CRUD de serviços", "Adiciona testes dos Services").

## Landing page (GitHub Pages)
No GitHub: **Settings > Pages > Build and deployment > Deploy from a branch**,
escolha a branch `main` e a pasta **/docs**. Link: `https://SEU-USUARIO.github.io/conectapro/`.

## Observações
- A senha é guardada como hash; ao atualizar um usuário, deixe a senha em branco para mantê-la.
- O tipo do usuário (cliente/profissional) não muda na edição; para trocar, cadastre outro.
