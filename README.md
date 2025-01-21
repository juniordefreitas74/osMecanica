# osMecanica
Ordem de Serviço para oficina mecânica 

# Sistema de Gerenciamento de Ordens de Serviço para Oficina Mecânica

Este repositório contém o modelo de banco de dados para um sistema de gerenciamento de ordens de serviço (OS) para uma oficina mecânica. O modelo foi projetado para atender às necessidades de registro de clientes, veículos, equipes de mecânicos, serviços, peças e o controle completo das ordens de serviço.

## Descrição do Sistema

O sistema tem como objetivo principal gerenciar as ordens de serviço em uma oficina mecânica, desde o cadastro de clientes e veículos até a execução dos serviços, registro de peças utilizadas e o cálculo dos valores totais.

### Funcionalidades Principais

*   **Cadastro de Clientes:** Armazenamento de informações de clientes (nome, CPF, telefone, endereço).
*   **Cadastro de Veículos:** Registro de veículos dos clientes (placa, marca, modelo, ano), com vínculo ao cliente.
*   **Gestão de Equipes:** Cadastro de equipes de mecânicos e seus membros, com suas respectivas especialidades.
*   **Gestão de Mecânicos:** Cadastro de mecânicos com seus dados (nome, CPF, endereço, especialidade) e vinculação a uma equipe.
*   **Cadastro de Serviços:** Registro de serviços oferecidos pela oficina, com seus respectivos valores de mão de obra.
*   **Cadastro de Peças:** Registro de peças utilizadas na oficina, com seus respectivos valores.
*   **Emissão de Ordens de Serviço:** Criação de ordens de serviço, com vínculo ao veículo e equipe responsável.
*   **Registro de Serviços/Peças na OS:** Adição de serviços e peças na ordem de serviço, com registro de quantidade e valores.
*   **Cálculo Automático de Valores:** Cálculo do valor total da OS baseado em serviços e peças.
*   **Status da OS:** Controle do status da ordem de serviço (aberta, em andamento, concluída, etc.).
*   **Autorização do Cliente:** Registro de autorização do cliente para execução da OS.

## Modelo de Banco de Dados

O modelo de banco de dados é composto pelas seguintes tabelas, com seus respectivos campos:

### Diagrama Entidade-Relacionamento (DER)

```mermaid'''
erDiagram
    CLIENTE {
        int id_cliente PK
        varchar nome
        varchar cpf
        varchar telefone
        varchar endereco
    }
    VEICULO {
        int id_veiculo PK
        int id_cliente FK
        varchar placa
        varchar marca
        varchar modelo
        varchar ano
    }
    EQUIPE_MECANICOS {
        int id_equipe PK
        varchar nome
        varchar especialidade
    }
    MECANICO {
        int id_mecanico PK
        int id_equipe FK
        varchar nome
        varchar cpf
        varchar endereco
        varchar especialidade
    }
    ORDEM_SERVICO {
        int id_os PK
        int id_veiculo FK
        int id_equipe FK
        datetime data_emissao
        decimal valor_total
        varchar status
        datetime data_conclusao
        boolean autorizada
    }
    SERVICO {
        int id_servico PK
        varchar nome
        decimal preco_mao_de_obra
        varchar descricao
    }
    PECA {
        int id_peca PK
        varchar nome
        decimal preco
        varchar descricao
    }
   ITEM_ORDEM_SERVICO {
        int id_item_os PK
        int id_os FK
        int id_servico FK
        int quantidade
        decimal valor_mao_de_obra
   }
   ITEM_ORDEM_PECA {
    int id_item_peca PK
    int id_os FK
    int id_peca FK
    int quantidade
    decimal valor_peca
   }
    
    
    CLIENTE ||--o{ VEICULO : possui
    VEICULO ||--o{ ORDEM_SERVICO : gera
    EQUIPE_MECANICOS ||--o{ ORDEM_SERVICO : alocada
    EQUIPE_MECANICOS ||--o{ MECANICO : contém
    ORDEM_SERVICO ||--o{ ITEM_ORDEM_SERVICO : contém
    SERVICO ||--o{ ITEM_ORDEM_SERVICO : relacionado
    ORDEM_SERVICO ||--o{ ITEM_ORDEM_PECA : contém
    PECA ||--o{ ITEM_ORDEM_PECA : relacionado
content_copy
download
Use code with caution.
Markdown
Esquema Relacional (SQL)
-- Tabela CLIENTE
CREATE TABLE CLIENTE (
    id_cliente INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    cpf VARCHAR(14) UNIQUE NOT NULL,
    telefone VARCHAR(20),
    endereco VARCHAR(255)
);

-- Tabela VEICULO
CREATE TABLE VEICULO (
    id_veiculo INT PRIMARY KEY AUTO_INCREMENT,
    id_cliente INT NOT NULL,
    placa VARCHAR(10) UNIQUE NOT NULL,
    marca VARCHAR(50) NOT NULL,
    modelo VARCHAR(50) NOT NULL,
    ano YEAR NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES CLIENTE(id_cliente)
);

-- Tabela EQUIPE_MECANICOS
CREATE TABLE EQUIPE_MECANICOS (
    id_equipe INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    especialidade VARCHAR(255)
);

-- Tabela MECANICO
CREATE TABLE MECANICO (
    id_mecanico INT PRIMARY KEY AUTO_INCREMENT,
    id_equipe INT NOT NULL,
    nome VARCHAR(255) NOT NULL,
    cpf VARCHAR(14) UNIQUE NOT NULL,
    endereco VARCHAR(255),
    especialidade VARCHAR(255),
    FOREIGN KEY (id_equipe) REFERENCES EQUIPE_MECANICOS(id_equipe)
);

-- Tabela ORDEM_SERVICO
CREATE TABLE ORDEM_SERVICO (
    id_os INT PRIMARY KEY AUTO_INCREMENT,
    id_veiculo INT NOT NULL,
    id_equipe INT NOT NULL,
    data_emissao DATETIME NOT NULL,
    valor_total DECIMAL(10,2),
    status VARCHAR(50) NOT NULL,
    data_conclusao DATETIME,
    autorizada BOOLEAN,
    FOREIGN KEY (id_veiculo) REFERENCES VEICULO(id_veiculo),
    FOREIGN KEY (id_equipe) REFERENCES EQUIPE_MECANICOS(id_equipe)
);

-- Tabela SERVICO
CREATE TABLE SERVICO (
    id_servico INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    preco_mao_de_obra DECIMAL(10, 2) NOT NULL,
    descricao TEXT
);

-- Tabela PECA
CREATE TABLE PECA(
  id_peca INT PRIMARY KEY AUTO_INCREMENT,
  nome VARCHAR(255) NOT NULL,
  preco DECIMAL(10,2) NOT NULL,
  descricao TEXT
);

-- Tabela ITEM_ORDEM_SERVICO
CREATE TABLE ITEM_ORDEM_SERVICO(
    id_item_os INT PRIMARY KEY AUTO_INCREMENT,
    id_os INT NOT NULL,
    id_servico INT NOT NULL,
    quantidade INT NOT NULL,
    valor_mao_de_obra DECIMAL(10,2),
    FOREIGN KEY (id_os) REFERENCES ORDEM_SERVICO(id_os),
    FOREIGN KEY (id_servico) REFERENCES SERVICO(id_servico)
);

-- Tabela ITEM_ORDEM_PECA
CREATE TABLE ITEM_ORDEM_PECA(
    id_item_peca INT PRIMARY KEY AUTO_INCREMENT,
    id_os INT NOT NULL,
    id_peca INT NOT NULL,
    quantidade INT NOT NULL,
    valor_peca DECIMAL(10,2),
    FOREIGN KEY (id_os) REFERENCES ORDEM_SERVICO(id_os),
    FOREIGN KEY (id_peca) REFERENCES PECA(id_peca)
);
