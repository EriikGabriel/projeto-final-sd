# Projeto final de Sistemas Distribuídos - Leilão Online Distribuído

**Integrantes**

- Erik Gabriel Rodrigo da Silva
- Felipe Bonadia de Oliveira Bravo

**Objetivo**

> Projetar, implementar, implantar e testar uma **plataforma de leilão online distribuída** utilizando **virtualização com Multipass**, **Redis**, **Docker Swarm** e **Flask**. O sistema deve ser escalável, tolerante a falhas e capaz de lidar com um grande número de usuários simultâneos com tempo de inatividade mínimo.

## Estrutura de pastas e arquivos do projeto

```
auction-system/
├── backend/
│ ├── app/
│ │ ├── init.py # Inicialização e configuração da aplicação Flask
│ │ ├── config.py # Variáveis de configuração (ex.: conexão com Redis)
│ │ ├── models.py # Definição das estruturas de dados para leilões e lances
│ │ ├── routes.py # Rotas da API:
│ │ │ # /create-auction, /view-auctions, /place-bid e /auction/{auction_id}
│ │ └── redis_pubsub.py # Implementação do Pub/Sub com Redis para notificações em tempo real
│ ├── run.py # Ponto de entrada da aplicação Flask
│ └── requirements.txt # Dependências Python (Flask, redis, Flask-SocketIO, etc.)
│
├── frontend/
│ ├── public/
│ ├── src/
│ │ ├── components/
│ │ │ ├── ActiveAuctions.jsx # Exibe a lista de leilões ativos
│ │ │ ├── AuctionCard.jsx # Mostra detalhes e lances em tempo real de um leilão
│ │ │ ├── ClosedAuctions.jsx # Exibe a lista de leilões encerrados
│ │ │ ├── CreateAuction.jsx # Formulário para criação de um novo leilão
│ │ │ └── Header.jsx # Cabeçalho da aplicação
│ │ ├── services/
│ │ │ └── api.js # Funções para interagir com a API do Flask (fetch/axios)
│ │ ├── utils/
│ │ │ └── cn.js # Função para lidar com as mudança de estilo (classes) do tailwind através de condicionais
│ │ ├── App.jsx # Componente principal que orquestra os demais componentes
│ │ ├── index.css # Folha de estilos principal da aplicação
│ │ └── main.jsx # Ponto de entrada da aplicação React
│ └── package.json # Dependências e scripts do projeto React
│
├── docker/
│ ├── Dockerfile # Define a imagem Docker para a aplicação (pode ser separado para backend e frontend, se necessário)
│ ├── docker-compose.yml # Configuração para orquestração local dos contêineres (Flask, Redis, React)
│ └── swarm-init.sh # Script para inicializar e configurar o cluster Docker Swarm
│
├── multipass/
│ └── setup_multipass.sh # Script para criar VMs com Multipass e configurar a rede entre os nós (Flask e Redis)
├── .gitignore # Arquivo .gitignore para o repositório github
└── README.md # Documentação geral do projeto
```

## Setup - Front-end

Para configurar o front-end, basta entrar na pasta _frontend_ e pelo terminal digitar os seguintes comandos

```bash
npm install # Para instalar todas as dependências do projeto
```

```bash
npm run dev # Para rodar o servidor de desenvolvimento local do projeto
```

Pronto! Agora o front-end deve estar acessível através da URL: [http://localhost:5173/](http://localhost:5173/)

## Setup - projeto

Para configurar o projeto, primeiro clone o repositório:
```bash
https://github.com/EriikGabriel/distributed-auction-sd.git 
```
Em seguida, no diretório raiz do projeto clonado, execute o comando:
```bash
./setup_multipass.sh
```
Pronto, agora seu projeto já está funcionando e esta apto para testes

Por útlimo, acesse o link do Fronted gerado no terminal e você verá a interface do Leilão

## Testes

Nessa seção descreveremos os testes realizados:

### **Cenários de Teste Automatizados**  

1. **Criar um leilão com sucesso**  
   - Enviar uma requisição para criar um leilão com um ID e um título válidos.  
   - Esperado: O leilão é armazenado no Redis e a resposta contém "Auction created!" com status 201.  

2. **Criar um leilão sem ID**  
   - Enviar uma requisição para criar um leilão sem fornecer um ID.  
   - Esperado: A API retorna erro "Dados inválidos!" com status 400.  

3. **Listar todos os leilões disponíveis**  
   - Enviar uma requisição para visualizar os leilões cadastrados.  
   - Esperado: Retorna uma lista de leilões com status 200.  

4. **Consultar detalhes de um leilão existente**  
   - Criar um leilão e consultar seus detalhes.  
   - Esperado: Retorna informações do leilão corretamente com status 200.  

5. **Consultar detalhes de um leilão inexistente**  
   - Consultar um leilão com ID inexistente.  
   - Esperado: Retorna erro "Leilão não encontrado!" com status 404.  

6. **Inserir um lance válido em um leilão ativo**  
   - Criar um leilão ativo e enviar um lance maior que o atual.  
   - Esperado: O lance é aceito, o valor do lance atual é atualizado e uma notificação é enviada via Pub/Sub.  

7. **Inserir um lance abaixo do valor atual**  
   - Criar um leilão com um lance inicial e tentar inserir um lance menor.  
   - Esperado: A API rejeita o lance e retorna erro "Bid muito baixo!" com status 400.  

8. **Inserir um lance em um leilão inativo**  
   - Criar um leilão inativo e tentar inserir um lance.  
   - Esperado: A API retorna erro "O leilão não está ativo!" com status 400.  

9. **Inserir um lance em um leilão inexistente**  
   - Tentar inserir um lance em um leilão que não existe.  
   - Esperado: A API retorna erro "Leilão não encontrado!" com status 404.  

10. **Carga com grande número de requisições**  
   - Enviar muitas requisições para criar leilões e inserir lances em alta frequência.  
   - Esperado: O sistema mantém a estabilidade e não apresenta falhas.  

11. **Notificações em tempo real (Pub/Sub)**  
   - Criar um leilão, conectar-se ao canal Pub/Sub e inserir um lance.  
   - Esperado: Todos os clientes conectados recebem a notificação do novo lance imediatamente .






