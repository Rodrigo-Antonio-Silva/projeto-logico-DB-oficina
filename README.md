# Projeto-Logico-de-Banco-de-Dados
<h1>🚗  Sistema de Gestão para Oficina Mecânica </h1>

<h3>📌 Descrição do Projeto</h3>
<p>Este projeto representa um modelo de banco de dados relacional para uma oficina mecânica.</p>

<h3>📊 Modelo Entidade-Relacionamento (ER)</h3>

<b>O modelo foi criado com base nos seguintes conceitos:</b>

<ul>
<li>Clientes possuem veículos.</li>
<li>Veículos podem ter vários serviços.</li>
<li>Serviços são executados por uma equipe de mecânicos.</li>
<li>Serviços podem envolver peças e mão de obra com valores específicos.</li>
</ul>

<h3>🛠 Modelo Lógico - SQL</h3>

<b>O banco de dados foi estruturado da seguinte forma:</b>

<i>🔹 Tabelas Principais</i>
<ul>
<li>cliente – Armazena informações dos clientes.</li>
<li>veiculo – Contém dados dos veículos cadastrados.</li>
<li>servico – Registra os serviços realizados.</li>
<li>mecanicos – Lista os mecânicos disponíveis.</li>
<li>equipe – Grupos de mecânicos especializados.</li>
</ul>
<i>🔗 Relacionamentos</i>
<ul>
<li>cliente_veiculo – Relaciona clientes aos seus veículos.</li>
<li>veiculos_servico – Associa veículos aos serviços realizados.</li>
<li>servico_preco_mao_obra – Liga serviços aos valores de mão de obra.</li>
<li>servicos_preco_peca – Conecta serviços às peças utilizadas.</li>
</ul>
<h3>1️⃣ Criar o Banco de Dados</h3>
## Estrutura do Banco de Dados da Oficina

Este repositório contém o script SQL para criar o banco de dados da oficina.

### Estrutura das Tabelas

```sql
CREATE DATABASE oficina;
USE oficina;

-- ... (restante do seu código SQL) ...
CREATE DATABASE oficina;
USE oficina;

CREATE TABLE cliente (
    idCliente INT PRIMARY KEY auto_increment,
    Pnome VARCHAR(15),
    NomeMeio VARCHAR(15),
    Sobrenome VARCHAR(15),
    CPF CHAR(11) UNIQUE,
    endereco VARCHAR(45),
    Data_nasc DATE,
    constraint cliente_dpf unique(CPF)
);

CREATE TABLE veiculo (
    idVeiculo INT PRIMARY KEY auto_increment,
    Montadora VARCHAR(15),
    Veiculo ENUM('Carro', 'Moto', 'Caminhão') default 'Carro',
    TpCombustivel ENUM('Gasolina', 'Etanol', 'Diesel') default 'Gasolina', 
    Ano INT
);

CREATE TABLE cliente_veiculo (
    Cliente_idCliente INT,
    Veiculos_idVeiculos INT,
    PRIMARY KEY (Cliente_idCliente, Veiculos_idVeiculos),
    constraint cliente_veiculo_cliente_fk foreign KEY (Cliente_idCliente) REFERENCES cliente(idCliente),
    constraint cliente_veiculo_veiculo_fk FOREIGN KEY (Veiculos_idVeiculos) REFERENCES veiculo(idVeiculo)
);

CREATE TABLE servico (
    idServico INT PRIMARY KEY auto_increment,
    TpServico ENUM('Troca de Óleo', 'Revisão', 'Pintura') default 'Revisão', 
    DtAvaliacao DATE not null,
    DtEntrega DATE not null,
    StatusServico Enum('Aberto', 'Em andamento', 'Concluído', 'Fechado') NOT NULL default 'Aberto',
    ValorServico DECIMAL(10,2) NOT NULL,
    Equipe_idEquipe INT,
    constraint equipe_fk FOREIGN KEY (Equipe_idEquipe) references equipe(idEquipe)
);

CREATE TABLE veiculos_servico (
    Veiculos_idVeiculos INT,
    Servicos_idServicos INT,
    PRIMARY KEY (Veiculos_idVeiculos, Servicos_idServicos),
    constraint veiculos_servicos_veiculos_fk FOREIGN KEY (Veiculos_idVeiculos) REFERENCES veiculo(idVeiculo),
    constraint veiculos_servico_servico_fk FOREIGN KEY (Servicos_idServicos) REFERENCES servico(idServico)
);

CREATE TABLE preco_mao_obra (
    idPreco INT PRIMARY KEY auto_increment,
    DescricaoMO VARCHAR(45),
    Preco DECIMAL(10,2) NOT NULL
);

CREATE TABLE servico_preco_mao_obra (
    idMO_Servico INT PRIMARY KEY auto_increment,
    Servicos_idServicos INT,
    PrecoMaoObra_idPreco INT,
    constraint servico_mo_servico_fk FOREIGN KEY (Servicos_idServicos) REFERENCES servico(idServico),
    constraint servico_mo_preco_fk FOREIGN KEY (PrecoMaoObra_idPreco) REFERENCES preco_mao_obra(idPreco)
);

CREATE TABLE preco_peca (
    idPrecoPecas INT PRIMARY KEY auto_increment,
    Descricao VARCHAR(45) NOT NULL,
    Preco DECIMAL(10,2) NOT NULL
);

CREATE TABLE servicos_preco_peca (
    idPrecoPecas INT PRIMARY KEY auto_increment,
    Servicos_idServicos INT,
    PrecoPecas_idPrecoPecas INT,
    constraint servico_peca_servico_fk FOREIGN KEY (Servicos_idServicos) REFERENCES servico(idServico),
    constraint servico_peca_peca_fk FOREIGN KEY (PrecoPecas_idPrecoPecas) REFERENCES preco_peca(idPrecoPecas)
);

CREATE TABLE mecanicos (
    idMecanicos INT PRIMARY KEY auto_increment,
    Nome VARCHAR(15) not null,
    Cod VARCHAR(15) not null,
    CPF CHAR(11) NOT NULL UNIQUE,
    logradouro VARCHAR(25) NOT NULL,
    cidade VARCHAR(15) NOT NULL,
    estado CHAR(2) NOT NULL,
    Especialidade VARCHAR(15) NOT NULL,
    constraint CPF_mecanicos unique(CPF)
);

CREATE TABLE equipe (
    idEquipe INT PRIMARY KEY,
    Especialidade VARCHAR(45),
    idLider INT,
    constraint equipe_lider_fk FOREIGN KEY (idLider) REFERENCES mecanicos(idMecanicos)
); 
4️⃣ ############### Executar Consultas ####################

🔎 Listar todos os clientes e seus veículos

SELECT c.idCliente, CONCAT(c.Pnome, ' ', c.Sobrenome) AS Nome, v.Montadora, v.Veiculo, v.Ano
FROM cliente c
JOIN cliente_veiculo cv ON c.idCliente = cv.Cliente_idCliente
JOIN veiculo v ON cv.Veiculos_idVeiculos = v.idVeiculo;

1	Carlos Silva	Toyota	Carro	2018
2	Mariana Souza	Honda	Moto	2020
3	Pedro Oliveira	Ford	Carro	2015

📌 Buscar serviços com valor acima de R$ 500,00

SELECT * FROM servico WHERE ValorServico > 500;

2	Revisão	2024-01-10	2024-01-15	Em andamento	600.00	2
3	Pintura	2024-01-20		Aberto	1200.00	1

⏳ Tempo médio de conclusão dos serviços

SELECT TpServico, AVG(DATEDIFF(DtEntrega, DtAvaliacao)) AS Media_Dias_Conclusao
FROM servico
WHERE DtEntrega IS NOT NULL
GROUP BY TpServico;

Troca de Óleo	1.0000
Revisão	5.0000

🏆 Equipe com maior número de serviços concluídos

SELECT e.idEquipe, e.Especialidade, COUNT(s.idServico) AS Total_Servicos
FROM equipe e
JOIN servico s ON e.idEquipe = s.Equipe_idEquipe
WHERE s.StatusServico = 'Concluído'
GROUP BY e.idEquipe
ORDER BY Total_Servicos DESC
LIMIT 1;

1	Motor	1

📌 Autor

Desenvolvido por Rodrigo. Se tiver sugestões ou melhorias, fique à vontade para contribuir! 😃
						
