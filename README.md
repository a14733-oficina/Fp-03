Análise Técnica do Projeto - M5 (Login e CRUD)

📝 Descrição do Projeto

Este documento combina a análise de dois módulos interligados:

1.
M5 Login (FP03): Implementa um sistema de autenticação (login) básico utilizando PHP e MySQL para validar credenciais de utilizadores.

2.
M5 CRUD (FP03): Implementa as operações CRUD (Create, Read, Update, Delete) para a gestão de dados, neste caso, de Alunos e Produtos.

O projeto demonstra a interação entre formulários HTML/Bootstrap e scripts PHP que utilizam a extensão PDO para comunicação com a base de dados. A análise da base de dados é baseada no dump SQL fornecido (if0_40156192_modulo5_db.sql).

🛠️ Tecnologias Utilizadas

Tecnologia
Propósito
PHP
Lógica de backend, processamento de formulários e c
omunicação com a base de dados.
PDO
Extensão do PHP para acesso à base de dados MySQL.
MySQL
Sistema de gestão de base de dados relacional (SGBDR) para armazenar as credenciais e os dados de Alunos/Produtos.
HTML/Bootstrap
Criação da interface de login e das listagens/formulários CRUD, garantindo um design responsivo.


📂 Estrutura de Ficheiros

O projeto é composto por dois módulos principais:

Módulo 1: Login (m5-login/)

Plain Text


/m5-login
├── conexao.php    (Configuração da ligação à base de dados)
├── erro.php       (Página apresentada em caso de falha de autenticação)
├── index.php      (Formulário de login)
└── verificar.php  (Script de processamento e validação de credenciais)


Módulo 2: CRUD (m5-crud/)

Plain Text


/m5-crud
├── alunos_apagar.php   (Operação DELETE para Alunos)
├── alunos_editar.php   (Operação UPDATE para Alunos)
├── alunos_form.php     (Formulário para INSERIR Alunos)
├── alunos_inserir.php  (Operação CREATE para Alunos)
├── alunos_lista.php    (Operação READ/Listagem de Alunos)
├── conexao.php         (Configuração da ligação à base de dados - duplicado)
├── index.php           (Página de índice do CRUD)
├── produtos_apagar.php (Operação DELETE para Produtos)
├── produtos_editar.php (Operação UPDATE para Produtos)
├── produtos_form.php   (Formulário para INSERIR Produtos)
├── produtos_inserir.php(Operação CREATE para Produtos)
└── produtos_lista.php  (Operação READ/Listagem de Produtos)


💻 Análise Detalhada do Código e Base de Dados

1. Configuração da Base de Dados (conexao.php)

O ficheiro conexao.php é idêntico em ambos os módulos e contém as credenciais de acesso:

Elemento
Detalhe
Observações
Host
sql300.infinityfree.com
Servidor de alojamento.
DB Name
if0_40156192_modulo5_db
Nome da base de dados.
Credenciais
Hardcoded
O nome de utilizador e a palavra-passe estão escritos diretamente no código, o que é uma falha de segurança grave e desaconselhada.


2. Estrutura da Base de Dados (Foco Principal)

A base de dados if0_40156192_modulo5_db é composta por três tabelas principais, conforme o dump SQL:

Tabela utilizadores (Foco no Login)

Esta tabela é usada pelo módulo de Login (verificar.php) para autenticação.

Coluna
Tipo de Dados
Observações
id
int(11)
Chave primária, AUTO_INCREMENT.
username
varchar(100)
Nome de utilizador.
password
varchar(100)
Palavra-passe.


Análise de Segurança Crítica:

O dump SQL confirma que as palavras-passe são armazenadas em texto simples (plain text).

SQL


INSERT INTO `utilizadores` (`id`, `username`, `password`) VALUES
(1, 'admin', 'admin'),
(2, 'pedropaiva', '1234');


A coluna password é do tipo VARCHAR(100), mas armazena valores como 'admin' e '1234'. Esta prática é a principal falha de segurança do projeto, pois expõe todas as credenciais em caso de violação da base de dados.

Tabela alunos (Foco no CRUD)

Esta tabela é usada para a gestão de dados de alunos.

Coluna
Tipo de Dados
Observações
id
int(11)
Chave primária, AUTO_INCREMENT.
nome
varchar(100)
Nome do aluno.
email
varchar(100)
Endereço de email do aluno.


Tabela produtos (Foco no CRUD)

Esta tabela é usada para a gestão de dados de produtos.

Coluna
Tipo de Dados
Observações
id
int(11)
Chave primária, AUTO_INCREMENT.
produto
varchar(100)
Nome do produto.
preco
float(6,2)
Preço do produto, com 6 dígitos no total e 2 casas decimais.


3. Análise das Operações de Código

O código PHP utiliza a extensão PDO e Prepared Statements para todas as operações de base de dados (Login, CRUD de Alunos e CRUD de Produtos).

•
Exemplo (Login): SELECT * FROM utilizadores WHERE username = :username AND password = :password

•
Exemplo (CRUD): INSERT INTO alunos (nome, email) VALUES (?, ?)

O uso de Prepared Statements é uma boa prática que protege o sistema contra ataques de SQL Injection.

💡 Sugestões de Melhoria e Refatoração

1. Segurança: Hashing de Palavras-Passe (Prioridade Máxima)

•
Recomendação: Implementar hashing seguro (e.g., password_hash() e password_verify() do PHP) para a tabela utilizadores. A coluna password deve ser alterada para um tipo de dados que suporte o hash (e.g., VARCHAR(255)).

2. Refatoração da Base de Dados e Conexão

•
Recomendação: Centralizar o ficheiro conexao.php num único local e garantir que todos os módulos o incluem.

•
Recomendação: Mover as credenciais da base de dados para um ficheiro de configuração seguro (.env ou similar) fora do diretório web para evitar o hardcoding.

3. Gestão de Sessões e Autorização

•
Recomendação: O módulo CRUD (m5-crud) não possui qualquer verificação de sessão. Após o login bem-sucedido, o sistema deve iniciar uma sessão e todas as páginas do CRUD devem verificar se o utilizador está autenticado antes de permitir o acesso.

4. Validação de Dados

•
Recomendação: Embora o código utilize trim() e verifique se os campos estão vazios, a validação de dados mais robusta (e.g., formato de email, comprimento máximo) deve ser implementada no lado do servidor, além da validação básica de HTML.

5. Design de Interface (CRUD Inline)

•
Observação: O ficheiro alunos_lista.php utiliza um método de edição "inline", onde os campos de texto e o botão "Guardar" estão diretamente na linha da tabela. Embora funcional, esta abordagem pode ser confusa.

•
Alternativa: Considerar a utilização de um formulário de edição separado (alunos_editar.php) ou modais (pop-ups) para uma melhor experiência do utilizador.

