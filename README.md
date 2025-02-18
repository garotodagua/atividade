# atividade
Exercício 1 (Com Postgres)
Crie uma API para um CRUD (Create, Read, Update, Delete) de uma das entidades abaixo:
Produto (nome, preço, quantidade)
Aluno (nome, email, matricula)
Imovel (endereco, valor, tipo (casa, terreno, apartamento) )
Sua API deve ser capaz de:
Listar todos os registros
Listar um registro específico (por id)
Criar um novo registro
Atualizar um registro
Deletar um registro
Adicionar junto ao código, o arquivo de configuração do Postman ou Insomnia para os testes (Sem esse arquivo,
a nota será 0)
Utilize Express e Prisma
Para criar uma API de CRUD utilizando Express e Prisma com PostgreSQL, siga os passos abaixo. Vou escolher a entidade Produto como exemplo.

Passo 1: Configuração do Projeto

Inicialize o projeto Node.js:
bash
Copy
mkdir crud-api
cd crud-api
npm init -y
Instale as dependências necessárias:
bash
Copy
npm install express @prisma/client
npm install --save-dev prisma
Inicialize o Prisma:
bash
Copy
npx prisma init
Isso criará um diretório prisma com um arquivo schema.prisma e um arquivo .env para configurar a conexão com o banco de dados.
Passo 2: Configuração do Banco de Dados

Configure o arquivo .env:
Abra o arquivo .env e configure a conexão com o PostgreSQL:
env
Copy
DATABASE_URL="postgresql://SEU_USUARIO:SUA_SENHA@localhost:5432/nome_do_banco"
Defina o modelo no schema.prisma:
Abra o arquivo prisma/schema.prisma e defina o modelo Produto:
prisma
Copy
model Produto {
  id        Int      @id @default(autoincrement())
  nome      String
  preco     Float
  quantidade Int
}
Crie e aplique as migrações:
bash
Copy
npx prisma migrate dev --name init
Passo 3: Criação da API com Express

Crie o arquivo index.js:
javascript
Copy
const express = require('express');
const { PrismaClient } = require('@prisma/client');

const prisma = new PrismaClient();
const app = express();
const port = 3000;

app.use(express.json());

// Listar todos os produtos
app.get('/produtos', async (req, res) => {
  const produtos = await prisma.produto.findMany();
  res.json(produtos);
});

// Listar um produto específico por id
app.get('/produtos/:id', async (req, res) => {
  const { id } = req.params;
  const produto = await prisma.produto.findUnique({
    where: { id: parseInt(id) },
  });
  res.json(produto);
});

// Criar um novo produto
app.post('/produtos', async (req, res) => {
  const { nome, preco, quantidade } = req.body;
  const novoProduto = await prisma.produto.create({
    data: { nome, preco, quantidade },
  });
  res.json(novoProduto);
});

// Atualizar um produto
app.put('/produtos/:id', async (req, res) => {
  const { id } = req.params;
  const { nome, preco, quantidade } = req.body;
  const produtoAtualizado = await prisma.produto.update({
    where: { id: parseInt(id) },
    data: { nome, preco, quantidade },
  });
  res.json(produtoAtualizado);
});

// Deletar um produto
app.delete('/produtos/:id', async (req, res) => {
  const { id } = req.params;
  const produtoDeletado = await prisma.produto.delete({
    where: { id: parseInt(id) },
  });
  res.json(produtoDeletado);
});

app.listen(port, () => {
  console.log(`Servidor rodando na porta ${port}`);
});
Passo 4: Testando a API

Inicie o servidor:
bash
Copy
node index.js
Importe a coleção no Postman ou Insomnia:
Crie um arquivo JSON com as rotas da API para importar no Postman ou Insomnia. Aqui está um exemplo básico:
json
Copy
{
  "info": {
    "_postman_id": "c0d0b0a0-0b0a-0c0d-0e0f-0a0b0c0d0e0f",
    "name": "CRUD Produtos API",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Listar todos os produtos",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "http://localhost:3000/produtos",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["produtos"]
        }
      }
    },
    {
      "name": "Listar um produto específico",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "http://localhost:3000/produtos/1",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["produtos", "1"]
        }
      }
    },
    {
      "name": "Criar um novo produto",
      "request": {
        "method": "POST",
        "header": [],
        "body": {
          "mode": "raw",
          "raw": "{\n  \"nome\": \"Produto A\",\n  \"preco\": 19.99,\n  \"quantidade\": 10\n}"
        },
        "url": {
          "raw": "http://localhost:3000/produtos",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["produtos"]
        }
      }
    },
    {
      "name": "Atualizar um produto",
      "request": {
        "method": "PUT",
        "header": [],
        "body": {
          "mode": "raw",
          "raw": "{\n  \"nome\": \"Produto A Atualizado\",\n  \"preco\": 29.99,\n  \"quantidade\": 20\n}"
        },
        "url": {
          "raw": "http://localhost:3000/produtos/1",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["produtos", "1"]
        }
      }
    },
    {
      "name": "Deletar um produto",
      "request": {
        "method": "DELETE",
        "header": [],
        "url": {
          "raw": "http://localhost:3000/produtos/1",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["produtos", "1"]
        }
      }
    }
  ]
}
Salve este JSON em um arquivo, por exemplo, CRUD_Produtos_API.postman_collection.json, e importe no Postman ou Insomnia.
Passo 5: Executando e Testando

Listar todos os produtos: GET http://localhost:3000/produtos
Listar um produto específico: GET http://localhost:3000/produtos/1
Criar um novo produto: POST http://localhost:3000/produtos com o corpo JSON:
json
Copy
{
  "nome": "Produto A",
  "preco": 19.99,
  "quantidade": 10
}
Atualizar um produto: PUT http://localhost:3000/produtos/1 com o corpo JSON:
json
Copy
{
  "nome": "Produto A Atualizado",
  "preco": 29.99,
  "quantidade": 20
}
Deletar um produto: DELETE http://localhost:3000/produtos/1
Conclusão

Agora você tem uma API básica de CRUD para a entidade Produto utilizando Express e Prisma com PostgreSQL. Certifique-se de que o arquivo de configuração do Postman ou Insomnia esteja incluído para os testes.
Exercício 2 (Com Postgres)
Crie uma API com as operações CRUD para Clientes contendo os seguintes atributos:
Nome, CPF, Telefone, CEP, Numero, Complemento
Quando for salvar no banco de dados, busque automaticamente o endereco do cliente através do CEP
informado, utilizando a API do ViaCEP.
Depois de buscar o CEP, salve o endereço do cliente no banco de dados completando os seguintes campos de
endereço: logradouro, bairro, cidade, estado
Adicionar junto ao código, o arquivo de configuração do Postman ou Insomnia para os testes (Sem esse arquivo,
a nota será 0)
Utilize Express e Prisma para o CRUD
Utilize Axios para buscar o endereço do cliente no ViaCEP (1000 exemplos na internet)
Para criar uma API de CRUD para Clientes com os atributos solicitados, incluindo a busca automática do endereço pelo CEP utilizando a API do ViaCEP, siga os passos abaixo. Vamos utilizar Express, Prisma e Axios.

Passo 1: Configuração do Projeto

Inicialize o projeto Node.js:
bash
Copy
mkdir crud-clientes
cd crud-clientes
npm init -y
Instale as dependências necessárias:
bash
Copy
npm install express @prisma/client axios
npm install --save-dev prisma
Inicialize o Prisma:
bash
Copy
npx prisma init
Isso criará um diretório prisma com um arquivo schema.prisma e um arquivo .env para configurar a conexão com o banco de dados.
Passo 2: Configuração do Banco de Dados

Configure o arquivo .env:
Abra o arquivo .env e configure a conexão com o PostgreSQL:
env
Copy
DATABASE_URL="postgresql://SEU_USUARIO:SUA_SENHA@localhost:5432/nome_do_banco"
Defina o modelo no schema.prisma:
Abra o arquivo prisma/schema.prisma e defina o modelo Cliente:
prisma
Copy
model Cliente {
  id          Int      @id @default(autoincrement())
  nome        String
  cpf         String   @unique
  telefone    String
  cep         String
  numero      String
  complemento String?
  logradouro  String
  bairro      String
  cidade      String
  estado      String
}
Crie e aplique as migrações:
bash
Copy
npx prisma migrate dev --name init
Passo 3: Criação da API com Express e Axios

Crie o arquivo index.js:
javascript
Copy
const express = require('express');
const { PrismaClient } = require('@prisma/client');
const axios = require('axios');

const prisma = new PrismaClient();
const app = express();
const port = 3000;

app.use(express.json());

// Função para buscar o endereço pelo CEP usando a API do ViaCEP
async function buscarEnderecoPorCEP(cep) {
  try {
    const response = await axios.get(`https://viacep.com.br/ws/${cep}/json/`);
    return response.data;
  } catch (error) {
    console.error('Erro ao buscar o CEP:', error);
    return null;
  }
}

// Listar todos os clientes
app.get('/clientes', async (req, res) => {
  const clientes = await prisma.cliente.findMany();
  res.json(clientes);
});

// Listar um cliente específico por id
app.get('/clientes/:id', async (req, res) => {
  const { id } = req.params;
  const cliente = await prisma.cliente.findUnique({
    where: { id: parseInt(id) },
  });
  res.json(cliente);
});

// Criar um novo cliente
app.post('/clientes', async (req, res) => {
  const { nome, cpf, telefone, cep, numero, complemento } = req.body;

  // Busca o endereço pelo CEP
  const endereco = await buscarEnderecoPorCEP(cep);
  if (!endereco || endereco.erro) {
    return res.status(400).json({ error: 'CEP inválido ou não encontrado' });
  }

  const novoCliente = await prisma.cliente.create({
    data: {
      nome,
      cpf,
      telefone,
      cep,
      numero,
      complemento,
      logradouro: endereco.logradouro,
      bairro: endereco.bairro,
      cidade: endereco.localidade,
      estado: endereco.uf,
    },
  });
  res.json(novoCliente);
});

// Atualizar um cliente
app.put('/clientes/:id', async (req, res) => {
  const { id } = req.params;
  const { nome, cpf, telefone, cep, numero, complemento } = req.body;

  // Busca o endereço pelo CEP
  const endereco = await buscarEnderecoPorCEP(cep);
  if (!endereco || endereco.erro) {
    return res.status(400).json({ error: 'CEP inválido ou não encontrado' });
  }

  const clienteAtualizado = await prisma.cliente.update({
    where: { id: parseInt(id) },
    data: {
      nome,
      cpf,
      telefone,
      cep,
      numero,
      complemento,
      logradouro: endereco.logradouro,
      bairro: endereco.bairro,
      cidade: endereco.localidade,
      estado: endereco.uf,
    },
  });
  res.json(clienteAtualizado);
});

// Deletar um cliente
app.delete('/clientes/:id', async (req, res) => {
  const { id } = req.params;
  const clienteDeletado = await prisma.cliente.delete({
    where: { id: parseInt(id) },
  });
  res.json(clienteDeletado);
});

app.listen(port, () => {
  console.log(`Servidor rodando na porta ${port}`);
});
Passo 4: Testando a API

Inicie o servidor:
bash
Copy
node index.js
Importe a coleção no Postman ou Insomnia:
Crie um arquivo JSON com as rotas da API para importar no Postman ou Insomnia. Aqui está um exemplo básico:
json
Copy
{
  "info": {
    "_postman_id": "c0d0b0a0-0b0a-0c0d-0e0f-0a0b0c0d0e0f",
    "name": "CRUD Clientes API",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Listar todos os clientes",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "http://localhost:3000/clientes",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["clientes"]
        }
      }
    },
    {
      "name": "Listar um cliente específico",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "http://localhost:3000/clientes/1",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["clientes", "1"]
        }
      }
    },
    {
      "name": "Criar um novo cliente",
      "request": {
        "method": "POST",
        "header": [],
        "body": {
          "mode": "raw",
          "raw": "{\n  \"nome\": \"João Silva\",\n  \"cpf\": \"123.456.789-00\",\n  \"telefone\": \"(11) 99999-9999\",\n  \"cep\": \"01001000\",\n  \"numero\": \"123\",\n  \"complemento\": \"Apto 101\"\n}"
        },
        "url": {
          "raw": "http://localhost:3000/clientes",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["clientes"]
        }
      }
    },
    {
      "name": "Atualizar um cliente",
      "request": {
        "method": "PUT",
        "header": [],
        "body": {
          "mode": "raw",
          "raw": "{\n  \"nome\": \"João Silva Atualizado\",\n  \"cpf\": \"123.456.789-00\",\n  \"telefone\": \"(11) 88888-8888\",\n  \"cep\": \"01001000\",\n  \"numero\": \"456\",\n  \"complemento\": \"Apto 202\"\n}"
        },
        "url": {
          "raw": "http://localhost:3000/clientes/1",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["clientes", "1"]
        }
      }
    },
    {
      "name": "Deletar um cliente",
      "request": {
        "method": "DELETE",
        "header": [],
        "url": {
          "raw": "http://localhost:3000/clientes/1",
          "protocol": "http",
          "host": ["localhost"],
          "port": "3000",
          "path": ["clientes", "1"]
        }
      }
    }
  ]
}
Salve este JSON em um arquivo, por exemplo, CRUD_Clientes_API.postman_collection.json, e importe no Postman ou Insomnia.
Passo 5: Executando e Testando

Listar todos os clientes: GET http://localhost:3000/clientes
Listar um cliente específico: GET http://localhost:3000/clientes/1
Criar um novo cliente: POST http://localhost:3000/clientes com o corpo JSON:
json
Copy
{
  "nome": "João Silva",
  "cpf": "123.456.789-00",
  "telefone": "(11) 99999-9999",
  "cep": "01001000",
  "numero": "123",
  "complemento": "Apto 101"
}
Atualizar um cliente: PUT http://localhost:3000/clientes/1 com o corpo JSON:
json
Copy
{
  "nome": "João Silva Atualizado",
  "cpf": "123.456.789-00",
  "telefone": "(11) 88888-8888",
  "cep": "01001000",
  "numero": "456",
  "complemento": "Apto 202"
}
Deletar um cliente: DELETE http://localhost:3000/clientes/1
Conclusão

Agora você tem uma API de CRUD para Clientes que busca automaticamente o endereço pelo CEP utilizando a API do ViaCEP. Certifique-se de incluir o arquivo de configuração do Postman ou Insomnia para os testes.
Como identificar o meu localhoost do computador e o que alterar no testo
O localhost é um endereço padrão que refere-se ao próprio computador onde o código está sendo executado. Ele é comumente representado pelo endereço IP 127.0.0.1. Quando você roda um servidor local (como o servidor Express que criamos), ele fica acessível através do localhost na porta especificada (no nosso caso, a porta 3000).

Aqui estão as respostas para suas perguntas:

1. Como identificar o localhost do computador?

O localhost é sempre o mesmo em qualquer computador: 127.0.0.1.
Quando você acessa http://localhost:3000 no navegador ou em ferramentas como Postman/Insomnia, está se referindo ao servidor rodando na sua própria máquina.
Se você quiser acessar o servidor de outro dispositivo na mesma rede, precisará usar o endereço IP da sua máquina na rede local (não o localhost).
Como descobrir o endereço IP da sua máquina na rede local:

No Windows:
Abra o Prompt de Comando (cmd).
Digite ipconfig e pressione Enter.
Procure por IPv4 Address na seção da sua conexão de rede (Wi-Fi ou Ethernet).
No Linux/Mac:
Abra o terminal.
Digite ifconfig (Linux/Mac) ou ip a (Linux).
Procure por inet na seção da sua conexão de rede.
Exemplo de endereço IP local: 192.168.1.100.

2. O que alterar no teste?

Se você estiver testando a API no mesmo computador onde o servidor está rodando:

Use http://localhost:3000 como base para todas as requisições.
Exemplo: GET http://localhost:3000/clientes.
Se você estiver testando a API de outro dispositivo na mesma rede:

Substitua localhost pelo endereço IP da máquina onde o servidor está rodando.
Exemplo: Se o IP da máquina do servidor for 192.168.1.100, use http://192.168.1.100:3000/clientes.
Atenção:

Certifique-se de que o firewall do computador onde o servidor está rodando permite conexões na porta 3000.
Se estiver usando um serviço de hospedagem ou um ambiente de nuvem, o endereço será fornecido pelo provedor (não será localhost).
3. Exemplo de alteração no Postman/Insomnia

Se você estiver usando o Postman ou Insomnia para testar a API, basta substituir localhost pelo endereço IP da máquina do servidor, se necessário.

Exemplo de requisição no Postman:

Mesmo computador:
Copy
GET http://localhost:3000/clientes
Outro dispositivo na rede:
Copy
GET http://192.168.1.100:3000/clientes
4. Resumo

Localhost: Use http://localhost:3000 se estiver testando no mesmo computador.
IP da rede local: Use http://<IP_DA_MAQUINA>:3000 se estiver testando de outro dispositivo na mesma rede.
Firewall: Certifique-se de que a porta 3000 está liberada no firewall.
Se precisar de mais ajuda, é só perguntar